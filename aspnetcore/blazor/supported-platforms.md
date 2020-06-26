---
title: ASP.NET Core Blazor obsługiwane platformy
author: guardrex
description: Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401938"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="d235e-103">ASP.NET Core Blazor obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="d235e-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="d235e-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d235e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="d235e-105">Wymagania dotyczące przeglądarek</span><span class="sxs-lookup"><span data-stu-id="d235e-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="d235e-106">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="d235e-106">Browser</span></span>                          | <span data-ttu-id="d235e-107">Wersja</span><span class="sxs-lookup"><span data-stu-id="d235e-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="d235e-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="d235e-108">Microsoft Edge</span></span>                   | <span data-ttu-id="d235e-109">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-109">Current</span></span>               |
| <span data-ttu-id="d235e-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="d235e-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="d235e-111">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-111">Current</span></span>               |
| <span data-ttu-id="d235e-112">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="d235e-112">Google Chrome, including Android</span></span> | <span data-ttu-id="d235e-113">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-113">Current</span></span>               |
| <span data-ttu-id="d235e-114">Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="d235e-114">Safari, including iOS</span></span>            | <span data-ttu-id="d235e-115">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-115">Current</span></span>               |
| <span data-ttu-id="d235e-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="d235e-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="d235e-117">Nieobsługiwane&dagger;</span><span class="sxs-lookup"><span data-stu-id="d235e-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="d235e-118">&dagger;Program Microsoft Internet Explorer nie obsługuje [zestawu webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="d235e-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="d235e-119">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="d235e-119">Browser</span></span>                          | <span data-ttu-id="d235e-120">Wersja</span><span class="sxs-lookup"><span data-stu-id="d235e-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="d235e-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="d235e-121">Microsoft Edge</span></span>                   | <span data-ttu-id="d235e-122">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-122">Current</span></span>    |
| <span data-ttu-id="d235e-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="d235e-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="d235e-124">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-124">Current</span></span>    |
| <span data-ttu-id="d235e-125">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="d235e-125">Google Chrome, including Android</span></span> | <span data-ttu-id="d235e-126">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-126">Current</span></span>    |
| <span data-ttu-id="d235e-127">Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="d235e-127">Safari, including iOS</span></span>            | <span data-ttu-id="d235e-128">Current</span><span class="sxs-lookup"><span data-stu-id="d235e-128">Current</span></span>    |
| <span data-ttu-id="d235e-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="d235e-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="d235e-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="d235e-130">11&dagger;</span></span> |

<span data-ttu-id="d235e-131">&dagger;Wymagane są dodatkowe wypełnienie (na przykład niesie obietnice zwiększenia można dodać za pośrednictwem [`Polyfill.io`](https://polyfill.io/v3/) pakietu).</span><span class="sxs-lookup"><span data-stu-id="d235e-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d235e-132">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d235e-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
