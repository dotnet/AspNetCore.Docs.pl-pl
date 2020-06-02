---
<span data-ttu-id="357aa-101">title: Migruj z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0 Author: pakrym Description: informacje na temat migrowania aplikacji non-ASP.NET Core, która używa Microsoft. Extensions. rejestrowanie z 2,1 do 2,2 lub 3,0.</span><span class="sxs-lookup"><span data-stu-id="357aa-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="357aa-102">MS. Author: pakrym MS. Custom: MVC MS. Date: 01/04/2019 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="357aa-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="357aa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="357aa-103">'Blazor'</span></span>
- <span data-ttu-id="357aa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="357aa-104">'Identity'</span></span>
- <span data-ttu-id="357aa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="357aa-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="357aa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="357aa-106">'Razor'</span></span>
- <span data-ttu-id="357aa-107">" SignalR UID: Migration/Logging-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="357aa-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="357aa-108">Migrowanie z Microsoft. Extensions. Logging 2,1 do 2,2 lub 3,0</span><span class="sxs-lookup"><span data-stu-id="357aa-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="357aa-109">W tym artykule opisano typowe kroki migrowania aplikacji non-ASP.NET Core, która używa `Microsoft.Extensions.Logging` od 2,1 do 2,2 lub 3,0.</span><span class="sxs-lookup"><span data-stu-id="357aa-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="357aa-110">Z wersji 2.1 do 2.2</span><span class="sxs-lookup"><span data-stu-id="357aa-110">2.1 to 2.2</span></span>

<span data-ttu-id="357aa-111">Ręcznie Utwórz `ServiceCollection` i Wywołaj `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="357aa-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="357aa-112">2,1 przykład:</span><span class="sxs-lookup"><span data-stu-id="357aa-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="357aa-113">2,2 przykład:</span><span class="sxs-lookup"><span data-stu-id="357aa-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="357aa-114">2,1 do 3,0</span><span class="sxs-lookup"><span data-stu-id="357aa-114">2.1 to 3.0</span></span>

<span data-ttu-id="357aa-115">W 3,0 użyj `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="357aa-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="357aa-116">2,1 przykład:</span><span class="sxs-lookup"><span data-stu-id="357aa-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="357aa-117">3,0 przykład:</span><span class="sxs-lookup"><span data-stu-id="357aa-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="357aa-118">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="357aa-118">Additional resources</span></span>

* <span data-ttu-id="357aa-119">[Microsoft. Extensions. Logging. Console pakiet NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="357aa-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
