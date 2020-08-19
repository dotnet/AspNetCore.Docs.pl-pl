---
title: Pomocnik tagu środowiska w ASP.NET Core
author: pkellner
description: Zdefiniowano pomocnika tagów środowiska ASP.NET Core, w tym wszystkie właściwości
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: de37bf704ac377a15ef600b96ba78056c51ea7be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633867"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="f68e1-103">Pomocnik tagu środowiska w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f68e1-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f68e1-104">Według [Piotr Kellner](https://peterkellner.net) i [Hisham bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="f68e1-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="f68e1-105">Pomocnik tagu środowiska warunkowo renderuje zawartą zawartość w oparciu o bieżące [środowisko hostingu](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f68e1-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="f68e1-106">Pojedynczy atrybut pomocnika tagów środowiska, `names` , jest rozdzielaną przecinkami listą nazw środowiska.</span><span class="sxs-lookup"><span data-stu-id="f68e1-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="f68e1-107">Jeśli dowolna z podanych nazw środowiska jest zgodna z bieżącym środowiskiem, załączona zawartość jest renderowana.</span><span class="sxs-lookup"><span data-stu-id="f68e1-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="f68e1-108">Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="f68e1-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="f68e1-109">Atrybuty pomocnika tagów środowiska</span><span class="sxs-lookup"><span data-stu-id="f68e1-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="f68e1-110">nazwy</span><span class="sxs-lookup"><span data-stu-id="f68e1-110">names</span></span>

<span data-ttu-id="f68e1-111">`names` akceptuje jedną nazwę środowiska hostingu lub listę rozdzielonych przecinkami nazw środowiska hostingu, które wyzwalają renderowanie załączonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="f68e1-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="f68e1-112">Wartości środowiskowe są porównywane z bieżącą wartością zwracaną przez [IHostingEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="f68e1-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="f68e1-113">Porównanie ignoruje wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="f68e1-113">The comparison ignores case.</span></span>

<span data-ttu-id="f68e1-114">W poniższym przykładzie jest używana pomocnik tagów środowiska.</span><span class="sxs-lookup"><span data-stu-id="f68e1-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="f68e1-115">Zawartość jest renderowana, jeśli środowisko hostingu jest przejściowe lub produkcyjne:</span><span class="sxs-lookup"><span data-stu-id="f68e1-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="f68e1-116">Dołączanie i wykluczanie atrybutów</span><span class="sxs-lookup"><span data-stu-id="f68e1-116">include and exclude attributes</span></span>

<span data-ttu-id="f68e1-117">`include`& `exclude` kontrolka atrybutów renderuje zawartą zawartość na podstawie nazw dołączanych lub wykluczonych środowisk hostingu.</span><span class="sxs-lookup"><span data-stu-id="f68e1-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="f68e1-118">include</span><span class="sxs-lookup"><span data-stu-id="f68e1-118">include</span></span>

<span data-ttu-id="f68e1-119">`include`Właściwość wykazuje podobne zachowanie w odróżnieniu od `names` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f68e1-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="f68e1-120">Środowisko wymienione w `include` wartości atrybutu musi być zgodne ze środowiskiem hostingu aplikacji ([IHostingEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)), aby renderować zawartość `<environment>` znacznika.</span><span class="sxs-lookup"><span data-stu-id="f68e1-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="f68e1-121">wykluczanie</span><span class="sxs-lookup"><span data-stu-id="f68e1-121">exclude</span></span>

<span data-ttu-id="f68e1-122">W przeciwieństwie do `include` atrybutu, zawartość `<environment>` tagu jest renderowana, gdy środowisko hostingu nie jest zgodne ze środowiskiem wymienionym w `exclude` wartości atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f68e1-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f68e1-123">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f68e1-123">Additional resources</span></span>

* <xref:fundamentals/environments>
