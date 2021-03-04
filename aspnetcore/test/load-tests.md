---
title: ASP.NET Core testowanie obciążenia/obciążeniowego
author: Jeremy-Meng
description: Poznaj kilka istotnych narzędzi i metod testowania obciążeniowego i testowania obciążeniowego ASP.NET Core aplikacji.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 5a860fe398dff2e12671b6854a80bbab3120499b
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109484"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core testowanie obciążenia/obciążeniowego

Testowanie obciążeniowe i testowanie obciążeniowe są ważne, aby zapewnić, że aplikacja sieci Web jest wydajna i skalowalna. Ich cele są różne, chociaż często korzystają z podobnych testów.

**Testy obciążenia**: należy sprawdzić, czy aplikacja może obsłużyć określone obciążenie użytkownikami w pewnym scenariuszu, zachowując jednocześnie cel odpowiedzi. Aplikacja jest uruchamiana w normalnych warunkach.

**Testy obciążeniowe**: testowanie stabilności aplikacji podczas uruchamiania w skrajnych warunkach, często przez długi czas. Testy zapewniają duże obciążenie użytkownikami, przerastają lub stopniowo zwiększają obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.

Testy obciążeniowe określają, czy aplikacja poddawana obciążeniom może odzyskać sprawność po awarii i bezpiecznie wrócić do oczekiwanego zachowania. W obszarze naprężenie aplikacja nie jest uruchamiana w normalnych warunkach.

Program Visual Studio 2019 ogłosił plany [zaniechania testowania obciążenia](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/). Odpowiednia usługa testowania obciążenia opartego na chmurze platformy Azure DevOps została zamknięta.

## <a name="third-party-tools"></a>Narzędzia innych firm

Poniższa lista zawiera narzędzia do oceny wydajności sieci Web innych firm z różnymi zestawami funkcji:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Chleb](https://locust.io/)
* [Moje przepięcio zachodnie wiatru](https://websurge.west-wind.com/)
* [W ramach](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
* [NBomber](https://github.com/PragmaticFlow/NBomber)
