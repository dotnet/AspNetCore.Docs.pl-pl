---
title: 'Skonfiguruj element dostosowujący dla ASP.NET Core Blazor'
author: guardrex
description: 'Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055805"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="1874e-103">Skonfiguruj element dostosowujący dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1874e-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="1874e-104">Autor [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="1874e-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="1874e-105">Blazor WebAssembly wykonuje przycinanie [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby zmniejszyć rozmiar opublikowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="1874e-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="1874e-106">Przycinanie aplikacji jest zoptymalizowane pod kątem rozmiaru, ale mogą one mieć szkodliwe skutki.</span><span class="sxs-lookup"><span data-stu-id="1874e-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="1874e-107">Aplikacje korzystające z odbicia lub powiązane funkcje dynamiczne mogą być przerywane po przycięciu, ponieważ element dostosowujący nie wie o zachowaniu dynamicznego i nie może w ogóle określić, które typy są wymagane do odbicia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="1874e-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="1874e-108">Aby przyciąć takie aplikacje, należy uzyskać informacje o wszelkich typach wymaganych przez odbicie w kodzie oraz w pakietach lub strukturach, od których zależy aplikacja.</span><span class="sxs-lookup"><span data-stu-id="1874e-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="1874e-109">Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, ważne jest, aby regularnie testować publikowane dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="1874e-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="1874e-110">Przycinanie aplikacji .NET można wyłączyć, ustawiając właściwość programu `PublishTrimmed` MSBuild na wartość `false` w pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1874e-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="1874e-111">Dodatkowe opcje konfigurowania elementu dostosowującego można znaleźć w [opcjach przycinania](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="1874e-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1874e-112">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1874e-112">Additional resources</span></span>

* [<span data-ttu-id="1874e-113">Przycinanie samodzielnych wdrożeń i plików wykonywalnych</span><span class="sxs-lookup"><span data-stu-id="1874e-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
