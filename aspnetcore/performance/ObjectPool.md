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
ms.openlocfilehash: 8244acb39a345875d80c5528a822de23f78b6e38
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403550"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="64982-103">Ponowne użycie obiektu za pomocą ObjectPool w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64982-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="64982-104">[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64982-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64982-105"><xref:Microsoft.Extensions.ObjectPool>jest częścią infrastruktury ASP.NET Core, która obsługuje przechowywanie w pamięci grupy obiektów do ponownego użycia, a nie pozwala na wyrzucanie obiektów jako elementów bezużytecznych.</span><span class="sxs-lookup"><span data-stu-id="64982-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="64982-106">Może być konieczne użycie puli obiektów, jeśli zarządzane obiekty są następujące:</span><span class="sxs-lookup"><span data-stu-id="64982-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="64982-107">Kosztowne, aby przydzielić/zainicjować.</span><span class="sxs-lookup"><span data-stu-id="64982-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="64982-108">Reprezentuje ograniczony zasób.</span><span class="sxs-lookup"><span data-stu-id="64982-108">Represent some limited resource.</span></span>
- <span data-ttu-id="64982-109">Używane do przewidywania i często.</span><span class="sxs-lookup"><span data-stu-id="64982-109">Used predictably and frequently.</span></span>

<span data-ttu-id="64982-110">Na przykład, struktura ASP.NET Core używa puli obiektów w niektórych miejscach do ponownego użycia <xref:System.Text.StringBuilder> wystąpień.</span><span class="sxs-lookup"><span data-stu-id="64982-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="64982-111">`StringBuilder`przypisuje własne bufory i zarządza nimi do przechowywania danych znakowych.</span><span class="sxs-lookup"><span data-stu-id="64982-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="64982-112">ASP.NET Core regularnie używa `StringBuilder` do implementowania funkcji, a ich użycie umożliwia korzystanie z zalet wydajności.</span><span class="sxs-lookup"><span data-stu-id="64982-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="64982-113">Buforowanie obiektów nie zawsze poprawia wydajność:</span><span class="sxs-lookup"><span data-stu-id="64982-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="64982-114">Chyba że koszt inicjacji obiektu jest wysoki, zwykle jest wolniejsze Pobieranie obiektu z puli.</span><span class="sxs-lookup"><span data-stu-id="64982-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="64982-115">Obiekty zarządzane przez pulę nie są przydzielone do momentu cofnięcia przydziału puli.</span><span class="sxs-lookup"><span data-stu-id="64982-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="64982-116">Używaj buforowania obiektów tylko po zebraniu danych wydajności przy użyciu realistycznych scenariuszy dla aplikacji lub biblioteki.</span><span class="sxs-lookup"><span data-stu-id="64982-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="64982-117">**Ostrzeżenie: `ObjectPool` nie implementuje `IDisposable` . Nie zalecamy używania jej z typami, które wymagają usunięcia.**</span><span class="sxs-lookup"><span data-stu-id="64982-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="64982-118">**Uwaga: ObjectPool nie nakłada limitu liczby obiektów, które zostanie przydzielone, spowoduje ograniczenie liczby obiektów zachowywanych.**</span><span class="sxs-lookup"><span data-stu-id="64982-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="64982-119">Pojęcia</span><span class="sxs-lookup"><span data-stu-id="64982-119">Concepts</span></span>

<span data-ttu-id="64982-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>— Abstrakcja puli obiektów podstawowych.</span><span class="sxs-lookup"><span data-stu-id="64982-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="64982-121">Służy do pobierania i zwracania obiektów.</span><span class="sxs-lookup"><span data-stu-id="64982-121">Used to get and return objects.</span></span>

<span data-ttu-id="64982-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implementuje ten element, aby dostosować sposób tworzenia obiektu i sposobu jego *resetowania* w przypadku powrotu do puli.</span><span class="sxs-lookup"><span data-stu-id="64982-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="64982-123">Ten element może zostać przesłany do puli obiektów, która została skonstruowana bezpośrednio... ORAZ</span><span class="sxs-lookup"><span data-stu-id="64982-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="64982-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>pełni rolę fabryki do tworzenia pul obiektów.</span><span class="sxs-lookup"><span data-stu-id="64982-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="64982-125">ObjectPool może być używana w aplikacji na wiele sposobów:</span><span class="sxs-lookup"><span data-stu-id="64982-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="64982-126">Tworzenie wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="64982-126">Instantiating a pool.</span></span>
* <span data-ttu-id="64982-127">Rejestrowanie puli w [iniekcji zależności](xref:fundamentals/dependency-injection) (di) jako wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="64982-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="64982-128">Rejestracja `ObjectPoolProvider<>` w programie i używanie jej w ramach fabryki.</span><span class="sxs-lookup"><span data-stu-id="64982-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="64982-129">Jak używać ObjectPool</span><span class="sxs-lookup"><span data-stu-id="64982-129">How to use ObjectPool</span></span>

<span data-ttu-id="64982-130">Wywołanie metody <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> Get obiektu i <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> zwrócenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="64982-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="64982-131">Nie jest wymagane, aby zwrócić każdy obiekt.</span><span class="sxs-lookup"><span data-stu-id="64982-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="64982-132">Jeśli nie zwracasz obiektu, zostanie on wyrzucony jako elementy bezużyteczne.</span><span class="sxs-lookup"><span data-stu-id="64982-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="64982-133">Przykład ObjectPool</span><span class="sxs-lookup"><span data-stu-id="64982-133">ObjectPool sample</span></span>

<span data-ttu-id="64982-134">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="64982-134">The following code:</span></span>

* <span data-ttu-id="64982-135">Dodaje `ObjectPoolProvider` do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="64982-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="64982-136">Dodaje i konfiguruje `ObjectPool<StringBuilder>` do kontenera di.</span><span class="sxs-lookup"><span data-stu-id="64982-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="64982-137">Dodaje `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="64982-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="64982-138">Poniższy kod implementuje`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="64982-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
