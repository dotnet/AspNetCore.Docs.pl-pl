---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Omówienie Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634816"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="515cc-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="515cc-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="515cc-104">Omówienie</span><span class="sxs-lookup"><span data-stu-id="515cc-104">Overview</span></span>

<span data-ttu-id="515cc-105">`Microsoft.NET.Sdk.Web` jest [zestawem SDK programu MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) do kompilowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="515cc-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="515cc-106">Istnieje możliwość utworzenia aplikacji ASP.NET Core bez tego zestawu SDK, ale zestaw SDK sieci Web:</span><span class="sxs-lookup"><span data-stu-id="515cc-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="515cc-107">Dostosowany do zapewniania środowiska pierwszej klasy.</span><span class="sxs-lookup"><span data-stu-id="515cc-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="515cc-108">Zalecany element docelowy dla większości użytkowników.</span><span class="sxs-lookup"><span data-stu-id="515cc-108">The recommended target for most users.</span></span>

<span data-ttu-id="515cc-109">Użyj zestawu Web. SDK w projekcie:</span><span class="sxs-lookup"><span data-stu-id="515cc-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="515cc-110">Funkcje włączone przy użyciu zestawu SDK sieci Web:</span><span class="sxs-lookup"><span data-stu-id="515cc-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="515cc-111">Projekty ukierunkowane na platformę .NET Core 3,0 lub nowsze niejawnie:</span><span class="sxs-lookup"><span data-stu-id="515cc-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="515cc-112">[ASP.NET Core udostępnionej platformy](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="515cc-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="515cc-113">[Analizatory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) zaprojektowane do kompilowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="515cc-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="515cc-114">Zestaw SDK sieci Web importuje elementy docelowe programu MSBuild, które umożliwiają korzystanie z profilów publikowania i publikowanie za pomocą narzędzia webdeploy.</span><span class="sxs-lookup"><span data-stu-id="515cc-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="515cc-115">Właściwości</span><span class="sxs-lookup"><span data-stu-id="515cc-115">Properties</span></span>

| <span data-ttu-id="515cc-116">Właściwość</span><span class="sxs-lookup"><span data-stu-id="515cc-116">Property</span></span> | <span data-ttu-id="515cc-117">Opis</span><span class="sxs-lookup"><span data-stu-id="515cc-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="515cc-118">Wyłącza niejawne odwołanie do `Microsoft.AspNetCore.App` udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="515cc-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="515cc-119">Wyłącza niejawne odwołanie do analizatorów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="515cc-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="515cc-120">Wyłącza niejawne odwołanie do Razor analizatorów składników podczas kompilowania Blazor aplikacji (serwera).</span><span class="sxs-lookup"><span data-stu-id="515cc-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
