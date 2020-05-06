---
title: Pomocnik tagów rozproszonej pamięci podręcznej w ASP.NET Core
author: pkellner
description: Dowiedz się, jak korzystać z pomocnika tagów rozproszonej pamięci podręcznej.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: df1daa68a3e18f7aad4507ce9526d76ff6a2114d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773919"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Pomocnik tagów rozproszonej pamięci podręcznej w ASP.NET Core

Według [Peterowi Kellner](https://peterkellner.net)

Pomocnik tagów rozproszonej pamięci podręcznej umożliwia znaczne zwiększenie wydajności aplikacji ASP.NET Core przez buforowanie jej zawartości w rozproszonym źródle pamięci podręcznej.

Aby zapoznać się z omówieniem pomocników tagów, <xref:mvc/views/tag-helpers/intro>Zobacz.

Pomocnik tagów rozproszonej pamięci podręcznej dziedziczy z tej samej klasy bazowej co pomocnik tagu pamięci podręcznej. Wszystkie atrybuty [pomocnika tagów pamięci podręcznej](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) są dostępne dla pomocnika tagów rozproszonych.

Pomocnik tagów rozproszonej pamięci podręcznej używa [iniekcji konstruktora](xref:fundamentals/dependency-injection#constructor-injection-behavior). <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Interfejs jest przekazywany do konstruktora pomocnika tagów rozproszonej pamięci podręcznej. Jeśli żadna konkretna implementacja `IDistributedCache` programu nie zostanie `Startup.ConfigureServices` utworzona w (*Startup.cs*), w ramach rozproszonej pamięci podręcznej w celu przechowywania danych buforowanych jako [pomocnika tagów pamięci](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)podręcznej w usłudze jest stosowany ten sam dostawca w pamięci.

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

Ciąg `name` jest wymagany. Ten `name` atrybut jest używany jako klucz dla każdego przechowywanego wystąpienia pamięci podręcznej. W przeciwieństwie do pomocnika tagu pamięci podręcznej, który przypisuje klucz pamięci Razor podręcznej do każdego wystąpienia Razor na podstawie nazwy strony i lokalizacji na stronie, pomocnik tagów rozproszonej pamięci `name`podręcznej tylko opiera swój klucz w atrybucie.

Przykład:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>IDistributedCache implementacje pomocnika tagów rozproszonej pamięci podręcznej

Istnieją dwie implementacje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wbudowane w ASP.NET Core. Jest on oparty na SQL Server, a drugi jest oparty na Redis. Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Szczegóły tych implementacji można znaleźć pod adresem <xref:performance/caching/distributed>. Obie implementacje wymagają ustawienia wystąpienia programu `IDistributedCache` w `Startup`systemie.

Nie istnieją żadne atrybuty tagów, które są skojarzone z użyciem żadnej konkretnej implementacji programu `IDistributedCache`.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
