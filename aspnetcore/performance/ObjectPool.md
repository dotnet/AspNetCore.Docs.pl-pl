---
title: Ponowne użycie obiektu za pomocą ObjectPool w ASP.NET Core
author: rick-anderson
description: Porady dotyczące zwiększania wydajności ASP.NET Core aplikacji przy użyciu programu ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 9df7f370eb550172493478bcd8d94a9541926fec
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793557"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="451dd-103">Ponowne użycie obiektu za pomocą ObjectPool w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="451dd-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="451dd-104">[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)i [Günther Foidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="451dd-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="451dd-105"><xref:Microsoft.Extensions.ObjectPool>jest częścią infrastruktury ASP.NET Core, która obsługuje przechowywanie w pamięci grupy obiektów do ponownego użycia, a nie pozwala na wyrzucanie obiektów jako elementów bezużytecznych.</span><span class="sxs-lookup"><span data-stu-id="451dd-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="451dd-106">Może być konieczne użycie puli obiektów, jeśli zarządzane obiekty są następujące:</span><span class="sxs-lookup"><span data-stu-id="451dd-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="451dd-107">Kosztowne, aby przydzielić/zainicjować.</span><span class="sxs-lookup"><span data-stu-id="451dd-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="451dd-108">Reprezentuje ograniczony zasób.</span><span class="sxs-lookup"><span data-stu-id="451dd-108">Represent some limited resource.</span></span>
- <span data-ttu-id="451dd-109">Używane do przewidywania i często.</span><span class="sxs-lookup"><span data-stu-id="451dd-109">Used predictably and frequently.</span></span>

<span data-ttu-id="451dd-110">Na przykład, struktura ASP.NET Core używa puli obiektów w niektórych miejscach do ponownego użycia <xref:System.Text.StringBuilder> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="451dd-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="451dd-111">`StringBuilder`przypisuje własne bufory i zarządza nimi do przechowywania danych znakowych.</span><span class="sxs-lookup"><span data-stu-id="451dd-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="451dd-112">ASP.NET Core regularnie używa `StringBuilder` do implementowania funkcji, a ich użycie umożliwia korzystanie z zalet wydajności.</span><span class="sxs-lookup"><span data-stu-id="451dd-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="451dd-113">Buforowanie obiektów nie zawsze poprawia wydajność:</span><span class="sxs-lookup"><span data-stu-id="451dd-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="451dd-114">Chyba że koszt inicjacji obiektu jest wysoki, zwykle jest wolniejsze Pobieranie obiektu z puli.</span><span class="sxs-lookup"><span data-stu-id="451dd-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="451dd-115">Obiekty zarządzane przez pulę nie są przydzielone do momentu cofnięcia przydziału puli.</span><span class="sxs-lookup"><span data-stu-id="451dd-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="451dd-116">Używaj buforowania obiektów tylko po zebraniu danych wydajności przy użyciu realistycznych scenariuszy dla aplikacji lub biblioteki.</span><span class="sxs-lookup"><span data-stu-id="451dd-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="451dd-117">**Ostrzeżenie: `ObjectPool` nie implementuje `IDisposable` . Nie zalecamy używania jej z typami, które wymagają usunięcia.**</span><span class="sxs-lookup"><span data-stu-id="451dd-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="451dd-118">`ObjectPool`w ASP.NET Core 3,0 i nowszych `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="451dd-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="451dd-119">**Uwaga: ObjectPool nie nakłada limitu liczby obiektów, które zostanie przydzielone, spowoduje ograniczenie liczby obiektów zachowywanych.**</span><span class="sxs-lookup"><span data-stu-id="451dd-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="451dd-120">Pojęcia</span><span class="sxs-lookup"><span data-stu-id="451dd-120">Concepts</span></span>

<span data-ttu-id="451dd-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>— Abstrakcja puli obiektów podstawowych.</span><span class="sxs-lookup"><span data-stu-id="451dd-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="451dd-122">Służy do pobierania i zwracania obiektów.</span><span class="sxs-lookup"><span data-stu-id="451dd-122">Used to get and return objects.</span></span>

<span data-ttu-id="451dd-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implementuje ten element, aby dostosować sposób tworzenia obiektu i sposobu jego *resetowania* w przypadku powrotu do puli.</span><span class="sxs-lookup"><span data-stu-id="451dd-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="451dd-124">Ten element może zostać przesłany do puli obiektów, która została skonstruowana bezpośrednio... ORAZ</span><span class="sxs-lookup"><span data-stu-id="451dd-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="451dd-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>pełni rolę fabryki do tworzenia pul obiektów.</span><span class="sxs-lookup"><span data-stu-id="451dd-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="451dd-126">ObjectPool może być używana w aplikacji na wiele sposobów:</span><span class="sxs-lookup"><span data-stu-id="451dd-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="451dd-127">Tworzenie wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="451dd-127">Instantiating a pool.</span></span>
* <span data-ttu-id="451dd-128">Rejestrowanie puli w [iniekcji zależności](xref:fundamentals/dependency-injection) (di) jako wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="451dd-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="451dd-129">Rejestracja `ObjectPoolProvider<>` w programie i używanie jej w ramach fabryki.</span><span class="sxs-lookup"><span data-stu-id="451dd-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="451dd-130">Jak używać ObjectPool</span><span class="sxs-lookup"><span data-stu-id="451dd-130">How to use ObjectPool</span></span>

<span data-ttu-id="451dd-131">Wywołanie metody <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> Get obiektu i <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> zwrócenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="451dd-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="451dd-132">Nie jest wymagane, aby zwrócić każdy obiekt.</span><span class="sxs-lookup"><span data-stu-id="451dd-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="451dd-133">Jeśli nie zwracasz obiektu, zostanie on wyrzucony jako elementy bezużyteczne.</span><span class="sxs-lookup"><span data-stu-id="451dd-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="451dd-134">Gdy <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> jest używany i `T` implementuje `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="451dd-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="451dd-135">Elementy, które ***nie*** są zwracane do puli, zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="451dd-135">Items that are ***not*** returned to the pool will be disposed.</span></span>
* <span data-ttu-id="451dd-136">Gdy pula zostanie usunięta przez DI, wszystkie elementy w puli zostaną usunięte.</span><span class="sxs-lookup"><span data-stu-id="451dd-136">When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="451dd-137">Uwaga: po usunięciu puli:</span><span class="sxs-lookup"><span data-stu-id="451dd-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="451dd-138">Wywoływanie `Get` zwraca `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="451dd-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="451dd-139">`return`Usuwa dany element.</span><span class="sxs-lookup"><span data-stu-id="451dd-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="451dd-140">Przykład ObjectPool</span><span class="sxs-lookup"><span data-stu-id="451dd-140">ObjectPool sample</span></span>

<span data-ttu-id="451dd-141">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="451dd-141">The following code:</span></span>

* <span data-ttu-id="451dd-142">Dodaje `ObjectPoolProvider` do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="451dd-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="451dd-143">Dodaje i konfiguruje `ObjectPool<StringBuilder>` do kontenera di.</span><span class="sxs-lookup"><span data-stu-id="451dd-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="451dd-144">Dodaje `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="451dd-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="451dd-145">Poniższy kod implementuje`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="451dd-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
