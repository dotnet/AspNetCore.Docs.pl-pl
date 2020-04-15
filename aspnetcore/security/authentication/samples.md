---
title: Przykłady uwierzytelniania dla ASP.NET Core
author: rick-anderson
description: Zawiera łącza do przykładów uwierzytelniania w repozytorium ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308218"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="9b735-103">Przykłady uwierzytelniania dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b735-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="9b735-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9b735-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b735-105">[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="9b735-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="9b735-106">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="9b735-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="9b735-107">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="9b735-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="9b735-108">Dostawca zasad niestandardowych — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="9b735-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="9b735-109">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b735-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="9b735-110">Roszczenia zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="9b735-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="9b735-111">Wybór między plikiem cookie a innym schematem uwierzytelniania na podstawie żądania</span><span class="sxs-lookup"><span data-stu-id="9b735-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="9b735-112">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="9b735-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="9b735-113">Uruchamianie przykładów</span><span class="sxs-lookup"><span data-stu-id="9b735-113">Run the samples</span></span>

* <span data-ttu-id="9b735-114">Wybierz [gałąź](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="9b735-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="9b735-115">Na przykład: `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="9b735-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="9b735-116">Sklonuj lub pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="9b735-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="9b735-117">Sprawdź, czy zainstalowano wersję [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pasującą do klonu repozytorium ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b735-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="9b735-118">Przejdź do próbki w *aspNetCore/src/Security/samples* i `dotnet run`uruchom próbkę za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="9b735-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b735-119">[Repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore) zawiera następujące przykłady uwierzytelniania w folderze *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="9b735-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="9b735-120">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="9b735-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="9b735-121">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="9b735-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="9b735-122">Dostawca zasad niestandardowych — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="9b735-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="9b735-123">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b735-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="9b735-124">Roszczenia zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="9b735-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="9b735-125">Wybór między plikiem cookie a innym schematem uwierzytelniania na podstawie żądania</span><span class="sxs-lookup"><span data-stu-id="9b735-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="9b735-126">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="9b735-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="9b735-127">Uruchamianie przykładów</span><span class="sxs-lookup"><span data-stu-id="9b735-127">Run the samples</span></span>

* <span data-ttu-id="9b735-128">Wybierz [gałąź](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="9b735-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="9b735-129">Na przykład: `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="9b735-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="9b735-130">Sklonuj lub pobierz [repozytorium ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="9b735-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="9b735-131">Sprawdź, czy zainstalowano wersję [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pasującą do klonu repozytorium ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b735-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="9b735-132">Przejdź do próbki w *aspNetCore/src/Security/samples* i `dotnet run`uruchom próbkę za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="9b735-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
