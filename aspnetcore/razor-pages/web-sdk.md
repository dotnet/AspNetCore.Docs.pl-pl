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
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>Omówienie

`Microsoft.NET.Sdk.Web` jest [zestawem SDK programu MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) do kompilowania aplikacji ASP.NET Core. Istnieje możliwość utworzenia aplikacji ASP.NET Core bez tego zestawu SDK, ale zestaw SDK sieci Web:

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
