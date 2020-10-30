---
title: Pomocnik tagów rozproszonej pamięci podręcznej w ASP.NET Core
author: pkellner
description: Dowiedz się, jak korzystać z pomocnika tagów rozproszonej pamięci podręcznej.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 04ab5be4d9cec066a4b7cd422a1566bcbb5a291a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061161"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Pomocnik tagów rozproszonej pamięci podręcznej w ASP.NET Core

Według [Peterowi Kellner](https://peterkellner.net)

Pomocnik tagów rozproszonej pamięci podręcznej umożliwia znaczne zwiększenie wydajności aplikacji ASP.NET Core przez buforowanie jej zawartości w rozproszonym źródle pamięci podręcznej.

Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .

Pomocnik tagów rozproszonej pamięci podręcznej dziedziczy z tej samej klasy bazowej co pomocnik tagu pamięci podręcznej. Wszystkie atrybuty [pomocnika tagów pamięci podręcznej](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) są dostępne dla pomocnika tagów rozproszonych.

Pomocnik tagów rozproszonej pamięci podręcznej używa [iniekcji konstruktora](xref:fundamentals/dependency-injection#constructor-injection-behavior). <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Interfejs jest przekazywany do konstruktora pomocnika tagów rozproszonej pamięci podręcznej. Jeśli żadna konkretna implementacja programu nie `IDistributedCache` zostanie utworzona w `Startup.ConfigureServices` ( *Startup.cs* ), w ramach rozproszonej pamięci podręcznej w celu przechowywania danych buforowanych jako [pomocnika tagów pamięci](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)podręcznej w usłudze jest stosowany ten sam dostawca w pamięci.

## <a name="distributed-cache-tag-helper-attributes"></a>Atrybuty pomocnika tagów rozproszonej pamięci podręcznej

### <a name="attributes-shared-with-the-cache-tag-helper"></a>Atrybuty udostępnione za pomocą pomocnika tagów pamięci podręcznej

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

Pomocnik tagów rozproszonej pamięci podręcznej dziedziczy z tej samej klasy jako pomocnika tagów pamięci podręcznej. Opisy tych atrybutów znajdują się w temacie [pomocnik tagów pamięci podręcznej](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="name"></a>name

| Typ atrybutu | Przykład                               |
| -------------- | ------------------------------------- |
| String         | `my-distributed-cache-unique-key-101` |

Ciąg `name` jest wymagany. Ten `name` atrybut jest używany jako klucz dla każdego przechowywanego wystąpienia pamięci podręcznej. W przeciwieństwie do pomocnika tagu pamięci podręcznej, który przypisuje klucz pamięci podręcznej do każdego wystąpienia na podstawie Razor nazwy strony i lokalizacji na Razor stronie, pomocnik tagów rozproszonej pamięci podręcznej tylko opiera swój klucz w atrybucie `name` .

Przykład:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>IDistributedCache implementacje pomocnika tagów rozproszonej pamięci podręcznej

Istnieją dwie implementacje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wbudowane w ASP.NET Core. Jest on oparty na SQL Server, a drugi jest oparty na Redis. Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Szczegóły tych implementacji można znaleźć pod adresem <xref:performance/caching/distributed> . Obie implementacje wymagają ustawienia wystąpienia programu `IDistributedCache` w systemie `Startup` .

Nie istnieją żadne atrybuty tagów, które są skojarzone z użyciem żadnej konkretnej implementacji programu `IDistributedCache` .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
