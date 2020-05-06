---
title: Migrowanie z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0
author: pakrym
description: Dowiedz się, jak przeprowadzić migrację aplikacji non-ASP.NET Core, która korzysta z Microsoft. Extensions. rejestrowanie z 2,1 do 2,2 lub 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777062"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="3749c-103">Migrowanie z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0</span><span class="sxs-lookup"><span data-stu-id="3749c-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="3749c-104">W tym artykule opisano typowe kroki migrowania aplikacji non-ASP.NET Core, która używa `Microsoft.Extensions.Logging` od 2,1 do 2,2 lub 3,0.</span><span class="sxs-lookup"><span data-stu-id="3749c-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="3749c-105">Z wersji 2.1 do 2.2</span><span class="sxs-lookup"><span data-stu-id="3749c-105">2.1 to 2.2</span></span>

<span data-ttu-id="3749c-106">Ręcznie Utwórz `ServiceCollection` i Wywołaj `AddLogging`.</span><span class="sxs-lookup"><span data-stu-id="3749c-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="3749c-107">2,1 przykład:</span><span class="sxs-lookup"><span data-stu-id="3749c-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="3749c-108">2,2 przykład:</span><span class="sxs-lookup"><span data-stu-id="3749c-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="3749c-109">2,1 do 3,0</span><span class="sxs-lookup"><span data-stu-id="3749c-109">2.1 to 3.0</span></span>

<span data-ttu-id="3749c-110">W 3,0 użyj `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="3749c-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="3749c-111">2,1 przykład:</span><span class="sxs-lookup"><span data-stu-id="3749c-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="3749c-112">3,0 przykład:</span><span class="sxs-lookup"><span data-stu-id="3749c-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="3749c-113">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3749c-113">Additional resources</span></span>

<xref:fundamentals/logging/index>