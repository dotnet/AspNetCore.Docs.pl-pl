---
title: Niezawodne usługi gRPC z terminami i anulowaniem
author: jamesnk
description: Dowiedz się, jak tworzyć niezawodne usługi gRPC z terminami i anulowaniem w programie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: 59b737a032ea37a554ad5ddd0f4d44e4e1602d88
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594441"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Niezawodne usługi gRPC z terminami i anulowaniem

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Terminy i anulowania są funkcjami używanymi przez klientów gRPC do przerwania wywołań w toku. W tym artykule omówiono, dlaczego terminy i anulowania są ważne oraz jak korzystać z nich w aplikacjach .NET gRPC.

## <a name="deadlines"></a>Terminy

Termin ostateczny pozwala klientowi gRPC określić, jak długo będzie czekać na ukończenie wywołania. Po przekroczeniu ostatecznego terminu wywołania zostanie anulowane. Ustawienie terminu ostatecznego jest ważne, ponieważ zapewnia górny limit czasu, w którym można uruchomić wywołanie. Usługa błędna uniemożliwia uruchamianie nieograniczonego i wyczerpania zasobów serwera. Terminy są przydatnym narzędziem do tworzenia niezawodnych aplikacji i powinny być skonfigurowane.

Konfiguracja terminu ostatecznego:

* Termin zostanie skonfigurowany przy użyciu `CallOptions.Deadline` podczas wywołania.
* Brak domyślnej wartości terminu ostatecznego. wywołania gRPC nie są ograniczane czasowo, chyba że zostanie określony termin ostateczny.
* Termin ostateczny to czas UTC, po upływie którego zostanie przekroczony termin. Na przykład `DateTime.UtcNow.AddSeconds(5)` jest to termin wynoszący 5 sekund od teraz.
* Jeśli zostanie użyta Ostatnia lub bieżąca godzina, wywołanie od razu przekroczy termin ostateczny.
* Termin ostateczny jest wysyłany z wywołaniem gRPC do usługi i jest niezależnie śledzony przez klienta i usługę. Istnieje możliwość, że wywołanie gRPC kończy się na jednej maszynie, ale przez czas odpowiedzi zwróconej do klienta został przekroczony termin ostateczny.

W przypadku przekroczenia terminu ostatecznego klient i usługa mają inne zachowanie:

* Klient natychmiast przerywa bazowe żądanie HTTP i zgłasza `DeadlineExceeded` błąd. Aplikacja kliencka może zdecydować się na przechwycenie błędu i wyświetlenie użytkownikowi komunikatu o przekroczeniu limitu czasu.
* Na serwerze, wykonywane żądanie HTTP zostało przerwane i zostanie zgłoszony [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) . Mimo że żądanie HTTP zostało przerwane, wywołanie gRPC będzie nadal działać na serwerze do momentu zakończenia metody. Ważne jest, aby token anulowania został przesłany do metod asynchronicznych, aby zostały anulowane wraz z wywołaniem. Na przykład przekazywanie tokenu anulowania do asynchronicznych zapytań bazy danych i żądań HTTP. Przekazywanie tokenu anulowania pozwala na szybkie zakończenie wywołania na serwerze i zwolnienie zasobów dla innych wywołań.

Skonfiguruj, `CallOptions.Deadline` Aby ustawić ostateczny termin wywołania gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Korzystanie `ServerCallContext.CancellationToken` z usługi gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Propagowanie terminów

Gdy wywołanie gRPC jest nawiązywane z wykonywanej usługi gRPC, termin powinien zostać rozpropagowany. Na przykład:

1. Wywołania aplikacji klienta `FrontendService.GetUser` z terminem ostatecznym.
2. `FrontendService` wywołania `UserService.GetUser` . Termin określony przez klienta powinien być określony przy użyciu nowego wywołania gRPC.
3. `UserService.GetUser` otrzymuje termin. Przekroczenie limitu czasu, jeśli zostanie przekroczony ostateczny termin aplikacji klienta.

Kontekst wywołania zawiera termin z `ServerCallContext.Deadline` :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

Ręczne propagowanie terminów może być kłopotliwe. Termin ostateczny musi zostać przesłany do każdego wywołania i można go łatwo przypadkowo pominąć. Automatyczne rozwiązanie jest dostępne z fabryką klienta gRPC. Określanie `EnableCallContextPropagation` :

* Automatycznie propaguje ostateczny termin i token anulowania do wywołań podrzędnych.
* Jest doskonałym sposobem zapewnienia, że złożone, zagnieżdżone scenariusze gRPC zawsze propagują termin i anulowanie.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Aby uzyskać więcej informacji, zobacz <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Anulowanie

Anulowanie umożliwia klientowi gRPC anulowanie długotrwałych wywołań, które nie są już potrzebne. Na przykład wywołanie gRPC, które powoduje, że aktualizacje w czasie rzeczywistym są uruchamiane, gdy użytkownik odwiedzi stronę w witrynie sieci Web. Strumień powinien zostać anulowany, gdy użytkownik nawiguje poza stroną.

Wywołanie gRPC można anulować na kliencie, przekazując token anulowania z [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) lub wywołując `Dispose` wywołanie.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

usługi gRPC, które można anulować, powinny:
* Przekaż `ServerCallContext.CancellationToken` do metod asynchronicznych. Anulowanie metod asynchronicznych umożliwia szybkie wykonywanie wywołań na serwerze.
* Propaguj token anulowania do wywołań podrzędnych. Propagowanie tokenu anulowania gwarantuje, że wywołania podrzędne są anulowane przy użyciu ich elementu nadrzędnego. [gRPC fabrykę klienta](xref:grpc/clientfactory) i `EnableCallContextPropagation()` automatycznie propaguje token anulowania.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/client>
* <xref:grpc/clientfactory>
