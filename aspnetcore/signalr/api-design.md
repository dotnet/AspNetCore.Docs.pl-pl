---
title: SignalR Zagadnienia dotyczące projektowania interfejsu API
author: anurse
description: Dowiedz się, jak projektować SignalR interfejsy API pod kątem zgodności między wersjami aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059653"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR Zagadnienia dotyczące projektowania interfejsu API

Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)

Ten artykuł zawiera wskazówki dotyczące kompilowania SignalR interfejsów API.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Używanie niestandardowych parametrów obiektów w celu zapewnienia zgodności z poprzednimi wersjami

Dodawanie parametrów do SignalR metody centrum (na kliencie lub serwerze) jest istotną *zmianą* . Oznacza to, że starsi klienci/serwery będą otrzymywać błędy podczas próby wywołania metody bez odpowiedniej liczby parametrów. Jednak Dodawanie właściwości do niestandardowego parametru obiektu **nie** jest istotną zmianą. Może to służyć do projektowania zgodnych interfejsów API, które są odporne na zmiany na kliencie lub serwerze.

Rozważmy na przykład interfejs API po stronie serwera, podobny do następującego:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Klient JavaScript wywołuje tę metodę, wykonując `invoke` następujące czynności:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Jeśli później dodasz drugi parametr do metody serwera, starsi klienci nie będą podawać tej wartości parametru. Przykład:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Gdy stary klient próbuje wywołać tę metodę, zostanie wyświetlony następujący błąd:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Na serwerze zobaczysz komunikat dziennika następujący:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Stary klient wysłał tylko jeden parametr, ale nowszy interfejs API serwera wymaga dwóch parametrów. Używanie obiektów niestandardowych jako parametrów zapewnia większą elastyczność. Przejdźmy do projektu oryginalnego interfejsu API, aby użyć niestandardowego obiektu:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Teraz klient używa obiektu do wywołania metody:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Zamiast dodawać parametr, Dodaj właściwość do `TotalLengthRequest` obiektu:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Gdy stary klient wysyła jeden parametr, dodatkowa `Param2` Właściwość zostanie pozostawiona `null` . Możesz wykryć komunikat wysyłany przez starszego klienta, sprawdzając, czy `Param2` dla `null` i Zastosuj wartość domyślną. Nowy klient może wysyłać oba parametry.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Ta sama technika działa w przypadku metod zdefiniowanych na kliencie. Można wysłać obiekt niestandardowy po stronie serwera:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Po stronie klienta uzyskuje się dostęp do `Message` właściwości, a nie za pomocą parametru:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Jeśli później zdecydujesz się dodać nadawcę wiadomości do ładunku, Dodaj właściwość do obiektu:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Starsze klienci nie będą oczekiwać `Sender` wartości, więc zignorują ją. Nowy klient może go zaakceptować przez aktualizację w celu odczytania nowej właściwości:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

W takim przypadku nowy klient jest również odporny na stary serwer, który nie udostępnia `Sender` wartości. Ponieważ stary serwer nie będzie dostarczać `Sender` wartości, klient sprawdza, czy nie istnieje przed uzyskaniem dostępu do niego.
