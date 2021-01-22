---
title: ASP.NET Core SignalR obsługiwane platformy
author: bradygaster
description: Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689230"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>ASP.NET Core SignalR obsługiwane platformy

## <a name="server-system-requirements"></a>Wymagania systemowe serwera

SignalR w przypadku ASP.NET Core obsługuje dowolną platformę serwera, która ASP.NET Core obsługiwana przez program.

## <a name="javascript-client"></a>Klient JavaScript

[Klient JavaScript](xref:signalr/javascript-client) działa w NodeJS 8 i nowszych wersjach oraz w następujących przeglądarkach:

| Przeglądarka                          | Wersja         |
| -------------------------------- | --------------- |
| Apple Safari, w tym iOS      | Obecne&dagger; |
| Google Chrome, w tym Android | Obecne&dagger; |
| Microsoft Edge                   | Obecne&dagger; |
| Mozilla Firefox                  | Obecne&dagger; |

&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.

Klient JavaScript nie obsługuje programu Internet Explorer i innych starszych przeglądarek. W przypadku nieobsługiwanych przeglądarek klient może mieć nieoczekiwane zachowanie i błędy.

## <a name="net-client"></a>Klient .NET

[Klient platformy .NET](xref:signalr/dotnet-client) działa na dowolnej platformie obsługiwanej przez ASP.NET Core. Na przykład [Deweloperzy platformy Xamarin mogą używać SignalR ](https://github.com/aspnet/Announcements/issues/305) do tworzenia aplikacji dla systemu Android za pomocą platformy Xamarin. Android 8.4.0.1 i nowszych oraz aplikacji systemu iOS przy użyciu platformy Xamarin. iOS 11.14.0.4 i nowszych.

Jeśli na serwerze są uruchomione usługi IIS, transport gniazd internetowych wymaga usług IIS 8,0 lub nowszych w systemie Windows Server 2012 lub nowszym. Inne transporty są obsługiwane na wszystkich platformach.

## <a name="java-client"></a>Klient Java

[Klient Java](xref:signalr/java-client) obsługuje język Java 8 i nowsze wersje.

## <a name="unsupported-clients"></a>Nieobsługiwane klienci

Następujący klienci są dostępni, ale są eksperymentalni lub nieoficjalni. Nie są one obecnie obsługiwane i mogą nie być dostępne.

* [Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
