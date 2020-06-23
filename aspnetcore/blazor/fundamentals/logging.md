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
ms.openlocfilehash: 841c4021d9217312b2601b0e775542c6455cca82
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240882"
---
# <a name="aspnet-core-blazor-logging"></a>BlazorRejestrowanie ASP.NET Core

## <a name="blazor-webassembly"></a>BlazorZestaw webassembly

Skonfiguruj rejestrowanie w Blazor aplikacjach webassembly przy użyciu `WebAssemblyHostBuilder.Logging` właściwości w `Program.Main` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging` .

Konfigurację rejestrowania można załadować z plików ustawień aplikacji. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="blazor-server"></a>BlazorServer

Ogólne wskazówki dotyczące rejestrowania ASP.NET Core można znaleźć w temacie <xref:fundamentals/logging/index> .

## <a name="blazor-webassembly-signalr-net-client-logging"></a>BlazorSignalRRejestrowanie klientów .NET zestawu Webassembly

Wstrzyknąć, <xref:Microsoft.Extensions.Logging.ILoggerProvider> Aby dodać `WebAssemblyConsoleLogger` do dostawców rejestrowania przekazaną do <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . W przeciwieństwie do tradycyjnego <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` jest otoką dla interfejsów API rejestrowania specyficznych dla przeglądarki (na przykład `console.log` ). Użycie w `WebAssemblyConsoleLogger` programie umożliwia rejestrowanie w ramach programu mono wewnątrz kontekstu przeglądarki.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a>Składniki logowania Razor

Rejestratory respektują konfigurację uruchamiania aplikacji.

`using`Dyrektywa for <xref:Microsoft.Extensions.Logging> jest wymagana do obsługi uzupełniania technologii IntelliSense dla interfejsów API, takich jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> i <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

Poniższy przykład demonstruje logowanie za pomocą <xref:Microsoft.Extensions.Logging.ILogger> składników w programie Razor :

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

Poniższy przykład demonstruje logowanie za pomocą <xref:Microsoft.Extensions.Logging.ILoggerFactory> składników w programie Razor :

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

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/index>
