---
title: ASP.NET Core Blazor obsługiwane platformy
author: guardrex
description: Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013531"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="c0dbe-103">ASP.NET Core Blazor obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="c0dbe-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="c0dbe-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c0dbe-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="c0dbe-105">Wymagania dotyczące przeglądarek</span><span class="sxs-lookup"><span data-stu-id="c0dbe-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="c0dbe-106">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="c0dbe-106">Browser</span></span>                          | <span data-ttu-id="c0dbe-107">Wersja</span><span class="sxs-lookup"><span data-stu-id="c0dbe-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="c0dbe-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c0dbe-108">Microsoft Edge</span></span>                   | <span data-ttu-id="c0dbe-109">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-109">Current</span></span>               |
| <span data-ttu-id="c0dbe-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c0dbe-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="c0dbe-111">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-111">Current</span></span>               |
| <span data-ttu-id="c0dbe-112">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="c0dbe-112">Google Chrome, including Android</span></span> | <span data-ttu-id="c0dbe-113">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-113">Current</span></span>               |
| <span data-ttu-id="c0dbe-114">Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="c0dbe-114">Safari, including iOS</span></span>            | <span data-ttu-id="c0dbe-115">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-115">Current</span></span>               |
| <span data-ttu-id="c0dbe-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c0dbe-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="c0dbe-117">Nieobsługiwane&dagger;</span><span class="sxs-lookup"><span data-stu-id="c0dbe-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="c0dbe-118">&dagger;Program Microsoft Internet Explorer nie obsługuje [zestawu webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="c0dbe-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="c0dbe-119">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="c0dbe-119">Browser</span></span>                          | <span data-ttu-id="c0dbe-120">Wersja</span><span class="sxs-lookup"><span data-stu-id="c0dbe-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="c0dbe-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c0dbe-121">Microsoft Edge</span></span>                   | <span data-ttu-id="c0dbe-122">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-122">Current</span></span>    |
| <span data-ttu-id="c0dbe-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c0dbe-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="c0dbe-124">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-124">Current</span></span>    |
| <span data-ttu-id="c0dbe-125">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="c0dbe-125">Google Chrome, including Android</span></span> | <span data-ttu-id="c0dbe-126">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-126">Current</span></span>    |
| <span data-ttu-id="c0dbe-127">Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="c0dbe-127">Safari, including iOS</span></span>            | <span data-ttu-id="c0dbe-128">Current</span><span class="sxs-lookup"><span data-stu-id="c0dbe-128">Current</span></span>    |
| <span data-ttu-id="c0dbe-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c0dbe-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="c0dbe-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="c0dbe-130">11&dagger;</span></span> |

<span data-ttu-id="c0dbe-131">&dagger;Wymagane są dodatkowe wypełnienie (na przykład niesie obietnice zwiększenia można dodać za pośrednictwem [`Polyfill.io`](https://polyfill.io/v3/) pakietu).</span><span class="sxs-lookup"><span data-stu-id="c0dbe-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0dbe-132">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c0dbe-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
