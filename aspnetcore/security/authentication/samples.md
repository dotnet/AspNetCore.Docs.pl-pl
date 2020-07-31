---
title: Przykłady uwierzytelniania dla ASP.NET Core
author: rick-anderson
description: Zawiera łącza do przykładów uwierzytelniania w repozytorium ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 3e5e487adafc09d38400ea58936c5c2e8385e84f
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303602"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="6765b-103">Przykłady uwierzytelniania dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6765b-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="6765b-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6765b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6765b-105">[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="6765b-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="6765b-106">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="6765b-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="6765b-107">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="6765b-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="6765b-108">Niestandardowy dostawca zasad — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="6765b-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="6765b-109">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6765b-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="6765b-110">[Oświadczenia zewnętrzne](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="6765b-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="6765b-111">Wybieranie między plikiem cookie a innym schematem uwierzytelniania na podstawie żądania</span><span class="sxs-lookup"><span data-stu-id="6765b-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="6765b-112">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="6765b-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="6765b-113">Uruchamianie przykładów</span><span class="sxs-lookup"><span data-stu-id="6765b-113">Run the samples</span></span>

* <span data-ttu-id="6765b-114">Wybierz [gałąź](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6765b-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="6765b-115">Na przykład `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="6765b-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="6765b-116">Klonuj lub Pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6765b-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="6765b-117">Sprawdź, czy zainstalowano wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) zgodną z klonem ASP.NET Core repozytorium.</span><span class="sxs-lookup"><span data-stu-id="6765b-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="6765b-118">Przejdź do przykładu w *AspNetCore/src/Security/Samples* i uruchom przykład za pomocą `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="6765b-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6765b-119">[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="6765b-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="6765b-120">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="6765b-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="6765b-121">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="6765b-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [<span data-ttu-id="6765b-122">Niestandardowy dostawca zasad — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="6765b-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="6765b-123">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6765b-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="6765b-124">[Oświadczenia zewnętrzne](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="6765b-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="6765b-125">Wybieranie między plikiem cookie a innym schematem uwierzytelniania na podstawie żądania</span><span class="sxs-lookup"><span data-stu-id="6765b-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="6765b-126">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="6765b-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="6765b-127">Uruchamianie przykładów</span><span class="sxs-lookup"><span data-stu-id="6765b-127">Run the samples</span></span>

* <span data-ttu-id="6765b-128">Wybierz [gałąź](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6765b-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="6765b-129">Na przykład `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="6765b-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="6765b-130">Klonuj lub Pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6765b-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="6765b-131">Sprawdź, czy zainstalowano wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) zgodną z klonem ASP.NET Core repozytorium.</span><span class="sxs-lookup"><span data-stu-id="6765b-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="6765b-132">Przejdź do przykładu w *AspNetCore/src/Security/Samples* i uruchom przykład za pomocą `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="6765b-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
