---
title: ASP.NET Core SignalR klienci
author: bradygaster
description: Dowiedz się, które funkcje są obsługiwane przez różnych SignalR klientów ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/client-features
ms.openlocfilehash: df0084411134167df31506a01362a0520821e577
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631761"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>ASP.NET Core SignalR klienci

## <a name="versioning-support-and-compatibility"></a>Przechowywanie wersji, obsługa i zgodność

SignalRKlienci znajdują się obok składników serwera i są zgodni z wersjami. Każdy obsługiwany klient może bezpiecznie połączyć się z dowolnym obsługiwanym serwerem, a wszelkie problemy ze zgodnością będą traktowane jako usterki. SignalR Klienci są obsługiwani w tym samym cyklu pomocy technicznej co w przypadku platformy .NET Core. Aby uzyskać szczegółowe informacje [, zobacz zasady obsługi .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

Wiele funkcji wymaga zgodnego klienta **i** serwera. Poniżej znajduje się tabela przedstawiająca minimalne wersje różnych funkcji.

Wersje 1. x są SignalR mapowane do wersji .NET Core 2,1 i 2,2 i mają ten sam okres istnienia. W wersji 3. x i nowszych SignalR wersja dokładnie pasuje do pozostałej części platformy .NET i ma ten sam cykl pomocy technicznej.

| Wersja programu SignalR | Wersja platformy .NET Core | Poziom pomocy technicznej | Koniec wsparcia |
| - | - | - | - |
| 1.0. x | 2.1. x | Długoterminowa pomoc techniczna | 21 sierpnia 2021 |
| 1.1. x | 2.2. x | Koniec okresu istnienia | 23 grudnia, 2019 |
| 3. x lub nowszy | *taka sama jak SignalR wersja* | Zobacz [zasady pomocy technicznej platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**Uwaga:** W ASP.NET Core 3,0 klient JavaScript został *przeniesiony* do `@microsoft/signalr` pakietu npm.

## <a name="feature-distribution"></a>Dystrybucja funkcji

W poniższej tabeli przedstawiono funkcje i obsługę klientów, którzy oferują pomoc techniczną w czasie rzeczywistym. Dla każdej funkcji jest wyświetlana *minimalna* wersja obsługująca tę funkcję. Jeśli żadna wersja nie jest wymieniona, ta funkcja nie jest obsługiwana.

| Cechy | Serwer | Klient .NET | Klient JavaScript | Klient Java |
| ---- | :-: | :-: | :-: | :-: |
| SignalRObsługa usługi platformy Azure |2.1.0|1.0.0|1.0.0|1.0.0|
| [Przesyłanie strumieniowe między serwerami i klientami](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Przesyłanie strumieniowe klient-serwer](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Automatyczne ponowne łączenie ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Transport gniazd WebSockets |2.1.0|1.0.0|1.0.0|1.0.0|
| Transport zdarzeń wysłanych przez serwer |2.1.0|1.0.0|1.0.0|❌|
| Długotrwały transport sondowania |2.1.0|1.0.0|1.0.0|3.0.0|
| Protokół centrum JSON |2.1.0|1.0.0|1.0.0|1.0.0|
| Protokół centrum MessagePack |2.1.0|1.0.0|1.0.0|❌|

Obsługa włączania dodatkowych funkcji klienta jest śledzona w [naszym monitorze problemów](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wprowadzenie SignalR do ASP.NET Core](xref:tutorials/signalr)
* [Obsługiwane platformy](xref:signalr/supported-platforms)
* [Koncentratory](xref:signalr/hubs)
* [Klient JavaScript](xref:signalr/javascript-client)
