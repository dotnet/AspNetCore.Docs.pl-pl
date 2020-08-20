---
title: Buforowanie w pamięci w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak buforować dane w pamięci w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: c4d21992695828e81e03eca92f167c0a3d69c724
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627289"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Buforowanie w pamięci w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo)i [Steve Smith](https://ardalis.com/)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Podstawowe informacje o buforowaniu

Buforowanie może znacząco poprawić wydajność i skalowalność aplikacji przez zmniejszenie ilości pracy wymaganej do wygenerowania zawartości. Buforowanie działa najlepiej w **przypadku rzadko używanych** danych. Buforowanie tworzy kopię danych, która może być zwracana znacznie szybciej niż ze źródła. Aplikacje powinny być zapisane i przetestowane w taki sposób, aby **nigdy nie** zależały od danych buforowanych.

ASP.NET Core obsługuje kilka różnych pamięci podręcznych. Najprostsza pamięć podręczna jest oparta na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). `IMemoryCache` Reprezentuje pamięć podręczną przechowywaną w pamięci serwera sieci Web. Aplikacje działające w farmie serwerów (wiele serwerów) powinny zapewnić, że sesje są w trakcie korzystania z pamięci podręcznej w pamięci. Sesje usługi Sticky Notes zapewniają, że kolejne żądania od klienta będą kierowane do tego samego serwera. Na przykład usługa Azure Web Apps używa [routingu żądań aplikacji](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) do kierowania wszystkich kolejnych żądań do tego samego serwera.

Sesje inne niż nietrwałe w kolektywie serwerów sieci Web wymagają [rozproszonej pamięci podręcznej](distributed.md) , aby uniknąć problemów ze spójnością pamięci W przypadku niektórych aplikacji rozproszonej pamięci podręcznej może obsługiwać większą skalowalność niż pamięć podręczna w pamięci. Użycie rozproszonej pamięci podręcznej powoduje odciążenie pamięci podręcznej do procesu zewnętrznego.

Pamięć podręczna w pamięci może przechowywać dowolny obiekt. Interfejs rozproszonej pamięci podręcznej jest ograniczony do `byte[]` . Magazyn w pamięci i rozproszonej pamięci podręcznej przechowuje elementy pamięci podręcznej jako pary klucz-wartość.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. buforowanie/elemencie MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Pakiet NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) może być używany z:

