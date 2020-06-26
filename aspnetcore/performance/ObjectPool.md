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
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Ponowne użycie obiektu za pomocą ObjectPool w ASP.NET Core

[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)i [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool>jest częścią infrastruktury ASP.NET Core, która obsługuje przechowywanie w pamięci grupy obiektów do ponownego użycia, a nie pozwala na wyrzucanie obiektów jako elementów bezużytecznych.

Może być konieczne użycie puli obiektów, jeśli zarządzane obiekty są następujące:

- Kosztowne, aby przydzielić/zainicjować.
- Reprezentuje ograniczony zasób.
- Używane do przewidywania i często.

Na przykład, struktura ASP.NET Core używa puli obiektów w niektórych miejscach do ponownego użycia <xref:System.Text.StringBuilder> wystąpień. `StringBuilder`przypisuje własne bufory i zarządza nimi do przechowywania danych znakowych. ASP.NET Core regularnie używa `StringBuilder` do implementowania funkcji, a ich użycie umożliwia korzystanie z zalet wydajności.

Buforowanie obiektów nie zawsze poprawia wydajność:

- Chyba że koszt inicjacji obiektu jest wysoki, zwykle jest wolniejsze Pobieranie obiektu z puli.
- Obiekty zarządzane przez pulę nie są przydzielone do momentu cofnięcia przydziału puli.

Używaj buforowania obiektów tylko po zebraniu danych wydajności przy użyciu realistycznych scenariuszy dla aplikacji lub biblioteki.

**Ostrzeżenie: `ObjectPool` nie implementuje `IDisposable` . Nie zalecamy używania jej z typami, które wymagają usunięcia.**

**Uwaga: ObjectPool nie nakłada limitu liczby obiektów, które zostanie przydzielone, spowoduje ograniczenie liczby obiektów zachowywanych.**

## <a name="concepts"></a>Pojęcia

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>— Abstrakcja puli obiektów podstawowych. Służy do pobierania i zwracania obiektów.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implementuje ten element, aby dostosować sposób tworzenia obiektu i sposobu jego *resetowania* w przypadku powrotu do puli. Ten element może zostać przesłany do puli obiektów, która została skonstruowana bezpośrednio... ORAZ

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>pełni rolę fabryki do tworzenia pul obiektów.
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool może być używana w aplikacji na wiele sposobów:

* Tworzenie wystąpienia puli.
* Rejestrowanie puli w [iniekcji zależności](xref:fundamentals/dependency-injection) (di) jako wystąpienie.
* Rejestracja `ObjectPoolProvider<>` w programie i używanie jej w ramach fabryki.

## <a name="how-to-use-objectpool"></a>Jak używać ObjectPool

Wywołanie metody <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> Get obiektu i <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> zwrócenia obiektu.  Nie jest wymagane, aby zwrócić każdy obiekt. Jeśli nie zwracasz obiektu, zostanie on wyrzucony jako elementy bezużyteczne.

## <a name="objectpool-sample"></a>Przykład ObjectPool

Następujący kod:

* Dodaje `ObjectPoolProvider` do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) (di).
* Dodaje i konfiguruje `ObjectPool<StringBuilder>` do kontenera di.
* Dodaje `BirthdayMiddleware` .

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Poniższy kod implementuje`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
