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
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>Omówienie

`Microsoft.NET.Sdk.Web`to [sdk projektu MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) do tworzenia aplikacji ASP.NET Core. Istnieje możliwość tworzenia aplikacji ASP.NET Core bez tego SDK, jednak web SDK jest:

* Dostosowane do zapewnienia najwyższej klasy doświadczenia.
* Zalecany cel dla większości użytkowników.

Użyj web.SDK w projekcie:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Funkcje włączone przy użyciu składnika SDK sieci Web:

* Projekty przeznaczone na .NET Core 3.0 lub nowsze niejawnie odwołują się:

  * Wspólna [struktura ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analizatory](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) przeznaczone do tworzenia aplikacji ASP.NET Core.
* Web SDK importuje obiekty docelowe MSBuild, które umożliwiają korzystanie z profili publikowania i publikowania przy użyciu funkcji WebDeploy.

### <a name="properties"></a>Właściwości

| Właściwość | Opis |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Wyłącza niejawne `Microsoft.AspNetCore.App` odwołanie do udostępnionej struktury. |
| `DisableImplicitAspNetCoreAnalyzers` | Wyłącza niejawne odwołanie do ASP.NET analizatorów rdzenia. |
| `DisableImplicitComponentsAnalyzers` | Wyłącza niejawne odwołanie do analizatorów składników Razor podczas tworzenia Blazor aplikacji (serwer). |
