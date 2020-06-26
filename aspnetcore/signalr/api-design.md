---
title: SignalRZagadnienia dotyczące projektowania interfejsu API
author: anurse
description: Dowiedz się, jak projektować SignalR interfejsy API pod kątem zgodności między wersjami aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/api-design
ms.openlocfilehash: 9ad8d30da552d3d3084534b8c7ca57386ad111ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407801"
---
# <a name="signalr-api-design-considerations"></a>SignalR<span data-ttu-id="d8822-103">Zagadnienia dotyczące projektowania interfejsu API</span><span class="sxs-lookup"><span data-stu-id="d8822-103"> API design considerations</span></span>

<span data-ttu-id="d8822-104">Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d8822-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="d8822-105">Ten artykuł zawiera wskazówki dotyczące kompilowania SignalR interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="d8822-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="d8822-106">Używanie niestandardowych parametrów obiektów w celu zapewnienia zgodności z poprzednimi wersjami</span><span class="sxs-lookup"><span data-stu-id="d8822-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="d8822-107">Dodawanie parametrów do SignalR metody centrum (na kliencie lub serwerze) jest istotną *zmianą*.</span><span class="sxs-lookup"><span data-stu-id="d8822-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="d8822-108">Oznacza to, że starsi klienci/serwery będą otrzymywać błędy podczas próby wywołania metody bez odpowiedniej liczby parametrów.</span><span class="sxs-lookup"><span data-stu-id="d8822-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="d8822-109">Jednak Dodawanie właściwości do niestandardowego parametru obiektu **nie** jest istotną zmianą.</span><span class="sxs-lookup"><span data-stu-id="d8822-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="d8822-110">Może to służyć do projektowania zgodnych interfejsów API, które są odporne na zmiany na kliencie lub serwerze.</span><span class="sxs-lookup"><span data-stu-id="d8822-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="d8822-111">Rozważmy na przykład interfejs API po stronie serwera, podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="d8822-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="d8822-112">Klient JavaScript wywołuje tę metodę, wykonując `invoke` następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d8822-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="d8822-113">Jeśli później dodasz drugi parametr do metody serwera, starsi klienci nie będą podawać tej wartości parametru.</span><span class="sxs-lookup"><span data-stu-id="d8822-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="d8822-114">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d8822-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="d8822-115">Gdy stary klient próbuje wywołać tę metodę, zostanie wyświetlony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="d8822-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="d8822-116">Na serwerze zobaczysz komunikat dziennika następujący:</span><span class="sxs-lookup"><span data-stu-id="d8822-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="d8822-117">Stary klient wysłał tylko jeden parametr, ale nowszy interfejs API serwera wymaga dwóch parametrów.</span><span class="sxs-lookup"><span data-stu-id="d8822-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="d8822-118">Używanie obiektów niestandardowych jako parametrów zapewnia większą elastyczność.</span><span class="sxs-lookup"><span data-stu-id="d8822-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="d8822-119">Przejdźmy do projektu oryginalnego interfejsu API, aby użyć niestandardowego obiektu:</span><span class="sxs-lookup"><span data-stu-id="d8822-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="d8822-120">Teraz klient używa obiektu do wywołania metody:</span><span class="sxs-lookup"><span data-stu-id="d8822-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="d8822-121">Zamiast dodawać parametr, Dodaj właściwość do `TotalLengthRequest` obiektu:</span><span class="sxs-lookup"><span data-stu-id="d8822-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="d8822-122">Gdy stary klient wysyła jeden parametr, dodatkowa `Param2` Właściwość zostanie pozostawiona `null` .</span><span class="sxs-lookup"><span data-stu-id="d8822-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="d8822-123">Możesz wykryć komunikat wysyłany przez starszego klienta, sprawdzając, czy `Param2` dla `null` i Zastosuj wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="d8822-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="d8822-124">Nowy klient może wysyłać oba parametry.</span><span class="sxs-lookup"><span data-stu-id="d8822-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="d8822-125">Ta sama technika działa w przypadku metod zdefiniowanych na kliencie.</span><span class="sxs-lookup"><span data-stu-id="d8822-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="d8822-126">Można wysłać obiekt niestandardowy po stronie serwera:</span><span class="sxs-lookup"><span data-stu-id="d8822-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="d8822-127">Po stronie klienta uzyskuje się dostęp do `Message` właściwości, a nie za pomocą parametru:</span><span class="sxs-lookup"><span data-stu-id="d8822-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="d8822-128">Jeśli później zdecydujesz się dodać nadawcę wiadomości do ładunku, Dodaj właściwość do obiektu:</span><span class="sxs-lookup"><span data-stu-id="d8822-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="d8822-129">Starsze klienci nie będą oczekiwać `Sender` wartości, więc zignorują ją.</span><span class="sxs-lookup"><span data-stu-id="d8822-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="d8822-130">Nowy klient może go zaakceptować przez aktualizację w celu odczytania nowej właściwości:</span><span class="sxs-lookup"><span data-stu-id="d8822-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="d8822-131">W takim przypadku nowy klient jest również odporny na stary serwer, który nie udostępnia `Sender` wartości.</span><span class="sxs-lookup"><span data-stu-id="d8822-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="d8822-132">Ponieważ stary serwer nie będzie dostarczać `Sender` wartości, klient sprawdza, czy nie istnieje przed uzyskaniem dostępu do niego.</span><span class="sxs-lookup"><span data-stu-id="d8822-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
