---
title: BlazorRejestrowanie ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat rejestrowania w Blazor aplikacjach, w tym konfiguracji poziomu dziennika oraz pisania komunikatów dziennika ze Razor składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
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
ms.openlocfilehash: 79a77a7e7f5c6c5ecb09ffa276ec1d0a3103e6d6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626509"
---
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorRejestrowanie ASP.NET Core

## Blazor WebAssembly

Skonfiguruj rejestrowanie w Blazor WebAssembly aplikacjach przy użyciu `WebAssemblyHostBuilder.Logging` właściwości w `Program.Main` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging` .

Konfigurację rejestrowania można załadować z plików ustawień aplikacji. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/configuration#logging-configuration>.

## Blazor Server

Ogólne wskazówki dotyczące rejestrowania ASP.NET Core można znaleźć w temacie <xref:fundamentals/logging/index> .

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a>Blazor WebAssemblySignalRRejestrowanie klienta platformy .NET

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

## <a name="log-in-no-locrazor-components"></a>Składniki logowania Razor

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/logging/index>
