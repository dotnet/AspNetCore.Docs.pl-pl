---
title: BlazorSkładniki szablonu ASP.NET Core
author: guardrex
description: Dowiedz się, w jaki sposób składniki szablonu mogą akceptować jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
ms.openlocfilehash: 28f98596046877672b2935cb1f0feb508548398d
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529933"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="b62ba-103">BlazorSkładniki szablonu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b62ba-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="b62ba-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b62ba-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b62ba-105">Składniki z szablonami są składnikami, które akceptują jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="b62ba-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="b62ba-106">Składniki z szablonami umożliwiają tworzenie składników wyższego poziomu, które są większe niż zwykłe składniki.</span><span class="sxs-lookup"><span data-stu-id="b62ba-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="b62ba-107">Oto kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="b62ba-107">A couple of examples include:</span></span>

* <span data-ttu-id="b62ba-108">Składnik tabeli, który umożliwia użytkownikowi określenie szablonów dla nagłówka, wierszy i stopki tabeli.</span><span class="sxs-lookup"><span data-stu-id="b62ba-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="b62ba-109">Składnik listy, który umożliwia użytkownikowi określenie szablonu do renderowania elementów na liście.</span><span class="sxs-lookup"><span data-stu-id="b62ba-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="b62ba-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b62ba-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="b62ba-111">Parametry szablonu</span><span class="sxs-lookup"><span data-stu-id="b62ba-111">Template parameters</span></span>

<span data-ttu-id="b62ba-112">Składnik szablonu jest definiowany przez określenie co najmniej jednego parametru składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment> lub <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="b62ba-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="b62ba-113">Fragment renderowania reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="b62ba-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="b62ba-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> przyjmuje parametr typu, który można określić podczas wywoływania fragmentu renderowania.</span><span class="sxs-lookup"><span data-stu-id="b62ba-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="b62ba-115">`TableTemplate` składnik ( `TableTemplate.razor` ):</span><span class="sxs-lookup"><span data-stu-id="b62ba-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="b62ba-116">W przypadku korzystania z składnika z szablonem parametry szablonu można określić za pomocą elementów podrzędnych, które pasują do nazw parametrów ( `TableHeader` i `RowTemplate` w poniższym przykładzie):</span><span class="sxs-lookup"><span data-stu-id="b62ba-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="b62ba-117">Ograniczenia typu ogólnego będą obsługiwane w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="b62ba-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="b62ba-118">Aby uzyskać więcej informacji, zobacz [Zezwalanie na ograniczenia typu ogólnego (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="b62ba-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="b62ba-119">Parametry kontekstu szablonu</span><span class="sxs-lookup"><span data-stu-id="b62ba-119">Template context parameters</span></span>

<span data-ttu-id="b62ba-120">Argumenty składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment%601> przekazane jako elementy mają niejawny parametr o nazwie `context` (na przykład z poprzedniego przykładu kodu `@context.PetId` ), ale można zmienić nazwę parametru przy użyciu `Context` atrybutu dla elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="b62ba-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="b62ba-121">W poniższym przykładzie `RowTemplate` `Context` atrybut elementu określa `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="b62ba-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

@code {
    ...
}
```

<span data-ttu-id="b62ba-122">Alternatywnie można określić `Context` atrybut dla elementu składnika.</span><span class="sxs-lookup"><span data-stu-id="b62ba-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="b62ba-123">Określony `Context` atrybut ma zastosowanie do wszystkich parametrów określonego szablonu.</span><span class="sxs-lookup"><span data-stu-id="b62ba-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="b62ba-124">Może to być przydatne, jeśli chcesz określić nazwę parametru zawartości dla niejawnej zawartości podrzędnej (bez żadnego elementu podrzędnego otoki).</span><span class="sxs-lookup"><span data-stu-id="b62ba-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="b62ba-125">W poniższym przykładzie `Context` atrybut pojawia się na `TableTemplate` elemencie i ma zastosowanie do wszystkich parametrów szablonu:</span><span class="sxs-lookup"><span data-stu-id="b62ba-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="b62ba-126">Składniki typu rodzajowego</span><span class="sxs-lookup"><span data-stu-id="b62ba-126">Generic-typed components</span></span>

<span data-ttu-id="b62ba-127">Składniki z szablonami są często wpisywane ogólnie.</span><span class="sxs-lookup"><span data-stu-id="b62ba-127">Templated components are often generically typed.</span></span> <span data-ttu-id="b62ba-128">Na przykład, składnik generyczny `ListViewTemplate` ( `ListViewTemplate.razor` ) może służyć do renderowania `IEnumerable<T>` wartości.</span><span class="sxs-lookup"><span data-stu-id="b62ba-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="b62ba-129">Aby zdefiniować składnik ogólny, użyj [`@typeparam`](xref:mvc/views/razor#typeparam) dyrektywy do określenia parametrów typu:</span><span class="sxs-lookup"><span data-stu-id="b62ba-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="b62ba-130">W przypadku używania składników o typie ogólnym parametr typu jest wnioskowany, jeśli jest to możliwe:</span><span class="sxs-lookup"><span data-stu-id="b62ba-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="b62ba-131">W przeciwnym razie parametr typu musi być jawnie określony przy użyciu atrybutu, który jest zgodny z nazwą parametru typu.</span><span class="sxs-lookup"><span data-stu-id="b62ba-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="b62ba-132">W poniższym przykładzie `TItem="Pet"` określa typ:</span><span class="sxs-lookup"><span data-stu-id="b62ba-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```

## <a name="additional-resources"></a><span data-ttu-id="b62ba-133">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b62ba-133">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
