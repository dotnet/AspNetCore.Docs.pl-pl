---
title: Host ASP.NET Core w kontenerach platformy Docker
author: rick-anderson
description: Odnajduj łącza do zasobów, aby dowiedzieć się, jak hostować aplikacje ASP.NET Core w kontenerach platformy Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: 78eb6f467c7e779e1bf998e8ac8e917a90895db9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440730"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="e68ca-103">Host ASP.NET Core w kontenerach platformy Docker</span><span class="sxs-lookup"><span data-stu-id="e68ca-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="e68ca-104">Następujące artykuły są dostępne do nauki o hostingu aplikacji ASP.NET Core w platformie Docker:</span><span class="sxs-lookup"><span data-stu-id="e68ca-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="e68ca-105">Wprowadzenie do kontenerów i platformy Docker</span><span class="sxs-lookup"><span data-stu-id="e68ca-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="e68ca-106">Zobacz, jak konteneryzacja jest podejście do tworzenia oprogramowania, w którym aplikacja lub usługa, jego zależności i jego konfiguracji są pakowane razem jako obraz kontenera.</span><span class="sxs-lookup"><span data-stu-id="e68ca-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="e68ca-107">Obraz można przetestować, a następnie wdrożyć na hoście.</span><span class="sxs-lookup"><span data-stu-id="e68ca-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="e68ca-108">Co to jest docker</span><span class="sxs-lookup"><span data-stu-id="e68ca-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="e68ca-109">Dowiedz się, jak docker jest projektem typu open source do automatyzacji wdrażania aplikacji jako przenośnych, samowystarczalnych kontenerów, które mogą działać w chmurze lub lokalnie.</span><span class="sxs-lookup"><span data-stu-id="e68ca-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="e68ca-110">Terminologia platformy Docker</span><span class="sxs-lookup"><span data-stu-id="e68ca-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="e68ca-111">Poznaj terminy i definicje technologii Platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="e68ca-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="e68ca-112">Kontenery, obrazy i rejestry platformy Docker</span><span class="sxs-lookup"><span data-stu-id="e68ca-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="e68ca-113">Dowiedz się, jak obrazy kontenerów platformy Docker są przechowywane w rejestrze obrazów w celu spójnego wdrażania w różnych środowiskach.</span><span class="sxs-lookup"><span data-stu-id="e68ca-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="e68ca-114"><xref:host-and-deploy/docker/building-net-docker-images>Dowiedz się, jak tworzyć i dockerize aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e68ca-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="e68ca-115">Eksploruj obrazy platformy Docker obsługiwane przez firmę Microsoft i badaj przypadki użycia.</span><span class="sxs-lookup"><span data-stu-id="e68ca-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="e68ca-116">Narzędzia kontenerów programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e68ca-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="e68ca-117">Dowiedz się, jak program Visual Studio obsługuje tworzenie, debugowanie i uruchamianie aplikacji ASP.NET Core przeznaczonych dla programu .NET Framework lub .NET Core w platformie Docker dla systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="e68ca-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="e68ca-118">Obsługiwane są kontenery zarówno systemu Windows, jak i Linux.</span><span class="sxs-lookup"><span data-stu-id="e68ca-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="e68ca-119">Publikowanie w usłudze Azure Container Registry</span><span class="sxs-lookup"><span data-stu-id="e68ca-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="e68ca-120">Dowiedz się, jak użyć rozszerzenia Narzędzia kontenerów programu Visual Studio do wdrożenia aplikacji ASP.NET Core na hoście platformy Docker na platformie Azure przy użyciu programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="e68ca-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="e68ca-121">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="e68ca-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="e68ca-122">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="e68ca-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="e68ca-123">Przekazywanie żądań za pośrednictwem serwera proxy często zasłania informacje o oryginalnym żądaniu, takie jak schemat i adres IP klienta.</span><span class="sxs-lookup"><span data-stu-id="e68ca-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="e68ca-124">Może być konieczne przekazanie niektórych informacji o żądaniu ręcznie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e68ca-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="e68ca-125">[GC przy użyciu platformy Docker i małych kontenerów](xref:performance/memory#sc) W tym artykule omówiono wybór GC z małymi pojemnikami.</span><span class="sxs-lookup"><span data-stu-id="e68ca-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>