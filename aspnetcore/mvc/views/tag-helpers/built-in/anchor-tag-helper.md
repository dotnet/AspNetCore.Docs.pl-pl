---
title: Pomocnik tagu kotwicy w ASP.NET Core
author: pkellner
description: Odkryj atrybuty pomocnika ASP.NET Core znacznika i rolę, jaką każdy atrybut odgrywa w rozszerzeniu zachowania tagu kotwicy HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 142ad62bbbc25fc5390331b253a6173f064ef162
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773968"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="149f9-103">Pomocnik tagu kotwicy w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="149f9-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="149f9-104">Według [Peterowi Kellner](https://peterkellner.net) i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="149f9-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="149f9-105">[Pomocnik tagu kotwicy](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) rozszerza standardowy tag kotwicy HTML (`<a ... ></a>`) przez dodanie nowych atrybutów.</span><span class="sxs-lookup"><span data-stu-id="149f9-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="149f9-106">Według Konwencji nazwy atrybutów są poprzedzone prefiksem `asp-`.</span><span class="sxs-lookup"><span data-stu-id="149f9-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="149f9-107">Wartość `href` atrybutu renderowanego elementu zakotwiczenia jest określana na podstawie wartości `asp-` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="149f9-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="149f9-108">Aby zapoznać się z omówieniem pomocników tagów, <xref:mvc/views/tag-helpers/intro>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="149f9-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="149f9-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="149f9-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="149f9-110">*SpeakerController* jest używany w przykładach w tym dokumencie:</span><span class="sxs-lookup"><span data-stu-id="149f9-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="149f9-111">Atrybuty pomocnika tagu kotwicy</span><span class="sxs-lookup"><span data-stu-id="149f9-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="149f9-112">ASP-Controller</span><span class="sxs-lookup"><span data-stu-id="149f9-112">asp-controller</span></span>

<span data-ttu-id="149f9-113">Atrybut [kontrolera ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) przypisuje kontroler używany do generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="149f9-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="149f9-114">Następujące znaczniki zawierają listę wszystkich głośników:</span><span class="sxs-lookup"><span data-stu-id="149f9-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="149f9-115">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="149f9-116">Jeśli `asp-controller` atrybut jest określony i `asp-action` nie jest, wartość domyślna `asp-action` to akcja kontrolera skojarzona z aktualnie wykonywanym widokiem.</span><span class="sxs-lookup"><span data-stu-id="149f9-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="149f9-117">Jeśli `asp-action` zostanie pominięty z poprzedniego znacznika, a pomocnik tagu kotwicy jest używany w widoku indeksu *HomeController*( *Index* */Home*), wygenerowany kod HTML jest:</span><span class="sxs-lookup"><span data-stu-id="149f9-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="149f9-118">ASP — akcja</span><span class="sxs-lookup"><span data-stu-id="149f9-118">asp-action</span></span>

<span data-ttu-id="149f9-119">Wartość atrybutu [akcji ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) reprezentuje nazwę akcji kontrolera zawartej w wygenerowanym `href` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="149f9-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="149f9-120">Poniższy znacznik ustawia wygenerowanej `href` wartości atrybutu na stronie oceny głośników:</span><span class="sxs-lookup"><span data-stu-id="149f9-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="149f9-121">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="149f9-122">Jeśli żaden `asp-controller` atrybut nie jest określony, używany jest domyślny kontroler wywołujący widok wykonujący bieżący widok.</span><span class="sxs-lookup"><span data-stu-id="149f9-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="149f9-123">Jeśli `asp-action` atrybut ma `Index`wartość, nie jest dołączany do adresu URL, co prowadzi do wywołania akcji domyślnej. `Index`</span><span class="sxs-lookup"><span data-stu-id="149f9-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="149f9-124">Określona akcja (lub domyślna) musi istnieć na kontrolerze, do którego odwołuje się `asp-controller`w.</span><span class="sxs-lookup"><span data-stu-id="149f9-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="149f9-125">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="149f9-125">asp-route-{value}</span></span>

<span data-ttu-id="149f9-126">Atrybut [ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) włącza prefiks trasy wieloznacznej.</span><span class="sxs-lookup"><span data-stu-id="149f9-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="149f9-127">Wszystkie wartości, które `{value}` zajmują symbol zastępczy, są interpretowane jako potencjalna wartość parametru trasy.</span><span class="sxs-lookup"><span data-stu-id="149f9-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="149f9-128">Jeśli trasa domyślna nie zostanie znaleziona, ten prefiks trasy jest dołączany do `href` wygenerowanego atrybutu jako parametr i wartość żądania.</span><span class="sxs-lookup"><span data-stu-id="149f9-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="149f9-129">W przeciwnym razie zostanie zastąpiony w szablonie trasy.</span><span class="sxs-lookup"><span data-stu-id="149f9-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="149f9-130">Rozważmy następującą akcję kontrolera:</span><span class="sxs-lookup"><span data-stu-id="149f9-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="149f9-131">Z domyślnym szablonem trasy zdefiniowanym podczas *uruchamiania. Konfiguracja*:</span><span class="sxs-lookup"><span data-stu-id="149f9-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="149f9-132">Widok MVC używa modelu dostarczonego przez akcję w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="149f9-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="149f9-133">`{id?}` Symbol zastępczy trasy domyślnej został dopasowany.</span><span class="sxs-lookup"><span data-stu-id="149f9-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="149f9-134">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="149f9-135">Załóżmy, że prefiks trasy nie jest częścią zgodnego szablonu routingu, tak jak w przypadku następującego widoku MVC:</span><span class="sxs-lookup"><span data-stu-id="149f9-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="149f9-136">Następujący kod HTML jest generowany, `speakerid` ponieważ nie został odnaleziony w zgodnej trasie:</span><span class="sxs-lookup"><span data-stu-id="149f9-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="149f9-137">`asp-controller` Jeśli albo `asp-action` nie zostanie określony, to to samo domyślne przetwarzanie zostanie wykonane, tak jak w `asp-route` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="149f9-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="149f9-138">ASP — Route</span><span class="sxs-lookup"><span data-stu-id="149f9-138">asp-route</span></span>

<span data-ttu-id="149f9-139">Atrybut [ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) służy do tworzenia łączenia adresów URL bezpośrednio z nazwaną trasą.</span><span class="sxs-lookup"><span data-stu-id="149f9-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="149f9-140">Przy użyciu [atrybutów routingu](xref:mvc/controllers/routing#attribute-routing), trasa może być nazywana, jak pokazano `SpeakerController` w i używane w `Evaluations` jej akcji:</span><span class="sxs-lookup"><span data-stu-id="149f9-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="149f9-141">W poniższym znaczniku `asp-route` atrybut odwołuje się do nazwanej trasy:</span><span class="sxs-lookup"><span data-stu-id="149f9-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="149f9-142">Pomocnik tagu kotwicy generuje trasę bezpośrednio do tej akcji kontrolera przy użyciu adresu URL */Speaker/Evaluations*.</span><span class="sxs-lookup"><span data-stu-id="149f9-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="149f9-143">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="149f9-144">Jeśli `asp-controller` wartość `asp-action` lub jest określona oprócz `asp-route`, wygenerowana trasa nie może być oczekiwana przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="149f9-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="149f9-145">Aby uniknąć konfliktu trasy, `asp-route` nie należy używać z atrybutami `asp-controller` i `asp-action` .</span><span class="sxs-lookup"><span data-stu-id="149f9-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="149f9-146">ASP — wszystkie trasy — dane</span><span class="sxs-lookup"><span data-stu-id="149f9-146">asp-all-route-data</span></span>

<span data-ttu-id="149f9-147">Atrybut [ASP-All-Route-Data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) obsługuje tworzenie słownika par klucz-wartość.</span><span class="sxs-lookup"><span data-stu-id="149f9-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="149f9-148">Klucz jest nazwą parametru, a wartość jest wartością parametru.</span><span class="sxs-lookup"><span data-stu-id="149f9-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="149f9-149">W poniższym przykładzie słownik został zainicjowany i przeszedł do Razor widoku.</span><span class="sxs-lookup"><span data-stu-id="149f9-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="149f9-150">Alternatywnie dane mogły zostać przesłane przy użyciu modelu.</span><span class="sxs-lookup"><span data-stu-id="149f9-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="149f9-151">Poprzedni kod generuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="149f9-152">`asp-all-route-data` Słownik jest spłaszczony, aby utworzyć ciąg QueryString spełniający wymagania przeciążonej `Evaluations` akcji:</span><span class="sxs-lookup"><span data-stu-id="149f9-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="149f9-153">Jeśli wszystkie klucze w słowniku pasują do parametrów trasy, te wartości są zastępowane w marszrucie, zgodnie z potrzebami.</span><span class="sxs-lookup"><span data-stu-id="149f9-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="149f9-154">Inne niezgodne wartości są generowane jako parametry żądania.</span><span class="sxs-lookup"><span data-stu-id="149f9-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="149f9-155">ASP — fragment</span><span class="sxs-lookup"><span data-stu-id="149f9-155">asp-fragment</span></span>

<span data-ttu-id="149f9-156">Atrybut [ASP-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) definiuje fragment adresu URL do dołączenia do adresu URL.</span><span class="sxs-lookup"><span data-stu-id="149f9-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="149f9-157">Pomocnik tagu kotwicy dodaje znak skrótu (#).</span><span class="sxs-lookup"><span data-stu-id="149f9-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="149f9-158">Rozważ następujące oznakowanie:</span><span class="sxs-lookup"><span data-stu-id="149f9-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="149f9-159">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="149f9-160">Tagi skrótów są przydatne podczas tworzenia aplikacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="149f9-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="149f9-161">Mogą one służyć do łatwego oznaczania i wyszukiwania w języku JavaScript, na przykład.</span><span class="sxs-lookup"><span data-stu-id="149f9-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="149f9-162">obszar ASP</span><span class="sxs-lookup"><span data-stu-id="149f9-162">asp-area</span></span>

<span data-ttu-id="149f9-163">Atrybut [obszaru ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) ustawia nazwę obszaru używaną do ustawiania odpowiedniej trasy.</span><span class="sxs-lookup"><span data-stu-id="149f9-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="149f9-164">W poniższych przykładach przedstawiono sposób `asp-area` , w jaki atrybut powoduje ponowne mapowanie tras.</span><span class="sxs-lookup"><span data-stu-id="149f9-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="149f9-165">Użycie na Razor stronach</span><span class="sxs-lookup"><span data-stu-id="149f9-165">Usage in Razor Pages</span></span>

Razor<span data-ttu-id="149f9-166">Obszary stron są obsługiwane w ASP.NET Core 2,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="149f9-166"> Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="149f9-167">Weź pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="149f9-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="149f9-168">**{Nazwa projektu}**</span><span class="sxs-lookup"><span data-stu-id="149f9-168">**{Project name}**</span></span>
  * <span data-ttu-id="149f9-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="149f9-169">**wwwroot**</span></span>
  * <span data-ttu-id="149f9-170">**Obszary**</span><span class="sxs-lookup"><span data-stu-id="149f9-170">**Areas**</span></span>
    * <span data-ttu-id="149f9-171">**Sesje**</span><span class="sxs-lookup"><span data-stu-id="149f9-171">**Sessions**</span></span>
      * <span data-ttu-id="149f9-172">**Pages**</span><span class="sxs-lookup"><span data-stu-id="149f9-172">**Pages**</span></span>
        * <span data-ttu-id="149f9-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="149f9-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="149f9-174">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="149f9-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="149f9-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="149f9-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="149f9-176">**Pages**</span><span class="sxs-lookup"><span data-stu-id="149f9-176">**Pages**</span></span>

<span data-ttu-id="149f9-177">Znacznikiem odwołującym się do strony *indeksu* Razor obszaru *sesji* jest:</span><span class="sxs-lookup"><span data-stu-id="149f9-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="149f9-178">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="149f9-179">Aby obsługiwać obszary w aplikacji Razor stron, wykonaj jedną z następujących czynności w programie `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="149f9-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="149f9-180">Ustaw [wersję zgodności](xref:mvc/compatibility-version) na 2,1 lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="149f9-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="149f9-181">Ustaw właściwość [RazorPagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) na `true`:</span><span class="sxs-lookup"><span data-stu-id="149f9-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="149f9-182">Użycie w MVC</span><span class="sxs-lookup"><span data-stu-id="149f9-182">Usage in MVC</span></span>

<span data-ttu-id="149f9-183">Weź pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="149f9-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="149f9-184">**{Nazwa projektu}**</span><span class="sxs-lookup"><span data-stu-id="149f9-184">**{Project name}**</span></span>
  * <span data-ttu-id="149f9-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="149f9-185">**wwwroot**</span></span>
  * <span data-ttu-id="149f9-186">**Obszary**</span><span class="sxs-lookup"><span data-stu-id="149f9-186">**Areas**</span></span>
    * <span data-ttu-id="149f9-187">**Blogi**</span><span class="sxs-lookup"><span data-stu-id="149f9-187">**Blogs**</span></span>
      * <span data-ttu-id="149f9-188">**Kontrolery**</span><span class="sxs-lookup"><span data-stu-id="149f9-188">**Controllers**</span></span>
        * <span data-ttu-id="149f9-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="149f9-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="149f9-190">**Widoki**</span><span class="sxs-lookup"><span data-stu-id="149f9-190">**Views**</span></span>
        * <span data-ttu-id="149f9-191">**Strona główna**</span><span class="sxs-lookup"><span data-stu-id="149f9-191">**Home**</span></span>
          * <span data-ttu-id="149f9-192">*AboutBlog. cshtml*</span><span class="sxs-lookup"><span data-stu-id="149f9-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="149f9-193">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="149f9-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="149f9-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="149f9-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="149f9-195">**Kontrolery**</span><span class="sxs-lookup"><span data-stu-id="149f9-195">**Controllers**</span></span>

<span data-ttu-id="149f9-196">Ustawienie `asp-area` "Blogi" prefiksuje *obszary katalogu/Blogi* do tras skojarzonych kontrolerów i widoków dla tego tagu zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="149f9-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="149f9-197">Znacznikiem odwołującym się do widoku *AboutBlog* jest:</span><span class="sxs-lookup"><span data-stu-id="149f9-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="149f9-198">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="149f9-199">Aby obsługiwać obszary w aplikacji MVC, szablon trasy musi zawierać odwołanie do obszaru, jeśli istnieje.</span><span class="sxs-lookup"><span data-stu-id="149f9-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="149f9-200">Ten szablon jest reprezentowany przez drugi parametr wywołania `routes.MapRoute` metody w trakcie *uruchamiania. Skonfiguruj*:</span><span class="sxs-lookup"><span data-stu-id="149f9-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="149f9-201">ASP — protokół</span><span class="sxs-lookup"><span data-stu-id="149f9-201">asp-protocol</span></span>

<span data-ttu-id="149f9-202">Atrybut [ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) służy do określania protokołu (takiego jak `https`) w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="149f9-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="149f9-203">Przykład:</span><span class="sxs-lookup"><span data-stu-id="149f9-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="149f9-204">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="149f9-205">Nazwa hosta w przykładzie jest localhost.</span><span class="sxs-lookup"><span data-stu-id="149f9-205">The host name in the example is localhost.</span></span> <span data-ttu-id="149f9-206">Pomocnik tagu kotwicy używa domeny publicznej witryny sieci Web podczas generowania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="149f9-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="149f9-207">ASP — Host</span><span class="sxs-lookup"><span data-stu-id="149f9-207">asp-host</span></span>

<span data-ttu-id="149f9-208">Atrybut [ASP-Host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) służy do określania nazwy hosta w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="149f9-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="149f9-209">Przykład:</span><span class="sxs-lookup"><span data-stu-id="149f9-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="149f9-210">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="149f9-211">ASP — Strona</span><span class="sxs-lookup"><span data-stu-id="149f9-211">asp-page</span></span>

<span data-ttu-id="149f9-212">Atrybut [ASP-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) jest używany ze Razor stronami.</span><span class="sxs-lookup"><span data-stu-id="149f9-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="149f9-213">Użyj go, aby ustawić wartość `href` atrybutu tagu kotwicy na określoną stronę.</span><span class="sxs-lookup"><span data-stu-id="149f9-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="149f9-214">Prefiks nazwy strony z ukośnikiem ("/") powoduje utworzenie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="149f9-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="149f9-215">Następujący przykład wskazuje na stronę uczestnika Razor :</span><span class="sxs-lookup"><span data-stu-id="149f9-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="149f9-216">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="149f9-217">Ten `asp-page` atrybut wzajemnie się wykluczają `asp-route`z `asp-controller`atrybutami `asp-action` , i.</span><span class="sxs-lookup"><span data-stu-id="149f9-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="149f9-218">Program `asp-page` może być jednak używany z `asp-route-{value}` programem w celu kontrolowania routingu, ponieważ ilustruje to następujące oznakowanie:</span><span class="sxs-lookup"><span data-stu-id="149f9-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="149f9-219">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="149f9-220">ASP — obsługa stron</span><span class="sxs-lookup"><span data-stu-id="149f9-220">asp-page-handler</span></span>

<span data-ttu-id="149f9-221">Atrybut [ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) jest używany ze Razor stronami.</span><span class="sxs-lookup"><span data-stu-id="149f9-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="149f9-222">Jest on przeznaczony do łączenia z konkretnymi programami obsługi stron.</span><span class="sxs-lookup"><span data-stu-id="149f9-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="149f9-223">Weź pod uwagę następujące procedury obsługi stron:</span><span class="sxs-lookup"><span data-stu-id="149f9-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="149f9-224">Skojarzone ze znacznikiem model strony łącza do obsługi `OnGetProfile` stron.</span><span class="sxs-lookup"><span data-stu-id="149f9-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="149f9-225">Zwróć uwagę `On<Verb>` , że prefiks nazwy metody obsługi stron został pominięty w wartości `asp-page-handler` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="149f9-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="149f9-226">Gdy metoda jest asynchroniczna, `Async` sufiks jest pomijany.</span><span class="sxs-lookup"><span data-stu-id="149f9-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="149f9-227">Wygenerowany kod HTML:</span><span class="sxs-lookup"><span data-stu-id="149f9-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="149f9-228">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="149f9-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
