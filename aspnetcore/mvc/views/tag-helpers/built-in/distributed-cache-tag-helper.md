---
title: Pomocnik tagów rozproszonej pamięci podręcznej w ASP.NET Core
author: pkellner
description: Dowiedz się, jak korzystać z pomocnika tagów rozproszonej pamięci podręcznej.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 04ab5be4d9cec066a4b7cd422a1566bcbb5a291a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061161"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="3ac67-103">Pomocnik tagów rozproszonej pamięci podręcznej w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ac67-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3ac67-104">Według [Peterowi Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="3ac67-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="3ac67-105">Pomocnik tagów rozproszonej pamięci podręcznej umożliwia znaczne zwiększenie wydajności aplikacji ASP.NET Core przez buforowanie jej zawartości w rozproszonym źródle pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3ac67-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="3ac67-106">Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="3ac67-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="3ac67-107">Pomocnik tagów rozproszonej pamięci podręcznej dziedziczy z tej samej klasy bazowej co pomocnik tagu pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3ac67-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="3ac67-108">Wszystkie atrybuty [pomocnika tagów pamięci podręcznej](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) są dostępne dla pomocnika tagów rozproszonych.</span><span class="sxs-lookup"><span data-stu-id="3ac67-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="3ac67-109">Pomocnik tagów rozproszonej pamięci podręcznej używa [iniekcji konstruktora](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="3ac67-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="3ac67-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Interfejs jest przekazywany do konstruktora pomocnika tagów rozproszonej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3ac67-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="3ac67-111">Jeśli żadna konkretna implementacja programu nie `IDistributedCache` zostanie utworzona w `Startup.ConfigureServices` ( *Startup.cs* ), w ramach rozproszonej pamięci podręcznej w celu przechowywania danych buforowanych jako [pomocnika tagów pamięci](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)podręcznej w usłudze jest stosowany ten sam dostawca w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3ac67-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` ( *Startup.cs* ), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="3ac67-112">Atrybuty pomocnika tagów rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="3ac67-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="3ac67-113">Atrybuty udostępnione za pomocą pomocnika tagów pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="3ac67-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-:::no-loc(cookie):::`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="3ac67-114">Pomocnik tagów rozproszonej pamięci podręcznej dziedziczy z tej samej klasy jako pomocnika tagów pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3ac67-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="3ac67-115">Opisy tych atrybutów znajdują się w temacie [pomocnik tagów pamięci podręcznej](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="3ac67-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="3ac67-116">name</span><span class="sxs-lookup"><span data-stu-id="3ac67-116">name</span></span>

| <span data-ttu-id="3ac67-117">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="3ac67-117">Attribute Type</span></span> | <span data-ttu-id="3ac67-118">Przykład</span><span class="sxs-lookup"><span data-stu-id="3ac67-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="3ac67-119">String</span><span class="sxs-lookup"><span data-stu-id="3ac67-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="3ac67-120">Ciąg `name` jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="3ac67-120">`name` is required.</span></span> <span data-ttu-id="3ac67-121">Ten `name` atrybut jest używany jako klucz dla każdego przechowywanego wystąpienia pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3ac67-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="3ac67-122">W przeciwieństwie do pomocnika tagu pamięci podręcznej, który przypisuje klucz pamięci podręcznej do każdego wystąpienia na podstawie :::no-loc(Razor)::: nazwy strony i lokalizacji na :::no-loc(Razor)::: stronie, pomocnik tagów rozproszonej pamięci podręcznej tylko opiera swój klucz w atrybucie `name` .</span><span class="sxs-lookup"><span data-stu-id="3ac67-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the :::no-loc(Razor)::: page name and location in the :::no-loc(Razor)::: page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="3ac67-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3ac67-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="3ac67-124">IDistributedCache implementacje pomocnika tagów rozproszonej pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="3ac67-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="3ac67-125">Istnieją dwie implementacje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wbudowane w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ac67-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="3ac67-126">Jest on oparty na SQL Server, a drugi jest oparty na Redis.</span><span class="sxs-lookup"><span data-stu-id="3ac67-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="3ac67-127">Dostępne są również implementacje innych firm, takie jak [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="3ac67-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="3ac67-128">Szczegóły tych implementacji można znaleźć pod adresem <xref:performance/caching/distributed> .</span><span class="sxs-lookup"><span data-stu-id="3ac67-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="3ac67-129">Obie implementacje wymagają ustawienia wystąpienia programu `IDistributedCache` w systemie `Startup` .</span><span class="sxs-lookup"><span data-stu-id="3ac67-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="3ac67-130">Nie istnieją żadne atrybuty tagów, które są skojarzone z użyciem żadnej konkretnej implementacji programu `IDistributedCache` .</span><span class="sxs-lookup"><span data-stu-id="3ac67-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ac67-131">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3ac67-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
