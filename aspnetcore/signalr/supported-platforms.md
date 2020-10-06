---
title: ASP.NET Core SignalR obsługiwane platformy
author: bradygaster
description: Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754505"
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

## <a name="net-client"></a>Klient .NET

[Klient platformy .NET](xref:signalr/dotnet-client) działa na dowolnej platformie obsługiwanej przez ASP.NET Core. Na przykład [Deweloperzy platformy Xamarin mogą używać SignalR ](https://github.com/aspnet/Announcements/issues/305) do tworzenia aplikacji dla systemu Android za pomocą platformy Xamarin. Android 8.4.0.1 i nowszych oraz aplikacji systemu iOS przy użyciu platformy Xamarin. iOS 11.14.0.4 i nowszych.

Jeśli na serwerze są uruchomione usługi IIS, transport gniazd internetowych wymaga usług IIS 8,0 lub nowszych w systemie Windows Server 2012 lub nowszym. Inne transporty są obsługiwane na wszystkich platformach.

## <a name="java-client"></a>Klient Java

[Klient Java](xref:signalr/java-client) obsługuje język Java 8 i nowsze wersje.

## <a name="unsupported-clients"></a>Nieobsługiwane klienci

Następujący klienci są dostępni, ale są eksperymentalni lub nieoficjalni. Nie są one obecnie obsługiwane i mogą nie być dostępne.

* [Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
