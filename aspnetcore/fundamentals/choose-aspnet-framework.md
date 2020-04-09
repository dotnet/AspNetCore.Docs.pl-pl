---
title: Wybierz między ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: W tym artykule wyjaśniono, ASP.NET Core a ASP.NET 4.x i jak wybrać między nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665243"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="21fc3-103">Wybierz między ASP.NET 4.x a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21fc3-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="21fc3-104">ASP.NET Core to przeprojektowanie ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="21fc3-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="21fc3-105">W tym artykule wymieniono różnice między nimi.</span><span class="sxs-lookup"><span data-stu-id="21fc3-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="21fc3-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21fc3-106">ASP.NET Core</span></span>

<span data-ttu-id="21fc3-107">ASP.NET Core to wieloplatformowa struktura open source do tworzenia nowoczesnych, opartych na chmurze aplikacji internetowych w systemach Windows, macOS lub Linux.</span><span class="sxs-lookup"><span data-stu-id="21fc3-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="21fc3-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="21fc3-108">ASP.NET 4.x</span></span>

<span data-ttu-id="21fc3-109">ASP.NET 4.x to dojrzała struktura, która zapewnia usługi potrzebne do tworzenia aplikacji sieci Web klasy korporacyjnej opartych na serwerze w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="21fc3-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="21fc3-110">Wybór struktury</span><span class="sxs-lookup"><span data-stu-id="21fc3-110">Framework selection</span></span>

<span data-ttu-id="21fc3-111">W poniższej tabeli porównano ASP.NET Core z ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="21fc3-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="21fc3-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21fc3-112">ASP.NET Core</span></span> | <span data-ttu-id="21fc3-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="21fc3-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="21fc3-114">Kompilacja dla systemu Windows, macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="21fc3-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="21fc3-115">Kompilacja dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="21fc3-115">Build for Windows</span></span>|
|<span data-ttu-id="21fc3-116">[Razor Pages](xref:razor-pages/index) to zalecane podejście do tworzenia interfejsu użytkownika sieci Web od ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="21fc3-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="21fc3-117">Zobacz też [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction)Web [API](xref:tutorials/first-web-api), i .</span><span class="sxs-lookup"><span data-stu-id="21fc3-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="21fc3-118">Używanie [formularzy sieci Web](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [interfejsu API sieci Web,](/aspnet/web-api/) [elementów WebHook](/aspnet/webhooks/)lub [stron sieci Web](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="21fc3-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="21fc3-119">Wiele wersji na maszynę</span><span class="sxs-lookup"><span data-stu-id="21fc3-119">Multiple versions per machine</span></span>|<span data-ttu-id="21fc3-120">Jedna wersja na maszynę</span><span class="sxs-lookup"><span data-stu-id="21fc3-120">One version per machine</span></span>|
|<span data-ttu-id="21fc3-121">Tworzenie za pomocą [programu Visual Studio,](https://visualstudio.microsoft.com/vs/) [programu Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)lub programu Visual Studio [Code](https://code.visualstudio.com/) przy użyciu języka C# lub F #</span><span class="sxs-lookup"><span data-stu-id="21fc3-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="21fc3-122">Tworzenie za pomocą [programu Visual Studio](https://visualstudio.microsoft.com/vs/) przy użyciu języka C#, VB lub F #</span><span class="sxs-lookup"><span data-stu-id="21fc3-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="21fc3-123">Wyższa wydajność niż ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="21fc3-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="21fc3-124">Dobra wydajność</span><span class="sxs-lookup"><span data-stu-id="21fc3-124">Good performance</span></span>|
|[<span data-ttu-id="21fc3-125">Korzystanie z core.NET środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="21fc3-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="21fc3-126">Korzystanie ze środowiska uruchomieniowego programu .NET Framework</span><span class="sxs-lookup"><span data-stu-id="21fc3-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="21fc3-127">Zobacz [ASP.NET Core targeting .NET Framework, aby](xref:index#target-framework) uzyskać informacje na temat obsługi ASP.NET Core 2.x w programie .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="21fc3-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="21fc3-128">ASP.NET Podstawowe scenariusze</span><span class="sxs-lookup"><span data-stu-id="21fc3-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="21fc3-129">Witryny internetowe</span><span class="sxs-lookup"><span data-stu-id="21fc3-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="21fc3-130">Interfejsy API</span><span class="sxs-lookup"><span data-stu-id="21fc3-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="21fc3-131">Przesyłanie w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="21fc3-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="21fc3-132">Wdrażanie aplikacji platformy ASP.NET Core na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="21fc3-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="21fc3-133">ASP.NET scenariusze 4.x</span><span class="sxs-lookup"><span data-stu-id="21fc3-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="21fc3-134">Witryny internetowe</span><span class="sxs-lookup"><span data-stu-id="21fc3-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="21fc3-135">Interfejsy API</span><span class="sxs-lookup"><span data-stu-id="21fc3-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="21fc3-136">Przesyłanie w czasie rzeczywistym</span><span class="sxs-lookup"><span data-stu-id="21fc3-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="21fc3-137">Tworzenie aplikacji sieci Web ASP.NET 4.x na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="21fc3-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="21fc3-138">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="21fc3-138">Additional resources</span></span>

* [<span data-ttu-id="21fc3-139">Wprowadzenie do platformy ASP.NET</span><span class="sxs-lookup"><span data-stu-id="21fc3-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="21fc3-140">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21fc3-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
