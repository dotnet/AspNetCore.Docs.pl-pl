---
title: Przykłady uwierzytelniania dla ASP.NET Core
author: rick-anderson
description: Zawiera łącza do przykładów uwierzytelniania w repozytorium ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060342"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="90c83-103">Przykłady uwierzytelniania dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90c83-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="90c83-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="90c83-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90c83-105">[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="90c83-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="90c83-106">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="90c83-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="90c83-107">[Cookie ponowne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="90c83-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="90c83-108">Niestandardowy dostawca zasad — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="90c83-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="90c83-109">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="90c83-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="90c83-110">[Oświadczenia zewnętrzne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="90c83-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="90c83-111">Wybieranie między cookie i innym schematem uwierzytelniania opartym na żądaniu</span><span class="sxs-lookup"><span data-stu-id="90c83-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="90c83-112">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="90c83-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="90c83-113">Uruchamianie przykładów</span><span class="sxs-lookup"><span data-stu-id="90c83-113">Run the samples</span></span>

* <span data-ttu-id="90c83-114">Wybierz [gałąź](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="90c83-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="90c83-115">Na przykład `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="90c83-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="90c83-116">Klonuj lub Pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="90c83-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="90c83-117">Sprawdź, czy zainstalowano wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) zgodną z klonem ASP.NET Core repozytorium.</span><span class="sxs-lookup"><span data-stu-id="90c83-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="90c83-118">Przejdź do przykładu w *AspNetCore/src/Security/Samples* i uruchom przykład za pomocą `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="90c83-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90c83-119">[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="90c83-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="90c83-120">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="90c83-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="90c83-121">[Cookie ponowne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="90c83-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="90c83-122">Niestandardowy dostawca zasad — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="90c83-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="90c83-123">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="90c83-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="90c83-124">[Oświadczenia zewnętrzne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="90c83-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="90c83-125">Wybieranie między cookie i innym schematem uwierzytelniania opartym na żądaniu</span><span class="sxs-lookup"><span data-stu-id="90c83-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="90c83-126">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="90c83-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="90c83-127">Uruchamianie przykładów</span><span class="sxs-lookup"><span data-stu-id="90c83-127">Run the samples</span></span>

* <span data-ttu-id="90c83-128">Wybierz [gałąź](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="90c83-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="90c83-129">Na przykład `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="90c83-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="90c83-130">Klonuj lub Pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="90c83-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="90c83-131">Sprawdź, czy zainstalowano wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) zgodną z klonem ASP.NET Core repozytorium.</span><span class="sxs-lookup"><span data-stu-id="90c83-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="90c83-132">Przejdź do przykładu w *AspNetCore/src/Security/Samples* i uruchom przykład za pomocą `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="90c83-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
