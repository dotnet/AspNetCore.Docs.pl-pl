---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Omówienie witryny Microsoft.NET.Sdk.Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661057"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="3d919-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="3d919-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="3d919-104">Omówienie</span><span class="sxs-lookup"><span data-stu-id="3d919-104">Overview</span></span>

<span data-ttu-id="3d919-105">`Microsoft.NET.Sdk.Web`to [sdk projektu MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) do tworzenia aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d919-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="3d919-106">Istnieje możliwość tworzenia aplikacji ASP.NET Core bez tego SDK, jednak web SDK jest:</span><span class="sxs-lookup"><span data-stu-id="3d919-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="3d919-107">Dostosowane do zapewnienia najwyższej klasy doświadczenia.</span><span class="sxs-lookup"><span data-stu-id="3d919-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="3d919-108">Zalecany cel dla większości użytkowników.</span><span class="sxs-lookup"><span data-stu-id="3d919-108">The recommended target for most users.</span></span>

<span data-ttu-id="3d919-109">Użyj web.SDK w projekcie:</span><span class="sxs-lookup"><span data-stu-id="3d919-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="3d919-110">Funkcje włączone przy użyciu składnika SDK sieci Web:</span><span class="sxs-lookup"><span data-stu-id="3d919-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="3d919-111">Projekty przeznaczone na .NET Core 3.0 lub nowsze niejawnie odwołują się:</span><span class="sxs-lookup"><span data-stu-id="3d919-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="3d919-112">Wspólna [struktura ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3d919-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="3d919-113">[Analizatory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) przeznaczone do tworzenia aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d919-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="3d919-114">Web SDK importuje obiekty docelowe MSBuild, które umożliwiają korzystanie z profili publikowania i publikowania przy użyciu funkcji WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="3d919-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="3d919-115">Właściwości</span><span class="sxs-lookup"><span data-stu-id="3d919-115">Properties</span></span>

| <span data-ttu-id="3d919-116">Właściwość</span><span class="sxs-lookup"><span data-stu-id="3d919-116">Property</span></span> | <span data-ttu-id="3d919-117">Opis</span><span class="sxs-lookup"><span data-stu-id="3d919-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="3d919-118">Wyłącza niejawne `Microsoft.AspNetCore.App` odwołanie do udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="3d919-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="3d919-119">Wyłącza niejawne odwołanie do ASP.NET analizatorów rdzenia.</span><span class="sxs-lookup"><span data-stu-id="3d919-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="3d919-120">Wyłącza niejawne odwołanie do analizatorów składników Razor podczas tworzenia Blazor aplikacji (serwer).</span><span class="sxs-lookup"><span data-stu-id="3d919-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
