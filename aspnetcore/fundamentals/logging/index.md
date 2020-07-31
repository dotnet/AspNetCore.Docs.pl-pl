---
title: Rejestrowanie na platformie .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać struktury rejestrowania dostarczonej przez pakiet NuGet Microsoft. Extensions. Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330772"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="f8e9f-103">Rejestrowanie na platformie .NET Core i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8e9f-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8e9f-104">[Kirka Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8e9f-105">Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f8e9f-106">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f8e9f-107">Większość przykładów kodu pokazanych w tym artykule pochodzą z ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="f8e9f-108">Części tych fragmentów kodu dotyczące rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która korzysta z [hosta ogólnego](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="f8e9f-109">Szablony aplikacji sieci Web ASP.NET Core korzystają z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="f8e9f-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="f8e9f-111">Dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-111">Logging providers</span></span>

<span data-ttu-id="f8e9f-112">Dostawcy rejestrowania przechowują dzienniki, z wyjątkiem `Console` dostawcy wyświetlającego dzienniki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="f8e9f-113">Na przykład dostawca usługi Azure Application Insights przechowuje dzienniki w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="f8e9f-114">Można włączyć wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="f8e9f-115">Domyślne szablony aplikacji sieci Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="f8e9f-116">Użyj [hosta ogólnego](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="f8e9f-117">Wywołanie <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , które dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="f8e9f-118">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-118">Console</span></span>](#console)
  * [<span data-ttu-id="f8e9f-119">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="f8e9f-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="f8e9f-121">[EventLog](#welog): tylko system Windows</span><span class="sxs-lookup"><span data-stu-id="f8e9f-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="f8e9f-122">Poprzedni kod przedstawia `Program` klasę utworzoną za pomocą szablonów aplikacji sieci web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="f8e9f-123">W następnych sekcjach przedstawiono przykłady w oparciu o szablony aplikacji sieci Web ASP.NET Core, które korzystają z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="f8e9f-124">[Aplikacje konsolowe inne niż host](#nhca) są omawiane w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="f8e9f-125">Aby zastąpić domyślny zestaw dostawców rejestrowania dodany przez `Host.CreateDefaultBuilder` , należy wywołać `ClearProviders` i dodać wymaganych dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="f8e9f-126">Na przykład następujący kod:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-126">For example, the following code:</span></span>

* <span data-ttu-id="f8e9f-127">Wywołuje, <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> Aby usunąć wszystkie <xref:Microsoft.Extensions.Logging.ILoggerProvider> wystąpienia z konstruktora.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="f8e9f-128">Dodaje dostawcę rejestrowania [konsoli](#console) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="f8e9f-129">Aby uzyskać dodatkowych dostawców, zobacz:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-129">For additional providers, see:</span></span>

* [<span data-ttu-id="f8e9f-130">Wbudowani dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="f8e9f-131">[Dostawcy rejestrowania innych firm](#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="f8e9f-132">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="f8e9f-132">Create logs</span></span>

<span data-ttu-id="f8e9f-133">Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu z [iniekcji zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="f8e9f-134">Poniższy przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-134">The following example:</span></span>

* <span data-ttu-id="f8e9f-135">Tworzy rejestrator, `ILogger<AboutModel>` który używa *kategorii* dziennika w pełni kwalifikowanej nazwy typu `AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="f8e9f-136">Kategoria dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="f8e9f-137">Wywołania <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> do rejestrowania na `Information` poziomie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="f8e9f-138">*Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="f8e9f-139">[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="f8e9f-140">Aby uzyskać informacje na temat Blazor , zobacz [Tworzenie dzienników w Blazor i Blazor WebAssembly ](#clib) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="f8e9f-141">[Tworzenie dzienników w programie głównych i początkowych](#clms) przedstawia sposób tworzenia dzienników w `Main` i `Startup` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="f8e9f-142">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-142">Configure logging</span></span>

<span data-ttu-id="f8e9f-143">Konfiguracja rejestrowania jest zwykle udostępniana w `Logging` sekcji *AppSettings*. `{Environment}` pliki *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="f8e9f-144">Następujące *appsettings.Development.js* pliku są generowane przez Szablony aplikacji sieci Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="f8e9f-145">W powyższym formacie JSON:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-145">In the preceding JSON:</span></span>

* <span data-ttu-id="f8e9f-146">`"Default"`Określono, `"Microsoft"` , i `"Microsoft.Hosting.Lifetime"` Kategorie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="f8e9f-147">`"Microsoft"`Kategoria dotyczy wszystkich kategorii, które zaczynają się od `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="f8e9f-148">Na przykład to ustawienie ma zastosowanie do `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="f8e9f-149">`"Microsoft"`Kategoria rejestruje się na poziomie dziennika `Warning` lub wyższym.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="f8e9f-150">`"Microsoft.Hosting.Lifetime"`Kategoria jest bardziej precyzyjna niż `"Microsoft"` Kategoria, więc jest `"Microsoft.Hosting.Lifetime"` rejestrowana w kategorii na poziomie dziennika "informacje" i wyższych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="f8e9f-151">Określony dostawca dziennika nie został określony, dlatego `LogLevel` dotyczy wszystkich włączonych dostawców rejestrowania z wyjątkiem [dziennika zdarzeń systemu Windows](#welog).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="f8e9f-152">`Logging`Właściwość może mieć <xref:Microsoft.Extensions.Logging.LogLevel> właściwości dostawcy dzienników i.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="f8e9f-153">`LogLevel`Określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f8e9f-154">W poprzednim pliku JSON `Information` i `Warning` poziomy dziennika są określone.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="f8e9f-155">`LogLevel`wskazuje ważność dziennika i zakres od 0 do 6:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="f8e9f-156">`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 i `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="f8e9f-157">Gdy `LogLevel` jest określony, funkcja rejestrowania jest włączona dla komunikatów na określonym poziomie i wyższych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="f8e9f-158">W poprzednim pliku JSON `Default` Kategoria jest zarejestrowana dla `Information` i wyższa.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="f8e9f-159">Na przykład,,, `Information` `Warning` `Error` i `Critical` komunikaty są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="f8e9f-160">Jeśli nie `LogLevel` jest określony, rejestrowane są wartości domyślne na `Information` poziomie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="f8e9f-161">Aby uzyskać więcej informacji, zobacz [poziomy dzienników](#llvl).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="f8e9f-162">Właściwość dostawcy może określać `LogLevel` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="f8e9f-163">`LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy i zastępuje ustawienia dziennika niedostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="f8e9f-164">Rozważmy następujące *appsettings.js* pliku:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="f8e9f-165">Ustawienia w `Logging.{providername}.LogLevel` ustawieniach przesłonięcia w programie `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="f8e9f-166">W powyższym kodzie JSON `Debug` domyślny poziom rejestrowania dostawcy jest ustawiany na `Information` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="f8e9f-167">Powyższe ustawienie określa `Information` poziom rejestrowania dla każdej `Logging:Debug:` kategorii z wyjątkiem `Microsoft.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="f8e9f-168">Gdy określona kategoria jest wymieniona, określona Kategoria zastępuje domyślną kategorię.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="f8e9f-169">W powyższym formacie JSON `Logging:Debug:LogLevel` Kategorie `"Microsoft.Hosting"` i `"Default"` zastępują ustawienia w`Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="f8e9f-170">Minimalny poziom dziennika można określić dla dowolnego z:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="f8e9f-171">Określeni dostawcy: na przykład`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="f8e9f-172">Określone kategorie: na przykład`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="f8e9f-173">Wszyscy dostawcy i wszystkie kategorie:`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="f8e9f-174">Wszystkie dzienniki poniżej minimalnego poziomu ***nie***są następujące:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="f8e9f-175">Przeszedł do dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-175">Passed to the provider.</span></span>
* <span data-ttu-id="f8e9f-176">Zarejestrowane lub wyświetlone.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-176">Logged or displayed.</span></span>

<span data-ttu-id="f8e9f-177">Aby pominąć wszystkie dzienniki, należy określić [wartość LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="f8e9f-178">`LogLevel.None`ma wartość 6, która jest większa niż `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="f8e9f-179">Jeśli dostawca obsługuje [zakresy rejestrowania](#logscopes), `IncludeScopes` wskazuje, czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f8e9f-180">Aby uzyskać więcej informacji, zobacz [zakresy dzienników](#logscopes)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="f8e9f-181">Następująca *appsettings.jsw* pliku zawiera wszystkich dostawców włączonych domyślnie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="f8e9f-182">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-182">In the preceding sample:</span></span>

* <span data-ttu-id="f8e9f-183">Kategorie i poziomy nie są sugerowanymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="f8e9f-184">Przykład podano, aby wyświetlić wszystkich dostawców domyślnych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="f8e9f-185">Ustawienia w `Logging.{providername}.LogLevel` ustawieniach przesłonięcia w programie `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="f8e9f-186">Na przykład poziom w `Debug.LogLevel.Default` zastępują poziom w `LogLevel.Default` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="f8e9f-187">Każdy domyślny *alias* dostawcy jest używany.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="f8e9f-188">Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="f8e9f-189">Aliasy wbudowane dostawcy to:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="f8e9f-190">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-190">Console</span></span>
  * <span data-ttu-id="f8e9f-191">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-191">Debug</span></span>
  * <span data-ttu-id="f8e9f-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-192">EventSource</span></span>
  * <span data-ttu-id="f8e9f-193">Elemencie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-193">EventLog</span></span>
  * <span data-ttu-id="f8e9f-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f8e9f-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="f8e9f-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f8e9f-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="f8e9f-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="f8e9f-197">Ustawianie poziomu dziennika według wiersza polecenia, zmiennych środowiskowych i innych konfiguracji</span><span class="sxs-lookup"><span data-stu-id="f8e9f-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="f8e9f-198">Poziom dziennika może być ustawiony przez dowolnego [dostawcę konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="f8e9f-199">Następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-199">The following commands:</span></span>

* <span data-ttu-id="f8e9f-200">Ustaw klucz środowiska na `Logging:LogLevel:Microsoft` wartość `Information` w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="f8e9f-201">Przetestuj ustawienia w przypadku korzystania z aplikacji utworzonej za pomocą szablonów aplikacji sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="f8e9f-202">`dotnet run`Polecenie musi być uruchamiane w katalogu projektu po użyciu `set` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="f8e9f-203">Poprzednie ustawienie środowiska:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-203">The preceding environment setting:</span></span>

* <span data-ttu-id="f8e9f-204">Jest ustawiana tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="f8e9f-205">Nie są odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="f8e9f-206">Następujące polecenie [setx](/windows-server/administration/windows-commands/setx) ustawia również klucz środowiska i wartość w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="f8e9f-207">W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="f8e9f-208">`/M`Przełącznik ustawia zmienną w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="f8e9f-209">Jeśli `/M` nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="f8e9f-210">Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="f8e9f-211">Ustawienia aplikacji Azure App Service są następujące:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="f8e9f-212">Szyfrowane i przesyłane przez zaszyfrowanego kanału.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="f8e9f-213">Uwidocznione jako zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-213">Exposed as environment variables.</span></span>

<span data-ttu-id="f8e9f-214">Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="f8e9f-215">Aby uzyskać więcej informacji na temat ustawiania ASP.NET Core wartości konfiguracji przy użyciu zmiennych środowiskowych, zobacz [zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="f8e9f-216">Aby uzyskać informacje na temat korzystania z innych źródeł konfiguracji, w tym z wiersza polecenia, Azure Key Vault, konfiguracja aplikacji platformy Azure, inne formaty plików i inne, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f8e9f-217">Jak są stosowane reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-217">How filtering rules are applied</span></span>

<span data-ttu-id="f8e9f-218">Po <xref:Microsoft.Extensions.Logging.ILogger%601> utworzeniu obiektu <xref:Microsoft.Extensions.Logging.ILoggerFactory> obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f8e9f-219">Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f8e9f-220">Dla każdego dostawcy i pary kategorii jest wybierana najbardziej konkretna reguła z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f8e9f-221">Następujący algorytm jest używany dla każdego dostawcy, gdy `ILogger` jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f8e9f-222">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f8e9f-223">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f8e9f-224">W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f8e9f-225">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f8e9f-226">Jeśli wybrano wiele reguł, zrób to **ostatnie** .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f8e9f-227">Jeśli nie wybrano żadnych reguł, użyj `MinimumLevel` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="f8e9f-228">Rejestrowanie danych wyjściowych z przebiegu dotnet i programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8e9f-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="f8e9f-229">Wyświetlane są dzienniki utworzone przy użyciu [domyślnych dostawców rejestrowania](#lp) :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="f8e9f-230">W programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8e9f-230">In Visual Studio</span></span>
  * <span data-ttu-id="f8e9f-231">W oknie dane wyjściowe debugowania podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="f8e9f-232">W oknie ASP.NET Core serwer sieci Web.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="f8e9f-233">W oknie konsoli, gdy aplikacja jest uruchamiana z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="f8e9f-234">Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f8e9f-235">ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="f8e9f-236">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-236">Log category</span></span>

<span data-ttu-id="f8e9f-237">Po `ILogger` utworzeniu obiektu zostanie określona *Kategoria* .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="f8e9f-238">Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f8e9f-239">Ciąg kategorii jest dowolny, ale Konwencja ma używać nazwy klasy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="f8e9f-240">Przykładowo może to być nazwa w kontrolerze `"TodoApi.Controllers.TodoController"` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="f8e9f-241">ASP.NET Core aplikacje sieci Web używają `ILogger<T>` do automatycznego pobrania `ILogger` wystąpienia, które używa w pełni kwalifikowanej nazwy typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="f8e9f-242">Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="f8e9f-243">`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="f8e9f-244">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-244">Log level</span></span>

<span data-ttu-id="f8e9f-245">W poniższej tabeli wymieniono <xref:Microsoft.Extensions.Logging.LogLevel> wartości, wygodną `Log{LogLevel}` metodę rozszerzenia oraz Sugerowane użycie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="f8e9f-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f8e9f-246">LogLevel</span></span> | <span data-ttu-id="f8e9f-247">Wartość</span><span class="sxs-lookup"><span data-stu-id="f8e9f-247">Value</span></span> | <span data-ttu-id="f8e9f-248">Metoda</span><span class="sxs-lookup"><span data-stu-id="f8e9f-248">Method</span></span> | <span data-ttu-id="f8e9f-249">Opis</span><span class="sxs-lookup"><span data-stu-id="f8e9f-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="f8e9f-250">Ślad</span><span class="sxs-lookup"><span data-stu-id="f8e9f-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-251">0</span><span class="sxs-lookup"><span data-stu-id="f8e9f-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="f8e9f-252">Zawierają najwięcej szczegółowych komunikatów.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-252">Contain the most detailed messages.</span></span> <span data-ttu-id="f8e9f-253">Te komunikaty mogą zawierać poufne dane aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="f8e9f-254">Te komunikaty są domyślnie wyłączone i ***nie*** powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="f8e9f-255">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-256">1</span><span class="sxs-lookup"><span data-stu-id="f8e9f-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="f8e9f-257">Na potrzeby debugowania i programowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-257">For debugging and development.</span></span> <span data-ttu-id="f8e9f-258">W środowisku produkcyjnym należy używać ostrożnie z powodu dużego wolumenu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="f8e9f-259">Informacje</span><span class="sxs-lookup"><span data-stu-id="f8e9f-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-260">2</span><span class="sxs-lookup"><span data-stu-id="f8e9f-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="f8e9f-261">Śledzi ogólny przepływ aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="f8e9f-262">Może mieć wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-262">May have long-term value.</span></span> |
| [<span data-ttu-id="f8e9f-263">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-264">3</span><span class="sxs-lookup"><span data-stu-id="f8e9f-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="f8e9f-265">Dla nietypowych lub nieoczekiwanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="f8e9f-266">Zwykle zawiera błędy lub warunki, które nie powodują błędu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="f8e9f-267">Błąd</span><span class="sxs-lookup"><span data-stu-id="f8e9f-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-268">4</span><span class="sxs-lookup"><span data-stu-id="f8e9f-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="f8e9f-269">W przypadku błędów i wyjątków, których nie można obsłużyć.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f8e9f-270">Te komunikaty wskazują na niepowodzenie podczas bieżącej operacji lub żądania, a nie awarię całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="f8e9f-271">Krytyczne</span><span class="sxs-lookup"><span data-stu-id="f8e9f-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-272">5</span><span class="sxs-lookup"><span data-stu-id="f8e9f-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="f8e9f-273">Dla niepowodzeń, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-273">For failures that require immediate attention.</span></span> <span data-ttu-id="f8e9f-274">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="f8e9f-275">Brak</span><span class="sxs-lookup"><span data-stu-id="f8e9f-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="f8e9f-276">6</span><span class="sxs-lookup"><span data-stu-id="f8e9f-276">6</span></span> | | <span data-ttu-id="f8e9f-277">Określa, że Kategoria rejestrowania nie powinna zapisywać żadnych komunikatów.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="f8e9f-278">W poprzedniej tabeli `LogLevel` znajduje się na liście od najniższej do najwyższej wagi.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="f8e9f-279">Pierwszy parametr metody [log](xref:Microsoft.Extensions.Logging.LoggerExtensions) <xref:Microsoft.Extensions.Logging.LogLevel> wskazuje ważność dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="f8e9f-280">Zamiast wywoływania `Log(LogLevel, ...)` , większość deweloperów wywołuje metody rozszerzenia [dziennika {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="f8e9f-281">`Log{LogLevel}`Metody rozszerzające [wywołują metodę log i określają wartość LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="f8e9f-282">Na przykład następujące dwa wywołania rejestrowania są funkcjonalnie równoważne i generują ten sam dziennik:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="f8e9f-283">`MyLogEvents.TestItem`jest IDENTYFIKATORem zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="f8e9f-284">`MyLogEvents`jest częścią przykładowej aplikacji i jest wyświetlana w sekcji [Identyfikator zdarzenia dziennika](#leid) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="f8e9f-285">Poniższy kod tworzy `Information` i `Warning` rejestruje:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="f8e9f-286">W poprzednim kodzie pierwszy `Log{LogLevel}` parametr, `MyLogEvents.GetItem` , jest [Identyfikator zdarzenia dziennika](#leid).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="f8e9f-287">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f8e9f-288">Parametry metody zostały wyjaśnione w sekcji [szablon komunikatu](#lmt) w dalszej części tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="f8e9f-289">Wywołaj odpowiednią `Log{LogLevel}` metodę, aby kontrolować, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku magazynowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="f8e9f-290">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-290">For example:</span></span>

* <span data-ttu-id="f8e9f-291">W środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-291">In production:</span></span>
  * <span data-ttu-id="f8e9f-292">Rejestrowanie na poziomie `Trace` lub `Information` powoduje utworzenie dużej ilości szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f8e9f-293">Aby kontrolować koszty i nie przekraczać limitów magazynowania danych `Trace` , `Information` komunikaty dzienników i na poziomie magazynu o dużej ilości danych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="f8e9f-294">Rozważ ograniczenie `Trace` i `Information` do określonych kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="f8e9f-295">Rejestrowanie przy `Warning` użyciu `Critical` poziomów powinno generować kilka komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="f8e9f-296">Koszty i limity magazynu zazwyczaj nie są przedmiotem obaw.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="f8e9f-297">Niektóre dzienniki zapewniają większą elastyczność w zakresie wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="f8e9f-298">W programie Development:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-298">In development:</span></span>
  * <span data-ttu-id="f8e9f-299">Ustaw wartość `Warning`.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="f8e9f-300">Dodawanie `Trace` lub `Information` komunikaty podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="f8e9f-301">Aby ograniczyć ilość danych wyjściowych, ustaw `Trace` lub `Information` tylko dla kategorii poddawanych badaniu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="f8e9f-302">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f8e9f-303">Rozważmy na przykład dane wyjściowe dziennika dla:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="f8e9f-304">RazorAplikacja ze stronami utworzona przy użyciu szablonów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="f8e9f-305">Rejestrowanie ustawione na`Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="f8e9f-306">Nawigacja do strony prywatność:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="f8e9f-307">Następujące zestawy JSON `Logging:Console:LogLevel:Microsoft:Information` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="f8e9f-308">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-308">Log event ID</span></span>

<span data-ttu-id="f8e9f-309">Każdy dziennik może określać *Identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f8e9f-310">Przykładowa aplikacja używa `MyLogEvents` klasy do definiowania identyfikatorów zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="f8e9f-311">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-311">An event ID associates a set of events.</span></span> <span data-ttu-id="f8e9f-312">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f8e9f-313">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f8e9f-314">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f8e9f-315">Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="f8e9f-316">Niektórzy dostawcy rejestrowania przechowują identyfikator zdarzenia w polu, co umożliwia filtrowanie identyfikatorów.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="f8e9f-317">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="f8e9f-318">Każdy interfejs API dziennika używa szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-318">Each log API uses a message template.</span></span> <span data-ttu-id="f8e9f-319">Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f8e9f-320">Użyj nazw dla symboli zastępczych, a nie liczby.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="f8e9f-321">Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f8e9f-322">W poniższym kodzie nazwy parametrów są spoza sekwencji w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f8e9f-323">Poprzedni kod tworzy komunikat dziennika z wartościami parametrów w kolejności:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="f8e9f-324">Takie podejście umożliwia dostawcom rejestrowania implementowanie [semantyki lub rejestrowania strukturalnego](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="f8e9f-325">Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f8e9f-326">Umożliwia to dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f8e9f-327">Rozważmy na przykład następujące metody rejestratora:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f8e9f-328">Na przykład podczas rejestrowania w usłudze Azure Table Storage:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="f8e9f-329">Każda jednostka tabeli platformy Azure może `ID` mieć `RequestTime` właściwości i.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="f8e9f-330">Tabele z właściwościami upraszczają zapytania dotyczące zarejestrowanych danych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="f8e9f-331">Na przykład zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez konieczności analizowania limitu czasu wiadomości tekstowej.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="f8e9f-332">Wyjątki dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-332">Log exceptions</span></span>

<span data-ttu-id="f8e9f-333">Metody rejestratora mają przeciążenia przyjmujące parametr wyjątku:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="f8e9f-334">Rejestrowanie wyjątków jest specyficzne dla dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="f8e9f-335">Domyślny poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-335">Default log level</span></span>

<span data-ttu-id="f8e9f-336">Jeśli domyślny poziom rejestrowania nie jest ustawiony, domyślna wartość poziomu dziennika to `Information` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="f8e9f-337">Rozważmy na przykład następującą aplikację sieci Web:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="f8e9f-338">Utworzono za pomocą szablonów aplikacji sieci Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="f8e9f-339">*appsettings.json* i *appsettings.Development.jspo* usunięciu lub zmianie nazwy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="f8e9f-340">Po powyższej instalacji przechodzenie do strony prywatność lub Strona główna powoduje utworzenie wielu `Trace` , `Debug` i `Information` komunikatów z `Microsoft` nazwą kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="f8e9f-341">Poniższy kod ustawia domyślny poziom rejestrowania, jeśli domyślny poziom rejestrowania nie jest ustawiony w konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="f8e9f-342">Ogólnie rzecz biorąc, poziomy dziennika należy określić w obszarze Konfiguracja i nie kod.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="f8e9f-343">Funkcja filtrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-343">Filter function</span></span>

<span data-ttu-id="f8e9f-344">Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="f8e9f-345">Poprzedni kod wyświetla dzienniki konsoli, gdy kategoria zawiera `Controller` lub `Microsoft` i poziom dziennika jest `Information` lub wyższy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="f8e9f-346">Ogólnie rzecz biorąc, poziomy dziennika należy określić w obszarze Konfiguracja i nie kod.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="f8e9f-347">Kategorie ASP.NET Core i EF Core</span><span class="sxs-lookup"><span data-stu-id="f8e9f-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="f8e9f-348">Poniższa tabela zawiera niektóre kategorie używane przez ASP.NET Core i Entity Framework Core z uwagami dotyczącymi dzienników:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="f8e9f-349">Kategoria</span><span class="sxs-lookup"><span data-stu-id="f8e9f-349">Category</span></span>                            | <span data-ttu-id="f8e9f-350">Uwagi</span><span class="sxs-lookup"><span data-stu-id="f8e9f-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f8e9f-351">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f8e9f-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f8e9f-352">Ogólna Diagnostyka ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f8e9f-353">Microsoft. AspNetCore. dataprotection</span><span class="sxs-lookup"><span data-stu-id="f8e9f-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f8e9f-354">Które klucze zostały wzięte pod uwagę, znaleziono i użyte.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f8e9f-355">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f8e9f-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f8e9f-356">Dozwolone hosty.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-356">Hosts allowed.</span></span> |
| <span data-ttu-id="f8e9f-357">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="f8e9f-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f8e9f-358">Jak długo trwa wykonywanie żądań HTTP i czas ich uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f8e9f-359">Które hostowanie zestawów uruchamiania zostało załadowane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f8e9f-360">Microsoft. AspNetCore. MVC</span><span class="sxs-lookup"><span data-stu-id="f8e9f-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f8e9f-361">MVC i Razor Diagnostyka.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f8e9f-362">Powiązanie modelu, wykonywanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f8e9f-363">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="f8e9f-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f8e9f-364">Informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-364">Route matching information.</span></span> |
| <span data-ttu-id="f8e9f-365">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="f8e9f-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f8e9f-366">Reagowanie na uruchamianie, zatrzymywanie i utrzymywanie aktywności.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f8e9f-367">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f8e9f-368">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f8e9f-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f8e9f-369">Obsługiwane pliki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-369">Files served.</span></span> |
| <span data-ttu-id="f8e9f-370">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f8e9f-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f8e9f-371">Ogólna Diagnostyka Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f8e9f-372">Aktywność i Konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="f8e9f-373">Aby wyświetlić więcej kategorii w oknie konsoli, należy ustawić **appsettings.Development.jsna** następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="f8e9f-374">Zakresy dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-374">Log scopes</span></span>

 <span data-ttu-id="f8e9f-375">*Zakres* może grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f8e9f-376">Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f8e9f-377">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f8e9f-378">Zakres:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-378">A scope:</span></span>

* <span data-ttu-id="f8e9f-379">Jest <xref:System.IDisposable> typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="f8e9f-380">Obowiązuje do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="f8e9f-381">Następujące dostawcy obsługują zakresy:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="f8e9f-382">AzureAppServicesFile i AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f8e9f-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="f8e9f-383">Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="f8e9f-384">Poniższy kod JSON włącza zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="f8e9f-385">Poniższy kod włącza zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="f8e9f-386">Ogólnie rzecz biorąc, rejestrowanie powinno być określone w konfiguracji, a nie w kodzie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="f8e9f-387">Wbudowani dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-387">Built-in logging providers</span></span>

<span data-ttu-id="f8e9f-388">ASP.NET Core obejmuje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="f8e9f-389">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-389">Console</span></span>](#console)
* [<span data-ttu-id="f8e9f-390">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-390">Debug</span></span>](#debug)
* [<span data-ttu-id="f8e9f-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="f8e9f-392">Elemencie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="f8e9f-393">AzureAppServicesFile i AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f8e9f-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="f8e9f-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="f8e9f-395">Aby uzyskać informacje dotyczące `stdout` rejestrowania i debugowania przy użyciu modułu ASP.NET Core, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="f8e9f-396">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-396">Console</span></span>

<span data-ttu-id="f8e9f-397">`Console`Dostawca rejestruje dane wyjściowe w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="f8e9f-398">Aby uzyskać więcej informacji na temat wyświetlania `Console` dzienników w programie Development, zobacz [Rejestrowanie danych wyjściowych z uruchamiania programu dotnet i programu Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="f8e9f-399">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-399">Debug</span></span>

<span data-ttu-id="f8e9f-400">`Debug`Dostawca zapisuje dane wyjściowe dziennika za pomocą klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="f8e9f-401">Wywołania do `System.Diagnostics.Debug.WriteLine` zapisu dla `Debug` dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="f8e9f-402">W systemie Linux `Debug` Lokalizacja dziennika dostawcy jest zależna od dystrybucji i może być jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="f8e9f-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="f8e9f-403">*/var/log/message*</span></span>
* <span data-ttu-id="f8e9f-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="f8e9f-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="f8e9f-405">Źródło zdarzeń</span><span class="sxs-lookup"><span data-stu-id="f8e9f-405">Event Source</span></span>

<span data-ttu-id="f8e9f-406">`EventSource`Dostawca zapisuje do międzyplatformowego źródła zdarzeń o nazwie `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f8e9f-407">W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="f8e9f-408">narzędzia śledzenia dotnet</span><span class="sxs-lookup"><span data-stu-id="f8e9f-408">dotnet trace tooling</span></span>

<span data-ttu-id="f8e9f-409">Narzędzie do [śledzenia dotnet](/dotnet/core/diagnostics/dotnet-trace) to międzyplatformowe narzędzie globalne interfejsu wiersza polecenia, które umożliwia zbieranie śladów programu .NET Core uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="f8e9f-410">Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy za pomocą <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="f8e9f-411">Aby uzyskać instrukcje dotyczące instalacji, zobacz [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="f8e9f-412">Użyj narzędzi śledzenia dotnet, aby zebrać ślad z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="f8e9f-413">Uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="f8e9f-414">Określ identyfikator procesu (PID) aplikacji .NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="f8e9f-415">W systemie Windows należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="f8e9f-416">Menedżer zadań (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="f8e9f-417">tasklist — polecenie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="f8e9f-418">Get-Process — polecenie programu PowerShell</span><span class="sxs-lookup"><span data-stu-id="f8e9f-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="f8e9f-419">W systemie Linux Użyj [polecenia pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="f8e9f-420">Znajdź identyfikator PID procesu, który ma taką samą nazwę jak zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="f8e9f-421">Wykonaj `dotnet trace` polecenie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="f8e9f-422">Ogólna składnia poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="f8e9f-423">W przypadku korzystania z powłoki poleceń programu PowerShell należy ująć `--providers` wartość w apostrofy ( `'` ):</span><span class="sxs-lookup"><span data-stu-id="f8e9f-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="f8e9f-424">Na platformach innych niż Windows Dodaj opcję, `-f speedscope` Aby zmienić format wyjściowego pliku śledzenia na `speedscope` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="f8e9f-425">Słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="f8e9f-425">Keyword</span></span> | <span data-ttu-id="f8e9f-426">Opis</span><span class="sxs-lookup"><span data-stu-id="f8e9f-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="f8e9f-427">1</span><span class="sxs-lookup"><span data-stu-id="f8e9f-427">1</span></span>       | <span data-ttu-id="f8e9f-428">Rejestruj meta zdarzenia dotyczące `LoggingEventSource` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="f8e9f-429">Nie rejestruje zdarzeń z `ILogger` ).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="f8e9f-430">2</span><span class="sxs-lookup"><span data-stu-id="f8e9f-430">2</span></span>       | <span data-ttu-id="f8e9f-431">Włącza zdarzenie, `Message` gdy `ILogger.Log()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f8e9f-432">Zawiera informacje w sposób programistyczny (nie sformatowany).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="f8e9f-433">4</span><span class="sxs-lookup"><span data-stu-id="f8e9f-433">4</span></span>       | <span data-ttu-id="f8e9f-434">Włącza zdarzenie, `FormatMessage` gdy `ILogger.Log()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f8e9f-435">Udostępnia sformatowaną wersję ciągu informacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="f8e9f-436">8</span><span class="sxs-lookup"><span data-stu-id="f8e9f-436">8</span></span>       | <span data-ttu-id="f8e9f-437">Włącza zdarzenie, `MessageJson` gdy `ILogger.Log()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f8e9f-438">Zawiera reprezentację argumentów w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="f8e9f-439">Poziom zdarzenia</span><span class="sxs-lookup"><span data-stu-id="f8e9f-439">Event Level</span></span> | <span data-ttu-id="f8e9f-440">Opis</span><span class="sxs-lookup"><span data-stu-id="f8e9f-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="f8e9f-441">0</span><span class="sxs-lookup"><span data-stu-id="f8e9f-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="f8e9f-442">1</span><span class="sxs-lookup"><span data-stu-id="f8e9f-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="f8e9f-443">2</span><span class="sxs-lookup"><span data-stu-id="f8e9f-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="f8e9f-444">3</span><span class="sxs-lookup"><span data-stu-id="f8e9f-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="f8e9f-445">4</span><span class="sxs-lookup"><span data-stu-id="f8e9f-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="f8e9f-446">5</span><span class="sxs-lookup"><span data-stu-id="f8e9f-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="f8e9f-447">`FilterSpecs`wpisy dla `{Logger Category}` i `{Event Level}` przedstawiają dodatkowe warunki filtrowania dzienników.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="f8e9f-448">Oddzielaj `FilterSpecs` wpisy średnikami ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="f8e9f-449">Przykład użycia powłoki poleceń systemu Windows (**Brak** pojedynczego cudzysłowu wokół `--providers` wartości):</span><span class="sxs-lookup"><span data-stu-id="f8e9f-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="f8e9f-450">Poprzednie polecenie aktywuje:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-450">The preceding command activates:</span></span>

   * <span data-ttu-id="f8e9f-451">Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów ( `4` ) dla błędów ( `2` ).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="f8e9f-452">`Microsoft.AspNetCore.Hosting`Rejestrowanie na `Informational` poziomie rejestrowania ( `4` ).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="f8e9f-453">Zatrzymaj narzędzia śledzenia dotnet, naciskając klawisz ENTER lub CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="f8e9f-454">Ślad jest zapisywany z nazwą *Trace. webtrace* w folderze, w którym `dotnet trace` jest wykonywane polecenie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="f8e9f-455">Otwórz ślad z [Narzędzia PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="f8e9f-456">Otwórz plik *Trace. webtrace* i zbadaj zdarzenia śledzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="f8e9f-457">Jeśli aplikacja nie kompiluje hosta za pomocą programu `CreateDefaultBuilder` , Dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="f8e9f-458">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-458">For more information, see:</span></span>

* <span data-ttu-id="f8e9f-459">[Śledzenie dla narzędzia do analizy wydajności (program dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja platformy .NET Core)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="f8e9f-460">[Śledzenie dla narzędzia analizy wydajności (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium dotnet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="f8e9f-461">[LoggingEventSource — Klasa](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API platformy .NET)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="f8e9f-462">[Źródło odwołania LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): Aby uzyskać Źródło odwołania dla innej wersji, Zmień gałąź na `release/{Version}` , gdzie `{Version}` jest wymagana wersja ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="f8e9f-463">[Narzędzia PerfView](#perfview): przydatne do wyświetlania śladów źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="f8e9f-464">Narzędzia PerfView</span><span class="sxs-lookup"><span data-stu-id="f8e9f-464">Perfview</span></span>

<span data-ttu-id="f8e9f-465">Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f8e9f-466">Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f8e9f-467">Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f8e9f-468">Nie przegap `*` na początku ciągu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="f8e9f-469">Dziennik zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="f8e9f-469">Windows EventLog</span></span>

<span data-ttu-id="f8e9f-470">`EventLog`Dostawca wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="f8e9f-471">W przeciwieństwie do innych dostawców `EventLog` dostawca nie dziedziczy ***not*** domyślnych ustawień nienależących do dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="f8e9f-472">Jeśli `EventLog` nie określono ustawień dziennika, [domyślnie ustawiana jest wartość LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="f8e9f-473">Aby rejestrować zdarzenia mniejsze niż <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , jawnie ustaw poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="f8e9f-474">Poniższy przykład ustawia domyślny poziom dziennika dziennika zdarzeń <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="f8e9f-475">[Przeciążania addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) można przekazać <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f8e9f-476">Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f8e9f-477">`LogName`: "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="f8e9f-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="f8e9f-478">`SourceName`: "Środowisko uruchomieniowe platformy .NET"</span><span class="sxs-lookup"><span data-stu-id="f8e9f-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="f8e9f-479">`MachineName`: Nazwa komputera lokalnego jest używana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="f8e9f-480">Poniższy kod zmienia `SourceName` wartość domyślną na `".NET Runtime"` `MyLogs` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="f8e9f-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f8e9f-481">Azure App Service</span></span>

<span data-ttu-id="f8e9f-482">Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="f8e9f-483">Pakiet dostawcy nie jest uwzględniony w udostępnionej infrastrukturze.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="f8e9f-484">Aby użyć dostawcy, Dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="f8e9f-485">Aby skonfigurować ustawienia dostawcy, użyj <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> , jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="f8e9f-486">Po wdrożeniu do Azure App Service aplikacja korzysta z ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) na stronie **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f8e9f-487">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f8e9f-488">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="f8e9f-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f8e9f-489">**Rejestrowanie aplikacji (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="f8e9f-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f8e9f-490">Domyślną lokalizacją plików dziennika jest folder *D: \\ Home \\ \\ LogFiles* , a domyślna nazwa pliku to *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f8e9f-491">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f8e9f-492">Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f8e9f-493">Ten dostawca rejestruje się tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f8e9f-494">Przesyłanie strumieniowe dzienników Azure</span><span class="sxs-lookup"><span data-stu-id="f8e9f-494">Azure log streaming</span></span>

<span data-ttu-id="f8e9f-495">Przesyłanie strumieniowe w usłudze Azure log obsługuje wyświetlanie dziennika w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="f8e9f-496">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e9f-496">The app server</span></span>
* <span data-ttu-id="f8e9f-497">Serwer sieci Web</span><span class="sxs-lookup"><span data-stu-id="f8e9f-497">The web server</span></span>
* <span data-ttu-id="f8e9f-498">Śledzenie nieudanych żądań</span><span class="sxs-lookup"><span data-stu-id="f8e9f-498">Failed request tracing</span></span>

<span data-ttu-id="f8e9f-499">Aby skonfigurować przesyłanie strumieniowe dzienników Azure:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f8e9f-500">Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="f8e9f-501">Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f8e9f-502">Wybierz **poziom**dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-502">Choose the log **Level**.</span></span> <span data-ttu-id="f8e9f-503">To ustawienie dotyczy tylko przesyłania strumieniowego dzienników Azure.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="f8e9f-504">Przejdź do strony **strumień dziennika** , aby wyświetlić dzienniki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="f8e9f-505">Zarejestrowane komunikaty są rejestrowane przy użyciu `ILogger` interfejsu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="f8e9f-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-506">Azure Application Insights</span></span>

<span data-ttu-id="f8e9f-507">Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na [platformie Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="f8e9f-508">Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f8e9f-509">Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f8e9f-510">Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f8e9f-511">Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f8e9f-512">Pakiet [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) jest przeznaczony dla ASP.NET 4. x, a nie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="f8e9f-513">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f8e9f-514">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f8e9f-515">[Application Insights dla ASP.NET Core aplikacji](/azure/azure-monitor/app/asp-net-core) — Zacznij tutaj, jeśli chcesz zaimplementować cały zakres Application Insights telemetrii wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f8e9f-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger](/azure/azure-monitor/app/ilogger) — Rozpocznij tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f8e9f-517">[Karty rejestrowania Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f8e9f-518">[Zainstaluj, skonfiguruj i zainicjuj samouczek Application INSIGHTS SDK](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f8e9f-519">Dostawcy rejestrowania innych firm</span><span class="sxs-lookup"><span data-stu-id="f8e9f-519">Third-party logging providers</span></span>

<span data-ttu-id="f8e9f-520">Platformy rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f8e9f-521">[ELMAH.IO](https://elmah.io/) ([repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f8e9f-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f8e9f-523">[JSNLog](https://jsnlog.com/) ([repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f8e9f-524">[KissLog.NET](https://kisslog.net/) ([repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f8e9f-525">[Log4Net](https://logging.apache.org/log4net/) ([repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f8e9f-526">[Loggr](https://loggr.net/) ([repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f8e9f-527">[NLOG](https://nlog-project.org/) ([repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f8e9f-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repozytorium GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="f8e9f-529">[Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f8e9f-530">[Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f8e9f-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f8e9f-532">Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f8e9f-533">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f8e9f-534">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f8e9f-535">Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f8e9f-536">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f8e9f-537">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="f8e9f-538">Aplikacja konsolowa niebędąca hostem</span><span class="sxs-lookup"><span data-stu-id="f8e9f-538">Non-host console app</span></span>

<span data-ttu-id="f8e9f-539">Przykład korzystania z hosta ogólnego w aplikacji konsolowej innej niż sieci Web można znaleźć w pliku *program.cs* [zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="f8e9f-540">Rejestrowanie kodu dla aplikacji bez hosta ogólnego różni się w sposób, w jaki [są dodawane dostawcy](#add-providers) i [są tworzone rejestratory](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="f8e9f-541">Dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-541">Logging providers</span></span>

<span data-ttu-id="f8e9f-542">W aplikacji konsolowej innej niż host Wywołaj `Add{provider name}` metodę rozszerzenia dostawcy podczas tworzenia `LoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="f8e9f-543">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="f8e9f-543">Create logs</span></span>

<span data-ttu-id="f8e9f-544">Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f8e9f-545">Użyj, `LoggerFactory` Aby utworzyć `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f8e9f-546">Poniższy przykład tworzy Rejestrator przy użyciu `LoggingConsoleApp.Program` jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="f8e9f-547">W następujących przykładowych ASP.NET podstawowych, Rejestrator jest używany do tworzenia dzienników `Information` jako poziom.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f8e9f-548">*Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="f8e9f-549">[Poziomy](#log-level) i [Kategorie](#log-category) zostały omówione bardziej szczegółowo w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="f8e9f-550">Rejestruj podczas konstruowania hosta</span><span class="sxs-lookup"><span data-stu-id="f8e9f-550">Log during host construction</span></span>

<span data-ttu-id="f8e9f-551">Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f8e9f-552">Można jednak użyć osobnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-552">However, a separate logger can be used.</span></span> <span data-ttu-id="f8e9f-553">W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="f8e9f-554">`AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="f8e9f-555">Konfigurowanie usługi, która zależy od ILogger</span><span class="sxs-lookup"><span data-stu-id="f8e9f-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="f8e9f-556">Wstrzykiwanie konstruktora rejestratora do `Startup` działa we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener di.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="f8e9f-557">Aby uzyskać informacje o tym, dlaczego dla hosta generycznego jest tworzony tylko jeden kontener, zobacz [zawiadomienie o rozdzieleniu zmian](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="f8e9f-558">Aby skonfigurować usługę, która jest zależna od `ILogger<T>` programu, należy użyć iniekcji konstruktora lub dostarczyć metody fabryki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="f8e9f-559">Podejście metody fabryki jest zalecane tylko wtedy, gdy nie ma żadnych innych opcji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="f8e9f-560">Rozważmy na przykład usługę, która wymaga `ILogger<T>` wystąpienia przez di:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="f8e9f-561">Poprzedni wyróżniony kod jest obiektem, który jest uruchamiany po raz pierwszy, [musi utworzyć wystąpienie](/dotnet/api/system.func-2) `MyService` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="f8e9f-562">W ten sposób można uzyskać dostęp do dowolnych zarejestrowanych usług.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="f8e9f-563">Tworzenie dzienników w głównej</span><span class="sxs-lookup"><span data-stu-id="f8e9f-563">Create logs in Main</span></span>

<span data-ttu-id="f8e9f-564">Następujący kod jest zarejestrowana `Main` przez pobranie `ILogger` wystąpienia z programu di po skompilowaniu hosta:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="f8e9f-565">Tworzenie dzienników w programie startowym</span><span class="sxs-lookup"><span data-stu-id="f8e9f-565">Create logs in Startup</span></span>

<span data-ttu-id="f8e9f-566">Poniższy kod zapisuje dzienniki w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="f8e9f-567">Zapisywanie dzienników przed ukończeniem instalacji programu DI Container w `Startup.ConfigureServices` metodzie nie jest obsługiwane:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="f8e9f-568">Iniekcja rejestratora do `Startup` konstruktora nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="f8e9f-569">Iniekcja rejestratora do `Startup.ConfigureServices` sygnatury metody nie jest obsługiwana</span><span class="sxs-lookup"><span data-stu-id="f8e9f-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="f8e9f-570">Przyczyną tego ograniczenia jest to, że rejestrowanie jest zależne od typu i konfiguracji, która z tego powodu zależy od DI.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="f8e9f-571">Kontener DI nie jest ustawiany do momentu `ConfigureServices` zakończenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="f8e9f-572">Aby uzyskać informacje na temat konfigurowania usługi, która zależy od `ILogger<T>` lub dlaczego wstrzyknięcie konstruktora rejestratora do `Startup` pracy we wcześniejszych wersjach, zobacz [Konfigurowanie usługi, która jest zależna od ILogger](#csdi)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f8e9f-573">Brak metod rejestratora asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="f8e9f-573">No asynchronous logger methods</span></span>

<span data-ttu-id="f8e9f-574">Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f8e9f-575">Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f8e9f-576">Rozważ ręczne zapisanie komunikatów dziennika w szybkim magazynie, a następnie przeniesienie ich do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="f8e9f-577">Na przykład podczas rejestrowania do SQL Server nie należy tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f8e9f-578">Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f8e9f-579">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="f8e9f-580">Zmień poziomy dziennika w działającej aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e9f-580">Change log levels in a running app</span></span>

<span data-ttu-id="f8e9f-581">Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f8e9f-582">Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f8e9f-583">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider)ponownie ładuje konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="f8e9f-584">Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="f8e9f-585">ILogger i ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="f8e9f-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="f8e9f-586"><xref:Microsoft.Extensions.Logging.ILogger%601>Interfejsy i <xref:Microsoft.Extensions.Logging.ILoggerFactory> i implementacje są zawarte w zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="f8e9f-587">Są one również dostępne w następujących pakietach NuGet:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="f8e9f-588">Interfejsy są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="f8e9f-589">Implementacje domyślne są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="f8e9f-590">Zastosuj reguły filtru dziennika w kodzie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-590">Apply log filter rules in code</span></span>

<span data-ttu-id="f8e9f-591">Preferowanym podejściem do ustawiania reguł filtru dziennika jest użycie [konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="f8e9f-592">Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="f8e9f-593">`logging.AddFilter("System", LogLevel.Debug)`Określa `System` kategorię i poziom dziennika `Debug` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="f8e9f-594">Filtr jest stosowany do wszystkich dostawców, ponieważ określony dostawca nie został skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="f8e9f-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`określając</span><span class="sxs-lookup"><span data-stu-id="f8e9f-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="f8e9f-596">`Debug`Dostawca rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="f8e9f-597">Poziom dziennika `Information` lub wyższy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="f8e9f-598">Wszystkie kategorie zaczynające się od `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="f8e9f-599">Tworzenie rejestratora niestandardowego</span><span class="sxs-lookup"><span data-stu-id="f8e9f-599">Create a custom logger</span></span>

<span data-ttu-id="f8e9f-600">Aby dodać niestandardowy rejestrator, Dodaj element <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory> :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="f8e9f-601">`ILoggerProvider`Tworzy co najmniej jedno `ILogger` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="f8e9f-602">`ILogger`Wystąpienia są używane przez platformę do rejestrowania informacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="f8e9f-603">Przykładowa konfiguracja rejestratora niestandardowego</span><span class="sxs-lookup"><span data-stu-id="f8e9f-603">Sample custom logger configuration</span></span>

<span data-ttu-id="f8e9f-604">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-604">The sample:</span></span>

* <span data-ttu-id="f8e9f-605">Został zaprojektowany jako bardzo podstawowy przykład, który ustawia kolor konsoli dziennika według identyfikatora zdarzenia i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="f8e9f-606">Rejestratory zazwyczaj nie zmieniają się według identyfikatora zdarzenia i nie są specyficzne dla poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="f8e9f-607">Tworzy różne wpisy konsoli kolorów dla każdego poziomu dziennika i identyfikatora zdarzenia przy użyciu następującego typu konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="f8e9f-608">Poprzedni kod ustawia poziom domyślny na `Warning` i kolor `Yellow` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="f8e9f-609">Jeśli wartość `EventId` jest równa 0, będziemy rejestrować wszystkie zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="f8e9f-610">Tworzenie rejestratora niestandardowego</span><span class="sxs-lookup"><span data-stu-id="f8e9f-610">Create the custom logger</span></span>

<span data-ttu-id="f8e9f-611">`ILogger`Nazwa kategorii implementacji jest zwykle źródłem rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="f8e9f-612">Na przykład typ, w którym jest tworzony Rejestrator:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="f8e9f-613">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-613">The preceding code:</span></span>

* <span data-ttu-id="f8e9f-614">Tworzy wystąpienie rejestratora na nazwę kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="f8e9f-615">Sprawdza `logLevel == _config.LogLevel` `IsEnabled` , czy każdy z nich `logLevel` ma unikatowy rejestrator.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="f8e9f-616">Ogólnie rzecz biorąc, rejestratory powinny również być włączone dla wszystkich wyższych poziomów dzienników:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="f8e9f-617">Tworzenie niestandardowego LoggerProvider</span><span class="sxs-lookup"><span data-stu-id="f8e9f-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="f8e9f-618">`LoggerProvider`Jest klasą, która tworzy wystąpienia rejestratora.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="f8e9f-619">Być może nie jest to konieczne do utworzenia wystąpienia rejestratora dla kategorii, ale jest to zrozumiałe dla niektórych rejestratorów, takich jak NLog lub log4net.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="f8e9f-620">W tym celu można również wybrać różne docelowe dane wyjściowe rejestrowania według kategorii, jeśli jest to konieczne:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="f8e9f-621">W powyższym kodzie <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> tworzy pojedyncze wystąpienie dla `ColorConsoleLogger` nazwy kategorii i zapisuje je w [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;</span><span class="sxs-lookup"><span data-stu-id="f8e9f-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="f8e9f-622">Użycie i rejestracja rejestratora niestandardowego</span><span class="sxs-lookup"><span data-stu-id="f8e9f-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="f8e9f-623">Zarejestruj rejestrator w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="f8e9f-624">Dla poprzedniego kodu, należy podać co najmniej jedną metodę rozszerzającą dla `ILoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="f8e9f-625">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f8e9f-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="f8e9f-626">Błędy rejestrowania należy utworzyć w repozytorium [GitHub.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8e9f-627">Autorzy [Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f8e9f-628">Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f8e9f-629">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f8e9f-630">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f8e9f-631">Dodaj dostawców</span><span class="sxs-lookup"><span data-stu-id="f8e9f-631">Add providers</span></span>

<span data-ttu-id="f8e9f-632">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f8e9f-633">Na przykład dostawca konsoli wyświetla dzienniki w konsoli programu, a Dostawca usługi Azure Application Insights przechowuje je na platformie Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f8e9f-634">Dzienniki mogą być wysyłane do wielu miejsc docelowych przez dodanie wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f8e9f-635">Aby dodać dostawcę, wywołaj `Add{provider name}` metodę rozszerzenia dostawcy w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="f8e9f-636">Poprzedzający kod wymaga odwołania do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="f8e9f-637">Domyślne wywołania szablonu projektu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , które dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="f8e9f-638">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-638">Console</span></span>
* <span data-ttu-id="f8e9f-639">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-639">Debug</span></span>
* <span data-ttu-id="f8e9f-640">EventSource (rozpoczęcie w ASP.NET Core 2,2)</span><span class="sxs-lookup"><span data-stu-id="f8e9f-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="f8e9f-641">Jeśli używasz programu `CreateDefaultBuilder` , możesz zastąpić domyślnych dostawców własnymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="f8e9f-642">Wywołaj <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> i Dodaj żądanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="f8e9f-643">Więcej informacji na temat [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych](#third-party-logging-providers) firm znajduje się w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f8e9f-644">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="f8e9f-644">Create logs</span></span>

<span data-ttu-id="f8e9f-645">Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f8e9f-646">W aplikacji sieci Web lub hostowanej usłudze Pobierz `ILogger` od iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f8e9f-647">W aplikacjach konsolowych innych niż host Użyj programu, `LoggerFactory` Aby utworzyć `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f8e9f-648">Poniższy ASP.NET Core przykład tworzy Rejestrator przy użyciu `TodoApiSample.Pages.AboutModel` jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f8e9f-649">*Kategoria* dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f8e9f-650">`ILogger<T>`Wystąpienie zapewniane przez program di tworzy dzienniki, które mają w pełni kwalifikowaną nazwę typu `T` jako kategorię.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f8e9f-651">W poniższych przykładach ASP.NET Core i aplikacji konsoli Rejestrator służy do tworzenia dzienników z użyciem `Information` jako poziom.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f8e9f-652">*Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="f8e9f-653">[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="f8e9f-654">Tworzenie dzienników w programie startowym</span><span class="sxs-lookup"><span data-stu-id="f8e9f-654">Create logs in Startup</span></span>

<span data-ttu-id="f8e9f-655">Aby napisać dzienniki w `Startup` klasie, Dołącz `ILogger` parametr do sygnatury konstruktora:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f8e9f-656">Tworzenie dzienników w klasie programu</span><span class="sxs-lookup"><span data-stu-id="f8e9f-656">Create logs in the Program class</span></span>

<span data-ttu-id="f8e9f-657">Aby napisać dzienniki w `Program` klasie, Pobierz `ILogger` wystąpienie z elementu di:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="f8e9f-658">Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f8e9f-659">Można jednak użyć osobnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-659">However, a separate logger can be used.</span></span> <span data-ttu-id="f8e9f-660">W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="f8e9f-661">`AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f8e9f-662">Brak metod rejestratora asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="f8e9f-662">No asynchronous logger methods</span></span>

<span data-ttu-id="f8e9f-663">Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f8e9f-664">Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f8e9f-665">Najpierw Rozważ zapisanie komunikatów dziennika do szybkiego sklepu, a następnie przeniesienie ich do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f8e9f-666">Na przykład jeśli rejestrujesz się do SQL Server, nie chcesz tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f8e9f-667">Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f8e9f-668">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f8e9f-669">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-669">Configuration</span></span>

<span data-ttu-id="f8e9f-670">Konfiguracja dostawcy rejestrowania jest świadczona przez co najmniej jednego dostawcę konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f8e9f-671">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f8e9f-672">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-672">Command-line arguments.</span></span>
* <span data-ttu-id="f8e9f-673">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-673">Environment variables.</span></span>
* <span data-ttu-id="f8e9f-674">Obiekty platformy .NET w pamięci.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="f8e9f-675">Magazyn niezaszyfrowanego [klucza tajnego](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f8e9f-676">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f8e9f-677">Dostawcy niestandardowi (instalowani lub utworzony).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="f8e9f-678">Na przykład konfiguracja rejestrowania jest zwykle dostarczana przez `Logging` sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f8e9f-679">Poniższy przykład pokazuje zawartość typowego *appsettings.Development.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="f8e9f-680">`Logging`Właściwość może mieć `LogLevel` właściwości dostawcy dzienników i.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f8e9f-681">`LogLevel`Właściwość w obszarze `Logging` określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f8e9f-682">W przykładzie, `System` i `Microsoft` Kategorie są rejestrowane na `Information` poziomie, a wszystkie inne logowania na `Debug` poziomie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f8e9f-683">Inne właściwości w obszarze `Logging` Określ dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f8e9f-684">Przykład dotyczy dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-684">The example is for the Console provider.</span></span> <span data-ttu-id="f8e9f-685">Jeśli dostawca obsługuje [zakresy rejestrowania](#log-scopes), `IncludeScopes` wskazuje, czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f8e9f-686">Właściwość dostawcy (taka jak `Console` w przykładzie) może także określić `LogLevel` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f8e9f-687">`LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f8e9f-688">Jeśli w programie są określone poziomy `Logging.{providername}.LogLevel` , zastępują one wszystko ustawione w `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="f8e9f-689">Rozważmy na przykład następujące dane JSON:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="f8e9f-690">W powyższym kodzie JSON `Console` Ustawienia dostawcy przesłaniają poprzedni poziom dziennika (domyślnie).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="f8e9f-691">Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f8e9f-692">Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f8e9f-693">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), który jest dodawany przez program `CreateDefaultBuilder` do odczytu plików ustawień, ponownie ładuje konfigurację rejestrowania domyślnie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f8e9f-694">Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f8e9f-695">Informacje o implementowaniu dostawców konfiguracji znajdują się w temacie <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f8e9f-696">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-696">Sample logging output</span></span>

<span data-ttu-id="f8e9f-697">W przypadku przykładowego kodu podanego w poprzedniej sekcji Dzienniki są wyświetlane w konsoli programu, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f8e9f-698">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="f8e9f-699">Poprzednie dzienniki zostały wygenerowane przez utworzenie żądania HTTP GET do przykładowej aplikacji w lokalizacji `http://localhost:5000/api/todo/0` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f8e9f-700">Oto przykład tych samych dzienników, które są wyświetlane w oknie debugowania podczas uruchamiania przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="f8e9f-701">Dzienniki utworzone przez `ILogger` wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="f8e9f-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="f8e9f-702">Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f8e9f-703">ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f8e9f-704">W pozostałej części tego artykułu opisano niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f8e9f-705">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="f8e9f-705">NuGet packages</span></span>

<span data-ttu-id="f8e9f-706">`ILogger`Interfejsy i `ILoggerFactory` są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)i domyślne implementacje dla nich są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f8e9f-707">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-707">Log category</span></span>

<span data-ttu-id="f8e9f-708">Po `ILogger` utworzeniu obiektu jest dla niego określona *Kategoria* .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f8e9f-709">Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f8e9f-710">Kategoria może być dowolnym ciągiem, ale Konwencja ma używać nazwy klasy, takiej jak "TodoApi. controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="f8e9f-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f8e9f-711">Użyj, `ILogger<T>` Aby uzyskać `ILogger` wystąpienie używające w pełni kwalifikowanej nazwy typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f8e9f-712">Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f8e9f-713">`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f8e9f-714">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-714">Log level</span></span>

<span data-ttu-id="f8e9f-715">Każdy dziennik Określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f8e9f-716">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f8e9f-717">Przykładowo można napisać `Information` Dziennik, gdy metoda jest zwykle zakończona, a dziennik, `Warning` gdy metoda zwraca *404, nie znaleziono* kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f8e9f-718">Poniższy kod tworzy `Information` i `Warning` rejestruje:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f8e9f-719">W poprzednim kodzie `MyLogEvents.GetItem` `MyLogEvents.GetItemNotFound` Parametry i są [identyfikatorem zdarzenia dziennika](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f8e9f-720">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f8e9f-721">Parametry metody zostały wyjaśnione w [sekcji szablon komunikatu dziennika](#lmt) w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="f8e9f-722">Metody rejestrowania, które obejmują poziom w nazwie metody (na przykład `LogInformation` i `LogWarning` ) są [metodami rozszerzającymi dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f8e9f-723">Te metody wywołują `Log` metodę, która pobiera `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f8e9f-724">Metodę można wywołać `Log` bezpośrednio zamiast jednej z tych metod rozszerzających, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f8e9f-725">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.Extensions.Logging.ILogger> i [kod źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f8e9f-726">ASP.NET Core definiuje następujące poziomy dziennika uporządkowane w tym miejscu od najniższej do najwyższej wagi.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f8e9f-727">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="f8e9f-727">Trace = 0</span></span>

  <span data-ttu-id="f8e9f-728">Aby uzyskać informacje, które są zazwyczaj cenne tylko dla debugowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f8e9f-729">Komunikaty te mogą zawierać poufne dane aplikacji, dlatego nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f8e9f-730">*Domyślnie wyłączona.*</span><span class="sxs-lookup"><span data-stu-id="f8e9f-730">*Disabled by default.*</span></span>

* <span data-ttu-id="f8e9f-731">Debuguj = 1</span><span class="sxs-lookup"><span data-stu-id="f8e9f-731">Debug = 1</span></span>

  <span data-ttu-id="f8e9f-732">Informacje, które mogą być przydatne podczas tworzenia i debugowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f8e9f-733">Przykład: `Entering method Configure with flag set to true.` Włącz `Debug` dzienniki poziomów w środowisku produkcyjnym tylko w przypadku rozwiązywania problemów, ze względu na dużą ilość dzienników.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f8e9f-734">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="f8e9f-734">Information = 2</span></span>

  <span data-ttu-id="f8e9f-735">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="f8e9f-736">Te dzienniki zwykle mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="f8e9f-737">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f8e9f-738">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="f8e9f-738">Warning = 3</span></span>

  <span data-ttu-id="f8e9f-739">Dla nietypowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f8e9f-740">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale konieczne może być zbadanie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f8e9f-741">Obsłużone wyjątki są typowym miejscem do korzystania z `Warning` poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f8e9f-742">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f8e9f-743">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="f8e9f-743">Error = 4</span></span>

  <span data-ttu-id="f8e9f-744">W przypadku błędów i wyjątków, których nie można obsłużyć.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f8e9f-745">Te komunikaty wskazują niepowodzenie w bieżącym działaniu lub operacji (np. bieżące żądanie HTTP), a nie awaria całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f8e9f-746">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f8e9f-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f8e9f-747">Krytyczne = 5</span><span class="sxs-lookup"><span data-stu-id="f8e9f-747">Critical = 5</span></span>

  <span data-ttu-id="f8e9f-748">Dla niepowodzeń, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-748">For failures that require immediate attention.</span></span> <span data-ttu-id="f8e9f-749">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f8e9f-750">Poziom dziennika służy do kontrolowania, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f8e9f-751">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-751">For example:</span></span>

* <span data-ttu-id="f8e9f-752">W środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-752">In production:</span></span>
  * <span data-ttu-id="f8e9f-753">Rejestrowanie na poziomie `Trace` za pomocą `Information` poziomów zapewnia wysoką liczbę szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f8e9f-754">Aby kontrolować koszty i nie przekraczać limitów magazynowania danych, należy rejestrować `Trace` `Information` komunikaty na poziomie w magazynach o wysokim poziomie ilości danych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f8e9f-755">Rejestrowanie na `Warning` poziomie `Critical` poziomów zwykle generuje mniejszą liczbę mniejszych komunikatów dzienników.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f8e9f-756">W związku z tym koszty i limity magazynu zazwyczaj nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f8e9f-757">Podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-757">During development:</span></span>
  * <span data-ttu-id="f8e9f-758">Rejestruj `Warning` `Critical` komunikaty w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f8e9f-759">Dodawanie `Trace` `Information` komunikatów podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f8e9f-760">W sekcji [filtrowanie dzienników](#log-filtering) w dalszej części tego artykułu wyjaśniono, jak kontrolować poziomy dzienników obsługiwane przez dostawcę.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f8e9f-761">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f8e9f-762">Przykłady dzienników znajdujące się wcześniej w tym artykule nie wykluczają dzienników poniżej `Information` , dlatego nie `Debug` `Trace` zostały utworzone dzienniki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f8e9f-763">Oto przykład dzienników konsoli utworzonych przez uruchomienie przykładowej aplikacji skonfigurowanej do wyświetlania `Debug` dzienników:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="f8e9f-764">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-764">Log event ID</span></span>

<span data-ttu-id="f8e9f-765">Każdy dziennik może określać *Identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f8e9f-766">Aplikacja Przykładowa wykonuje to przy użyciu lokalnie zdefiniowanej `LoggingEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f8e9f-767">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-767">An event ID associates a set of events.</span></span> <span data-ttu-id="f8e9f-768">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f8e9f-769">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f8e9f-770">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f8e9f-771">Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f8e9f-772">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-772">Log message template</span></span>

<span data-ttu-id="f8e9f-773">Każdy dziennik Określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-773">Each log specifies a message template.</span></span> <span data-ttu-id="f8e9f-774">Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f8e9f-775">Użyj nazw dla symboli zastępczych, a nie liczby.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f8e9f-776">Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f8e9f-777">W poniższym kodzie Zwróć uwagę, że nazwy parametrów są poza kolejnością w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f8e9f-778">Ten kod tworzy komunikat dziennika z wartościami parametrów w kolejności:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f8e9f-779">Struktura rejestrowania działa w ten sposób, aby dostawcy rejestrowania mogli zaimplementować [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f8e9f-780">Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f8e9f-781">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f8e9f-782">Na przykład załóżmy, że wywołania metod rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f8e9f-783">W przypadku wysyłania dzienników do usługi Azure Table Storage każda jednostka tabeli platformy Azure może mieć `ID` właściwości i `RequestTime` , które upraszczają zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f8e9f-784">Zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania limitu czasu wiadomości tekstowej.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f8e9f-785">Wyjątki rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-785">Logging exceptions</span></span>

<span data-ttu-id="f8e9f-786">Metody rejestratora mają przeciążenia umożliwiające przekazanie wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f8e9f-787">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f8e9f-788">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f8e9f-789">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="f8e9f-789">Log filtering</span></span>

<span data-ttu-id="f8e9f-790">Można określić minimalny poziom rejestrowania dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f8e9f-791">Wszystkie dzienniki poniżej minimalnego poziomu nie są przesyłane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f8e9f-792">Aby pominąć wszystkie dzienniki, określ `LogLevel.None` jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f8e9f-793">Wartość całkowita `LogLevel.None` wynosi 6, która jest większa niż `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f8e9f-794">Utwórz reguły filtru w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="f8e9f-794">Create filter rules in configuration</span></span>

<span data-ttu-id="f8e9f-795">Kod szablonu projektu wywołuje `CreateDefaultBuilder` w celu skonfigurowania rejestrowania dla dostawców konsoli, debugowania i EventSource (ASP.NET Core 2,2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f8e9f-796">`CreateDefaultBuilder`Metoda konfiguruje rejestrowanie, aby wyszukać konfigurację w `Logging` sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f8e9f-797">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f8e9f-798">Ten kod JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f8e9f-799">Dla każdego dostawcy wybierana jest pojedyncza reguła, gdy `ILogger` tworzony jest obiekt.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f8e9f-800">Filtrowanie reguł w kodzie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-800">Filter rules in code</span></span>

<span data-ttu-id="f8e9f-801">Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="f8e9f-802">Drugi `AddFilter` określa dostawcę debugowania za pomocą nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f8e9f-803">Pierwszy `AddFilter` ma zastosowanie do wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f8e9f-804">Jak są stosowane reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-804">How filtering rules are applied</span></span>

<span data-ttu-id="f8e9f-805">Dane konfiguracji i `AddFilter` kod przedstawiony w powyższych przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f8e9f-806">Pierwsze sześć pochodzi z przykładu konfiguracji, a ostatnie dwa pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f8e9f-807">Liczba</span><span class="sxs-lookup"><span data-stu-id="f8e9f-807">Number</span></span> | <span data-ttu-id="f8e9f-808">Dostawca</span><span class="sxs-lookup"><span data-stu-id="f8e9f-808">Provider</span></span>      | <span data-ttu-id="f8e9f-809">Kategorie zaczynające się od...</span><span class="sxs-lookup"><span data-stu-id="f8e9f-809">Categories that begin with ...</span></span>          | <span data-ttu-id="f8e9f-810">Minimalny poziom rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f8e9f-811">1</span><span class="sxs-lookup"><span data-stu-id="f8e9f-811">1</span></span>      | <span data-ttu-id="f8e9f-812">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-812">Debug</span></span>         | <span data-ttu-id="f8e9f-813">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-813">All categories</span></span>                          | <span data-ttu-id="f8e9f-814">Informacje</span><span class="sxs-lookup"><span data-stu-id="f8e9f-814">Information</span></span>       |
| <span data-ttu-id="f8e9f-815">2</span><span class="sxs-lookup"><span data-stu-id="f8e9f-815">2</span></span>      | <span data-ttu-id="f8e9f-816">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-816">Console</span></span>       | <span data-ttu-id="f8e9f-817">Microsoft. AspNetCore. MVC. Razor .. Wewnętrz</span><span class="sxs-lookup"><span data-stu-id="f8e9f-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f8e9f-818">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-818">Warning</span></span>           |
| <span data-ttu-id="f8e9f-819">3</span><span class="sxs-lookup"><span data-stu-id="f8e9f-819">3</span></span>      | <span data-ttu-id="f8e9f-820">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-820">Console</span></span>       | <span data-ttu-id="f8e9f-821">Microsoft. AspNetCore. MVC. Razor ..Razor</span><span class="sxs-lookup"><span data-stu-id="f8e9f-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f8e9f-822">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-822">Debug</span></span>             |
| <span data-ttu-id="f8e9f-823">4</span><span class="sxs-lookup"><span data-stu-id="f8e9f-823">4</span></span>      | <span data-ttu-id="f8e9f-824">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-824">Console</span></span>       | <span data-ttu-id="f8e9f-825">Microsoft. AspNetCore. MVC.Razor</span><span class="sxs-lookup"><span data-stu-id="f8e9f-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f8e9f-826">Błąd</span><span class="sxs-lookup"><span data-stu-id="f8e9f-826">Error</span></span>             |
| <span data-ttu-id="f8e9f-827">5</span><span class="sxs-lookup"><span data-stu-id="f8e9f-827">5</span></span>      | <span data-ttu-id="f8e9f-828">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-828">Console</span></span>       | <span data-ttu-id="f8e9f-829">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-829">All categories</span></span>                          | <span data-ttu-id="f8e9f-830">Informacje</span><span class="sxs-lookup"><span data-stu-id="f8e9f-830">Information</span></span>       |
| <span data-ttu-id="f8e9f-831">6</span><span class="sxs-lookup"><span data-stu-id="f8e9f-831">6</span></span>      | <span data-ttu-id="f8e9f-832">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="f8e9f-832">All providers</span></span> | <span data-ttu-id="f8e9f-833">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-833">All categories</span></span>                          | <span data-ttu-id="f8e9f-834">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-834">Debug</span></span>             |
| <span data-ttu-id="f8e9f-835">7</span><span class="sxs-lookup"><span data-stu-id="f8e9f-835">7</span></span>      | <span data-ttu-id="f8e9f-836">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="f8e9f-836">All providers</span></span> | <span data-ttu-id="f8e9f-837">System</span><span class="sxs-lookup"><span data-stu-id="f8e9f-837">System</span></span>                                  | <span data-ttu-id="f8e9f-838">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-838">Debug</span></span>             |
| <span data-ttu-id="f8e9f-839">8</span><span class="sxs-lookup"><span data-stu-id="f8e9f-839">8</span></span>      | <span data-ttu-id="f8e9f-840">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-840">Debug</span></span>         | <span data-ttu-id="f8e9f-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f8e9f-841">Microsoft</span></span>                               | <span data-ttu-id="f8e9f-842">Ślad</span><span class="sxs-lookup"><span data-stu-id="f8e9f-842">Trace</span></span>             |

<span data-ttu-id="f8e9f-843">Po `ILogger` utworzeniu obiektu `ILoggerFactory` obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f8e9f-844">Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f8e9f-845">Najbardziej konkretną regułą można wybrać dla każdego dostawcy i pary kategorii z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f8e9f-846">Następujący algorytm jest używany dla każdego dostawcy, gdy `ILogger` jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f8e9f-847">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f8e9f-848">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f8e9f-849">W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f8e9f-850">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f8e9f-851">Jeśli wybrano wiele reguł, zrób to **ostatnie** .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f8e9f-852">Jeśli nie wybrano żadnych reguł, użyj `MinimumLevel` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f8e9f-853">Na powyższej liście reguł Załóżmy, że tworzysz `ILogger` obiekt dla kategorii "Microsoft. AspNetCore. MVC. Razor . Razor ViewEngine":</span><span class="sxs-lookup"><span data-stu-id="f8e9f-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f8e9f-854">Dla dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f8e9f-855">Reguła 8 jest najbardziej specyficzna, więc jest to jedna wybrana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f8e9f-856">W przypadku dostawcy konsoli obowiązują reguły 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f8e9f-857">Reguła 3 jest najbardziej specyficzna.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="f8e9f-858">Wystąpienie wyników `ILogger` wysyła dzienniki `Trace` poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f8e9f-859">Dzienniki `Debug` poziomów i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f8e9f-860">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="f8e9f-860">Provider aliases</span></span>

<span data-ttu-id="f8e9f-861">Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f8e9f-862">W przypadku dostawców wbudowanych Użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f8e9f-863">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-863">Console</span></span>
* <span data-ttu-id="f8e9f-864">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-864">Debug</span></span>
* <span data-ttu-id="f8e9f-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-865">EventSource</span></span>
* <span data-ttu-id="f8e9f-866">Elemencie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-866">EventLog</span></span>
* <span data-ttu-id="f8e9f-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-867">TraceSource</span></span>
* <span data-ttu-id="f8e9f-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f8e9f-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="f8e9f-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f8e9f-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f8e9f-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f8e9f-871">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="f8e9f-871">Default minimum level</span></span>

<span data-ttu-id="f8e9f-872">Istnieje ustawienie minimalnego poziomu, które działa tylko wtedy, gdy nie mają zastosowania żadne reguły z konfiguracji lub kodu dla danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f8e9f-873">Poniższy przykład pokazuje, jak ustawić poziom minimalny:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f8e9f-874">Jeśli poziom minimalny nie został jawnie ustawiony, wartość domyślna to `Information` , co oznacza, że `Trace` dzienniki i `Debug` są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f8e9f-875">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-875">Filter functions</span></span>

<span data-ttu-id="f8e9f-876">Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f8e9f-877">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f8e9f-878">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f8e9f-879">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="f8e9f-879">System categories and levels</span></span>

<span data-ttu-id="f8e9f-880">Poniżej przedstawiono niektóre kategorie używane przez ASP.NET Core i Entity Framework Core, z informacjami o dziennikach, od których należy się spodziewać:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f8e9f-881">Kategoria</span><span class="sxs-lookup"><span data-stu-id="f8e9f-881">Category</span></span>                            | <span data-ttu-id="f8e9f-882">Uwagi</span><span class="sxs-lookup"><span data-stu-id="f8e9f-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f8e9f-883">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f8e9f-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f8e9f-884">Ogólna Diagnostyka ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f8e9f-885">Microsoft. AspNetCore. dataprotection</span><span class="sxs-lookup"><span data-stu-id="f8e9f-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f8e9f-886">Które klucze zostały wzięte pod uwagę, znaleziono i użyte.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f8e9f-887">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f8e9f-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f8e9f-888">Dozwolone hosty.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-888">Hosts allowed.</span></span> |
| <span data-ttu-id="f8e9f-889">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="f8e9f-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f8e9f-890">Jak długo trwa wykonywanie żądań HTTP i czas ich uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f8e9f-891">Które hostowanie zestawów uruchamiania zostało załadowane.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f8e9f-892">Microsoft. AspNetCore. MVC</span><span class="sxs-lookup"><span data-stu-id="f8e9f-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f8e9f-893">MVC i Razor Diagnostyka.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f8e9f-894">Powiązanie modelu, wykonywanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f8e9f-895">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="f8e9f-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f8e9f-896">Informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-896">Route matching information.</span></span> |
| <span data-ttu-id="f8e9f-897">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="f8e9f-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f8e9f-898">Reagowanie na uruchamianie, zatrzymywanie i utrzymywanie aktywności.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f8e9f-899">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f8e9f-900">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f8e9f-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f8e9f-901">Obsługiwane pliki.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-901">Files served.</span></span> |
| <span data-ttu-id="f8e9f-902">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f8e9f-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f8e9f-903">Ogólna Diagnostyka Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f8e9f-904">Aktywność i Konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f8e9f-905">Zakresy dziennika</span><span class="sxs-lookup"><span data-stu-id="f8e9f-905">Log scopes</span></span>

 <span data-ttu-id="f8e9f-906">*Zakres* może grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f8e9f-907">Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f8e9f-908">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f8e9f-909">Zakres jest `IDisposable` typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę i obowiązuje do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f8e9f-910">Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f8e9f-911">Poniższy kod włącza zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f8e9f-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="f8e9f-913">Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane do włączenia rejestrowania na podstawie zakresu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f8e9f-914">Informacje o konfiguracji znajdują się w sekcji [Konfiguracja](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f8e9f-915">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f8e9f-916">Wbudowani dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-916">Built-in logging providers</span></span>

<span data-ttu-id="f8e9f-917">ASP.NET Core dostarcza następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f8e9f-918">Konsola</span><span class="sxs-lookup"><span data-stu-id="f8e9f-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="f8e9f-919">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f8e9f-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f8e9f-921">Elemencie</span><span class="sxs-lookup"><span data-stu-id="f8e9f-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f8e9f-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f8e9f-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f8e9f-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f8e9f-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f8e9f-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f8e9f-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f8e9f-926">Aby uzyskać informacje na temat strumienia stdout i rejestrowania debugowania za pomocą modułu ASP.NET Core, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f8e9f-927">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="f8e9f-927">Console provider</span></span>

<span data-ttu-id="f8e9f-928">Pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Provider wysyła dane wyjściowe dziennika do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f8e9f-929">Aby wyświetlić dane wyjściowe rejestrowania konsoli, Otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f8e9f-930">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="f8e9f-930">Debug provider</span></span>

<span data-ttu-id="f8e9f-931">Pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) Provider zapisuje dane wyjściowe dziennika przy użyciu klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) ( `Debug.WriteLine` wywołania metod).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f8e9f-932">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f8e9f-933">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="f8e9f-933">Event Source provider</span></span>

<span data-ttu-id="f8e9f-934">Pakiet dostawcy [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformę źródła zdarzeń, używając nazwy `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f8e9f-935">W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f8e9f-936">Dostawca źródła zdarzeń zostanie dodany automatycznie, gdy `CreateDefaultBuilder` jest wywoływana w celu skompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="f8e9f-937">Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f8e9f-938">Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f8e9f-939">Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f8e9f-940">(Nie przegap gwiazdki na początku ciągu znaków).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Narzędzia PerfView dodatkowych dostawców](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f8e9f-942">Dostawca dziennika zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="f8e9f-942">Windows EventLog provider</span></span>

<span data-ttu-id="f8e9f-943">Pakiet dostawcy [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f8e9f-944">[Przeciążenia addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umożliwiają przekazywanie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f8e9f-945">Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f8e9f-946">`LogName`: "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="f8e9f-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="f8e9f-947">`SourceName`: "Środowisko uruchomieniowe platformy .NET"</span><span class="sxs-lookup"><span data-stu-id="f8e9f-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="f8e9f-948">`MachineName`: Nazwa komputera lokalnego jest używana.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="f8e9f-949">Zdarzenia są rejestrowane dla [poziomu ostrzeżeń i wyższych](#log-level).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f8e9f-950">Poniższy przykład ustawia domyślny poziom dziennika dziennika zdarzeń <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="f8e9f-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f8e9f-951">Dostawca TraceSource</span><span class="sxs-lookup"><span data-stu-id="f8e9f-951">TraceSource provider</span></span>

<span data-ttu-id="f8e9f-952">Pakiet dostawcy [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f8e9f-953">[Przeciążenia AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie danych w przełączniku źródłowym i odbiorniku śledzenia.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f8e9f-954">Aby można było korzystać z tego dostawcy, aplikacja musi działać na .NET Framework (a nie na platformie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f8e9f-955">Dostawca może kierować komunikaty do różnych [odbiorników](/dotnet/framework/debug-trace-profile/trace-listeners), takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f8e9f-956">Dostawca Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f8e9f-956">Azure App Service provider</span></span>

<span data-ttu-id="f8e9f-957">Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f8e9f-958">Pakiet dostawcy nie jest uwzględniony w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f8e9f-959">Podczas określania elementu docelowego .NET Framework lub odwoływania się do niego `Microsoft.AspNetCore.App` , Dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="f8e9f-960"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>Przeciążenie umożliwia przekazywanie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> .</span><span class="sxs-lookup"><span data-stu-id="f8e9f-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="f8e9f-961">Obiekt Settings może przesłonić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu BLOB i limit rozmiaru pliku.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="f8e9f-962">(*Szablon danych wyjściowych* to szablon wiadomości, który jest stosowany do wszystkich dzienników oprócz tego, co jest dostępne w `ILogger` wywołaniu metody).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="f8e9f-963">Po wdrożeniu w aplikacji App Service, aplikacja będzie przestrzegać ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f8e9f-964">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f8e9f-965">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="f8e9f-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f8e9f-966">**Rejestrowanie aplikacji (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="f8e9f-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f8e9f-967">Domyślną lokalizacją plików dziennika jest folder *D: \\ Home \\ \\ LogFiles* , a domyślna nazwa pliku to *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f8e9f-968">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f8e9f-969">Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f8e9f-970">Dostawca działa tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f8e9f-971">Nie ma ono wpływu, gdy projekt jest uruchamiany lokalnie, &mdash; nie zapisuje w plikach lokalnych ani w lokalnym magazynie programistycznym dla obiektów BLOB.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f8e9f-972">Przesyłanie strumieniowe dzienników Azure</span><span class="sxs-lookup"><span data-stu-id="f8e9f-972">Azure log streaming</span></span>

<span data-ttu-id="f8e9f-973">Usługa przesyłania strumieniowego w usłudze Azure log umożliwia wyświetlanie dziennika aktywności w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f8e9f-974">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e9f-974">The app server</span></span>
* <span data-ttu-id="f8e9f-975">Serwer sieci Web</span><span class="sxs-lookup"><span data-stu-id="f8e9f-975">The web server</span></span>
* <span data-ttu-id="f8e9f-976">Śledzenie nieudanych żądań</span><span class="sxs-lookup"><span data-stu-id="f8e9f-976">Failed request tracing</span></span>

<span data-ttu-id="f8e9f-977">Aby skonfigurować przesyłanie strumieniowe dzienników Azure:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f8e9f-978">Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f8e9f-979">Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f8e9f-980">Wybierz **poziom**dziennika.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-980">Choose the log **Level**.</span></span> <span data-ttu-id="f8e9f-981">To ustawienie dotyczy tylko przesyłania strumieniowego dzienników platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f8e9f-982">Przejdź do strony **strumień dziennika** , aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f8e9f-983">Są one rejestrowane przez aplikację za pomocą `ILogger` interfejsu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f8e9f-984">Rejestrowanie śledzenia Application Insights platformy Azure</span><span class="sxs-lookup"><span data-stu-id="f8e9f-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f8e9f-985">Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na platformie Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f8e9f-986">Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f8e9f-987">Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f8e9f-988">Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f8e9f-989">Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f8e9f-990">Nie używaj pakietu [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) &mdash; , który jest przeznaczony dla ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f8e9f-991">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f8e9f-992">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="f8e9f-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f8e9f-993">[Application Insights dla ASP.NET Core aplikacji](/azure/azure-monitor/app/asp-net-core) — Zacznij tutaj, jeśli chcesz zaimplementować cały zakres Application Insights telemetrii wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f8e9f-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger](/azure/azure-monitor/app/ilogger) — Rozpocznij tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f8e9f-995">[Karty rejestrowania Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f8e9f-996">[Zainstaluj, skonfiguruj i zainicjuj samouczek Application INSIGHTS SDK](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f8e9f-997">Dostawcy rejestrowania innych firm</span><span class="sxs-lookup"><span data-stu-id="f8e9f-997">Third-party logging providers</span></span>

<span data-ttu-id="f8e9f-998">Platformy rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f8e9f-999">[ELMAH.IO](https://elmah.io/) ([repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f8e9f-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f8e9f-1001">[JSNLog](https://jsnlog.com/) ([repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f8e9f-1002">[KissLog.NET](https://kisslog.net/) ([repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f8e9f-1003">[Log4Net](https://logging.apache.org/log4net/) ([repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f8e9f-1004">[Loggr](https://loggr.net/) ([repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f8e9f-1005">[NLOG](https://nlog-project.org/) ([repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f8e9f-1006">[Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f8e9f-1007">[Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f8e9f-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f8e9f-1009">Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f8e9f-1010">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f8e9f-1011">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f8e9f-1012">Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f8e9f-1013">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f8e9f-1014">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8e9f-1015">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f8e9f-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
