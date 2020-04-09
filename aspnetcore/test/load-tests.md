---
title: ASP.NET Podstawowe testy obciążenia/warunków skrajnych
author: Jeremy-Meng
description: Dowiedz się więcej o kilku godnych uwagi narzędziach i podejściach do testowania obciążenia i testowania warunków skrajnych ASP.NET aplikacji Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664690"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="079a1-103">ASP.NET Podstawowe testy obciążenia/warunków skrajnych</span><span class="sxs-lookup"><span data-stu-id="079a1-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="079a1-104">Testowanie obciążenia i testy warunków skrajnych są ważne, aby upewnić się, że aplikacja internetowa jest wydajna i skalowalna.</span><span class="sxs-lookup"><span data-stu-id="079a1-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="079a1-105">Ich cele są różne, mimo że często mają podobne testy.</span><span class="sxs-lookup"><span data-stu-id="079a1-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="079a1-106">**Testy** &ndash; obciążenia Sprawdź, czy aplikacja może obsługiwać określone obciążenie użytkowników dla określonego scenariusza, jednocześnie spełniając cel odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="079a1-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="079a1-107">Aplikacja jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="079a1-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="079a1-108">**Testy warunków skrajnych** &ndash; Test stabilności aplikacji podczas pracy w ekstremalnych warunkach, często przez długi okres czasu.</span><span class="sxs-lookup"><span data-stu-id="079a1-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="079a1-109">Testy umieszczają duże obciążenie użytkownika, skoki lub stopniowo zwiększające obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="079a1-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="079a1-110">Testy warunków skrajnych określają, czy aplikacja pod wpływem stresu może odzyskać po awarii i bezpiecznie powrócić do oczekiwanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="079a1-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="079a1-111">Pod wpływem stresu aplikacja nie jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="079a1-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="079a1-112">Visual Studio 2019 to ostatnia wersja programu Visual Studio z funkcjami testu obciążenia.</span><span class="sxs-lookup"><span data-stu-id="079a1-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="079a1-113">Dla klientów wymagających narzędzi do testowania obciążenia w przyszłości zalecamy alternatywne narzędzia, takie jak Apache JMeter, Akamai CloudTest i BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="079a1-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="079a1-114">Aby uzyskać więcej informacji, zobacz [informacje o wersji programu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="079a1-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="079a1-115">Narzędzia programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="079a1-115">Visual Studio tools</span></span>

<span data-ttu-id="079a1-116">Visual Studio umożliwia użytkownikom tworzenie, tworzenie i debugowanie testów wydajności sieci web i ładowania.</span><span class="sxs-lookup"><span data-stu-id="079a1-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="079a1-117">Dostępna jest opcja tworzenia testów przez rejestrowanie akcji w przeglądarce internetowej.</span><span class="sxs-lookup"><span data-stu-id="079a1-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="079a1-118">Aby uzyskać informacje na temat tworzenia, konfigurowania i uruchamiania projektów testów obciążenia przy użyciu programu Visual Studio 2017, zobacz [Szybki start: Tworzenie projektu testu obciążenia](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="079a1-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="079a1-119">Testy obciążenia można skonfigurować do uruchamiania lokalnie lub uruchamiania w chmurze przy użyciu usługi Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="079a1-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="079a1-120">Narzędzia innych firm</span><span class="sxs-lookup"><span data-stu-id="079a1-120">Third-party tools</span></span>

<span data-ttu-id="079a1-121">Poniższa lista zawiera narzędzia wydajności sieci web innych firm z różnymi zestawami funkcji:</span><span class="sxs-lookup"><span data-stu-id="079a1-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="079a1-122">Miernik Apache</span><span class="sxs-lookup"><span data-stu-id="079a1-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="079a1-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="079a1-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="079a1-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="079a1-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="079a1-125">k6</span><span class="sxs-lookup"><span data-stu-id="079a1-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="079a1-126">Locust</span><span class="sxs-lookup"><span data-stu-id="079a1-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="079a1-127">Zachodni Wiatr WebSurge</span><span class="sxs-lookup"><span data-stu-id="079a1-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="079a1-128">Siatki</span><span class="sxs-lookup"><span data-stu-id="079a1-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="079a1-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="079a1-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

