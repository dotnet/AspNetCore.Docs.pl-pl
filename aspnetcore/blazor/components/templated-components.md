---
title: BlazorSkładniki szablonu ASP.NET Core
author: guardrex
description: Dowiedz się, w jaki sposób składniki szablonu mogą akceptować jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2021
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
uid: blazor/components/templated-components
ms.openlocfilehash: 6c94218f3808baca18f23a53688bafdd6354e760
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589481"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="dd986-103">BlazorSkładniki szablonu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd986-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="dd986-104">Składniki z szablonami są składnikami, które akceptują jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="dd986-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="dd986-105">Składniki z szablonami umożliwiają tworzenie składników wyższego poziomu, które są większe niż zwykłe składniki.</span><span class="sxs-lookup"><span data-stu-id="dd986-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="dd986-106">Oto kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="dd986-106">A couple of examples include:</span></span>

* <span data-ttu-id="dd986-107">Składnik tabeli, który umożliwia użytkownikowi określenie szablonów dla nagłówka, wierszy i stopki tabeli.</span><span class="sxs-lookup"><span data-stu-id="dd986-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="dd986-108">Składnik listy, który umożliwia użytkownikowi określenie szablonu do renderowania elementów na liście.</span><span class="sxs-lookup"><span data-stu-id="dd986-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="dd986-109">Składnik szablonu jest definiowany przez określenie co najmniej jednego parametru składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment> lub <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="dd986-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="dd986-110">Fragment renderowania reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="dd986-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="dd986-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> przyjmuje parametr typu, który można określić podczas wywoływania fragmentu renderowania.</span><span class="sxs-lookup"><span data-stu-id="dd986-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="dd986-112">Często składniki z szablonami są typowo wpisane, jak pokazano w poniższym `TableTemplate` składniku.</span><span class="sxs-lookup"><span data-stu-id="dd986-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="dd986-113">Typ ogólny `<T>` w tym przykładzie służy do renderowania `IReadOnlyList<T>` wartości, które w tym przypadku to seria wierszy PET w składniku wyświetlającym tabelę zwierząt domowych.</span><span class="sxs-lookup"><span data-stu-id="dd986-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="dd986-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="dd986-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="dd986-115">W przypadku korzystania z składnika z szablonem parametry szablonu można określić za pomocą elementów podrzędnych, które pasują do nazw parametrów.</span><span class="sxs-lookup"><span data-stu-id="dd986-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="dd986-116">W poniższym przykładzie `<TableHeader>...</TableHeader>` i `<RowTemplate>...<RowTemplate>` Podaj <xref:Microsoft.AspNetCore.Components.RenderFragment%601> Szablony dla `TableHeader` i `RowTemplate` `TableTemplate` składnika.</span><span class="sxs-lookup"><span data-stu-id="dd986-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="dd986-117">Określ `Context` atrybut w elemencie składnika, gdy chcesz określić nazwę parametru zawartości dla niejawnej zawartości podrzędnej (bez żadnego otoki elementu podrzędnego).</span><span class="sxs-lookup"><span data-stu-id="dd986-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="dd986-118">W poniższym przykładzie `Context` atrybut jest wyświetlany w `TableTemplate` elemencie i ma zastosowanie do wszystkich <xref:Microsoft.AspNetCore.Components.RenderFragment%601> parametrów szablonu.</span><span class="sxs-lookup"><span data-stu-id="dd986-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="dd986-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="dd986-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="dd986-120">Alternatywnie można zmienić nazwę parametru przy użyciu `Context` atrybutu dla <xref:Microsoft.AspNetCore.Components.RenderFragment%601> elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="dd986-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="dd986-121">W poniższym przykładzie `Context` ustawiono wartość `RowTemplate` zamiast `TableTemplate` :</span><span class="sxs-lookup"><span data-stu-id="dd986-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="dd986-122">Argumenty składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment%601> mają niejawny parametr o nazwie `context` , który może być używany.</span><span class="sxs-lookup"><span data-stu-id="dd986-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="dd986-123">W poniższym przykładzie `Context` nie ustawiono.</span><span class="sxs-lookup"><span data-stu-id="dd986-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="dd986-124">`@context.{PROPERTY}` dostarcza wartości PET do szablonu, gdzie `{PROPERTY}` jest `Pet` właściwością:</span><span class="sxs-lookup"><span data-stu-id="dd986-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="dd986-125">W przypadku używania składników o typie ogólnym parametr typu jest wnioskowany, jeśli jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="dd986-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="dd986-126">Można jednak jawnie określić typ z atrybutem, który ma nazwę zgodną z parametrem typu, który znajduje się `TItem` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="dd986-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a><span data-ttu-id="dd986-127">Ograniczenia typu ogólnego</span><span class="sxs-lookup"><span data-stu-id="dd986-127">Generic type constraints</span></span>

> [!NOTE]
> <span data-ttu-id="dd986-128">Ograniczenia typu ogólnego będą obsługiwane w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="dd986-128">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="dd986-129">Aby uzyskać więcej informacji, zobacz [Zezwalanie na ograniczenia typu ogólnego (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="dd986-129">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd986-130">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="dd986-130">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
