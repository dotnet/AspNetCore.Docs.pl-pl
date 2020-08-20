---
title: Migrowanie z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0
author: pakrym
description: Dowiedz się, jak przeprowadzić migrację aplikacji non-ASP.NET Core, która korzysta z Microsoft. Extensions. rejestrowanie z 2,1 do 2,2 lub 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 8ef07118e3a885e41221402bdfc2d7e942c6dd73
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634101"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="cb846-103">Migrowanie z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0</span><span class="sxs-lookup"><span data-stu-id="cb846-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="cb846-104">W tym artykule opisano typowe kroki migrowania aplikacji non-ASP.NET Core, która używa `Microsoft.Extensions.Logging` od 2,1 do 2,2 lub 3,0.</span><span class="sxs-lookup"><span data-stu-id="cb846-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="cb846-105">Z wersji 2.1 do 2.2</span><span class="sxs-lookup"><span data-stu-id="cb846-105">2.1 to 2.2</span></span>

<span data-ttu-id="cb846-106">Ręcznie Utwórz `ServiceCollection` i Wywołaj `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb846-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="cb846-107">2,1 przykład:</span><span class="sxs-lookup"><span data-stu-id="cb846-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="cb846-108">2,2 przykład:</span><span class="sxs-lookup"><span data-stu-id="cb846-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="cb846-109">2,1 do 3,0</span><span class="sxs-lookup"><span data-stu-id="cb846-109">2.1 to 3.0</span></span>

<span data-ttu-id="cb846-110">W 3,0 użyj `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="cb846-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="cb846-111">2,1 przykład:</span><span class="sxs-lookup"><span data-stu-id="cb846-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="cb846-112">3,0 przykład:</span><span class="sxs-lookup"><span data-stu-id="cb846-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="cb846-113">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb846-113">Additional resources</span></span>

* <span data-ttu-id="cb846-114">[Microsoft. Extensions. Logging. Console pakiet NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="cb846-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
