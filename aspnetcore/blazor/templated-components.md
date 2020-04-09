---
title: składniki szablonów Blazor ASP.NET Core
author: guardrex
description: Dowiedz się, jak składniki szablonów mogą akceptować jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989500"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="a9909-103">składniki szablonów Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9909-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="a9909-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a9909-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a9909-105">Składniki szablonów to składniki, które akceptują jeden lub więcej szablonów interfejsu użytkownika jako parametrów, które mogą być następnie używane jako część logiki renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="a9909-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="a9909-106">Składniki szablonowe umożliwiają tworzenie składników wyższego poziomu, które są bardziej wielokrotnego pożytku niż zwykłe składniki.</span><span class="sxs-lookup"><span data-stu-id="a9909-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="a9909-107">Kilka przykładów to:</span><span class="sxs-lookup"><span data-stu-id="a9909-107">A couple of examples include:</span></span>

* <span data-ttu-id="a9909-108">Składnik tabeli, który umożliwia użytkownikowi określenie szablonów nagłówka tabeli, wierszy i stopki.</span><span class="sxs-lookup"><span data-stu-id="a9909-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="a9909-109">Składnik listy, który umożliwia użytkownikowi określenie szablonu renderowania elementów na liście.</span><span class="sxs-lookup"><span data-stu-id="a9909-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="a9909-110">Parametry szablonu</span><span class="sxs-lookup"><span data-stu-id="a9909-110">Template parameters</span></span>

<span data-ttu-id="a9909-111">Składnik szablonu jest definiowany przez określenie jednego `RenderFragment` lub `RenderFragment<T>`więcej parametrów składowych typu lub .</span><span class="sxs-lookup"><span data-stu-id="a9909-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="a9909-112">Fragment renderowania reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="a9909-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="a9909-113">`RenderFragment<T>`przyjmuje parametr typu, który można określić, gdy wywoływany jest fragment renderowania.</span><span class="sxs-lookup"><span data-stu-id="a9909-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="a9909-114">`TableTemplate`Składnik:</span><span class="sxs-lookup"><span data-stu-id="a9909-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="a9909-115">Podczas korzystania z składnika szablonu parametry szablonu można określić przy`TableHeader` użyciu `RowTemplate` elementów podrzędnych, które pasują do nazw parametrów ( i w poniższym przykładzie):</span><span class="sxs-lookup"><span data-stu-id="a9909-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="a9909-116">Ogólne ograniczenia typu będą obsługiwane w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="a9909-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="a9909-117">Aby uzyskać więcej informacji, zobacz [Zezwalanie na ogólne ograniczenia typu (#8433 dotnet/aspnetcore).](https://github.com/dotnet/aspnetcore/issues/8433)</span><span class="sxs-lookup"><span data-stu-id="a9909-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="a9909-118">Parametry kontekstu szablonu</span><span class="sxs-lookup"><span data-stu-id="a9909-118">Template context parameters</span></span>

<span data-ttu-id="a9909-119">Argumenty składnika `RenderFragment<T>` typu przekazywane jako elementy `context` mają niejawny parametr o `@context.PetId`nazwie (na przykład z `Context` poprzedniego przykładu kodu), ale można zmienić nazwę parametru przy użyciu atrybutu elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="a9909-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="a9909-120">W poniższym przykładzie `RowTemplate` `Context` atrybut elementu określa `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="a9909-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="a9909-121">Alternatywnie można określić `Context` atrybut elementu komponentu.</span><span class="sxs-lookup"><span data-stu-id="a9909-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="a9909-122">Określony `Context` atrybut ma zastosowanie do wszystkich określonych parametrów szablonu.</span><span class="sxs-lookup"><span data-stu-id="a9909-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="a9909-123">Może to być przydatne, gdy chcesz określić nazwę parametru zawartości dla niejawnej zawartości podrzędnej (bez zawijania elementu podrzędnego).</span><span class="sxs-lookup"><span data-stu-id="a9909-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="a9909-124">W poniższym przykładzie `Context` atrybut pojawia `TableTemplate` się w elemencie i ma zastosowanie do wszystkich parametrów szablonu:</span><span class="sxs-lookup"><span data-stu-id="a9909-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="a9909-125">Składniki typu ogólnego</span><span class="sxs-lookup"><span data-stu-id="a9909-125">Generic-typed components</span></span>

<span data-ttu-id="a9909-126">Składniki szablonów są często typizowane ogólnie.</span><span class="sxs-lookup"><span data-stu-id="a9909-126">Templated components are often generically typed.</span></span> <span data-ttu-id="a9909-127">Na przykład składnik `ListViewTemplate` ogólny może służyć `IEnumerable<T>` do renderowania wartości.</span><span class="sxs-lookup"><span data-stu-id="a9909-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="a9909-128">Aby zdefiniować składnik ogólny, należy użyć dyrektywy, [`@typeparam`](xref:mvc/views/razor#typeparam) aby określić parametry typu:</span><span class="sxs-lookup"><span data-stu-id="a9909-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="a9909-129">W przypadku używania składników typu ogólnego, parametr typu jest wywnioskowany, jeśli to możliwe:</span><span class="sxs-lookup"><span data-stu-id="a9909-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="a9909-130">W przeciwnym razie parametr type musi być jawnie określony przy użyciu atrybutu, który pasuje do nazwy parametru typu.</span><span class="sxs-lookup"><span data-stu-id="a9909-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="a9909-131">W poniższym `TItem="Pet"` przykładzie określa typ:</span><span class="sxs-lookup"><span data-stu-id="a9909-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
