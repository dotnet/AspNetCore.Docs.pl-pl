---
title: ASP.NET Core Blazor obsługiwane platformy
author: guardrex
description: Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754544"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="844ee-103">ASP.NET Core Blazor obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="844ee-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="844ee-104">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="844ee-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="844ee-105">Blazor WebAssembly i Blazor Server są obsługiwane w przeglądarkach przedstawionych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="844ee-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="844ee-106">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="844ee-106">Browser</span></span>                          | <span data-ttu-id="844ee-107">Wersja</span><span class="sxs-lookup"><span data-stu-id="844ee-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="844ee-108">Apple Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="844ee-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="844ee-109">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-109">Current&dagger;</span></span> |
| <span data-ttu-id="844ee-110">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="844ee-110">Google Chrome, including Android</span></span> | <span data-ttu-id="844ee-111">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-111">Current&dagger;</span></span> |
| <span data-ttu-id="844ee-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="844ee-112">Microsoft Edge</span></span>                   | <span data-ttu-id="844ee-113">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-113">Current&dagger;</span></span> |
| <span data-ttu-id="844ee-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="844ee-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="844ee-115">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-115">Current&dagger;</span></span> |  

<span data-ttu-id="844ee-116">&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="844ee-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="844ee-117">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="844ee-117">Browser</span></span>                          | <span data-ttu-id="844ee-118">Wersja</span><span class="sxs-lookup"><span data-stu-id="844ee-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="844ee-119">Apple Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="844ee-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="844ee-120">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-120">Current&dagger;</span></span>       |
| <span data-ttu-id="844ee-121">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="844ee-121">Google Chrome, including Android</span></span> | <span data-ttu-id="844ee-122">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-122">Current&dagger;</span></span>       |
| <span data-ttu-id="844ee-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="844ee-123">Microsoft Edge</span></span>                   | <span data-ttu-id="844ee-124">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-124">Current&dagger;</span></span>       |
| <span data-ttu-id="844ee-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="844ee-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="844ee-126">Nieobsługiwane&Dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="844ee-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="844ee-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="844ee-128">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-128">Current&dagger;</span></span>       |  

<span data-ttu-id="844ee-129">&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="844ee-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="844ee-130">&Dagger;Program Microsoft Internet Explorer nie obsługuje [zestawu webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="844ee-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="844ee-131">Przeglądarka</span><span class="sxs-lookup"><span data-stu-id="844ee-131">Browser</span></span>                          | <span data-ttu-id="844ee-132">Wersja</span><span class="sxs-lookup"><span data-stu-id="844ee-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="844ee-133">Apple Safari, w tym iOS</span><span class="sxs-lookup"><span data-stu-id="844ee-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="844ee-134">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-134">Current&dagger;</span></span> |
| <span data-ttu-id="844ee-135">Google Chrome, w tym Android</span><span class="sxs-lookup"><span data-stu-id="844ee-135">Google Chrome, including Android</span></span> | <span data-ttu-id="844ee-136">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-136">Current&dagger;</span></span> |
| <span data-ttu-id="844ee-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="844ee-137">Microsoft Edge</span></span>                   | <span data-ttu-id="844ee-138">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-138">Current&dagger;</span></span> |
| <span data-ttu-id="844ee-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="844ee-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="844ee-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-140">11&Dagger;</span></span>      |
| <span data-ttu-id="844ee-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="844ee-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="844ee-142">Obecne&dagger;</span><span class="sxs-lookup"><span data-stu-id="844ee-142">Current&dagger;</span></span> |

<span data-ttu-id="844ee-143">&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="844ee-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="844ee-144">&Dagger;Dodatkowe wypełnienia są wymagane.</span><span class="sxs-lookup"><span data-stu-id="844ee-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="844ee-145">Na przykład niesie obietnice zwiększenia można dodać za pośrednictwem [`Polyfill.io`](https://polyfill.io/v3/) pakietu.</span><span class="sxs-lookup"><span data-stu-id="844ee-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="844ee-146">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="844ee-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
