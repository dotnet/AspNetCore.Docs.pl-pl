---
title: Niezawodne usługi gRPC z terminami i anulowaniem
author: jamesnk
description: Dowiedz się, jak tworzyć niezawodne usługi gRPC z terminami i anulowaniem w programie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059926"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="fe22d-103">Niezawodne usługi gRPC z terminami i anulowaniem</span><span class="sxs-lookup"><span data-stu-id="fe22d-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="fe22d-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fe22d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fe22d-105">Terminy i anulowania są funkcjami używanymi przez klientów gRPC do przerwania wywołań w toku.</span><span class="sxs-lookup"><span data-stu-id="fe22d-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="fe22d-106">W tym artykule omówiono, dlaczego terminy i anulowania są ważne oraz jak korzystać z nich w aplikacjach .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="fe22d-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="fe22d-107">Terminy</span><span class="sxs-lookup"><span data-stu-id="fe22d-107">Deadlines</span></span>

<span data-ttu-id="fe22d-108">Termin ostateczny pozwala klientowi gRPC określić, jak długo będzie czekać na ukończenie wywołania.</span><span class="sxs-lookup"><span data-stu-id="fe22d-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="fe22d-109">Po przekroczeniu ostatecznego terminu wywołania zostanie anulowane.</span><span class="sxs-lookup"><span data-stu-id="fe22d-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="fe22d-110">Ustawienie terminu ostatecznego jest ważne, ponieważ zapewnia górny limit czasu, w którym można uruchomić wywołanie.</span><span class="sxs-lookup"><span data-stu-id="fe22d-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="fe22d-111">Usługa błędna uniemożliwia uruchamianie nieograniczonego i wyczerpania zasobów serwera.</span><span class="sxs-lookup"><span data-stu-id="fe22d-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="fe22d-112">Terminy są przydatnym narzędziem do tworzenia niezawodnych aplikacji i powinny być skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="fe22d-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="fe22d-113">Konfiguracja terminu ostatecznego:</span><span class="sxs-lookup"><span data-stu-id="fe22d-113">Deadline configuration:</span></span>

* <span data-ttu-id="fe22d-114">Termin zostanie skonfigurowany przy użyciu `CallOptions.Deadline` podczas wywołania.</span><span class="sxs-lookup"><span data-stu-id="fe22d-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="fe22d-115">Brak domyślnej wartości terminu ostatecznego.</span><span class="sxs-lookup"><span data-stu-id="fe22d-115">There is no default deadline value.</span></span> <span data-ttu-id="fe22d-116">wywołania gRPC nie są ograniczane czasowo, chyba że zostanie określony termin ostateczny.</span><span class="sxs-lookup"><span data-stu-id="fe22d-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="fe22d-117">Termin ostateczny to czas UTC, po upływie którego zostanie przekroczony termin.</span><span class="sxs-lookup"><span data-stu-id="fe22d-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="fe22d-118">Na przykład `DateTime.UtcNow.AddSeconds(5)` jest to termin wynoszący 5 sekund od teraz.</span><span class="sxs-lookup"><span data-stu-id="fe22d-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="fe22d-119">Jeśli zostanie użyta Ostatnia lub bieżąca godzina, wywołanie od razu przekroczy termin ostateczny.</span><span class="sxs-lookup"><span data-stu-id="fe22d-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="fe22d-120">Termin ostateczny jest wysyłany z wywołaniem gRPC do usługi i jest niezależnie śledzony przez klienta i usługę.</span><span class="sxs-lookup"><span data-stu-id="fe22d-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="fe22d-121">Istnieje możliwość, że wywołanie gRPC kończy się na jednej maszynie, ale przez czas odpowiedzi zwróconej do klienta został przekroczony termin ostateczny.</span><span class="sxs-lookup"><span data-stu-id="fe22d-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="fe22d-122">W przypadku przekroczenia terminu ostatecznego klient i usługa mają inne zachowanie:</span><span class="sxs-lookup"><span data-stu-id="fe22d-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="fe22d-123">Klient natychmiast przerywa bazowe żądanie HTTP i zgłasza `DeadlineExceeded` błąd.</span><span class="sxs-lookup"><span data-stu-id="fe22d-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="fe22d-124">Aplikacja kliencka może zdecydować się na przechwycenie błędu i wyświetlenie użytkownikowi komunikatu o przekroczeniu limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="fe22d-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="fe22d-125">Na serwerze, wykonywane żądanie HTTP zostało przerwane i zostanie zgłoszony [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) .</span><span class="sxs-lookup"><span data-stu-id="fe22d-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="fe22d-126">Mimo że żądanie HTTP zostało przerwane, wywołanie gRPC będzie nadal działać na serwerze do momentu zakończenia metody.</span><span class="sxs-lookup"><span data-stu-id="fe22d-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="fe22d-127">Ważne jest, aby token anulowania został przesłany do metod asynchronicznych, aby zostały anulowane wraz z wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="fe22d-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="fe22d-128">Na przykład przekazywanie tokenu anulowania do asynchronicznych zapytań bazy danych i żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe22d-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="fe22d-129">Przekazywanie tokenu anulowania pozwala na szybkie zakończenie wywołania na serwerze i zwolnienie zasobów dla innych wywołań.</span><span class="sxs-lookup"><span data-stu-id="fe22d-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="fe22d-130">Skonfiguruj, `CallOptions.Deadline` Aby ustawić ostateczny termin wywołania gRPC:</span><span class="sxs-lookup"><span data-stu-id="fe22d-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="fe22d-131">Korzystanie `ServerCallContext.CancellationToken` z usługi gRPC:</span><span class="sxs-lookup"><span data-stu-id="fe22d-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="fe22d-132">Propagowanie terminów</span><span class="sxs-lookup"><span data-stu-id="fe22d-132">Propagating deadlines</span></span>

