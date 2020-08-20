---
title: ASP.NET Core testowanie obciążenia/obciążeniowego
author: Jeremy-Meng
description: Poznaj kilka istotnych narzędzi i metod testowania obciążeniowego i testowania obciążeniowego ASP.NET Core aplikacji.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 8e3ca41312922cbf44361601c38e455b342e9fe1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632814"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="c7216-103">ASP.NET Core testowanie obciążenia/obciążeniowego</span><span class="sxs-lookup"><span data-stu-id="c7216-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="c7216-104">Testowanie obciążeniowe i testowanie obciążeniowe są ważne, aby zapewnić, że aplikacja sieci Web jest wydajna i skalowalna.</span><span class="sxs-lookup"><span data-stu-id="c7216-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="c7216-105">Ich cele są różne, chociaż często korzystają z podobnych testów.</span><span class="sxs-lookup"><span data-stu-id="c7216-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="c7216-106">**Testy obciążenia**: należy sprawdzić, czy aplikacja może obsłużyć określone obciążenie użytkownikami w pewnym scenariuszu, zachowując jednocześnie cel odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c7216-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="c7216-107">Aplikacja jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="c7216-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="c7216-108">**Testy obciążeniowe**: testowanie stabilności aplikacji podczas uruchamiania w skrajnych warunkach, często przez długi czas.</span><span class="sxs-lookup"><span data-stu-id="c7216-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="c7216-109">Testy zapewniają duże obciążenie użytkownikami, przerastają lub stopniowo zwiększają obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c7216-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="c7216-110">Testy obciążeniowe określają, czy aplikacja poddawana obciążeniom może odzyskać sprawność po awarii i bezpiecznie wrócić do oczekiwanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="c7216-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="c7216-111">W obszarze naprężenie aplikacja nie jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="c7216-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="c7216-112">Program Visual Studio 2019 ogłosił plany [zaniechania testowania obciążenia](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="c7216-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="c7216-113">Odpowiednia usługa testowania obciążenia opartego na chmurze platformy Azure DevOps została zamknięta.</span><span class="sxs-lookup"><span data-stu-id="c7216-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="c7216-114">Narzędzia innych firm</span><span class="sxs-lookup"><span data-stu-id="c7216-114">Third-party tools</span></span>

<span data-ttu-id="c7216-115">Poniższa lista zawiera narzędzia do oceny wydajności sieci Web innych firm z różnymi zestawami funkcji:</span><span class="sxs-lookup"><span data-stu-id="c7216-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="c7216-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="c7216-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="c7216-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="c7216-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="c7216-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="c7216-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="c7216-119">k6</span><span class="sxs-lookup"><span data-stu-id="c7216-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="c7216-120">Chleb</span><span class="sxs-lookup"><span data-stu-id="c7216-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="c7216-121">Moje przepięcio zachodnie wiatru</span><span class="sxs-lookup"><span data-stu-id="c7216-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="c7216-122">W ramach</span><span class="sxs-lookup"><span data-stu-id="c7216-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="c7216-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="c7216-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)