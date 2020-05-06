---
title: ASP.NET Core SignalR obsługiwane platformy
author: bradygaster
description: Dowiedz się więcej na temat obsługiwanych SignalRplatform dla ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772608"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Obsługiwane platformy ASP.NET Core sygnalizujące

## <a name="server-system-requirements"></a>Wymagania systemowe serwera

Program sygnalizujący dla ASP.NET Core obsługuje dowolną platformę serwera, którą ASP.NET Core obsługuje.

## <a name="javascript-client"></a>Klient JavaScript

[Klient JavaScript](xref:signalr/javascript-client) działa w NodeJS 8 i nowszych wersjach oraz w następujących przeglądarkach:

| Przeglądarka                         | Wersja         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Obecne&dagger; |
| Mozilla Firefox                 | Obecne&dagger; |
| Google Chrome; obejmuje system Android | Obecne&dagger; |
| Safari obejmuje system iOS            | Obecne&dagger; |
| Microsoft Internet Explorer     | 11              |

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
