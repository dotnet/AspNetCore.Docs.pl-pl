---
title: ASP.NET Core testowanie obciążenia/obciążeniowego
author: Jeremy-Meng
description: Poznaj kilka istotnych narzędzi i metod testowania obciążeniowego i testowania obciążeniowego ASP.NET Core aplikacji.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
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
ms.openlocfilehash: b433c29b0c959925b996142ccfab177c27183cc4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021916"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="b29b1-103">ASP.NET Core testowanie obciążenia/obciążeniowego</span><span class="sxs-lookup"><span data-stu-id="b29b1-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="b29b1-104">Testowanie obciążeniowe i testowanie obciążeniowe są ważne, aby zapewnić, że aplikacja sieci Web jest wydajna i skalowalna.</span><span class="sxs-lookup"><span data-stu-id="b29b1-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="b29b1-105">Ich cele są różne, chociaż często korzystają z podobnych testów.</span><span class="sxs-lookup"><span data-stu-id="b29b1-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="b29b1-106">**Testy obciążenia**: należy sprawdzić, czy aplikacja może obsłużyć określone obciążenie użytkownikami w pewnym scenariuszu, zachowując jednocześnie cel odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b29b1-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="b29b1-107">Aplikacja jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="b29b1-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="b29b1-108">**Testy obciążeniowe**: testowanie stabilności aplikacji podczas uruchamiania w skrajnych warunkach, często przez długi czas.</span><span class="sxs-lookup"><span data-stu-id="b29b1-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="b29b1-109">Testy zapewniają duże obciążenie użytkownikami, przerastają lub stopniowo zwiększają obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b29b1-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="b29b1-110">Testy obciążeniowe określają, czy aplikacja poddawana obciążeniom może odzyskać sprawność po awarii i bezpiecznie wrócić do oczekiwanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="b29b1-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="b29b1-111">W obszarze naprężenie aplikacja nie jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="b29b1-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="b29b1-112">Program Visual Studio 2019 ogłosił plany [zaniechania testowania obciążenia](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="b29b1-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="b29b1-113">Odpowiednia usługa testowania obciążenia opartego na chmurze platformy Azure DevOps została zamknięta.</span><span class="sxs-lookup"><span data-stu-id="b29b1-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="b29b1-114">Narzędzia innych firm</span><span class="sxs-lookup"><span data-stu-id="b29b1-114">Third-party tools</span></span>

<span data-ttu-id="b29b1-115">Poniższa lista zawiera narzędzia do oceny wydajności sieci Web innych firm z różnymi zestawami funkcji:</span><span class="sxs-lookup"><span data-stu-id="b29b1-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="b29b1-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="b29b1-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="b29b1-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="b29b1-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="b29b1-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="b29b1-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="b29b1-119">k6</span><span class="sxs-lookup"><span data-stu-id="b29b1-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="b29b1-120">Chleb</span><span class="sxs-lookup"><span data-stu-id="b29b1-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="b29b1-121">Moje przepięcio zachodnie wiatru</span><span class="sxs-lookup"><span data-stu-id="b29b1-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="b29b1-122">W ramach</span><span class="sxs-lookup"><span data-stu-id="b29b1-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="b29b1-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="b29b1-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)