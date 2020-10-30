---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Omówienie Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059757"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>Omówienie

`Microsoft.NET.Sdk.Web` jest [zestawem SDK programu MSBuild](/visualstudio/msbuild/how-to-use-project-sdk) do kompilowania aplikacji ASP.NET Core. Istnieje możliwość utworzenia aplikacji ASP.NET Core bez tego zestawu SDK, ale zestaw SDK sieci Web:

* Dostosowany do zapewniania środowiska pierwszej klasy.
* Zalecany element docelowy dla większości użytkowników.

Użyj zestawu Web. SDK w projekcie:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Funkcje włączone przy użyciu zestawu SDK sieci Web:

* Projekty ukierunkowane na platformę .NET Core 3,0 lub nowsze niejawnie:

  * [ASP.NET Core udostępnionej platformy](xref:fundamentals/metapackage-app).
  * [Analizatory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) zaprojektowane do kompilowania aplikacji ASP.NET Core.
* Zestaw SDK sieci Web importuje elementy docelowe programu MSBuild, które umożliwiają korzystanie z profilów publikowania i publikowanie za pomocą narzędzia webdeploy.

### <a name="properties"></a>Właściwości

| Właściwość | Opis |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Wyłącza niejawne odwołanie do `Microsoft.AspNetCore.App` udostępnionej struktury. |
| `DisableImplicitAspNetCoreAnalyzers` | Wyłącza niejawne odwołanie do analizatorów ASP.NET Core. |
| `DisableImplicitComponentsAnalyzers` | Wyłącza niejawne odwołanie do Razor analizatorów składników podczas kompilowania Blazor aplikacji (serwera). |