<span data-ttu-id="fe22d-133">Gdy wywołanie gRPC jest nawiązywane z wykonywanej usługi gRPC, termin powinien zostać rozpropagowany.</span><span class="sxs-lookup"><span data-stu-id="fe22d-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="fe22d-134">Przykład:</span><span class="sxs-lookup"><span data-stu-id="fe22d-134">For example:</span></span>

1. <span data-ttu-id="fe22d-135">Wywołania aplikacji klienta `FrontendService.GetUser` z terminem ostatecznym.</span><span class="sxs-lookup"><span data-stu-id="fe22d-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="fe22d-136">`FrontendService` wywołania `UserService.GetUser` .</span><span class="sxs-lookup"><span data-stu-id="fe22d-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="fe22d-137">Termin określony przez klienta powinien być określony przy użyciu nowego wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="fe22d-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="fe22d-138">`UserService.GetUser` otrzymuje termin.</span><span class="sxs-lookup"><span data-stu-id="fe22d-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="fe22d-139">Przekroczenie limitu czasu, jeśli zostanie przekroczony ostateczny termin aplikacji klienta.</span><span class="sxs-lookup"><span data-stu-id="fe22d-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="fe22d-140">Kontekst wywołania zawiera termin z `ServerCallContext.Deadline` :</span><span class="sxs-lookup"><span data-stu-id="fe22d-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="fe22d-141">Ręczne propagowanie terminów może być kłopotliwe.</span><span class="sxs-lookup"><span data-stu-id="fe22d-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="fe22d-142">Termin ostateczny musi zostać przesłany do każdego wywołania i można go łatwo przypadkowo pominąć.</span><span class="sxs-lookup"><span data-stu-id="fe22d-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="fe22d-143">Automatyczne rozwiązanie jest dostępne z fabryką klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="fe22d-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="fe22d-144">Określanie `EnableCallContextPropagation` :</span><span class="sxs-lookup"><span data-stu-id="fe22d-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="fe22d-145">Automatycznie propaguje ostateczny termin i token anulowania do wywołań podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="fe22d-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="fe22d-146">Jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.</span><span class="sxs-lookup"><span data-stu-id="fe22d-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="fe22d-147">Aby uzyskać więcej informacji, zobacz <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="fe22d-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="fe22d-148">Anulowanie</span><span class="sxs-lookup"><span data-stu-id="fe22d-148">Cancellation</span></span>

<span data-ttu-id="fe22d-149">Anulowanie umożliwia klientowi gRPC anulowanie długotrwałych wywołań, które nie są już potrzebne.</span><span class="sxs-lookup"><span data-stu-id="fe22d-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="fe22d-150">Na przykład wywołanie gRPC, które powoduje, że aktualizacje w czasie rzeczywistym są uruchamiane, gdy użytkownik odwiedzi stronę w witrynie sieci Web.</span><span class="sxs-lookup"><span data-stu-id="fe22d-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="fe22d-151">Strumień powinien zostać anulowany, gdy użytkownik nawiguje poza stroną.</span><span class="sxs-lookup"><span data-stu-id="fe22d-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="fe22d-152">Wywołanie gRPC można anulować na kliencie, przekazując token anulowania z [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) lub wywołując `Dispose` wywołanie.</span><span class="sxs-lookup"><span data-stu-id="fe22d-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="fe22d-153">usługi gRPC, które można anulować, powinny:</span><span class="sxs-lookup"><span data-stu-id="fe22d-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="fe22d-154">Przekaż `ServerCallContext.CancellationToken` do metod asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="fe22d-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="fe22d-155">Anulowanie metod asynchronicznych umożliwia szybkie wykonywanie wywołań na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fe22d-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="fe22d-156">Propaguj token anulowania do wywołań podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="fe22d-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="fe22d-157">Propagowanie tokenu anulowania gwarantuje, że wywołania podrzędne są anulowane przy użyciu ich elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="fe22d-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="fe22d-158">[gRPC fabrykę klienta](xref:grpc/clientfactory) i `EnableCallContextPropagation()` automatycznie propaguje token anulowania.</span><span class="sxs-lookup"><span data-stu-id="fe22d-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe22d-159">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fe22d-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
