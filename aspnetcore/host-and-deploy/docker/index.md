---
title: ASP.NET Core hosta w kontenerach platformy Docker
author: rick-anderson
description: Odkryj linki do zasobów, aby dowiedzieć się, jak hostować aplikacje ASP.NET Core w kontenerach platformy Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 35837eb2bd1ed2321663e1ae334eb20b18e5587a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015780"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="a6e88-103">ASP.NET Core hosta w kontenerach platformy Docker</span><span class="sxs-lookup"><span data-stu-id="a6e88-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="a6e88-104">Następujące artykuły są dostępne na potrzeby uczenia się, jak hostować ASP.NET Core aplikacje w programie Docker:</span><span class="sxs-lookup"><span data-stu-id="a6e88-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="a6e88-105">Wprowadzenie do kontenerów i platformy Docker</span><span class="sxs-lookup"><span data-stu-id="a6e88-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="a6e88-106">Zobacz, jak kontenerach jest podejściem do tworzenia oprogramowania, w którym aplikacja lub usługa, jej zależności i jej konfiguracja są pakowane razem jako obraz kontenera.</span><span class="sxs-lookup"><span data-stu-id="a6e88-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="a6e88-107">Obraz może zostać przetestowany, a następnie wdrożony na hoście.</span><span class="sxs-lookup"><span data-stu-id="a6e88-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="a6e88-108">Co to jest platforma Docker</span><span class="sxs-lookup"><span data-stu-id="a6e88-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="a6e88-109">Odkryj, jak platforma Docker to projekt typu "open source" służący do automatyzowania wdrażania aplikacji jako przenośnych, samowystarczalnych kontenerów, które mogą być uruchamiane w chmurze lub lokalnie.</span><span class="sxs-lookup"><span data-stu-id="a6e88-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="a6e88-110">Terminologia platformy Docker</span><span class="sxs-lookup"><span data-stu-id="a6e88-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="a6e88-111">Poznaj warunki i definicje dotyczące technologii platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="a6e88-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="a6e88-112">Kontenery, obrazy i rejestry platformy Docker</span><span class="sxs-lookup"><span data-stu-id="a6e88-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="a6e88-113">Dowiedz się, w jaki sposób obrazy kontenerów platformy Docker są przechowywane w rejestrze obrazów na potrzeby spójnego wdrażania w różnych środowiskach.</span><span class="sxs-lookup"><span data-stu-id="a6e88-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="a6e88-114"><xref:host-and-deploy/docker/building-net-docker-images>Dowiedz się, jak kompilować i przekształcać aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a6e88-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="a6e88-115">Eksploruj obrazy platformy Docker obsługiwane przez firmę Microsoft i badaj przypadki użycia.</span><span class="sxs-lookup"><span data-stu-id="a6e88-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="a6e88-116">Narzędzia kontenerów programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6e88-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="a6e88-117">Dowiedz się, w jaki sposób program Visual Studio obsługuje kompilowanie, debugowanie i uruchamianie aplikacji ASP.NET Core przeznaczonych dla .NET Framework lub .NET Core w Docker for Windows.</span><span class="sxs-lookup"><span data-stu-id="a6e88-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="a6e88-118">Obsługiwane są kontenery zarówno systemu Windows, jak i Linux.</span><span class="sxs-lookup"><span data-stu-id="a6e88-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="a6e88-119">Publikowanie w usłudze Azure Container Registry</span><span class="sxs-lookup"><span data-stu-id="a6e88-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="a6e88-120">Dowiedz się, jak za pomocą rozszerzenia Visual Studio Container Tools wdrożyć aplikację ASP.NET Core na hoście Docker na platformie Azure przy użyciu programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="a6e88-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="a6e88-121">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="a6e88-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="a6e88-122">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="a6e88-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="a6e88-123">Przekazywanie żądań za pomocą serwera proxy często zasłania informacje o oryginalnym żądaniu, takie jak schemat i adres IP klienta.</span><span class="sxs-lookup"><span data-stu-id="a6e88-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="a6e88-124">Może być konieczne ręczne przekazanie pewnych informacji dotyczących żądania do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6e88-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="a6e88-125">[GC przy użyciu platformy Docker i małych kontenerów](xref:performance/memory#sc) Omawia wybór GC z małymi kontenerami.</span><span class="sxs-lookup"><span data-stu-id="a6e88-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>