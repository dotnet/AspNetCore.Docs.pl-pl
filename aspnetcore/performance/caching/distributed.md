---
title: Rozproszone buforowanie w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak za pomocą rozproszonej pamięci podręcznej ASP.NET Core poprawić wydajność i skalowalność aplikacji, szczególnie w środowisku chmury lub farmy serwerów.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 6f89046f2e1805111dd81b3282253a72a7c6ea09
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281019"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="e682d-103">Rozproszone buforowanie w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e682d-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="e682d-104">Autorzy [Mohsin Nasir](https://github.com/mohsinnasir) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e682d-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e682d-105">Rozproszonej pamięci podręcznej to pamięć podręczna współdzielona przez wiele serwerów aplikacji, zazwyczaj obsługiwana jako usługa zewnętrzna do serwerów aplikacji, które mają do niego dostęp.</span><span class="sxs-lookup"><span data-stu-id="e682d-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="e682d-106">Rozproszona pamięć podręczna może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie gdy aplikacja jest hostowana przez usługę w chmurze lub farmę serwerów.</span><span class="sxs-lookup"><span data-stu-id="e682d-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="e682d-107">Rozproszonej pamięci podręcznej ma kilka zalet w porównaniu z innymi scenariuszami buforowania, w których buforowane dane są przechowywane na poszczególnych serwerach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="e682d-108">Dane przechowywane w pamięci podręcznej są dystrybuowane:</span><span class="sxs-lookup"><span data-stu-id="e682d-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="e682d-109">Jest *spójna* (spójna) między żądaniami do wielu serwerów.</span><span class="sxs-lookup"><span data-stu-id="e682d-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="e682d-110">Przeżyje ponowne uruchomienia serwera i wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="e682d-111">Nie używa pamięci lokalnej.</span><span class="sxs-lookup"><span data-stu-id="e682d-111">Doesn't use local memory.</span></span>

<span data-ttu-id="e682d-112">Konfiguracja rozproszonej pamięci podręcznej jest specyficzna dla implementacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="e682d-113">W tym artykule opisano sposób konfigurowania SQL Server i rozproszonej pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="e682d-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="e682d-114">Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w witrynie GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="e682d-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="e682d-115">Niezależnie od tego, która implementacja została wybrana, aplikacja współdziała z pamięcią podręczną przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="e682d-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="e682d-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e682d-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e682d-117">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="e682d-117">Prerequisites</span></span>

