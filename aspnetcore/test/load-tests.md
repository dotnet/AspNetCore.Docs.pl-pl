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
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Podstawowe testy obciążenia/warunków skrajnych

Testowanie obciążenia i testy warunków skrajnych są ważne, aby upewnić się, że aplikacja internetowa jest wydajna i skalowalna. Ich cele są różne, mimo że często mają podobne testy.

**Testy** &ndash; obciążenia Sprawdź, czy aplikacja może obsługiwać określone obciążenie użytkowników dla określonego scenariusza, jednocześnie spełniając cel odpowiedzi. Aplikacja jest uruchamiana w normalnych warunkach.

**Testy warunków skrajnych** &ndash; Test stabilności aplikacji podczas pracy w ekstremalnych warunkach, często przez długi okres czasu. Testy umieszczają duże obciążenie użytkownika, skoki lub stopniowo zwiększające obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.

Testy warunków skrajnych określają, czy aplikacja pod wpływem stresu może odzyskać po awarii i bezpiecznie powrócić do oczekiwanego zachowania. Pod wpływem stresu aplikacja nie jest uruchamiana w normalnych warunkach.

Visual Studio 2019 to ostatnia wersja programu Visual Studio z funkcjami testu obciążenia. Dla klientów wymagających narzędzi do testowania obciążenia w przyszłości zalecamy alternatywne narzędzia, takie jak Apache JMeter, Akamai CloudTest i BlazeMeter. Aby uzyskać więcej informacji, zobacz [informacje o wersji programu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Narzędzia programu Visual Studio

Visual Studio umożliwia użytkownikom tworzenie, tworzenie i debugowanie testów wydajności sieci web i ładowania. Dostępna jest opcja tworzenia testów przez rejestrowanie akcji w przeglądarce internetowej.

Aby uzyskać informacje na temat tworzenia, konfigurowania i uruchamiania projektów testów obciążenia przy użyciu programu Visual Studio 2017, zobacz [Szybki start: Tworzenie projektu testu obciążenia](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Testy obciążenia można skonfigurować do uruchamiania lokalnie lub uruchamiania w chmurze przy użyciu usługi Azure DevOps.

## <a name="third-party-tools"></a>Narzędzia innych firm

Poniższa lista zawiera narzędzia wydajności sieci web innych firm z różnymi zestawami funkcji:

* [Miernik Apache](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [Zachodni Wiatr WebSurge](https://websurge.west-wind.com/)
* [Siatki](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

