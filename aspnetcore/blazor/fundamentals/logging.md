---
title: BlazorRejestrowanie ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat rejestrowania w Blazor aplikacjach, w tym konfiguracji poziomu dziennika oraz pisania komunikatów dziennika ze Razor składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: b0448d5f6e5e16a726eb2274dcacb4dfa8314b5d
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103839"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="f0446-103">BlazorRejestrowanie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0446-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="f0446-104">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="f0446-104"> WebAssembly</span></span>

<span data-ttu-id="f0446-105">Skonfiguruj rejestrowanie w Blazor aplikacjach webassembly przy użyciu `WebAssemblyHostBuilder.Logging` właściwości w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f0446-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="f0446-106">`Logging`Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging` .</span><span class="sxs-lookup"><span data-stu-id="f0446-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="f0446-107">Konfigurację rejestrowania można załadować z plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f0446-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="f0446-108">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="f0446-108">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="f0446-109">Server</span><span class="sxs-lookup"><span data-stu-id="f0446-109"> Server</span></span>

<span data-ttu-id="f0446-110">Ogólne wskazówki dotyczące rejestrowania ASP.NET Core można znaleźć w temacie <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="f0446-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor<span data-ttu-id="f0446-111">SignalRRejestrowanie klientów .NET zestawu Webassembly</span><span class="sxs-lookup"><span data-stu-id="f0446-111"> WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="f0446-112">Wstrzyknąć, <xref:Microsoft.Extensions.Logging.ILoggerProvider> Aby dodać `WebAssemblyConsoleLogger` do dostawców rejestrowania przekazaną do <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f0446-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="f0446-113">W przeciwieństwie do tradycyjnego <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` jest otoką dla interfejsów API rejestrowania specyficznych dla przeglądarki (na przykład `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="f0446-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="f0446-114">Użycie w `WebAssemblyConsoleLogger` programie umożliwia rejestrowanie w ramach programu mono wewnątrz kontekstu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f0446-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="f0446-115">Składniki logowania Razor</span><span class="sxs-lookup"><span data-stu-id="f0446-115">Log in Razor components</span></span>

<span data-ttu-id="f0446-116">Rejestratory respektują konfigurację uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f0446-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="f0446-117">`using`Dyrektywa for <xref:Microsoft.Extensions.Logging> jest wymagana do obsługi uzupełniania technologii IntelliSense dla interfejsów API, takich jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> i <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="f0446-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="f0446-118">Poniższy przykład demonstruje logowanie za pomocą <xref:Microsoft.Extensions.Logging.ILogger> składników w programie Razor :</span><span class="sxs-lookup"><span data-stu-id="f0446-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="f0446-119">Poniższy przykład demonstruje logowanie za pomocą <xref:Microsoft.Extensions.Logging.ILoggerFactory> składników w programie Razor :</span><span class="sxs-lookup"><span data-stu-id="f0446-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f0446-120">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f0446-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
