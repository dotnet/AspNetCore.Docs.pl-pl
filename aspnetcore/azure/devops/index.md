---
title: Metodyka DevOps z platformami ASP.NET Core i Azure
author: CamSoper
description: Przewodnik, który zawiera kompleksowe wskazówki dotyczące tworzenia potoku DevOps dla aplikacji core ASP.NET hostowane na platformie Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658082"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="c0576-103">Metodyka DevOps z platformami ASP.NET Core i Azure</span><span class="sxs-lookup"><span data-stu-id="c0576-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="c0576-104">[![Obraz okładki](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="c0576-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="c0576-105">Przez [Cam Soper](https://twitter.com/camsoper) i [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c0576-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="c0576-106">Ten przewodnik jest dostępny w formacie [PDF do pobrania.](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="c0576-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="c0576-107">Powitanie</span><span class="sxs-lookup"><span data-stu-id="c0576-107">Welcome</span></span> 

<span data-ttu-id="c0576-108">Witamy w przewodniku po cyklu życia rozwoju platformy Azure dla platformy .NET!</span><span class="sxs-lookup"><span data-stu-id="c0576-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="c0576-109">W tym przewodniku przedstawiono podstawowe pojęcia tworzenia cyklu życia rozwoju wokół platformy Azure przy użyciu narzędzi i procesów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c0576-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="c0576-110">Po zakończeniu tego przewodnika będziesz czerpać korzyści z dojrzałego paska narzędzi DevOps.</span><span class="sxs-lookup"><span data-stu-id="c0576-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="c0576-111">Dla kogo przeznaczony jest ten przewodnik</span><span class="sxs-lookup"><span data-stu-id="c0576-111">Who this guide is for</span></span>

<span data-ttu-id="c0576-112">Powinieneś być doświadczonym deweloperem ASP.NET Core (poziom 200-300).</span><span class="sxs-lookup"><span data-stu-id="c0576-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="c0576-113">Nie musisz wiedzieć nic o platformie Azure, ponieważ omówimy to w tym wstępie.</span><span class="sxs-lookup"><span data-stu-id="c0576-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="c0576-114">Ten przewodnik może być również przydatny dla inżynierów DevOps, którzy są bardziej skoncentrowani na operacjach niż na rozwoju.</span><span class="sxs-lookup"><span data-stu-id="c0576-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="c0576-115">Ten przewodnik jest przeznaczony dla deweloperów systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="c0576-115">This guide targets Windows developers.</span></span> <span data-ttu-id="c0576-116">Jednak Linux i macOS są w pełni obsługiwane przez .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0576-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="c0576-117">Aby dostosować ten przewodnik dla linuksa / macOS, uważaj na objaśnienia dla różnic w systemie Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="c0576-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="c0576-118">Czego nie obejmuje ten przewodnik</span><span class="sxs-lookup"><span data-stu-id="c0576-118">What this guide doesn't cover</span></span>

<span data-ttu-id="c0576-119">Ten przewodnik koncentruje się na kompleksowym środowisko ciągłego wdrażania dla deweloperów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c0576-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="c0576-120">Nie jest wyczerpującym przewodnikiem po wszystkich rzeczach platformy Azure i nie koncentruje się na interfejsach API platformy .NET dla usług platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="c0576-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="c0576-121">Nacisk kładzie się na ciągłą integrację, wdrażanie, monitorowanie i debugowanie.</span><span class="sxs-lookup"><span data-stu-id="c0576-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="c0576-122">Pod koniec przewodnika oferowane są zalecenia dotyczące kolejnych kroków.</span><span class="sxs-lookup"><span data-stu-id="c0576-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="c0576-123">Zawarte w sugestie są usługi platformy Azure platformy, które są przydatne do ASP.NET core deweloperów.</span><span class="sxs-lookup"><span data-stu-id="c0576-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="c0576-124">Co jest w tym przewodniku</span><span class="sxs-lookup"><span data-stu-id="c0576-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="c0576-125">Narzędzia i pliki do pobrania</span><span class="sxs-lookup"><span data-stu-id="c0576-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="c0576-126">Dowiedz się, gdzie można uzyskać narzędzia użyte w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="c0576-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="c0576-127">Wdrażanie do usługi App Service</span><span class="sxs-lookup"><span data-stu-id="c0576-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="c0576-128">Poznaj różne metody wdrażania aplikacji ASP.NET Core w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c0576-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="c0576-129">Ciągła integracja i ciągłe wdrażanie</span><span class="sxs-lookup"><span data-stu-id="c0576-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="c0576-130">Stwórz kompleksowe rozwiązanie do ciągłej integracji i wdrażania aplikacji ASP.NET Core za pomocą usługi GitHub, usługi Azure DevOps i platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="c0576-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="c0576-131">Monitorowanie i debugowanie</span><span class="sxs-lookup"><span data-stu-id="c0576-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="c0576-132">Użyj narzędzi platformy Azure do monitorowania, rozwiązywania problemów i dostrajania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0576-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="c0576-133">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="c0576-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="c0576-134">Inne ścieżki szkoleniowe dla ASP.NET Core developer learning Azure.</span><span class="sxs-lookup"><span data-stu-id="c0576-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="c0576-135">Dodatkowa lektura wprowadzająca</span><span class="sxs-lookup"><span data-stu-id="c0576-135">Additional introductory reading</span></span>

<span data-ttu-id="c0576-136">Jeśli jest to twoja pierwsza ekspozycja na chmurę obliczeniową, te artykuły wyjaśniają podstawy.</span><span class="sxs-lookup"><span data-stu-id="c0576-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="c0576-137">Co to jest przetwarzanie w chmurze?</span><span class="sxs-lookup"><span data-stu-id="c0576-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="c0576-138">Przykłady przetwarzania w chmurze</span><span class="sxs-lookup"><span data-stu-id="c0576-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="c0576-139">Co to jest IaaS?</span><span class="sxs-lookup"><span data-stu-id="c0576-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="c0576-140">Co to jest PaaS?</span><span class="sxs-lookup"><span data-stu-id="c0576-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
