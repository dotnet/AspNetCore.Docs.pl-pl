---
title: Pomocnik tagu skryptu w ASP.NET Core
author: rick-anderson
ms.author: riande
description: Odkryj atrybuty pomocnika tagów ASP.NET Core i rolę, jaką każdy atrybut odgrywa w rozszerzeniu zachowania tagu skryptu HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- 'appsettings.json'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053504"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="2c622-103">Pomocnik tagu skryptu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c622-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2c622-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c622-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2c622-105">[Pomocnik tagu skryptu](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generuje link do pliku skryptu podstawowego lub odwracania.</span><span class="sxs-lookup"><span data-stu-id="2c622-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="2c622-106">Zazwyczaj podstawowy plik skryptu znajduje się w [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="2c622-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="2c622-107">Pomocnik tagu skryptu pozwala określić sieć CDN dla pliku skryptu i rezerwę, gdy sieć CDN nie jest dostępna.</span><span class="sxs-lookup"><span data-stu-id="2c622-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="2c622-108">Pomocnik tagów skryptu zapewnia wydajność sieci CDN z niezawodną obsługą hostingu lokalnego.</span><span class="sxs-lookup"><span data-stu-id="2c622-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="2c622-109">Poniższy Razor znacznik pokazuje `script` element z rezerwą:</span><span class="sxs-lookup"><span data-stu-id="2c622-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="2c622-110">Nie używaj `<script>` atrybutu [Ustąp](https://developer.mozilla.org/docs/Web/HTML/Element/script) elementu, aby odroczyć ładowanie skryptu CDN.</span><span class="sxs-lookup"><span data-stu-id="2c622-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="2c622-111">Pomocnik tagów skryptu renderuje kod JavaScript, który natychmiast wykonuje wyrażenie [ASP-Fallback-test](#asp-fallback-test) .</span><span class="sxs-lookup"><span data-stu-id="2c622-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="2c622-112">Wyrażenie nie powiedzie się, jeśli ładowanie skryptu sieci CDN zostało odroczone.</span><span class="sxs-lookup"><span data-stu-id="2c622-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="2c622-113">Atrybuty pomocnika często używanych tagów skryptu</span><span class="sxs-lookup"><span data-stu-id="2c622-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="2c622-114">Zobacz [pomocnik tagów skryptów](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) dla wszystkich atrybutów pomocnika tagów skryptu, właściwości i metod.</span><span class="sxs-lookup"><span data-stu-id="2c622-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="2c622-115">ASP — test Fallback</span><span class="sxs-lookup"><span data-stu-id="2c622-115">asp-fallback-test</span></span>

<span data-ttu-id="2c622-116">Metoda skryptu zdefiniowana w podstawowym skrypcie do użycia dla testu powrotu.</span><span class="sxs-lookup"><span data-stu-id="2c622-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="2c622-117">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="2c622-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="2c622-118">ASP — Fallback-src</span><span class="sxs-lookup"><span data-stu-id="2c622-118">asp-fallback-src</span></span>

<span data-ttu-id="2c622-119">Adres URL tagu skryptu, do którego ma zostać nastąpi powrót w przypadku niepowodzenia pierwszego planu.</span><span class="sxs-lookup"><span data-stu-id="2c622-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="2c622-120">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="2c622-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c622-121">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2c622-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
