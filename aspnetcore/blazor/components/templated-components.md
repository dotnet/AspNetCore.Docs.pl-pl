---
title: BlazorSkładniki szablonu ASP.NET Core
author: guardrex
description: Dowiedz się, w jaki sposób składniki szablonu mogą akceptować jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: 74601905b7317ad8d9763fe0d747ba36bd0b1389
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393798"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a>BlazorSkładniki szablonu ASP.NET Core

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

Składniki z szablonami są składnikami, które akceptują jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika. Składniki z szablonami umożliwiają tworzenie składników wyższego poziomu, które są większe niż zwykłe składniki. Oto kilka przykładów:

* Składnik tabeli, który umożliwia użytkownikowi określenie szablonów dla nagłówka, wierszy i stopki tabeli.
* Składnik listy, który umożliwia użytkownikowi określenie szablonu do renderowania elementów na liście.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="template-parameters"></a>Parametry szablonu

Składnik szablonu jest definiowany przez określenie co najmniej jednego parametru składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment> lub <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Fragment renderowania reprezentuje segment interfejsu użytkownika do renderowania. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> przyjmuje parametr typu, który można określić podczas wywoływania fragmentu renderowania.

`TableTemplate` składnik ( `TableTemplate.razor` ):

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

W przypadku korzystania z składnika z szablonem parametry szablonu można określić za pomocą elementów podrzędnych, które pasują do nazw parametrów ( `TableHeader` i `RowTemplate` w poniższym przykładzie):

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
> Ograniczenia typu ogólnego będą obsługiwane w przyszłych wydaniach. Aby uzyskać więcej informacji, zobacz [Zezwalanie na ograniczenia typu ogólnego (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Parametry kontekstu szablonu

Argumenty składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment%601> przekazane jako elementy mają niejawny parametr o nazwie `context` (na przykład z poprzedniego przykładu kodu `@context.PetId` ), ale można zmienić nazwę parametru przy użyciu `Context` atrybutu dla elementu podrzędnego. W poniższym przykładzie `RowTemplate` `Context` atrybut elementu określa `pet` parametr:

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

Alternatywnie można określić `Context` atrybut dla elementu składnika. Określony `Context` atrybut ma zastosowanie do wszystkich parametrów określonego szablonu. Może to być przydatne, jeśli chcesz określić nazwę parametru zawartości dla niejawnej zawartości podrzędnej (bez żadnego elementu podrzędnego otoki). W poniższym przykładzie `Context` atrybut pojawia się na `TableTemplate` elemencie i ma zastosowanie do wszystkich parametrów szablonu:

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

## <a name="generic-typed-components"></a>Składniki typu rodzajowego

Składniki z szablonami są często wpisywane ogólnie. Na przykład, składnik generyczny `ListViewTemplate` ( `ListViewTemplate.razor` ) może służyć do renderowania `IEnumerable<T>` wartości. Aby zdefiniować składnik ogólny, użyj [`@typeparam`](xref:mvc/views/razor#typeparam) dyrektywy do określenia parametrów typu:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

W przypadku używania składników o typie ogólnym parametr typu jest wnioskowany, jeśli jest to możliwe:

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

W przeciwnym razie parametr typu musi być jawnie określony przy użyciu atrybutu, który jest zgodny z nazwą parametru typu. W poniższym przykładzie `TItem="Pet"` określa typ:

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