<span data-ttu-id="e682d-118">Aby użyć rozproszonej pamięci podręcznej SQL Server, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="e682d-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="e682d-119">Aby użyć rozproszonej pamięci podręcznej Redis, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="e682d-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="e682d-120">Aby użyć rozproszonej pamięci podręcznej NCache, Dodaj odwołanie do pakietu do pakietu [NCache. Microsoft. Extensions. cache. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="e682d-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="e682d-121">IDistributedCache, interfejs</span><span class="sxs-lookup"><span data-stu-id="e682d-121">IDistributedCache interface</span></span>

<span data-ttu-id="e682d-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Interfejs udostępnia następujące metody do manipulowania elementami w implementacji rozproszonej pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="e682d-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="e682d-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Akceptuje klucz ciągu i pobiera buforowany element jako `byte[]` tablicę, jeśli znajduje się w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="e682d-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Dodaje element (jako `byte[]` tablicowy) do pamięci podręcznej przy użyciu klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="e682d-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="e682d-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Odświeża element w pamięci podręcznej na podstawie jego klucza, resetowanie przekroczenia limitu czasu wygaśnięcia (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="e682d-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="e682d-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Usuwa element pamięci podręcznej na podstawie jego klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="e682d-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="e682d-127">Ustanów usługi rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="e682d-127">Establish distributed caching services</span></span>

<span data-ttu-id="e682d-128">Zarejestruj implementację programu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e682d-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e682d-129">Implementacje wdrożone w ramach platformy opisane w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="e682d-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="e682d-130">Pamięć podręczna pamięci rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="e682d-131">Pamięć podręczna rozproszonej SQL Server</span><span class="sxs-lookup"><span data-stu-id="e682d-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="e682d-132">Pamięć podręczna Redis rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="e682d-133">Pamięć podręczna NCache rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="e682d-134">Pamięć podręczna pamięci rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-134">Distributed Memory Cache</span></span>

<span data-ttu-id="e682d-135">Pamięć podręczna pamięci podręcznej ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) to implementacja udostępniona przez platformę <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , która przechowuje elementy w pamięci.</span><span class="sxs-lookup"><span data-stu-id="e682d-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="e682d-136">Pamięć podręczna pamięci rozproszonej nie jest rzeczywistą rozproszoną pamięcią podręczną.</span><span class="sxs-lookup"><span data-stu-id="e682d-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="e682d-137">Elementy buforowane są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="e682d-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="e682d-138">Pamięć podręczna pamięci rozproszonej jest przydatną implementacją:</span><span class="sxs-lookup"><span data-stu-id="e682d-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="e682d-139">W scenariuszach projektowania i testowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="e682d-140">Gdy jest używany pojedynczy serwer w środowisku produkcyjnym i użycie pamięci nie jest problemem.</span><span class="sxs-lookup"><span data-stu-id="e682d-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="e682d-141">Implementacja rozproszonej pamięci podręcznej pamięć podręczna magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="e682d-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="e682d-142">Pozwala to na wdrożenie prawdziwego rozwiązania do buforowania rozproszonego w przyszłości, jeśli będzie konieczne przeprowadzenie wielu węzłów lub odporności na uszkodzenia.</span><span class="sxs-lookup"><span data-stu-id="e682d-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="e682d-143">Przykładowa aplikacja korzysta z pamięci podręcznej pamięci rozproszonej, gdy aplikacja jest uruchamiana w środowisku programistycznym w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="e682d-144">Pamięć podręczna rozproszonej SQL Server</span><span class="sxs-lookup"><span data-stu-id="e682d-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="e682d-145">Implementacja rozproszonej pamięci podręcznej SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umożliwia rozproszonej pamięci podręcznej użycie bazy danych SQL Server jako magazynu zapasowego.</span><span class="sxs-lookup"><span data-stu-id="e682d-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="e682d-146">Aby utworzyć SQL Server tabelę elementów w pamięci podręcznej w wystąpieniu SQL Server, można użyć `sql-cache` Narzędzia.</span><span class="sxs-lookup"><span data-stu-id="e682d-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="e682d-147">Narzędzie tworzy tabelę z określoną nazwą i schematem.</span><span class="sxs-lookup"><span data-stu-id="e682d-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="e682d-148">Utwórz tabelę w SQL Server, uruchamiając `sql-cache create` polecenie.</span><span class="sxs-lookup"><span data-stu-id="e682d-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="e682d-149">Podaj wystąpienie SQL Server ( `Data Source` ), bazę danych ( `Initial Catalog` ), schemat (na przykład `dbo` ) i nazwę tabeli (na przykład `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="e682d-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="e682d-150">Komunikat jest rejestrowany w celu wskazania, że narzędzie zakończyło się pomyślnie:</span><span class="sxs-lookup"><span data-stu-id="e682d-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="e682d-151">Tabela utworzona przez `sql-cache` Narzędzie ma następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="e682d-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela pamięci podręcznej SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="e682d-153">Aplikacja powinna manipulować wartościami pamięci podręcznej przy użyciu wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , a nie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="e682d-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="e682d-154">Przykładowa aplikacja jest wdrażana <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> w środowisku innym niż programowanie w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="e682d-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(I opcjonalnie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) są zwykle przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera wpisów tajnych](xref:security/app-secrets) lub w *appsettings.json* / *appSettings. { ENVIRONMENT}. JSON* — pliki).</span><span class="sxs-lookup"><span data-stu-id="e682d-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="e682d-156">Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane w systemach kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="e682d-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="e682d-157">Redis Cache dystrybuowane</span><span class="sxs-lookup"><span data-stu-id="e682d-157">Distributed Redis Cache</span></span>

<span data-ttu-id="e682d-158">[Redis](https://redis.io/) to magazyn danych typu "open source", który jest często używany jako rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="e682d-159">[Azure Redis Cache](https://azure.microsoft.com/services/cache/) można skonfigurować dla aplikacji ASP.NET Core hostowanej na platformie Azure i użyć Azure Redis Cache do lokalnego projektowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="e682d-160">Aplikacja konfiguruje implementację pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> wystąpienia ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ).</span><span class="sxs-lookup"><span data-stu-id="e682d-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="e682d-161">Aby uzyskać więcej informacji, zobacz [Pamięć podręczna Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span><span class="sxs-lookup"><span data-stu-id="e682d-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="e682d-162">[Ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/19542) w usłudze GitHub zawiera omówienie alternatywnych metod w lokalnej pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="e682d-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="e682d-163">Pamięć podręczna NCache rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-163">Distributed NCache Cache</span></span>

<span data-ttu-id="e682d-164">[NCache](https://github.com/Alachisoft/NCache) to rozproszona pamięć podręczna rozproszonej w pamięci, opracowana natywnie w .NET i .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e682d-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="e682d-165">NCache działa lokalnie i skonfigurowano jako klaster rozproszonej pamięci podręcznej dla aplikacji ASP.NET Core działającej na platformie Azure lub na innych platformach hostingowych.</span><span class="sxs-lookup"><span data-stu-id="e682d-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="e682d-166">Aby zainstalować i skonfigurować NCache na komputerze lokalnym, zobacz [przewodnik wprowadzenie dla systemu Windows (.NET i .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="e682d-166">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="e682d-167">Aby skonfigurować NCache:</span><span class="sxs-lookup"><span data-stu-id="e682d-167">To configure NCache:</span></span>

1. <span data-ttu-id="e682d-168">Zainstaluj pakiet [NuGet NCache Open Source](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="e682d-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="e682d-169">Skonfiguruj klaster pamięci podręcznej w programie [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="e682d-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="e682d-170">Dodaj następujący kod do pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e682d-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="e682d-171">Korzystanie z rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="e682d-171">Use the distributed cache</span></span>

<span data-ttu-id="e682d-172">Aby użyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, zażądaj wystąpienia z <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dowolnego konstruktora w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="e682d-173">Wystąpienie jest dostarczane przez [wstrzyknięcie zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e682d-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="e682d-174">Po rozpoczęciu przykładowej aplikacji <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest wstrzykiwana do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e682d-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="e682d-175">Bieżący czas jest buforowany przy użyciu <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [host ogólny: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="e682d-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="e682d-176">Przykładowa aplikacja wstrzykiwa do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` użycia na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="e682d-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="e682d-177">Za każdym razem, gdy strona indeksu zostanie załadowana, pamięć podręczna jest sprawdzana pod kątem pamięci podręcznej w programie `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="e682d-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="e682d-178">Jeśli czas w pamięci podręcznej nie upłynął, zostanie wyświetlony czas.</span><span class="sxs-lookup"><span data-stu-id="e682d-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="e682d-179">Jeśli od czasu ostatniego dostępu do pamięci podręcznej upłynie 20 sekund (podczas ostatniego ładowania strony), na stronie zostanie wyświetlona *godzina wygaśnięcia pamięci podręcznej*.</span><span class="sxs-lookup"><span data-stu-id="e682d-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="e682d-180">Natychmiast Aktualizuj buforowany czas do bieżącego czasu, wybierając przycisk **Resetuj buforowany czas** .</span><span class="sxs-lookup"><span data-stu-id="e682d-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="e682d-181">Przycisk wyzwala `OnPostResetCachedTime` metodę procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="e682d-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="e682d-182">Nie ma potrzeby używania pojedynczego lub zakresu okresu istnienia dla <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpień (co najmniej dla wbudowanych implementacji).</span><span class="sxs-lookup"><span data-stu-id="e682d-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="e682d-183">Możesz również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienie wszędzie tam, gdzie może być potrzebne, zamiast używać di, ale utworzenie wystąpienia w kodzie może sprawić, że kod będzie trudniejszy do przetestowania i narusza [zasadę jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="e682d-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="e682d-184">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="e682d-184">Recommendations</span></span>

<span data-ttu-id="e682d-185">Podczas decydowania, która implementacja <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest Najlepsza dla aplikacji, należy wziąć pod uwagę następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="e682d-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="e682d-186">Istniejąca infrastruktura</span><span class="sxs-lookup"><span data-stu-id="e682d-186">Existing infrastructure</span></span>
* <span data-ttu-id="e682d-187">Wymagania dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="e682d-187">Performance requirements</span></span>
* <span data-ttu-id="e682d-188">Koszt</span><span class="sxs-lookup"><span data-stu-id="e682d-188">Cost</span></span>
* <span data-ttu-id="e682d-189">Środowisko pracy zespołu</span><span class="sxs-lookup"><span data-stu-id="e682d-189">Team experience</span></span>

<span data-ttu-id="e682d-190">Rozwiązania pamięci podręcznej zwykle polegają na magazynie w pamięci, aby zapewnić szybkie pobieranie danych z pamięci podręcznej, ale pamięć jest ograniczonym zasobem i kosztowna.</span><span class="sxs-lookup"><span data-stu-id="e682d-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="e682d-191">Przechowuj tylko często używane dane w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="e682d-192">Ogólnie pamięć podręczna Redis zapewnia wyższą przepływność i mniejsze opóźnienia niż w przypadku pamięci podręcznej SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e682d-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="e682d-193">Jednak testy porównawcze są zwykle wymagane do określenia charakterystyki wydajności strategii buforowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="e682d-194">Gdy SQL Server jest używany jako magazyn zapasowy rozproszonej pamięci podręcznej, korzystanie z tej samej bazy danych dla pamięci podręcznej i zwykłego magazynu danych aplikacji może mieć negatywny wpływ na wydajność obu tych elementów.</span><span class="sxs-lookup"><span data-stu-id="e682d-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="e682d-195">Zalecamy używanie dedykowanego wystąpienia SQL Server dla magazynu zapasowego rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e682d-196">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e682d-196">Additional resources</span></span>

* [<span data-ttu-id="e682d-197">Redis Cache na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e682d-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="e682d-198">SQL Database na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e682d-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="e682d-199">[ASP.NET Core dostawcę IDistributedCache dla NCache w farmach serwerów sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="e682d-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e682d-200">Rozproszonej pamięci podręcznej to pamięć podręczna współdzielona przez wiele serwerów aplikacji, zazwyczaj obsługiwana jako usługa zewnętrzna do serwerów aplikacji, które mają do niego dostęp.</span><span class="sxs-lookup"><span data-stu-id="e682d-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="e682d-201">Rozproszona pamięć podręczna może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie gdy aplikacja jest hostowana przez usługę w chmurze lub farmę serwerów.</span><span class="sxs-lookup"><span data-stu-id="e682d-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="e682d-202">Rozproszonej pamięci podręcznej ma kilka zalet w porównaniu z innymi scenariuszami buforowania, w których buforowane dane są przechowywane na poszczególnych serwerach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="e682d-203">Dane przechowywane w pamięci podręcznej są dystrybuowane:</span><span class="sxs-lookup"><span data-stu-id="e682d-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="e682d-204">Jest *spójna* (spójna) między żądaniami do wielu serwerów.</span><span class="sxs-lookup"><span data-stu-id="e682d-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="e682d-205">Przeżyje ponowne uruchomienia serwera i wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="e682d-206">Nie używa pamięci lokalnej.</span><span class="sxs-lookup"><span data-stu-id="e682d-206">Doesn't use local memory.</span></span>

<span data-ttu-id="e682d-207">Konfiguracja rozproszonej pamięci podręcznej jest specyficzna dla implementacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="e682d-208">W tym artykule opisano sposób konfigurowania SQL Server i rozproszonej pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="e682d-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="e682d-209">Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w witrynie GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="e682d-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="e682d-210">Niezależnie od tego, która implementacja została wybrana, aplikacja współdziała z pamięcią podręczną przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="e682d-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="e682d-211">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e682d-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e682d-212">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="e682d-212">Prerequisites</span></span>

<span data-ttu-id="e682d-213">Aby użyć rozproszonej pamięci podręcznej SQL Server, należy odwołać się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub dodać odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="e682d-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="e682d-214">Aby użyć rozproszonej pamięci podręcznej Redis, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="e682d-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="e682d-215">Pakiet Redis nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu Redis oddzielnie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e682d-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="e682d-216">Aby użyć rozproszonej pamięci podręcznej NCache, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [NCache. Microsoft. Extensions. cache. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="e682d-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="e682d-217">Pakiet NCache nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu NCache oddzielnie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e682d-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="e682d-218">IDistributedCache, interfejs</span><span class="sxs-lookup"><span data-stu-id="e682d-218">IDistributedCache interface</span></span>

<span data-ttu-id="e682d-219"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Interfejs udostępnia następujące metody do manipulowania elementami w implementacji rozproszonej pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="e682d-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="e682d-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Akceptuje klucz ciągu i pobiera buforowany element jako `byte[]` tablicę, jeśli znajduje się w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="e682d-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Dodaje element (jako `byte[]` tablicowy) do pamięci podręcznej przy użyciu klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="e682d-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="e682d-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Odświeża element w pamięci podręcznej na podstawie jego klucza, resetowanie przekroczenia limitu czasu wygaśnięcia (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="e682d-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="e682d-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Usuwa element pamięci podręcznej na podstawie jego klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="e682d-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="e682d-224">Ustanów usługi rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="e682d-224">Establish distributed caching services</span></span>

<span data-ttu-id="e682d-225">Zarejestruj implementację programu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e682d-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e682d-226">Implementacje wdrożone w ramach platformy opisane w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="e682d-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="e682d-227">Pamięć podręczna pamięci rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="e682d-228">Pamięć podręczna rozproszonej SQL Server</span><span class="sxs-lookup"><span data-stu-id="e682d-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="e682d-229">Pamięć podręczna Redis rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="e682d-230">Pamięć podręczna NCache rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="e682d-231">Pamięć podręczna pamięci rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-231">Distributed Memory Cache</span></span>

<span data-ttu-id="e682d-232">Pamięć podręczna pamięci podręcznej ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) to implementacja udostępniona przez platformę <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , która przechowuje elementy w pamięci.</span><span class="sxs-lookup"><span data-stu-id="e682d-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="e682d-233">Pamięć podręczna pamięci rozproszonej nie jest rzeczywistą rozproszoną pamięcią podręczną.</span><span class="sxs-lookup"><span data-stu-id="e682d-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="e682d-234">Elementy buforowane są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="e682d-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="e682d-235">Pamięć podręczna pamięci rozproszonej jest przydatną implementacją:</span><span class="sxs-lookup"><span data-stu-id="e682d-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="e682d-236">W scenariuszach projektowania i testowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="e682d-237">Gdy jest używany pojedynczy serwer w środowisku produkcyjnym i użycie pamięci nie jest problemem.</span><span class="sxs-lookup"><span data-stu-id="e682d-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="e682d-238">Implementacja rozproszonej pamięci podręcznej pamięć podręczna magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="e682d-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="e682d-239">Pozwala to na wdrożenie prawdziwego rozwiązania do buforowania rozproszonego w przyszłości, jeśli będzie konieczne przeprowadzenie wielu węzłów lub odporności na uszkodzenia.</span><span class="sxs-lookup"><span data-stu-id="e682d-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="e682d-240">Przykładowa aplikacja korzysta z pamięci podręcznej pamięci rozproszonej, gdy aplikacja jest uruchamiana w środowisku programistycznym w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="e682d-241">Pamięć podręczna rozproszonej SQL Server</span><span class="sxs-lookup"><span data-stu-id="e682d-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="e682d-242">Implementacja rozproszonej pamięci podręcznej SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umożliwia rozproszonej pamięci podręcznej użycie bazy danych SQL Server jako magazynu zapasowego.</span><span class="sxs-lookup"><span data-stu-id="e682d-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="e682d-243">Aby utworzyć SQL Server tabelę elementów w pamięci podręcznej w wystąpieniu SQL Server, można użyć `sql-cache` Narzędzia.</span><span class="sxs-lookup"><span data-stu-id="e682d-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="e682d-244">Narzędzie tworzy tabelę z określoną nazwą i schematem.</span><span class="sxs-lookup"><span data-stu-id="e682d-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="e682d-245">Utwórz tabelę w SQL Server, uruchamiając `sql-cache create` polecenie.</span><span class="sxs-lookup"><span data-stu-id="e682d-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="e682d-246">Podaj wystąpienie SQL Server ( `Data Source` ), bazę danych ( `Initial Catalog` ), schemat (na przykład `dbo` ) i nazwę tabeli (na przykład `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="e682d-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="e682d-247">Komunikat jest rejestrowany w celu wskazania, że narzędzie zakończyło się pomyślnie:</span><span class="sxs-lookup"><span data-stu-id="e682d-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="e682d-248">Tabela utworzona przez `sql-cache` Narzędzie ma następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="e682d-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela pamięci podręcznej SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="e682d-250">Aplikacja powinna manipulować wartościami pamięci podręcznej przy użyciu wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , a nie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="e682d-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="e682d-251">Przykładowa aplikacja jest wdrażana <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> w środowisku innym niż programowanie w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="e682d-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(I opcjonalnie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) są zwykle przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera wpisów tajnych](xref:security/app-secrets) lub w *appsettings.json* / *appSettings. { ENVIRONMENT}. JSON* — pliki).</span><span class="sxs-lookup"><span data-stu-id="e682d-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="e682d-253">Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane w systemach kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="e682d-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="e682d-254">Redis Cache dystrybuowane</span><span class="sxs-lookup"><span data-stu-id="e682d-254">Distributed Redis Cache</span></span>

<span data-ttu-id="e682d-255">[Redis](https://redis.io/) to magazyn danych typu "open source", który jest często używany jako rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="e682d-256">Można używać Redis lokalnie i można skonfigurować [Azure Redis Cache](https://azure.microsoft.com/services/cache/) dla aplikacji ASP.NET Core hostowanej na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="e682d-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="e682d-257">Aplikacja konfiguruje implementację pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> wystąpienia ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) w środowisku innym niż programowanie w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="e682d-258">Aby zainstalować Redis na komputerze lokalnym:</span><span class="sxs-lookup"><span data-stu-id="e682d-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="e682d-259">Zainstaluj [pakiet Redis czekolady](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="e682d-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="e682d-260">Uruchom `redis-server` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="e682d-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="e682d-261">Pamięć podręczna NCache rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-261">Distributed NCache Cache</span></span>

<span data-ttu-id="e682d-262">[NCache](https://github.com/Alachisoft/NCache) to rozproszona pamięć podręczna rozproszonej w pamięci, opracowana natywnie w .NET i .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e682d-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="e682d-263">NCache działa lokalnie i skonfigurowano jako klaster rozproszonej pamięci podręcznej dla aplikacji ASP.NET Core działającej na platformie Azure lub na innych platformach hostingowych.</span><span class="sxs-lookup"><span data-stu-id="e682d-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="e682d-264">Aby zainstalować i skonfigurować NCache na komputerze lokalnym, zobacz [przewodnik wprowadzenie dla systemu Windows (.NET i .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="e682d-264">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="e682d-265">Aby skonfigurować NCache:</span><span class="sxs-lookup"><span data-stu-id="e682d-265">To configure NCache:</span></span>

1. <span data-ttu-id="e682d-266">Zainstaluj pakiet [NuGet NCache Open Source](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="e682d-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="e682d-267">Skonfiguruj klaster pamięci podręcznej w programie [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="e682d-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="e682d-268">Dodaj następujący kod do pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e682d-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="e682d-269">Korzystanie z rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="e682d-269">Use the distributed cache</span></span>

<span data-ttu-id="e682d-270">Aby użyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, zażądaj wystąpienia z <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dowolnego konstruktora w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="e682d-271">Wystąpienie jest dostarczane przez [wstrzyknięcie zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e682d-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="e682d-272">Po rozpoczęciu przykładowej aplikacji <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest wstrzykiwana do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e682d-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="e682d-273">Bieżący czas jest buforowany przy użyciu <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [host sieci Web: IApplicationLifetime Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="e682d-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="e682d-274">Przykładowa aplikacja wstrzykiwa do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` użycia na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="e682d-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="e682d-275">Za każdym razem, gdy strona indeksu zostanie załadowana, pamięć podręczna jest sprawdzana pod kątem pamięci podręcznej w programie `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="e682d-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="e682d-276">Jeśli czas w pamięci podręcznej nie upłynął, zostanie wyświetlony czas.</span><span class="sxs-lookup"><span data-stu-id="e682d-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="e682d-277">Jeśli od czasu ostatniego dostępu do pamięci podręcznej upłynie 20 sekund (podczas ostatniego ładowania strony), na stronie zostanie wyświetlona *godzina wygaśnięcia pamięci podręcznej*.</span><span class="sxs-lookup"><span data-stu-id="e682d-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="e682d-278">Natychmiast Aktualizuj buforowany czas do bieżącego czasu, wybierając przycisk **Resetuj buforowany czas** .</span><span class="sxs-lookup"><span data-stu-id="e682d-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="e682d-279">Przycisk wyzwala `OnPostResetCachedTime` metodę procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="e682d-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="e682d-280">Nie ma potrzeby używania pojedynczego lub zakresu okresu istnienia dla <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpień (co najmniej dla wbudowanych implementacji).</span><span class="sxs-lookup"><span data-stu-id="e682d-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="e682d-281">Możesz również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienie wszędzie tam, gdzie może być potrzebne, zamiast używać di, ale utworzenie wystąpienia w kodzie może sprawić, że kod będzie trudniejszy do przetestowania i narusza [zasadę jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="e682d-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="e682d-282">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="e682d-282">Recommendations</span></span>

<span data-ttu-id="e682d-283">Podczas decydowania, która implementacja <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest Najlepsza dla aplikacji, należy wziąć pod uwagę następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="e682d-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="e682d-284">Istniejąca infrastruktura</span><span class="sxs-lookup"><span data-stu-id="e682d-284">Existing infrastructure</span></span>
* <span data-ttu-id="e682d-285">Wymagania dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="e682d-285">Performance requirements</span></span>
* <span data-ttu-id="e682d-286">Koszt</span><span class="sxs-lookup"><span data-stu-id="e682d-286">Cost</span></span>
* <span data-ttu-id="e682d-287">Środowisko pracy zespołu</span><span class="sxs-lookup"><span data-stu-id="e682d-287">Team experience</span></span>

<span data-ttu-id="e682d-288">Rozwiązania pamięci podręcznej zwykle polegają na magazynie w pamięci, aby zapewnić szybkie pobieranie danych z pamięci podręcznej, ale pamięć jest ograniczonym zasobem i kosztowna.</span><span class="sxs-lookup"><span data-stu-id="e682d-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="e682d-289">Przechowuj tylko często używane dane w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="e682d-290">Ogólnie pamięć podręczna Redis zapewnia wyższą przepływność i mniejsze opóźnienia niż w przypadku pamięci podręcznej SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e682d-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="e682d-291">Jednak testy porównawcze są zwykle wymagane do określenia charakterystyki wydajności strategii buforowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="e682d-292">Gdy SQL Server jest używany jako magazyn zapasowy rozproszonej pamięci podręcznej, korzystanie z tej samej bazy danych dla pamięci podręcznej i zwykłego magazynu danych aplikacji może mieć negatywny wpływ na wydajność obu tych elementów.</span><span class="sxs-lookup"><span data-stu-id="e682d-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="e682d-293">Zalecamy używanie dedykowanego wystąpienia SQL Server dla magazynu zapasowego rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e682d-294">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e682d-294">Additional resources</span></span>

* [<span data-ttu-id="e682d-295">Redis Cache na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e682d-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="e682d-296">SQL Database na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e682d-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="e682d-297">[ASP.NET Core dostawcę IDistributedCache dla NCache w farmach serwerów sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="e682d-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e682d-298">Rozproszonej pamięci podręcznej to pamięć podręczna współdzielona przez wiele serwerów aplikacji, zazwyczaj obsługiwana jako usługa zewnętrzna do serwerów aplikacji, które mają do niego dostęp.</span><span class="sxs-lookup"><span data-stu-id="e682d-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="e682d-299">Rozproszona pamięć podręczna może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie gdy aplikacja jest hostowana przez usługę w chmurze lub farmę serwerów.</span><span class="sxs-lookup"><span data-stu-id="e682d-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="e682d-300">Rozproszonej pamięci podręcznej ma kilka zalet w porównaniu z innymi scenariuszami buforowania, w których buforowane dane są przechowywane na poszczególnych serwerach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="e682d-301">Dane przechowywane w pamięci podręcznej są dystrybuowane:</span><span class="sxs-lookup"><span data-stu-id="e682d-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="e682d-302">Jest *spójna* (spójna) między żądaniami do wielu serwerów.</span><span class="sxs-lookup"><span data-stu-id="e682d-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="e682d-303">Przeżyje ponowne uruchomienia serwera i wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="e682d-304">Nie używa pamięci lokalnej.</span><span class="sxs-lookup"><span data-stu-id="e682d-304">Doesn't use local memory.</span></span>

<span data-ttu-id="e682d-305">Konfiguracja rozproszonej pamięci podręcznej jest specyficzna dla implementacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="e682d-306">W tym artykule opisano sposób konfigurowania SQL Server i rozproszonej pamięci podręcznej Redis.</span><span class="sxs-lookup"><span data-stu-id="e682d-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="e682d-307">Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w witrynie GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="e682d-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="e682d-308">Niezależnie od tego, która implementacja została wybrana, aplikacja współdziała z pamięcią podręczną przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="e682d-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="e682d-309">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e682d-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e682d-310">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="e682d-310">Prerequisites</span></span>

<span data-ttu-id="e682d-311">Aby użyć rozproszonej pamięci podręcznej SQL Server, należy odwołać się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub dodać odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="e682d-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="e682d-312">Aby użyć rozproszonej pamięci podręcznej Redis, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="e682d-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="e682d-313">Pakiet Redis nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu Redis oddzielnie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e682d-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="e682d-314">Aby użyć rozproszonej pamięci podręcznej NCache, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [NCache. Microsoft. Extensions. cache. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="e682d-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="e682d-315">Pakiet NCache nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu NCache oddzielnie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e682d-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="e682d-316">IDistributedCache, interfejs</span><span class="sxs-lookup"><span data-stu-id="e682d-316">IDistributedCache interface</span></span>

<span data-ttu-id="e682d-317"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Interfejs udostępnia następujące metody do manipulowania elementami w implementacji rozproszonej pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="e682d-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="e682d-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Akceptuje klucz ciągu i pobiera buforowany element jako `byte[]` tablicę, jeśli znajduje się w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="e682d-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Dodaje element (jako `byte[]` tablicowy) do pamięci podręcznej przy użyciu klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="e682d-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="e682d-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Odświeża element w pamięci podręcznej na podstawie jego klucza, resetowanie przekroczenia limitu czasu wygaśnięcia (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="e682d-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="e682d-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Usuwa element pamięci podręcznej na podstawie jego klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="e682d-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="e682d-322">Ustanów usługi rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="e682d-322">Establish distributed caching services</span></span>

<span data-ttu-id="e682d-323">Zarejestruj implementację programu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e682d-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e682d-324">Implementacje wdrożone w ramach platformy opisane w tym temacie:</span><span class="sxs-lookup"><span data-stu-id="e682d-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="e682d-325">Pamięć podręczna pamięci rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="e682d-326">Pamięć podręczna rozproszonej SQL Server</span><span class="sxs-lookup"><span data-stu-id="e682d-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="e682d-327">Pamięć podręczna Redis rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="e682d-328">Pamięć podręczna NCache rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="e682d-329">Pamięć podręczna pamięci rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-329">Distributed Memory Cache</span></span>

<span data-ttu-id="e682d-330">Pamięć podręczna pamięci podręcznej ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) to implementacja udostępniona przez platformę <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , która przechowuje elementy w pamięci.</span><span class="sxs-lookup"><span data-stu-id="e682d-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="e682d-331">Pamięć podręczna pamięci rozproszonej nie jest rzeczywistą rozproszoną pamięcią podręczną.</span><span class="sxs-lookup"><span data-stu-id="e682d-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="e682d-332">Elementy buforowane są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="e682d-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="e682d-333">Pamięć podręczna pamięci rozproszonej jest przydatną implementacją:</span><span class="sxs-lookup"><span data-stu-id="e682d-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="e682d-334">W scenariuszach projektowania i testowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="e682d-335">Gdy jest używany pojedynczy serwer w środowisku produkcyjnym i użycie pamięci nie jest problemem.</span><span class="sxs-lookup"><span data-stu-id="e682d-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="e682d-336">Implementacja rozproszonej pamięci podręcznej pamięć podręczna magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="e682d-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="e682d-337">Pozwala to na wdrożenie prawdziwego rozwiązania do buforowania rozproszonego w przyszłości, jeśli będzie konieczne przeprowadzenie wielu węzłów lub odporności na uszkodzenia.</span><span class="sxs-lookup"><span data-stu-id="e682d-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="e682d-338">Przykładowa aplikacja korzysta z pamięci podręcznej pamięci rozproszonej, gdy aplikacja jest uruchamiana w środowisku programistycznym w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="e682d-339">Pamięć podręczna rozproszonej SQL Server</span><span class="sxs-lookup"><span data-stu-id="e682d-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="e682d-340">Implementacja rozproszonej pamięci podręcznej SQL Server ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umożliwia rozproszonej pamięci podręcznej użycie bazy danych SQL Server jako magazynu zapasowego.</span><span class="sxs-lookup"><span data-stu-id="e682d-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="e682d-341">Aby utworzyć SQL Server tabelę elementów w pamięci podręcznej w wystąpieniu SQL Server, można użyć `sql-cache` Narzędzia.</span><span class="sxs-lookup"><span data-stu-id="e682d-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="e682d-342">Narzędzie tworzy tabelę z określoną nazwą i schematem.</span><span class="sxs-lookup"><span data-stu-id="e682d-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="e682d-343">Utwórz tabelę w SQL Server, uruchamiając `sql-cache create` polecenie.</span><span class="sxs-lookup"><span data-stu-id="e682d-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="e682d-344">Podaj wystąpienie SQL Server ( `Data Source` ), bazę danych ( `Initial Catalog` ), schemat (na przykład `dbo` ) i nazwę tabeli (na przykład `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="e682d-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="e682d-345">Komunikat jest rejestrowany w celu wskazania, że narzędzie zakończyło się pomyślnie:</span><span class="sxs-lookup"><span data-stu-id="e682d-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="e682d-346">Tabela utworzona przez `sql-cache` Narzędzie ma następujący schemat:</span><span class="sxs-lookup"><span data-stu-id="e682d-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela pamięci podręcznej SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="e682d-348">Aplikacja powinna manipulować wartościami pamięci podręcznej przy użyciu wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , a nie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="e682d-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="e682d-349">Przykładowa aplikacja jest wdrażana <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> w środowisku innym niż programowanie w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e682d-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="e682d-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(I opcjonalnie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) są zwykle przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera wpisów tajnych](xref:security/app-secrets) lub w *appsettings.json* / *appSettings. { ENVIRONMENT}. JSON* — pliki).</span><span class="sxs-lookup"><span data-stu-id="e682d-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="e682d-351">Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane w systemach kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="e682d-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="e682d-352">Redis Cache dystrybuowane</span><span class="sxs-lookup"><span data-stu-id="e682d-352">Distributed Redis Cache</span></span>

<span data-ttu-id="e682d-353">[Redis](https://redis.io/) to magazyn danych typu "open source", który jest często używany jako rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="e682d-354">Można używać Redis lokalnie i można skonfigurować [Azure Redis Cache](https://azure.microsoft.com/services/cache/) dla aplikacji ASP.NET Core hostowanej na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="e682d-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="e682d-355">Aplikacja konfiguruje implementację pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.Redis.RedisCache> wystąpienia ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):</span><span class="sxs-lookup"><span data-stu-id="e682d-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="e682d-356">Aby zainstalować Redis na komputerze lokalnym:</span><span class="sxs-lookup"><span data-stu-id="e682d-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="e682d-357">Zainstaluj [pakiet Redis czekolady](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="e682d-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="e682d-358">Uruchom `redis-server` polecenie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="e682d-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="e682d-359">Pamięć podręczna NCache rozproszonej</span><span class="sxs-lookup"><span data-stu-id="e682d-359">Distributed NCache Cache</span></span>

<span data-ttu-id="e682d-360">[NCache](https://github.com/Alachisoft/NCache) to rozproszona pamięć podręczna rozproszonej w pamięci, opracowana natywnie w .NET i .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e682d-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="e682d-361">NCache działa lokalnie i skonfigurowano jako klaster rozproszonej pamięci podręcznej dla aplikacji ASP.NET Core działającej na platformie Azure lub na innych platformach hostingowych.</span><span class="sxs-lookup"><span data-stu-id="e682d-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="e682d-362">Aby zainstalować i skonfigurować NCache na komputerze lokalnym, zobacz [przewodnik wprowadzenie dla systemu Windows (.NET i .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="e682d-362">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="e682d-363">Aby skonfigurować NCache:</span><span class="sxs-lookup"><span data-stu-id="e682d-363">To configure NCache:</span></span>

1. <span data-ttu-id="e682d-364">Zainstaluj pakiet [NuGet NCache Open Source](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="e682d-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="e682d-365">Skonfiguruj klaster pamięci podręcznej w programie [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="e682d-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="e682d-366">Dodaj następujący kod do pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e682d-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="e682d-367">Korzystanie z rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="e682d-367">Use the distributed cache</span></span>

<span data-ttu-id="e682d-368">Aby użyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, zażądaj wystąpienia z <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> dowolnego konstruktora w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e682d-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="e682d-369">Wystąpienie jest dostarczane przez [wstrzyknięcie zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e682d-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="e682d-370">Po rozpoczęciu przykładowej aplikacji <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest wstrzykiwana do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e682d-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="e682d-371">Bieżący czas jest buforowany przy użyciu <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [host sieci Web: IApplicationLifetime Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="e682d-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="e682d-372">Przykładowa aplikacja wstrzykiwa do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` użycia na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="e682d-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="e682d-373">Za każdym razem, gdy strona indeksu zostanie załadowana, pamięć podręczna jest sprawdzana pod kątem pamięci podręcznej w programie `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="e682d-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="e682d-374">Jeśli czas w pamięci podręcznej nie upłynął, zostanie wyświetlony czas.</span><span class="sxs-lookup"><span data-stu-id="e682d-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="e682d-375">Jeśli od czasu ostatniego dostępu do pamięci podręcznej upłynie 20 sekund (podczas ostatniego ładowania strony), na stronie zostanie wyświetlona *godzina wygaśnięcia pamięci podręcznej*.</span><span class="sxs-lookup"><span data-stu-id="e682d-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="e682d-376">Natychmiast Aktualizuj buforowany czas do bieżącego czasu, wybierając przycisk **Resetuj buforowany czas** .</span><span class="sxs-lookup"><span data-stu-id="e682d-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="e682d-377">Przycisk wyzwala `OnPostResetCachedTime` metodę procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="e682d-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="e682d-378">Nie ma potrzeby używania pojedynczego lub zakresu okresu istnienia dla <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpień (co najmniej dla wbudowanych implementacji).</span><span class="sxs-lookup"><span data-stu-id="e682d-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="e682d-379">Możesz również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienie wszędzie tam, gdzie może być potrzebne, zamiast używać di, ale utworzenie wystąpienia w kodzie może sprawić, że kod będzie trudniejszy do przetestowania i narusza [zasadę jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="e682d-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="e682d-380">Zalecenia</span><span class="sxs-lookup"><span data-stu-id="e682d-380">Recommendations</span></span>

<span data-ttu-id="e682d-381">Podczas decydowania, która implementacja <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest Najlepsza dla aplikacji, należy wziąć pod uwagę następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="e682d-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="e682d-382">Istniejąca infrastruktura</span><span class="sxs-lookup"><span data-stu-id="e682d-382">Existing infrastructure</span></span>
* <span data-ttu-id="e682d-383">Wymagania dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="e682d-383">Performance requirements</span></span>
* <span data-ttu-id="e682d-384">Koszt</span><span class="sxs-lookup"><span data-stu-id="e682d-384">Cost</span></span>
* <span data-ttu-id="e682d-385">Środowisko pracy zespołu</span><span class="sxs-lookup"><span data-stu-id="e682d-385">Team experience</span></span>

<span data-ttu-id="e682d-386">Rozwiązania pamięci podręcznej zwykle polegają na magazynie w pamięci, aby zapewnić szybkie pobieranie danych z pamięci podręcznej, ale pamięć jest ograniczonym zasobem i kosztowna.</span><span class="sxs-lookup"><span data-stu-id="e682d-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="e682d-387">Przechowuj tylko często używane dane w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="e682d-388">Ogólnie pamięć podręczna Redis zapewnia wyższą przepływność i mniejsze opóźnienia niż w przypadku pamięci podręcznej SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e682d-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="e682d-389">Jednak testy porównawcze są zwykle wymagane do określenia charakterystyki wydajności strategii buforowania.</span><span class="sxs-lookup"><span data-stu-id="e682d-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="e682d-390">Gdy SQL Server jest używany jako magazyn zapasowy rozproszonej pamięci podręcznej, korzystanie z tej samej bazy danych dla pamięci podręcznej i zwykłego magazynu danych aplikacji może mieć negatywny wpływ na wydajność obu tych elementów.</span><span class="sxs-lookup"><span data-stu-id="e682d-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="e682d-391">Zalecamy używanie dedykowanego wystąpienia SQL Server dla magazynu zapasowego rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="e682d-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e682d-392">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e682d-392">Additional resources</span></span>

* [<span data-ttu-id="e682d-393">Redis Cache na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e682d-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="e682d-394">SQL Database na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e682d-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="e682d-395">[ASP.NET Core dostawcę IDistributedCache dla NCache w farmach serwerów sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="e682d-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
