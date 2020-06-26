---
title: Pomocnik tagów częściowych w ASP.NET Core
author: scottaddie
description: Odkryj pomocnika tagów częściowej ASP.NET Core i rolę, w której każda z jego atrybutów jest odtwarzana w wyniku renderowania częściowego widoku.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: d92707ff06125a4b65f1ec18e402f5fe86547b5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407918"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="3fe98-103">Pomocnik tagów częściowych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fe98-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3fe98-104">Przez [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3fe98-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3fe98-105">Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="3fe98-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="3fe98-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3fe98-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="3fe98-107">Omówienie</span><span class="sxs-lookup"><span data-stu-id="3fe98-107">Overview</span></span>

<span data-ttu-id="3fe98-108">Pomocnik tagów częściowych służy do renderowania [widoku częściowego](xref:mvc/views/partial) na Razor stronach i w aplikacjach MVC.</span><span class="sxs-lookup"><span data-stu-id="3fe98-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="3fe98-109">Weź pod uwagę, że:</span><span class="sxs-lookup"><span data-stu-id="3fe98-109">Consider that it:</span></span>

* <span data-ttu-id="3fe98-110">Wymaga ASP.NET Core 2,1 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="3fe98-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="3fe98-111">Jest alternatywną [składnią pomocnika HTML](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="3fe98-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="3fe98-112">Renderuje widok częściowy asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="3fe98-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="3fe98-113">Opcje pomocnika HTML do renderowania widoku częściowego obejmują:</span><span class="sxs-lookup"><span data-stu-id="3fe98-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="3fe98-114">Model *produktu* jest używany w przykładach w tym dokumencie:</span><span class="sxs-lookup"><span data-stu-id="3fe98-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="3fe98-115">Poniżej znajduje się spis atrybutów pomocnika tagów częściowych.</span><span class="sxs-lookup"><span data-stu-id="3fe98-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="3fe98-116">name</span><span class="sxs-lookup"><span data-stu-id="3fe98-116">name</span></span>

<span data-ttu-id="3fe98-117">`name` Atrybut jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="3fe98-117">The `name` attribute is required.</span></span> <span data-ttu-id="3fe98-118">Wskazuje nazwę lub ścieżkę widoku częściowego, który ma być renderowany.</span><span class="sxs-lookup"><span data-stu-id="3fe98-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="3fe98-119">Po podaniu nazwy widoku częściowego zostanie zainicjowany proces [odnajdywania widoku](xref:mvc/views/overview#view-discovery) .</span><span class="sxs-lookup"><span data-stu-id="3fe98-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="3fe98-120">Ten proces jest pomijany, gdy podano jawną ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="3fe98-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="3fe98-121">Wszystkie akceptowalne `name` wartości można znaleźć w sekcji [odnajdywanie widoku częściowego](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="3fe98-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="3fe98-122">Następujące oznakowanie używa jawnej ścieżki, wskazując, że *_ProductPartial. cshtml* ma być załadowany z folderu *udostępnionego* .</span><span class="sxs-lookup"><span data-stu-id="3fe98-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="3fe98-123">Użycie atrybutu [for](#for) powoduje przekazanie modelu do widoku częściowego w celu powiązania.</span><span class="sxs-lookup"><span data-stu-id="3fe98-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="3fe98-124">dla</span><span class="sxs-lookup"><span data-stu-id="3fe98-124">for</span></span>

<span data-ttu-id="3fe98-125">Ten `for` atrybut przypisuje [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) do oceny względem bieżącego modelu.</span><span class="sxs-lookup"><span data-stu-id="3fe98-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="3fe98-126">A `ModelExpression` wnioskuje `@Model.` składnię.</span><span class="sxs-lookup"><span data-stu-id="3fe98-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="3fe98-127">Na przykład, `for="Product"` można użyć zamiast `for="@Model.Product"` .</span><span class="sxs-lookup"><span data-stu-id="3fe98-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="3fe98-128">To domyślne zachowanie wnioskowania jest zastępowane przy użyciu `@` symbolu do zdefiniowania wyrażenia wbudowanego.</span><span class="sxs-lookup"><span data-stu-id="3fe98-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="3fe98-129">Następujące znaczniki są ładowane *_ProductPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3fe98-129">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="3fe98-130">Widok częściowy jest powiązany z właściwością skojarzonego modelu strony `Product` :</span><span class="sxs-lookup"><span data-stu-id="3fe98-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="3fe98-131">model</span><span class="sxs-lookup"><span data-stu-id="3fe98-131">model</span></span>

<span data-ttu-id="3fe98-132">Ten `model` atrybut przypisuje wystąpienie modelu do przekazania do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="3fe98-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="3fe98-133">`model`Nie można używać atrybutu z atrybutem [for](#for) .</span><span class="sxs-lookup"><span data-stu-id="3fe98-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="3fe98-134">W poniższym znaczniku `Product` jest tworzone wystąpienie nowego obiektu i przekazanie do niego `model` atrybutu w celu powiązania:</span><span class="sxs-lookup"><span data-stu-id="3fe98-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="3fe98-135">Widok — dane</span><span class="sxs-lookup"><span data-stu-id="3fe98-135">view-data</span></span>

<span data-ttu-id="3fe98-136">`view-data`Atrybut przypisuje [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) do przekazania do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="3fe98-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="3fe98-137">Poniższy znacznik sprawia, że cała kolekcja ViewData jest dostępna dla widoku częściowego:</span><span class="sxs-lookup"><span data-stu-id="3fe98-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="3fe98-138">W poprzednim kodzie `IsNumberReadOnly` wartość klucza jest ustawiona na `true` i dodana do kolekcji Viewdata.</span><span class="sxs-lookup"><span data-stu-id="3fe98-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="3fe98-139">W związku z tym, `ViewData["IsNumberReadOnly"]` jest dostępny w ramach następującego widoku częściowego:</span><span class="sxs-lookup"><span data-stu-id="3fe98-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="3fe98-140">W tym przykładzie wartość `ViewData["IsNumberReadOnly"]` określa, czy pole *liczbowe* jest wyświetlane jako tylko do odczytu.</span><span class="sxs-lookup"><span data-stu-id="3fe98-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="3fe98-141">Migrowanie z pomocnika HTML</span><span class="sxs-lookup"><span data-stu-id="3fe98-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="3fe98-142">Rozważmy następujący przykładowy asynchroniczny pomocnik HTML.</span><span class="sxs-lookup"><span data-stu-id="3fe98-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="3fe98-143">Kolekcja produktów jest powtarzana i wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="3fe98-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="3fe98-144">Na `PartialAsync` pierwszy parametr metody jest ładowany widok częściowy *_ProductPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3fe98-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="3fe98-145">Wystąpienie `Product` modelu jest przekazane do widoku częściowego w celu powiązania.</span><span class="sxs-lookup"><span data-stu-id="3fe98-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="3fe98-146">Poniższy pomocnik tagów częściowych osiąga takie samo asynchroniczne zachowanie renderowania, jak `PartialAsync` pomocnik html.</span><span class="sxs-lookup"><span data-stu-id="3fe98-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="3fe98-147">`model`Atrybutowi przypisano `Product` wystąpienie modelu dla powiązania do widoku częściowego.</span><span class="sxs-lookup"><span data-stu-id="3fe98-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="3fe98-148">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3fe98-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
