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
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorRejestrowanie ASP.NET Core

::: zone pivot="webassembly"

Skonfiguruj niestandardowe rejestrowanie w Blazor WebAssembly aplikacjach przy użyciu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> właściwości.

Dodaj przestrzeń nazw dla <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> do `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

W programie w programie `Program.Main` `Program.cs` należy określić minimalny poziom rejestrowania przy użyciu <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> i dodać niestandardowego dostawcę rejestrowania:

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging` .

Konfigurację rejestrowania można załadować z plików ustawień aplikacji. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="no-locsignalr-net-client-logging"></a>SignalR Rejestrowanie klienta platformy .NET

Wstrzyknąć, <xref:Microsoft.Extensions.Logging.ILoggerProvider> Aby dodać `WebAssemblyConsoleLogger` do dostawców rejestrowania przekazaną do <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . W przeciwieństwie do tradycyjnego <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` jest otoką dla interfejsów API rejestrowania specyficznych dla przeglądarki (na przykład `console.log` ). Użycie w `WebAssemblyConsoleLogger` programie umożliwia rejestrowanie w ramach programu mono wewnątrz kontekstu przeglądarki.

> [!NOTE]
> `WebAssemblyConsoleLogger` jest [wewnętrzny](/dotnet/csharp/language-reference/keywords/internal) i nie jest dostępny do bezpośredniego użycia w kodzie dewelopera.

Dodaj przestrzeń nazw dla <xref:Microsoft.Extensions.Logging?displayProperty=fullName> i wstrzyknąć <xref:Microsoft.Extensions.Logging.ILoggerProvider> do składnika:

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

W [ `OnInitializedAsync` metodzie](xref:blazor/components/lifecycle#component-initialization-methods)składnika Użyj <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Ogólne wskazówki dotyczące rejestrowania ASP.NET Core odnoszące się do programu Blazor Server znajdują się w temacie <xref:fundamentals/logging/index> .

::: zone-end

## <a name="log-in-no-locrazor-components"></a>Składniki logowania Razor

Rejestratory respektują konfigurację uruchamiania aplikacji.

`using`Dyrektywa for <xref:Microsoft.Extensions.Logging> jest wymagana do obsługi uzupełniania [technologii IntelliSense](/visualstudio/ide/using-intellisense) dla interfejsów API, takich jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> i <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

Poniższy przykład demonstruje rejestrowanie za pomocą <xref:Microsoft.Extensions.Logging.ILogger> składników w programie.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

Poniższy przykład demonstruje rejestrowanie za pomocą <xref:Microsoft.Extensions.Logging.ILoggerFactory> składników w programie.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/logging/index>
