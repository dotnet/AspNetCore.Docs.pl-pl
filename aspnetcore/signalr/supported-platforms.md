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
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="ba7c1-103">Obsługiwane platformy ASP.NET Core sygnalizujące</span><span class="sxs-lookup"><span data-stu-id="ba7c1-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="ba7c1-104">Wymagania systemowe serwera</span><span class="sxs-lookup"><span data-stu-id="ba7c1-104">Server system requirements</span></span>

<span data-ttu-id="ba7c1-105">Program sygnalizujący dla ASP.NET Core obsługuje dowolną platformę serwera, którą ASP.NET Core obsługuje.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="ba7c1-106">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="ba7c1-106">JavaScript client</span></span>

<span data-ttu-id="ba7c1-107">[Klient JavaScript](xref:signalr/javascript-client) działa w NodeJS 8 i nowszych wersjach oraz w następujących przeglądarkach:</span><span class="sxs-lookup"><span data-stu-id="ba7c1-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="ba7c1-108">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="ba7c1-108">Browser</span></span>                         | <span data-ttu-id="ba7c1-109">Wersja</span><span class="sxs-lookup"><span data-stu-id="ba7c1-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="ba7c1-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ba7c1-110">Microsoft Edge</span></span>                  | <span data-ttu-id="ba7c1-111">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba7c1-111">Current&dagger;</span></span> |
| <span data-ttu-id="ba7c1-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="ba7c1-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="ba7c1-113">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba7c1-113">Current&dagger;</span></span> |
| <span data-ttu-id="ba7c1-114">Google Chrome; obejmuje system Android</span><span class="sxs-lookup"><span data-stu-id="ba7c1-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="ba7c1-115">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba7c1-115">Current&dagger;</span></span> |
| <span data-ttu-id="ba7c1-116">Safari obejmuje system iOS</span><span class="sxs-lookup"><span data-stu-id="ba7c1-116">Safari; includes iOS</span></span>            | <span data-ttu-id="ba7c1-117">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba7c1-117">Current&dagger;</span></span> |
| <span data-ttu-id="ba7c1-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ba7c1-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="ba7c1-119">11</span><span class="sxs-lookup"><span data-stu-id="ba7c1-119">11</span></span>              |

<span data-ttu-id="ba7c1-120">&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="ba7c1-121">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="ba7c1-121">.NET client</span></span>

<span data-ttu-id="ba7c1-122">[Klient platformy .NET](xref:signalr/dotnet-client) działa na dowolnej platformie obsługiwanej przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="ba7c1-123">Na przykład [Deweloperzy platformy Xamarin mogą używać SignalR ](https://github.com/aspnet/Announcements/issues/305) do tworzenia aplikacji dla systemu Android za pomocą platformy Xamarin. Android 8.4.0.1 i nowszych oraz aplikacji systemu iOS przy użyciu platformy Xamarin. iOS 11.14.0.4 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="ba7c1-124">Jeśli na serwerze są uruchomione usługi IIS, transport gniazd internetowych wymaga usług IIS 8,0 lub nowszych w systemie Windows Server 2012 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="ba7c1-125">Inne transporty są obsługiwane na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="ba7c1-126">Klient Java</span><span class="sxs-lookup"><span data-stu-id="ba7c1-126">Java client</span></span>

<span data-ttu-id="ba7c1-127">[Klient Java](xref:signalr/java-client) obsługuje język Java 8 i nowsze wersje.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="ba7c1-128">Nieobsługiwane klienci</span><span class="sxs-lookup"><span data-stu-id="ba7c1-128">Unsupported clients</span></span>

<span data-ttu-id="ba7c1-129">Następujący klienci są dostępni, ale są eksperymentalni lub nieoficjalni.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="ba7c1-130">Nie są one obecnie obsługiwane i mogą nie być dostępne.</span><span class="sxs-lookup"><span data-stu-id="ba7c1-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="ba7c1-131">[Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="ba7c1-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="ba7c1-132">[Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="ba7c1-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
