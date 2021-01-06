---
title: BlazorRejestrowanie ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat rejestrowania w Blazor aplikacjach, w tym konfiguracji poziomu dziennika oraz pisania komunikatów dziennika ze Razor składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97666836"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="df745-103">BlazorRejestrowanie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df745-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="df745-104">Skonfiguruj niestandardowe rejestrowanie w Blazor WebAssembly aplikacjach przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> właściwości.</span><span class="sxs-lookup"><span data-stu-id="df745-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="df745-105">Dodaj przestrzeń nazw dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> do `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="df745-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="df745-106">W programie w programie `Program.Main` `Program.cs` należy określić minimalny poziom rejestrowania przy użyciu <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> i dodać niestandardowego dostawcę rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="df745-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="df745-107">`Logging`Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging` .</span><span class="sxs-lookup"><span data-stu-id="df745-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="df745-108">Konfigurację rejestrowania można załadować z plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df745-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="df745-109">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="df745-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="df745-110">SignalR Rejestrowanie klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="df745-110">SignalR .NET client logging</span></span>

<span data-ttu-id="df745-111">Wstrzyknąć, <xref:Microsoft.Extensions.Logging.ILoggerProvider> Aby dodać `WebAssemblyConsoleLogger` do dostawców rejestrowania przekazaną do <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="df745-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="df745-112">W przeciwieństwie do tradycyjnego <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` jest otoką dla interfejsów API rejestrowania specyficznych dla przeglądarki (na przykład `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="df745-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="df745-113">Użycie w `WebAssemblyConsoleLogger` programie umożliwia rejestrowanie w ramach programu mono wewnątrz kontekstu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="df745-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="df745-114">`WebAssemblyConsoleLogger` jest [wewnętrzny](/dotnet/csharp/language-reference/keywords/internal) i nie jest dostępny do bezpośredniego użycia w kodzie dewelopera.</span><span class="sxs-lookup"><span data-stu-id="df745-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="df745-115">Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Logging?displayProperty=fullName> i wstrzyknąć <xref:Microsoft.Extensions.Logging.ILoggerProvider> do składnika:</span><span class="sxs-lookup"><span data-stu-id="df745-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="df745-116">W [ `OnInitializedAsync` metodzie](xref:blazor/components/lifecycle#component-initialization-methods)składnika Użyj <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="df745-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="df745-117">Ogólne wskazówki dotyczące rejestrowania ASP.NET Core odnoszące się do programu Blazor Server znajdują się w temacie <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="df745-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="df745-118">Składniki logowania Razor</span><span class="sxs-lookup"><span data-stu-id="df745-118">Log in Razor components</span></span>

<span data-ttu-id="df745-119">Rejestratory respektują konfigurację uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df745-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="df745-120">`using`Dyrektywa for <xref:Microsoft.Extensions.Logging> jest wymagana do obsługi uzupełniania [technologii IntelliSense](/visualstudio/ide/using-intellisense) dla interfejsów API, takich jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> i <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="df745-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="df745-121">Poniższy przykład demonstruje rejestrowanie za pomocą <xref:Microsoft.Extensions.Logging.ILogger> składników w programie.</span><span class="sxs-lookup"><span data-stu-id="df745-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="df745-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="df745-122">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="df745-123">Poniższy przykład demonstruje rejestrowanie za pomocą <xref:Microsoft.Extensions.Logging.ILoggerFactory> składników w programie.</span><span class="sxs-lookup"><span data-stu-id="df745-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="df745-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="df745-124">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="df745-125">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="df745-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
