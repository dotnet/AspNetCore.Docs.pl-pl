---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Omówienie Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: razor-pages/web-sdk
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059757"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="fe558-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="fe558-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="fe558-104">Omówienie</span><span class="sxs-lookup"><span data-stu-id="fe558-104">Overview</span></span>

<span data-ttu-id="fe558-105">`Microsoft.NET.Sdk.Web` jest [zestawem SDK programu MSBuild](/visualstudio/msbuild/how-to-use-project-sdk) do kompilowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe558-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="fe558-106">Istnieje możliwość utworzenia aplikacji ASP.NET Core bez tego zestawu SDK, ale zestaw SDK sieci Web:</span><span class="sxs-lookup"><span data-stu-id="fe558-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="fe558-107">Dostosowany do zapewniania środowiska pierwszej klasy.</span><span class="sxs-lookup"><span data-stu-id="fe558-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="fe558-108">Zalecany element docelowy dla większości użytkowników.</span><span class="sxs-lookup"><span data-stu-id="fe558-108">The recommended target for most users.</span></span>

<span data-ttu-id="fe558-109">Użyj zestawu Web. SDK w projekcie:</span><span class="sxs-lookup"><span data-stu-id="fe558-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="fe558-110">Funkcje włączone przy użyciu zestawu SDK sieci Web:</span><span class="sxs-lookup"><span data-stu-id="fe558-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="fe558-111">Projekty ukierunkowane na platformę .NET Core 3,0 lub nowsze niejawnie:</span><span class="sxs-lookup"><span data-stu-id="fe558-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="fe558-112">[ASP.NET Core udostępnionej platformy](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="fe558-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="fe558-113">[Analizatory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) zaprojektowane do kompilowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe558-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="fe558-114">Zestaw SDK sieci Web importuje elementy docelowe programu MSBuild, które umożliwiają korzystanie z profilów publikowania i publikowanie za pomocą narzędzia webdeploy.</span><span class="sxs-lookup"><span data-stu-id="fe558-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="fe558-115">Właściwości</span><span class="sxs-lookup"><span data-stu-id="fe558-115">Properties</span></span>

| <span data-ttu-id="fe558-116">Właściwość</span><span class="sxs-lookup"><span data-stu-id="fe558-116">Property</span></span> | <span data-ttu-id="fe558-117">Opis</span><span class="sxs-lookup"><span data-stu-id="fe558-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="fe558-118">Wyłącza niejawne odwołanie do `Microsoft.AspNetCore.App` udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="fe558-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="fe558-119">Wyłącza niejawne odwołanie do analizatorów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe558-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="fe558-120">Wyłącza niejawne odwołanie do :::no-loc(Razor)::: analizatorów składników podczas kompilowania :::no-loc(Blazor)::: aplikacji (serwera).</span><span class="sxs-lookup"><span data-stu-id="fe558-120">Disables implicit reference to :::no-loc(Razor)::: Components analyzers when building :::no-loc(Blazor)::: (server) applications.</span></span> |