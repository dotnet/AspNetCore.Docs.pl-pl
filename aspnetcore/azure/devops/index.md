---
title: Metodyka DevOps z platformami ASP.NET Core i Azure
author: CamSoper
description: Przewodnik, który oferuje kompleksowe wskazówki dotyczące tworzenia potoku DevOps dla aplikacji ASP.NET Core hostowanej na platformie Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: 10df1eab6de064b824ce067aff3ecc07e81c38fd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012270"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="7b0a1-103">Metodyka DevOps z platformami ASP.NET Core i Azure</span><span class="sxs-lookup"><span data-stu-id="7b0a1-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="7b0a1-104">[![Obraz okładki](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="7b0a1-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="7b0a1-105">Według [Soper](https://twitter.com/camsoper) i [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="7b0a1-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="7b0a1-106">Ten przewodnik jest dostępny jako [książka elektroniczna do pobrania](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="7b0a1-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="7b0a1-107">Powitanie</span><span class="sxs-lookup"><span data-stu-id="7b0a1-107">Welcome</span></span> 

<span data-ttu-id="7b0a1-108">Witamy w przewodniku po cyklu życia projektowania platformy Azure dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="7b0a1-109">W tym przewodniku przedstawiono podstawowe pojęcia związane z tworzeniem cyklu życia programowania na platformie Azure przy użyciu narzędzi i procesów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="7b0a1-110">Po zakończeniu tego przewodnika skorzystać zalety DevOps łańcucha narzędzi.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="7b0a1-111">Dla kogo jest ten przewodnik</span><span class="sxs-lookup"><span data-stu-id="7b0a1-111">Who this guide is for</span></span>

<span data-ttu-id="7b0a1-112">Powinien być doświadczonym ASP.NET Core deweloperem (poziom 200-300).</span><span class="sxs-lookup"><span data-stu-id="7b0a1-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="7b0a1-113">Nie musisz niczego wiedzieć o platformie Azure, ponieważ zajmiemy się tym wprowadzeniem.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="7b0a1-114">Ten przewodnik może być również przydatny w przypadku inżynierów DevOps, którzy są bardziej ukierunkowani na operacje niż programowanie.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="7b0a1-115">Ten przewodnik jest przeznaczony dla deweloperów systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-115">This guide targets Windows developers.</span></span> <span data-ttu-id="7b0a1-116">Jednak system Linux i macOS są w pełni obsługiwane przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="7b0a1-117">Aby dostosować ten przewodnik dla systemu Linux/macOS, Obejrzyj objaśnienia dotyczące różnic systemu Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="7b0a1-118">Czym nie obejmuje ten przewodnik</span><span class="sxs-lookup"><span data-stu-id="7b0a1-118">What this guide doesn't cover</span></span>

<span data-ttu-id="7b0a1-119">Ten przewodnik koncentruje się na kompleksowym doświadczeniu wdrażania dla deweloperów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="7b0a1-120">Nie jest to wyczerpujący Przewodnik dotyczący wszystkich rzeczy na platformie Azure i nie koncentruje się na interfejsach API platformy .NET dla usług platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="7b0a1-121">Ten nacisk dotyczy całej integracji, wdrażania, monitorowania i debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="7b0a1-122">W górnej części przewodnika są oferowane zalecenia dotyczące następnych kroków.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="7b0a1-123">Uwzględniono w sugestiach usługi platformy Azure, które są przydatne do ASP.NET Core deweloperów.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="7b0a1-124">Co znajduje się w tym przewodniku</span><span class="sxs-lookup"><span data-stu-id="7b0a1-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="7b0a1-125">Narzędzia i pliki do pobrania</span><span class="sxs-lookup"><span data-stu-id="7b0a1-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="7b0a1-126">Dowiedz się, gdzie uzyskać narzędzia używane w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="7b0a1-127">Wdrażanie w usłudze App Service</span><span class="sxs-lookup"><span data-stu-id="7b0a1-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="7b0a1-128">Poznaj różne metody wdrażania aplikacji ASP.NET Core w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="7b0a1-129">Ciągła integracja i ciągłe wdrażanie</span><span class="sxs-lookup"><span data-stu-id="7b0a1-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="7b0a1-130">Twórz kompleksowe rozwiązanie do ciągłej integracji i wdrażania aplikacji ASP.NET Core z usługami GitHub, Azure DevOps Services i Azure.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="7b0a1-131">Monitorowanie i debugowanie</span><span class="sxs-lookup"><span data-stu-id="7b0a1-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="7b0a1-132">Korzystaj z narzędzi platformy Azure do monitorowania, rozwiązywania problemów i dostrajania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="7b0a1-133">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="7b0a1-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="7b0a1-134">Inne ścieżki szkoleniowe dla ASP.NET Core deweloperów uczenie się na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="7b0a1-135">Dodatkowy odczyt wprowadzający</span><span class="sxs-lookup"><span data-stu-id="7b0a1-135">Additional introductory reading</span></span>

<span data-ttu-id="7b0a1-136">Jeśli jest to pierwsze narażenie na chmurę obliczeniową, te artykuły wyjaśniają podstawowe informacje.</span><span class="sxs-lookup"><span data-stu-id="7b0a1-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="7b0a1-137">Co to jest chmura obliczeniowa?</span><span class="sxs-lookup"><span data-stu-id="7b0a1-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="7b0a1-138">Przykłady chmury obliczeniowej</span><span class="sxs-lookup"><span data-stu-id="7b0a1-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="7b0a1-139">Co to jest IaaS?</span><span class="sxs-lookup"><span data-stu-id="7b0a1-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="7b0a1-140">Co to jest PaaS?</span><span class="sxs-lookup"><span data-stu-id="7b0a1-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
