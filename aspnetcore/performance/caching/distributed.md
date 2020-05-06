---
title: Rozproszone buforowanie w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak za pomocą rozproszonej pamięci podręcznej ASP.NET Core poprawić wydajność i skalowalność aplikacji, szczególnie w środowisku chmury lub farmy serwerów.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 206ff55aa530cd06c162e49f400b436e9fb9f07a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775300"
---
# <a name="distributed-caching-in-aspnet-core"></a>Rozproszone buforowanie w ASP.NET Core

Autorzy [Mohsin Nasir](https://github.com/mohsinnasir) i [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Rozproszonej pamięci podręcznej to pamięć podręczna współdzielona przez wiele serwerów aplikacji, zazwyczaj obsługiwana jako usługa zewnętrzna do serwerów aplikacji, które mają do niego dostęp. Rozproszona pamięć podręczna może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie gdy aplikacja jest hostowana przez usługę w chmurze lub farmę serwerów.

Rozproszonej pamięci podręcznej ma kilka zalet w porównaniu z innymi scenariuszami buforowania, w których buforowane dane są przechowywane na poszczególnych serwerach aplikacji.

Dane przechowywane w pamięci podręcznej są dystrybuowane:

* Jest *spójna* (spójna) między żądaniami do wielu serwerów.
* Przeżyje ponowne uruchomienia serwera i wdrożenia aplikacji.
* Nie używa pamięci lokalnej.

Konfiguracja rozproszonej pamięci podręcznej jest specyficzna dla implementacji. W tym artykule opisano sposób konfigurowania SQL Server i rozproszonej pamięci podręcznej Redis. Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w witrynie GitHub](https://github.com/Alachisoft/NCache)). Niezależnie od tego, która implementacja została wybrana, aplikacja współdziała z pamięcią podręczną przy <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> użyciu interfejsu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć rozproszonej pamięci podręcznej SQL Server, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Aby użyć rozproszonej pamięci podręcznej Redis, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .

Aby użyć rozproszonej pamięci podręcznej NCache, Dodaj odwołanie do pakietu do pakietu [NCache. Microsoft. Extensions. cache. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .

## <a name="idistributedcache-interface"></a>IDistributedCache, interfejs

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Interfejs udostępnia następujące metody do manipulowania elementami w implementacji rozproszonej pamięci podręcznej:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Akceptuje klucz ciągu i pobiera buforowany element jako `byte[]` tablicę, jeśli znajduje się w pamięci podręcznej.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Dodaje element (jako `byte[]` tablicowy) do pamięci podręcznej przy użyciu klucza ciągu.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Odświeża element w pamięci podręcznej na podstawie jego klucza, resetowanie przekroczenia limitu czasu wygaśnięcia (jeśli istnieje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Usuwa element pamięci podręcznej na podstawie jego klucza ciągu.

## <a name="establish-distributed-caching-services"></a>Ustanów usługi rozproszonej pamięci podręcznej

Zarejestruj implementację programu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w `Startup.ConfigureServices`programie. Implementacje wdrożone w ramach platformy opisane w tym temacie:

* [Pamięć podręczna pamięci rozproszonej](#distributed-memory-cache)
* [Pamięć podręczna rozproszonej SQL Server](#distributed-sql-server-cache)
* [Pamięć podręczna Redis rozproszonej](#distributed-redis-cache)
* [Pamięć podręczna NCache rozproszonej](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Pamięć podręczna pamięci rozproszonej

Pamięć podręczna pamięci podręcznej (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) to implementacja udostępniona przez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> platformę, która przechowuje elementy w pamięci. Pamięć podręczna pamięci rozproszonej nie jest rzeczywistą rozproszoną pamięcią podręczną. Elementy buforowane są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.

Pamięć podręczna pamięci rozproszonej jest przydatną implementacją:

* W scenariuszach projektowania i testowania.
* Gdy jest używany pojedynczy serwer w środowisku produkcyjnym i użycie pamięci nie jest problemem. Implementacja rozproszonej pamięci podręcznej pamięć podręczna magazynu danych. Pozwala to na wdrożenie prawdziwego rozwiązania do buforowania rozproszonego w przyszłości, jeśli będzie konieczne przeprowadzenie wielu węzłów lub odporności na uszkodzenia.

Przykładowa aplikacja korzysta z pamięci podręcznej pamięci rozproszonej, gdy aplikacja jest uruchamiana w środowisku `Startup.ConfigureServices`programistycznym w programie:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Pamięć podręczna rozproszonej SQL Server

Implementacja rozproszonej pamięci podręcznej SQL Server (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) umożliwia rozproszonej pamięci podręcznej użycie bazy danych SQL Server jako magazynu zapasowego. Aby utworzyć SQL Server tabelę elementów w pamięci podręcznej w wystąpieniu SQL Server, można użyć `sql-cache` narzędzia. Narzędzie tworzy tabelę z określoną nazwą i schematem.

Utwórz tabelę w SQL Server, uruchamiając `sql-cache create` polecenie. Podaj wystąpienie`Data Source`SQL Server (), bazę danych (`Initial Catalog`), schemat (na przykład `dbo`) i nazwę tabeli (na przykład `TestCache`):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Komunikat jest rejestrowany w celu wskazania, że narzędzie zakończyło się pomyślnie:

```console
Table and index were created successfully.
```

Tabela utworzona przez `sql-cache` narzędzie ma następujący schemat:

![Tabela pamięci podręcznej SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikacja powinna manipulować wartościami pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>wystąpienia, a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>nie.

Przykładowa aplikacja jest <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> wdrażana w środowisku innym niż programowanie `Startup.ConfigureServices`w programie:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> ( <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> I opcjonalnie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) są zwykle przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera wpisów tajnych](xref:security/app-secrets) lub w pliku AppSettings *. JSON*/*. { ENVIRONMENT}. JSON* — pliki). Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane w systemach kontroli źródła.

### <a name="distributed-redis-cache"></a>Redis Cache dystrybuowane

[Redis](https://redis.io/) to magazyn danych typu "open source", który jest często używany jako rozproszonej pamięci podręcznej. Można używać Redis lokalnie i można skonfigurować [Azure Redis Cache](https://azure.microsoft.com/services/cache/) dla aplikacji ASP.NET Core hostowanej na platformie Azure.

Aplikacja konfiguruje implementację pamięci podręcznej <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> przy<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>użyciu wystąpienia () w środowisku innym niż `Startup.ConfigureServices`programowanie w programie:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Aby zainstalować Redis na komputerze lokalnym:

1. Zainstaluj [pakiet Redis czekolady](https://chocolatey.org/packages/redis-64/).
1. Uruchom `redis-server` polecenie w wierszu polecenia.

### <a name="distributed-ncache-cache"></a>Pamięć podręczna NCache rozproszonej

[NCache](https://github.com/Alachisoft/NCache) to rozproszona pamięć podręczna rozproszonej w pamięci, opracowana natywnie w .NET i .NET Core. NCache działa lokalnie i skonfigurowano jako klaster rozproszonej pamięci podręcznej dla aplikacji ASP.NET Core działającej na platformie Azure lub na innych platformach hostingowych.

Aby zainstalować i skonfigurować NCache na komputerze lokalnym, zobacz [Przewodnik po NCache wprowadzenie dla systemu Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Aby skonfigurować NCache:

1. Zainstaluj pakiet [NuGet NCache Open Source](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Skonfiguruj klaster pamięci podręcznej w programie [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Dodaj następujący kod do pliku `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Korzystanie z rozproszonej pamięci podręcznej

Aby użyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, zażądaj wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z dowolnego konstruktora w aplikacji. Wystąpienie jest dostarczane przez [wstrzyknięcie zależności (di)](xref:fundamentals/dependency-injection).

Po rozpoczęciu przykładowej <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> aplikacji jest wstrzykiwana `Startup.Configure`do. Bieżący czas jest buforowany przy użyciu <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [host ogólny: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Przykładowa aplikacja wstrzykiwa <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do użycia `IndexModel` na stronie indeksu.

Za każdym razem, gdy strona indeksu zostanie załadowana, pamięć podręczna jest sprawdzana `OnGetAsync`pod kątem pamięci podręcznej w programie. Jeśli czas w pamięci podręcznej nie upłynął, zostanie wyświetlony czas. Jeśli od czasu ostatniego dostępu do pamięci podręcznej upłynie 20 sekund (podczas ostatniego ładowania strony), na stronie zostanie wyświetlona *godzina wygaśnięcia pamięci podręcznej*.

Natychmiast Aktualizuj buforowany czas do bieżącego czasu, wybierając przycisk **Resetuj buforowany czas** . Przycisk wyzwala metodę `OnPostResetCachedTime` procedury obsługi.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Nie ma potrzeby używania pojedynczego lub zakresu okresu istnienia dla <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpień (co najmniej dla wbudowanych implementacji).
>
> Możesz również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienie wszędzie tam, gdzie może być potrzebne, zamiast używać di, ale utworzenie wystąpienia w kodzie może sprawić, że kod będzie trudniejszy do przetestowania i narusza [zasadę jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Zalecenia

Podczas decydowania, która implementacja <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest Najlepsza dla aplikacji, należy wziąć pod uwagę następujące kwestie:

* Istniejąca infrastruktura
* Wymagania dotyczące wydajności
* Koszty
* Środowisko pracy zespołu

Rozwiązania pamięci podręcznej zwykle polegają na magazynie w pamięci, aby zapewnić szybkie pobieranie danych z pamięci podręcznej, ale pamięć jest ograniczonym zasobem i kosztowna. Przechowuj tylko często używane dane w pamięci podręcznej.

Ogólnie pamięć podręczna Redis zapewnia wyższą przepływność i mniejsze opóźnienia niż w przypadku pamięci podręcznej SQL Server. Jednak testy porównawcze są zwykle wymagane do określenia charakterystyki wydajności strategii buforowania.

Gdy SQL Server jest używany jako magazyn zapasowy rozproszonej pamięci podręcznej, korzystanie z tej samej bazy danych dla pamięci podręcznej i zwykłego magazynu danych aplikacji może mieć negatywny wpływ na wydajność obu tych elementów. Zalecamy używanie dedykowanego wystąpienia SQL Server dla magazynu zapasowego rozproszonej pamięci podręcznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Redis Cache na platformie Azure](/azure/azure-cache-for-redis/)
* [SQL Database na platformie Azure](/azure/sql-database/)
* [ASP.NET Core dostawcę IDistributedCache dla NCache w farmach serwerów sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Rozproszonej pamięci podręcznej to pamięć podręczna współdzielona przez wiele serwerów aplikacji, zazwyczaj obsługiwana jako usługa zewnętrzna do serwerów aplikacji, które mają do niego dostęp. Rozproszona pamięć podręczna może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie gdy aplikacja jest hostowana przez usługę w chmurze lub farmę serwerów.

Rozproszonej pamięci podręcznej ma kilka zalet w porównaniu z innymi scenariuszami buforowania, w których buforowane dane są przechowywane na poszczególnych serwerach aplikacji.

Dane przechowywane w pamięci podręcznej są dystrybuowane:

* Jest *spójna* (spójna) między żądaniami do wielu serwerów.
* Przeżyje ponowne uruchomienia serwera i wdrożenia aplikacji.
* Nie używa pamięci lokalnej.

Konfiguracja rozproszonej pamięci podręcznej jest specyficzna dla implementacji. W tym artykule opisano sposób konfigurowania SQL Server i rozproszonej pamięci podręcznej Redis. Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w witrynie GitHub](https://github.com/Alachisoft/NCache)). Niezależnie od tego, która implementacja została wybrana, aplikacja współdziała z pamięcią podręczną przy <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> użyciu interfejsu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć rozproszonej pamięci podręcznej SQL Server, należy odwołać się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub dodać odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Aby użyć rozproszonej pamięci podręcznej Redis, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) . Pakiet Redis nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu Redis oddzielnie w pliku projektu.

Aby użyć rozproszonej pamięci podręcznej NCache, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [NCache. Microsoft. Extensions. cache. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . Pakiet NCache nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu NCache oddzielnie w pliku projektu.

## <a name="idistributedcache-interface"></a>IDistributedCache, interfejs

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Interfejs udostępnia następujące metody do manipulowania elementami w implementacji rozproszonej pamięci podręcznej:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Akceptuje klucz ciągu i pobiera buforowany element jako `byte[]` tablicę, jeśli znajduje się w pamięci podręcznej.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Dodaje element (jako `byte[]` tablicowy) do pamięci podręcznej przy użyciu klucza ciągu.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Odświeża element w pamięci podręcznej na podstawie jego klucza, resetowanie przekroczenia limitu czasu wygaśnięcia (jeśli istnieje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Usuwa element pamięci podręcznej na podstawie jego klucza ciągu.

## <a name="establish-distributed-caching-services"></a>Ustanów usługi rozproszonej pamięci podręcznej

Zarejestruj implementację programu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w `Startup.ConfigureServices`programie. Implementacje wdrożone w ramach platformy opisane w tym temacie:

* [Pamięć podręczna pamięci rozproszonej](#distributed-memory-cache)
* [Pamięć podręczna rozproszonej SQL Server](#distributed-sql-server-cache)
* [Pamięć podręczna Redis rozproszonej](#distributed-redis-cache)
* [Pamięć podręczna NCache rozproszonej](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Pamięć podręczna pamięci rozproszonej

Pamięć podręczna pamięci podręcznej (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) to implementacja udostępniona przez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> platformę, która przechowuje elementy w pamięci. Pamięć podręczna pamięci rozproszonej nie jest rzeczywistą rozproszoną pamięcią podręczną. Elementy buforowane są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.

Pamięć podręczna pamięci rozproszonej jest przydatną implementacją:

* W scenariuszach projektowania i testowania.
* Gdy jest używany pojedynczy serwer w środowisku produkcyjnym i użycie pamięci nie jest problemem. Implementacja rozproszonej pamięci podręcznej pamięć podręczna magazynu danych. Pozwala to na wdrożenie prawdziwego rozwiązania do buforowania rozproszonego w przyszłości, jeśli będzie konieczne przeprowadzenie wielu węzłów lub odporności na uszkodzenia.

Przykładowa aplikacja korzysta z pamięci podręcznej pamięci rozproszonej, gdy aplikacja jest uruchamiana w środowisku `Startup.ConfigureServices`programistycznym w programie:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Pamięć podręczna rozproszonej SQL Server

Implementacja rozproszonej pamięci podręcznej SQL Server (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) umożliwia rozproszonej pamięci podręcznej użycie bazy danych SQL Server jako magazynu zapasowego. Aby utworzyć SQL Server tabelę elementów w pamięci podręcznej w wystąpieniu SQL Server, można użyć `sql-cache` narzędzia. Narzędzie tworzy tabelę z określoną nazwą i schematem.

Utwórz tabelę w SQL Server, uruchamiając `sql-cache create` polecenie. Podaj wystąpienie`Data Source`SQL Server (), bazę danych (`Initial Catalog`), schemat (na przykład `dbo`) i nazwę tabeli (na przykład `TestCache`):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Komunikat jest rejestrowany w celu wskazania, że narzędzie zakończyło się pomyślnie:

```console
Table and index were created successfully.
```

Tabela utworzona przez `sql-cache` narzędzie ma następujący schemat:

![Tabela pamięci podręcznej SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikacja powinna manipulować wartościami pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>wystąpienia, a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>nie.

Przykładowa aplikacja jest <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> wdrażana w środowisku innym niż programowanie `Startup.ConfigureServices`w programie:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> ( <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> I opcjonalnie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) są zwykle przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera wpisów tajnych](xref:security/app-secrets) lub w pliku AppSettings *. JSON*/*. { ENVIRONMENT}. JSON* — pliki). Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane w systemach kontroli źródła.

### <a name="distributed-redis-cache"></a>Redis Cache dystrybuowane

[Redis](https://redis.io/) to magazyn danych typu "open source", który jest często używany jako rozproszonej pamięci podręcznej. Można używać Redis lokalnie i można skonfigurować [Azure Redis Cache](https://azure.microsoft.com/services/cache/) dla aplikacji ASP.NET Core hostowanej na platformie Azure.

Aplikacja konfiguruje implementację pamięci podręcznej <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> przy<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>użyciu wystąpienia () w środowisku innym niż `Startup.ConfigureServices`programowanie w programie:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Aby zainstalować Redis na komputerze lokalnym:

1. Zainstaluj [pakiet Redis czekolady](https://chocolatey.org/packages/redis-64/).
1. Uruchom `redis-server` polecenie w wierszu polecenia.

### <a name="distributed-ncache-cache"></a>Pamięć podręczna NCache rozproszonej

[NCache](https://github.com/Alachisoft/NCache) to rozproszona pamięć podręczna rozproszonej w pamięci, opracowana natywnie w .NET i .NET Core. NCache działa lokalnie i skonfigurowano jako klaster rozproszonej pamięci podręcznej dla aplikacji ASP.NET Core działającej na platformie Azure lub na innych platformach hostingowych.

Aby zainstalować i skonfigurować NCache na komputerze lokalnym, zobacz [Przewodnik po NCache wprowadzenie dla systemu Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Aby skonfigurować NCache:

1. Zainstaluj pakiet [NuGet NCache Open Source](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Skonfiguruj klaster pamięci podręcznej w programie [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Dodaj następujący kod do pliku `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Korzystanie z rozproszonej pamięci podręcznej

Aby użyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, zażądaj wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z dowolnego konstruktora w aplikacji. Wystąpienie jest dostarczane przez [wstrzyknięcie zależności (di)](xref:fundamentals/dependency-injection).

Po rozpoczęciu przykładowej <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> aplikacji jest wstrzykiwana `Startup.Configure`do. Bieżący czas jest buforowany przy użyciu <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [host sieci Web: IApplicationLifetime Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Przykładowa aplikacja wstrzykiwa <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do użycia `IndexModel` na stronie indeksu.

Za każdym razem, gdy strona indeksu zostanie załadowana, pamięć podręczna jest sprawdzana `OnGetAsync`pod kątem pamięci podręcznej w programie. Jeśli czas w pamięci podręcznej nie upłynął, zostanie wyświetlony czas. Jeśli od czasu ostatniego dostępu do pamięci podręcznej upłynie 20 sekund (podczas ostatniego ładowania strony), na stronie zostanie wyświetlona *godzina wygaśnięcia pamięci podręcznej*.

Natychmiast Aktualizuj buforowany czas do bieżącego czasu, wybierając przycisk **Resetuj buforowany czas** . Przycisk wyzwala metodę `OnPostResetCachedTime` procedury obsługi.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Nie ma potrzeby używania pojedynczego lub zakresu okresu istnienia dla <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpień (co najmniej dla wbudowanych implementacji).
>
> Możesz również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienie wszędzie tam, gdzie może być potrzebne, zamiast używać di, ale utworzenie wystąpienia w kodzie może sprawić, że kod będzie trudniejszy do przetestowania i narusza [zasadę jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Zalecenia

Podczas decydowania, która implementacja <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest Najlepsza dla aplikacji, należy wziąć pod uwagę następujące kwestie:

* Istniejąca infrastruktura
* Wymagania dotyczące wydajności
* Koszty
* Środowisko pracy zespołu

Rozwiązania pamięci podręcznej zwykle polegają na magazynie w pamięci, aby zapewnić szybkie pobieranie danych z pamięci podręcznej, ale pamięć jest ograniczonym zasobem i kosztowna. Przechowuj tylko często używane dane w pamięci podręcznej.

Ogólnie pamięć podręczna Redis zapewnia wyższą przepływność i mniejsze opóźnienia niż w przypadku pamięci podręcznej SQL Server. Jednak testy porównawcze są zwykle wymagane do określenia charakterystyki wydajności strategii buforowania.

Gdy SQL Server jest używany jako magazyn zapasowy rozproszonej pamięci podręcznej, korzystanie z tej samej bazy danych dla pamięci podręcznej i zwykłego magazynu danych aplikacji może mieć negatywny wpływ na wydajność obu tych elementów. Zalecamy używanie dedykowanego wystąpienia SQL Server dla magazynu zapasowego rozproszonej pamięci podręcznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Redis Cache na platformie Azure](/azure/azure-cache-for-redis/)
* [SQL Database na platformie Azure](/azure/sql-database/)
* [ASP.NET Core dostawcę IDistributedCache dla NCache w farmach serwerów sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Rozproszonej pamięci podręcznej to pamięć podręczna współdzielona przez wiele serwerów aplikacji, zazwyczaj obsługiwana jako usługa zewnętrzna do serwerów aplikacji, które mają do niego dostęp. Rozproszona pamięć podręczna może zwiększyć wydajność i skalowalność aplikacji ASP.NET Core, szczególnie gdy aplikacja jest hostowana przez usługę w chmurze lub farmę serwerów.

Rozproszonej pamięci podręcznej ma kilka zalet w porównaniu z innymi scenariuszami buforowania, w których buforowane dane są przechowywane na poszczególnych serwerach aplikacji.

Dane przechowywane w pamięci podręcznej są dystrybuowane:

* Jest *spójna* (spójna) między żądaniami do wielu serwerów.
* Przeżyje ponowne uruchomienia serwera i wdrożenia aplikacji.
* Nie używa pamięci lokalnej.

Konfiguracja rozproszonej pamięci podręcznej jest specyficzna dla implementacji. W tym artykule opisano sposób konfigurowania SQL Server i rozproszonej pamięci podręcznej Redis. Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w witrynie GitHub](https://github.com/Alachisoft/NCache)). Niezależnie od tego, która implementacja została wybrana, aplikacja współdziała z pamięcią podręczną przy <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> użyciu interfejsu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć rozproszonej pamięci podręcznej SQL Server, należy odwołać się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub dodać odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Aby użyć rozproszonej pamięci podręcznej Redis, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. cache. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) . Pakiet Redis nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu Redis oddzielnie w pliku projektu.

Aby użyć rozproszonej pamięci podręcznej NCache, odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , a następnie Dodaj odwołanie do pakietu do pakietu [NCache. Microsoft. Extensions. cache. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . Pakiet NCache nie znajduje się w `Microsoft.AspNetCore.App` pakiecie, dlatego należy odwołać się do pakietu NCache oddzielnie w pliku projektu.

## <a name="idistributedcache-interface"></a>IDistributedCache, interfejs

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Interfejs udostępnia następujące metody do manipulowania elementami w implementacji rozproszonej pamięci podręcznej:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Akceptuje klucz ciągu i pobiera buforowany element jako `byte[]` tablicę, jeśli znajduje się w pamięci podręcznej.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Dodaje element (jako `byte[]` tablicowy) do pamięci podręcznej przy użyciu klucza ciągu.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Odświeża element w pamięci podręcznej na podstawie jego klucza, resetowanie przekroczenia limitu czasu wygaśnięcia (jeśli istnieje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Usuwa element pamięci podręcznej na podstawie jego klucza ciągu.

## <a name="establish-distributed-caching-services"></a>Ustanów usługi rozproszonej pamięci podręcznej

Zarejestruj implementację programu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> w `Startup.ConfigureServices`programie. Implementacje wdrożone w ramach platformy opisane w tym temacie:

* [Pamięć podręczna pamięci rozproszonej](#distributed-memory-cache)
* [Pamięć podręczna rozproszonej SQL Server](#distributed-sql-server-cache)
* [Pamięć podręczna Redis rozproszonej](#distributed-redis-cache)
* [Pamięć podręczna NCache rozproszonej](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Pamięć podręczna pamięci rozproszonej

Pamięć podręczna pamięci podręcznej (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) to implementacja udostępniona przez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> platformę, która przechowuje elementy w pamięci. Pamięć podręczna pamięci rozproszonej nie jest rzeczywistą rozproszoną pamięcią podręczną. Elementy buforowane są przechowywane przez wystąpienie aplikacji na serwerze, na którym działa aplikacja.

Pamięć podręczna pamięci rozproszonej jest przydatną implementacją:

* W scenariuszach projektowania i testowania.
* Gdy jest używany pojedynczy serwer w środowisku produkcyjnym i użycie pamięci nie jest problemem. Implementacja rozproszonej pamięci podręcznej pamięć podręczna magazynu danych. Pozwala to na wdrożenie prawdziwego rozwiązania do buforowania rozproszonego w przyszłości, jeśli będzie konieczne przeprowadzenie wielu węzłów lub odporności na uszkodzenia.

Przykładowa aplikacja korzysta z pamięci podręcznej pamięci rozproszonej, gdy aplikacja jest uruchamiana w środowisku `Startup.ConfigureServices`programistycznym w programie:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Pamięć podręczna rozproszonej SQL Server

Implementacja rozproszonej pamięci podręcznej SQL Server (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) umożliwia rozproszonej pamięci podręcznej użycie bazy danych SQL Server jako magazynu zapasowego. Aby utworzyć SQL Server tabelę elementów w pamięci podręcznej w wystąpieniu SQL Server, można użyć `sql-cache` narzędzia. Narzędzie tworzy tabelę z określoną nazwą i schematem.

Utwórz tabelę w SQL Server, uruchamiając `sql-cache create` polecenie. Podaj wystąpienie`Data Source`SQL Server (), bazę danych (`Initial Catalog`), schemat (na przykład `dbo`) i nazwę tabeli (na przykład `TestCache`):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Komunikat jest rejestrowany w celu wskazania, że narzędzie zakończyło się pomyślnie:

```console
Table and index were created successfully.
```

Tabela utworzona przez `sql-cache` narzędzie ma następujący schemat:

![Tabela pamięci podręcznej SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikacja powinna manipulować wartościami pamięci podręcznej przy użyciu <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>wystąpienia, a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>nie.

Przykładowa aplikacja jest <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> wdrażana w środowisku innym niż programowanie `Startup.ConfigureServices`w programie:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> ( <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> I opcjonalnie <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) są zwykle przechowywane poza kontrolą źródła (na przykład przechowywane przez [Menedżera wpisów tajnych](xref:security/app-secrets) lub w pliku AppSettings *. JSON*/*. { ENVIRONMENT}. JSON* — pliki). Parametry połączenia mogą zawierać poświadczenia, które powinny być przechowywane w systemach kontroli źródła.

### <a name="distributed-redis-cache"></a>Redis Cache dystrybuowane

[Redis](https://redis.io/) to magazyn danych typu "open source", który jest często używany jako rozproszonej pamięci podręcznej. Można używać Redis lokalnie i można skonfigurować [Azure Redis Cache](https://azure.microsoft.com/services/cache/) dla aplikacji ASP.NET Core hostowanej na platformie Azure.

Aplikacja konfiguruje implementację pamięci podręcznej <xref:Microsoft.Extensions.Caching.Redis.RedisCache> przy<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>użyciu wystąpienia ():

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Aby zainstalować Redis na komputerze lokalnym:

1. Zainstaluj [pakiet Redis czekolady](https://chocolatey.org/packages/redis-64/).
1. Uruchom `redis-server` polecenie w wierszu polecenia.

### <a name="distributed-ncache-cache"></a>Pamięć podręczna NCache rozproszonej

[NCache](https://github.com/Alachisoft/NCache) to rozproszona pamięć podręczna rozproszonej w pamięci, opracowana natywnie w .NET i .NET Core. NCache działa lokalnie i skonfigurowano jako klaster rozproszonej pamięci podręcznej dla aplikacji ASP.NET Core działającej na platformie Azure lub na innych platformach hostingowych.

Aby zainstalować i skonfigurować NCache na komputerze lokalnym, zobacz [Przewodnik po NCache wprowadzenie dla systemu Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Aby skonfigurować NCache:

1. Zainstaluj pakiet [NuGet NCache Open Source](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Skonfiguruj klaster pamięci podręcznej w programie [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Dodaj następujący kod do pliku `Startup.ConfigureServices`:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Korzystanie z rozproszonej pamięci podręcznej

Aby użyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfejsu, zażądaj wystąpienia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z dowolnego konstruktora w aplikacji. Wystąpienie jest dostarczane przez [wstrzyknięcie zależności (di)](xref:fundamentals/dependency-injection).

Po rozpoczęciu przykładowej <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> aplikacji jest wstrzykiwana `Startup.Configure`do. Bieżący czas jest buforowany przy użyciu <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Aby uzyskać więcej informacji, zobacz [host sieci Web: IApplicationLifetime Interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Przykładowa aplikacja wstrzykiwa <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do użycia `IndexModel` na stronie indeksu.

Za każdym razem, gdy strona indeksu zostanie załadowana, pamięć podręczna jest sprawdzana `OnGetAsync`pod kątem pamięci podręcznej w programie. Jeśli czas w pamięci podręcznej nie upłynął, zostanie wyświetlony czas. Jeśli od czasu ostatniego dostępu do pamięci podręcznej upłynie 20 sekund (podczas ostatniego ładowania strony), na stronie zostanie wyświetlona *godzina wygaśnięcia pamięci podręcznej*.

Natychmiast Aktualizuj buforowany czas do bieżącego czasu, wybierając przycisk **Resetuj buforowany czas** . Przycisk wyzwala metodę `OnPostResetCachedTime` procedury obsługi.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Nie ma potrzeby używania pojedynczego lub zakresu okresu istnienia dla <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpień (co najmniej dla wbudowanych implementacji).
>
> Możesz również utworzyć <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wystąpienie wszędzie tam, gdzie może być potrzebne, zamiast używać di, ale utworzenie wystąpienia w kodzie może sprawić, że kod będzie trudniejszy do przetestowania i narusza [zasadę jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Zalecenia

Podczas decydowania, która implementacja <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> jest Najlepsza dla aplikacji, należy wziąć pod uwagę następujące kwestie:

* Istniejąca infrastruktura
* Wymagania dotyczące wydajności
* Koszty
* Środowisko pracy zespołu

Rozwiązania pamięci podręcznej zwykle polegają na magazynie w pamięci, aby zapewnić szybkie pobieranie danych z pamięci podręcznej, ale pamięć jest ograniczonym zasobem i kosztowna. Przechowuj tylko często używane dane w pamięci podręcznej.

Ogólnie pamięć podręczna Redis zapewnia wyższą przepływność i mniejsze opóźnienia niż w przypadku pamięci podręcznej SQL Server. Jednak testy porównawcze są zwykle wymagane do określenia charakterystyki wydajności strategii buforowania.

Gdy SQL Server jest używany jako magazyn zapasowy rozproszonej pamięci podręcznej, korzystanie z tej samej bazy danych dla pamięci podręcznej i zwykłego magazynu danych aplikacji może mieć negatywny wpływ na wydajność obu tych elementów. Zalecamy używanie dedykowanego wystąpienia SQL Server dla magazynu zapasowego rozproszonej pamięci podręcznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Redis Cache na platformie Azure](/azure/azure-cache-for-redis/)
* [SQL Database na platformie Azure](/azure/sql-database/)
* [ASP.NET Core dostawcę IDistributedCache dla NCache w farmach serwerów sieci Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache w serwisie GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 