* .NET Standard 2,0 lub nowszy.
* Dowolna [implementacja platformy .NET](/dotnet/standard/net-standard#net-implementation-support) , która jest przeznaczona dla .NET Standard 2,0 lub nowszych. Na przykład ASP.NET Core 2,0 lub nowszy.
* .NET Framework 4,5 lub nowszy.

[Firma Microsoft. Extensions. buforowanie. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (opisana w tym artykule) jest zalecana w porównaniu ze względu na to, że jest `System.Runtime.Caching` / `MemoryCache` lepiej zintegrowana z ASP.NET Core. Na przykład `IMemoryCache` działa natywnie z [iniekcją ASP.NET Core zależności](xref:fundamentals/dependency-injection).

Użyj `System.Runtime.Caching` / `MemoryCache` jako mostka zgodności podczas przenoszenia kodu z ASP.NET 4. x do ASP.NET Core.

## <a name="cache-guidelines"></a>Wskazówki dotyczące pamięci podręcznej

* Kod powinien zawsze mieć opcję rezerwową, aby pobrać dane i **nie** zależał od dostępnej wartości w pamięci podręcznej.
* Pamięć podręczna używa nieodpowiedniego zasobu, pamięci. Ogranicz wzrost rozmiaru pamięci podręcznej:
  * **Nie** należy używać zewnętrznych danych wejściowych jako kluczy pamięci podręcznej.
  * Użyj wygaśnięć, aby ograniczyć wzrost rozmiaru pamięci podręcznej.
  * [Użyj SetSize, size i SizeLimit, aby ograniczyć rozmiar pamięci podręcznej](#use-setsize-size-and-sizelimit-to-limit-cache-size). Środowisko uruchomieniowe ASP.NET Core **nie ogranicza rozmiaru** pamięci podręcznej na podstawie nacisku pamięci. Aby ograniczyć rozmiar pamięci podręcznej, należy do dewelopera.

## <a name="use-imemorycache"></a>Użyj IMemoryCache

> [!WARNING]
> Użycie pamięci podręcznej pamięci *współużytkowanej* przed [iniekcją zależności](xref:fundamentals/dependency-injection) i wywołaniem `SetSize` , `Size` lub `SizeLimit` w celu ograniczenia rozmiaru pamięci podręcznej może spowodować niepowodzenie aplikacji. Po ustawieniu limitu rozmiaru w pamięci podręcznej, wszystkie wpisy muszą określać rozmiar podczas dodawania. Może to prowadzić do problemów, ponieważ deweloperzy mogą nie mieć pełnej kontroli nad używaniem udostępnionej pamięci podręcznej. Na przykład Entity Framework Core używa udostępnionej pamięci podręcznej i nie określa rozmiaru. Jeśli aplikacja ustawi limit rozmiaru pamięci podręcznej i użyje EF Core, aplikacja zgłosi `InvalidOperationException` .
> W przypadku korzystania z `SetSize` , `Size` , lub `SizeLimit` do ograniczania pamięci podręcznej, należy utworzyć pojedynczą pamięć podręczną dla buforowania. Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz [Używanie SetSize, size i SizeLimit w celu ograniczenia rozmiaru pamięci podręcznej](#use-setsize-size-and-sizelimit-to-limit-cache-size).
> Udostępniona pamięć podręczna jest współdzielona przez inne struktury lub biblioteki. Na przykład EF Core używa udostępnionej pamięci podręcznej i nie określa rozmiaru. 

Buforowanie w pamięci to *Usługa* , do której odwołuje się aplikacja przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection). Zażądaj `IMemoryCache` wystąpienia w konstruktorze:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

Poniższy kod używa [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) aby sprawdzić, czy czas znajduje się w pamięci podręcznej. Jeśli czas nie jest buforowany, nowy wpis zostanie utworzony i dodany do pamięci podręcznej z [zestawem](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_). `CacheKeys`Klasa jest częścią przykładu pobierania.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

Bieżąca godzina i w pamięci podręcznej są wyświetlane:

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

Buforowana `DateTime` wartość pozostaje w pamięci podręcznej, gdy istnieją żądania w określonym limicie czasu.

Poniższy kod używa [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) i [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) do buforowania danych.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Następujący kod wywołuje [pobieranie](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) , aby pobrać buforowany czas:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

Poniższy kod pobiera lub tworzy buforowany element z bezwzględnym wygaśnięciem:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

W pamięci podręcznej zestaw elementów z przewijanym okresem ważności jest zagrożony przestarzałą. Jeśli dostęp do niego jest możliwy częściej niż przedział czasu wygaśnięcia, element nigdy nie wygaśnie. Połącz okresowe wygaśnięcie i bezwzględne wygaśnięcie w celu zagwarantowania, że element wygaśnie po upływie bezwzględnego czasu wygaśnięcia. Bezwzględne wygaśnięcie Ustawia górną granicę, jak długo element może być buforowany, przy jednoczesnym dopuszczeniu do wcześniejszego wygaśnięcia elementu, jeśli nie zostanie on żądany w przedziale czasu wygaśnięcia. Gdy są określone okresy ważności bezwzględne i przesuwania, wygasające są logiczne logicznie. Jeśli przedział czasu wygaśnięcia *lub* bezwzględny czas wygaśnięcia, element zostanie wykluczony z pamięci podręcznej.

Poniższy kod pobiera lub tworzy buforowany element z przewinięciem *i* bezwzględnym okresem ważności:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

Poprzedni kod gwarantuje, że dane nie będą przechowywane w pamięci podręcznej dłużej niż czas bezwzględny.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> , i <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> są metodami rozszerzającymi w <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> klasie. Te metody zwiększają możliwości programu <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Poniższy przykład:

* Ustawia czas wygaśnięcia. Żądania, które uzyskują dostęp do tego elementu w pamięci podręcznej, spowodują zresetowanie zegara zakończenia przewijania.
* Ustawia priorytet pamięci podręcznej na [CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).
* Ustawia [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , który zostanie wywołany po wykluczeniu wpisu z pamięci podręcznej. Wywołanie zwrotne jest uruchamiane w innym wątku niż kod, który usuwa element z pamięci podręcznej.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Użyj SetSize, size i SizeLimit, aby ograniczyć rozmiar pamięci podręcznej

`MemoryCache`Wystąpienie może opcjonalnie określić i wymusić limit rozmiaru. Limit rozmiaru pamięci podręcznej nie ma zdefiniowanej jednostki miary, ponieważ pamięć podręczna nie ma mechanizmu mierzenia rozmiaru wpisów. Jeśli ustawiono limit rozmiaru pamięci podręcznej, wszystkie wpisy muszą określać rozmiar. Środowisko uruchomieniowe ASP.NET Core nie ogranicza rozmiaru pamięci podręcznej na podstawie nacisku pamięci. Aby ograniczyć rozmiar pamięci podręcznej, należy do dewelopera. Określony rozmiar jest w jednostkach wybranych przez dewelopera.

Na przykład:

* Jeśli aplikacja sieci Web była przede wszystkim buforowania ciągów, każdy rozmiar wpisu pamięci podręcznej może być długością ciągu.
* Aplikacja może określić rozmiar wszystkich wpisów jako 1, a limit rozmiaru to liczba wpisów.

Jeśli <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> nie jest ustawiona, pamięć podręczna rośnie bez powiązania. Środowisko uruchomieniowe ASP.NET Core nie przycina pamięci podręcznej, gdy ilość pamięci systemowej jest niska. Aplikacje muszą być zaprojektowane w celu:

* Ogranicz wzrost rozmiaru pamięci podręcznej.
* Połączenie <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> lub <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> Jeśli dostępna pamięć jest ograniczona:

Poniższy kod tworzy bezjednostkowy rozmiar <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> dostępny w ramach [iniekcji zależności](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` nie ma jednostek. Wpisy w pamięci podręcznej muszą określać rozmiar w jednostkach, które są uważane za najbardziej odpowiednie, jeśli ustawiono limit rozmiaru pamięci podręcznej. Wszyscy użytkownicy wystąpienia pamięci podręcznej powinni używać tego samego systemu jednostek. Wpis nie zostanie zapisany w pamięci podręcznej, jeśli suma rozmiarów buforowanych wpisów przekroczy wartość określoną przez `SizeLimit` . Jeśli limit rozmiaru pamięci podręcznej nie zostanie ustawiony, rozmiar pamięci podręcznej ustawiony na wpis zostanie zignorowany.

Poniższy kod rejestruje `MyMemoryCache` z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` jest tworzony jako pamięć podręczna niezależna pamięci dla składników, które są świadome pamięci podręcznej ograniczonej rozmiaru i wiedzą, jak ustawić odpowiednio rozmiar wpisu pamięci podręcznej.

Następujący kod używa `MyMemoryCache` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

Rozmiar wpisu pamięci podręcznej może być ustawiony przez <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> lub <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> metody rozszerzenia:

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>Elemencie MemoryCache. Compact

`MemoryCache.Compact` próbuje usunąć określony procent pamięci podręcznej w następującej kolejności:

* Wszystkie elementy wygasłe.
* Elementy według priorytetu. Elementy o najniższym priorytecie są usuwane jako pierwsze.
* Ostatnio używane obiekty.
* Elementy z najwcześniejszym bezwzględnym okresem ważności.
* Elementy z najwcześniejszym okresem ważności.

Przypięte elementy z priorytetem <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> nigdy nie są usuwane. Poniższy kod usuwa element pamięci podręcznej i wywołuje `Compact` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Aby uzyskać więcej informacji, zobacz artykuł [Compact Source w witrynie GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Zależności pamięci podręcznej

Poniższy przykład pokazuje, jak wygasa wpis pamięci podręcznej, Jeśli wpis zależny wygaśnie. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Element jest dodawany do elementu w pamięci podręcznej. Gdy `Cancel` jest wywoływana w `CancellationTokenSource` , oba wpisy pamięci podręcznej są wykluczone.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

Użycie a <xref:System.Threading.CancellationTokenSource> umożliwia wykluczenie wielu wpisów pamięci podręcznej jako grupy. Ze `using` wzorcem w powyższym kodzie wpisy pamięci podręcznej utworzone wewnątrz `using` bloku będą dziedziczyć wyzwalacze i ustawienia wygasania.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Wygaśnięcie nie odbywa się w tle. Nie ma czasomierza, który aktywnie skanuje pamięć podręczną pod kątem wygasłych elementów. Wszystkie działania w pamięci podręcznej ( `Get` , `Set` , `Remove` ) mogą wyzwalać skanowanie w tle dla elementów, które utraciły ważność. Czasomierz w `CancellationTokenSource` ( <xref:System.Threading.CancellationTokenSource.CancelAfter*> ) również usuwa wpis i wyzwala skanowanie pod kątem wygasłych elementów. W poniższym przykładzie zastosowano [CancellationTokenSource (TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) dla zarejestrowanego tokenu. Gdy ten token wyzwala, usuwa wpis natychmiast i wyzwala wywołania zwrotne wykluczenia:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* W przypadku użycia wywołania zwrotnego do ponownego wypełnienia elementu pamięci podręcznej:

  * Wiele żądań może znaleźć pustą wartość klucza w pamięci podręcznej, ponieważ wywołanie zwrotne nie zostało zakończone.
  * Może to spowodować, że kilka wątków będzie przepełniać element w pamięci podręcznej.

* Gdy jeden wpis pamięci podręcznej jest używany do utworzenia innego, element podrzędny kopiuje tokeny wygaśnięcia i czas wygaśnięcia na podstawie czasu. Element podrzędny nie wygasł przez ręczne usunięcie lub aktualizację wpisu nadrzędnego.

* Użyj, <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> Aby ustawić wywołania zwrotne, które będą wyzwalane po usunięciu wpisu pamięci podręcznej z tej pamięci.
* W przypadku większości aplikacji `IMemoryCache` jest włączona. Na przykład wywoływanie `AddMvc` , `AddControllersWithViews` , `AddRazorPages` , `AddMvcCore().AddRazorViewEngine` , i wiele innych `Add{Service}` metod w `ConfigureServices` , włącza `IMemoryCache` . W przypadku aplikacji, które nie wywołuje jednej z powyższych `Add{Service}` metod, może być konieczne wywołanie metody <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> `ConfigureServices` .

## <a name="background-cache-update"></a>Aktualizacja pamięci podręcznej w tle

Użyj [usługi w tle](xref:fundamentals/host/hosted-services) , takiej jak <xref:Microsoft.Extensions.Hosting.IHostedService> Aby zaktualizować pamięć podręczną. Usługa w tle może ponownie obliczyć wpisy, a następnie przypisać je do pamięci podręcznej tylko wtedy, gdy są gotowe.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo)i [Steve Smith](https://ardalis.com/)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Podstawowe informacje o buforowaniu

Buforowanie może znacząco poprawić wydajność i skalowalność aplikacji przez zmniejszenie ilości pracy wymaganej do wygenerowania zawartości. Buforowanie działa najlepiej w przypadku rzadko używanych danych. Buforowanie tworzy kopię danych, która może być zwracana znacznie szybciej niż z oryginalnego źródła. Kod powinien być zapisany i przetestowany w taki sposób, aby **nigdy nie** zależał od danych buforowanych.

ASP.NET Core obsługuje kilka różnych pamięci podręcznych. Najprostsza pamięć podręczna jest oparta na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), która reprezentuje pamięć podręczną przechowywaną w pamięci serwera sieci Web. Aplikacje działające w farmie serwerów (wiele serwerów) powinny mieć pewność, że sesje są w trakcie korzystania z pamięci podręcznej w pamięci. Sesje usługi Sticky Notes zapewniają, że późniejsze żądania od klienta będą kierowane do tego samego serwera. Na przykład usługa Azure Web Apps używa [routingu żądań aplikacji](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) do kierowania wszystkich żądań od agenta użytkownika na ten sam serwer.

Sesje inne niż nietrwałe w kolektywie serwerów sieci Web wymagają [rozproszonej pamięci podręcznej](distributed.md) , aby uniknąć problemów ze spójnością pamięci W przypadku niektórych aplikacji rozproszonej pamięci podręcznej może obsługiwać większą skalowalność niż pamięć podręczna w pamięci. Użycie rozproszonej pamięci podręcznej powoduje odciążenie pamięci podręcznej do procesu zewnętrznego.

Pamięć podręczna w pamięci może przechowywać dowolny obiekt. Interfejs rozproszonej pamięci podręcznej jest ograniczony do `byte[]` . Magazyn w pamięci i rozproszonej pamięci podręcznej przechowuje elementy pamięci podręcznej jako pary klucz-wartość.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. buforowanie/elemencie MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Pakiet NuGet](https://www.nuget.org/packages/System.Runtime.Caching/)) może być używany z:

* .NET Standard 2,0 lub nowszy.
* Dowolna [implementacja platformy .NET](/dotnet/standard/net-standard#net-implementation-support) , która jest przeznaczona dla .NET Standard 2,0 lub nowszych. Na przykład ASP.NET Core 2,0 lub nowszy.
* .NET Framework 4,5 lub nowszy.

[Firma Microsoft. Extensions. buforowanie. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (opisana w tym artykule) jest zalecana w porównaniu ze względu na to, że jest `System.Runtime.Caching` / `MemoryCache` lepiej zintegrowana z ASP.NET Core. Na przykład `IMemoryCache` działa natywnie z [iniekcją ASP.NET Core zależności](xref:fundamentals/dependency-injection).

Użyj `System.Runtime.Caching` / `MemoryCache` jako mostka zgodności podczas przenoszenia kodu z ASP.NET 4. x do ASP.NET Core.

## <a name="cache-guidelines"></a>Wskazówki dotyczące pamięci podręcznej

* Kod powinien zawsze mieć opcję rezerwową, aby pobrać dane i **nie** zależał od dostępnej wartości w pamięci podręcznej.
* Pamięć podręczna używa nieodpowiedniego zasobu, pamięci. Ogranicz wzrost rozmiaru pamięci podręcznej:
  * **Nie** należy używać zewnętrznych danych wejściowych jako kluczy pamięci podręcznej.
  * Użyj wygaśnięć, aby ograniczyć wzrost rozmiaru pamięci podręcznej.
  * [Użyj SetSize, size i SizeLimit, aby ograniczyć rozmiar pamięci podręcznej](#use-setsize-size-and-sizelimit-to-limit-cache-size). Środowisko uruchomieniowe ASP.NET Core nie ogranicza rozmiaru pamięci podręcznej na podstawie nacisku pamięci. Aby ograniczyć rozmiar pamięci podręcznej, należy do dewelopera.

## <a name="using-imemorycache"></a>Korzystanie z IMemoryCache

> [!WARNING]
> Użycie pamięci podręcznej pamięci *współużytkowanej* przed [iniekcją zależności](xref:fundamentals/dependency-injection) i wywołaniem `SetSize` , `Size` lub `SizeLimit` w celu ograniczenia rozmiaru pamięci podręcznej może spowodować niepowodzenie aplikacji. Po ustawieniu limitu rozmiaru w pamięci podręcznej, wszystkie wpisy muszą określać rozmiar podczas dodawania. Może to prowadzić do problemów, ponieważ deweloperzy mogą nie mieć pełnej kontroli nad używaniem udostępnionej pamięci podręcznej. Na przykład Entity Framework Core używa udostępnionej pamięci podręcznej i nie określa rozmiaru. Jeśli aplikacja ustawi limit rozmiaru pamięci podręcznej i użyje EF Core, aplikacja zgłosi `InvalidOperationException` .
> W przypadku korzystania z `SetSize` , `Size` , lub `SizeLimit` do ograniczania pamięci podręcznej, należy utworzyć pojedynczą pamięć podręczną dla buforowania. Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz [Używanie SetSize, size i SizeLimit w celu ograniczenia rozmiaru pamięci podręcznej](#use-setsize-size-and-sizelimit-to-limit-cache-size).

Buforowanie w pamięci to *Usługa* , do której odwołuje się aplikacja przy użyciu [iniekcji zależności](../../fundamentals/dependency-injection.md). Wywołanie `AddMemoryCache` w `ConfigureServices` :

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Zażądaj `IMemoryCache` wystąpienia w konstruktorze:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache` wymaga pakietu NuGet [Microsoft. Extensions. buforowanie. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), który jest dostępny w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Poniższy kod używa [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) aby sprawdzić, czy czas znajduje się w pamięci podręcznej. Jeśli czas nie jest buforowany, nowy wpis zostanie utworzony i dodany do pamięci podręcznej z [zestawem](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Bieżąca godzina i w pamięci podręcznej są wyświetlane:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

Buforowana `DateTime` wartość pozostaje w pamięci podręcznej, gdy istnieją żądania w określonym limicie czasu. Na poniższej ilustracji przedstawiono bieżący czas i wcześniejszy czas pobrany z pamięci podręcznej:

![Widok indeksu z dwoma różnymi godzinami wyświetlania](memory/_static/time.png)

Poniższy kod używa [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) i [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) do buforowania danych.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Następujący kod wywołuje [pobieranie](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) , aby pobrać buforowany czas:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> , i [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) są metodami rozszerzenia części klasy [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) , która rozszerza możliwości programu <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Zobacz [metody IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) i [CacheExtensions metody](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) opisujące inne metody pamięci podręcznej.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Poniższy przykład:

* Ustawia czas wygaśnięcia. Żądania, które uzyskują dostęp do tego elementu w pamięci podręcznej, spowodują zresetowanie zegara zakończenia przewijania.
* Ustawia priorytet pamięci podręcznej na `CacheItemPriority.NeverRemove` .
* Ustawia [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , który zostanie wywołany po wykluczeniu wpisu z pamięci podręcznej. Wywołanie zwrotne jest uruchamiane w innym wątku niż kod, który usuwa element z pamięci podręcznej.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Użyj SetSize, size i SizeLimit, aby ograniczyć rozmiar pamięci podręcznej

`MemoryCache`Wystąpienie może opcjonalnie określić i wymusić limit rozmiaru. Limit rozmiaru pamięci podręcznej nie ma zdefiniowanej jednostki miary, ponieważ pamięć podręczna nie ma mechanizmu mierzenia rozmiaru wpisów. Jeśli ustawiono limit rozmiaru pamięci podręcznej, wszystkie wpisy muszą określać rozmiar. Środowisko uruchomieniowe ASP.NET Core nie ogranicza rozmiaru pamięci podręcznej na podstawie nacisku pamięci. Aby ograniczyć rozmiar pamięci podręcznej, należy do dewelopera. Określony rozmiar jest w jednostkach wybranych przez dewelopera.

Na przykład:

* Jeśli aplikacja sieci Web była przede wszystkim buforowania ciągów, każdy rozmiar wpisu pamięci podręcznej może być długością ciągu.
* Aplikacja może określić rozmiar wszystkich wpisów jako 1, a limit rozmiaru to liczba wpisów.

Jeśli <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> nie jest ustawiona, pamięć podręczna rośnie bez powiązania. Środowisko uruchomieniowe ASP.NET Core nie przycina pamięci podręcznej, gdy ilość pamięci systemowej jest niska. Aplikacje są w znacznym stopniu zaprojektowane pod kątem:

* Ogranicz wzrost rozmiaru pamięci podręcznej.
* Połączenie <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> lub <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> Jeśli dostępna pamięć jest ograniczona:

Poniższy kod tworzy bezjednostkowy rozmiar <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> dostępny w ramach [iniekcji zależności](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` nie ma jednostek. Wpisy w pamięci podręcznej muszą określać rozmiar w jednostkach, które są uważane za najbardziej odpowiednie, jeśli ustawiono limit rozmiaru pamięci podręcznej. Wszyscy użytkownicy wystąpienia pamięci podręcznej powinni używać tego samego systemu jednostek. Wpis nie zostanie zapisany w pamięci podręcznej, jeśli suma rozmiarów buforowanych wpisów przekroczy wartość określoną przez `SizeLimit` . Jeśli limit rozmiaru pamięci podręcznej nie zostanie ustawiony, rozmiar pamięci podręcznej ustawiony na wpis zostanie zignorowany.

Poniższy kod rejestruje `MyMemoryCache` z kontenerem [iniekcji zależności](xref:fundamentals/dependency-injection) .

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` jest tworzony jako pamięć podręczna niezależna pamięci dla składników, które są świadome pamięci podręcznej ograniczonej rozmiaru i wiedzą, jak ustawić odpowiednio rozmiar wpisu pamięci podręcznej.

Następujący kod używa `MyMemoryCache` :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Rozmiar wpisu pamięci podręcznej można ustawić przez [rozmiar](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) lub metodę rozszerzenia [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>Elemencie MemoryCache. Compact

`MemoryCache.Compact` próbuje usunąć określony procent pamięci podręcznej w następującej kolejności:

* Wszystkie elementy wygasłe.
* Elementy według priorytetu. Elementy o najniższym priorytecie są usuwane jako pierwsze.
* Ostatnio używane obiekty.
* Elementy z najwcześniejszym bezwzględnym okresem ważności.
* Elementy z najwcześniejszym okresem ważności.

Przypięte elementy z priorytetem <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> nigdy nie są usuwane.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Aby uzyskać więcej informacji, zobacz artykuł [Compact Source w witrynie GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Zależności pamięci podręcznej

Poniższy przykład pokazuje, jak wygasa wpis pamięci podręcznej, Jeśli wpis zależny wygaśnie. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Element jest dodawany do elementu w pamięci podręcznej. Gdy `Cancel` jest wywoływana w `CancellationTokenSource` , oba wpisy pamięci podręcznej są wykluczone.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Użycie a `CancellationTokenSource` umożliwia wykluczenie wielu wpisów pamięci podręcznej jako grupy. Ze `using` wzorcem w powyższym kodzie wpisy pamięci podręcznej utworzone wewnątrz `using` bloku będą dziedziczyć wyzwalacze i ustawienia wygasania.

## <a name="additional-notes"></a>Uwagi dodatkowe

* W przypadku użycia wywołania zwrotnego do ponownego wypełnienia elementu pamięci podręcznej:

  * Wiele żądań może znaleźć pustą wartość klucza w pamięci podręcznej, ponieważ wywołanie zwrotne nie zostało zakończone.
  * Może to spowodować, że kilka wątków będzie przepełniać element w pamięci podręcznej.

* Gdy jeden wpis pamięci podręcznej jest używany do utworzenia innego, element podrzędny kopiuje tokeny wygaśnięcia i czas wygaśnięcia na podstawie czasu. Element podrzędny nie wygasł przez ręczne usunięcie lub aktualizację wpisu nadrzędnego.

* Użyj [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) , aby ustawić wywołania zwrotne, które zostaną wyzwolone po usunięciu wpisu pamięci podręcznej z pamięci.

## <a name="background-cache-update"></a>Aktualizacja pamięci podręcznej w tle

Użyj [usługi w tle](xref:fundamentals/host/hosted-services) , takiej jak <xref:Microsoft.Extensions.Hosting.IHostedService> Aby zaktualizować pamięć podręczną. Usługa w tle może ponownie obliczyć wpisy, a następnie przypisać je do pamięci podręcznej tylko wtedy, gdy są gotowe.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
