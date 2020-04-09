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
# <a name="aspnet-core-opno-locblazor-templated-components"></a>składniki szablonów Blazor ASP.NET Core

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

Składniki szablonów to składniki, które akceptują jeden lub więcej szablonów interfejsu użytkownika jako parametrów, które mogą być następnie używane jako część logiki renderowania składnika. Składniki szablonowe umożliwiają tworzenie składników wyższego poziomu, które są bardziej wielokrotnego pożytku niż zwykłe składniki. Kilka przykładów to:

* Składnik tabeli, który umożliwia użytkownikowi określenie szablonów nagłówka tabeli, wierszy i stopki.
* Składnik listy, który umożliwia użytkownikowi określenie szablonu renderowania elementów na liście.

## <a name="template-parameters"></a>Parametry szablonu

Składnik szablonu jest definiowany przez określenie jednego `RenderFragment` lub `RenderFragment<T>`więcej parametrów składowych typu lub . Fragment renderowania reprezentuje segment interfejsu użytkownika do renderowania. `RenderFragment<T>`przyjmuje parametr typu, który można określić, gdy wywoływany jest fragment renderowania.

`TableTemplate`Składnik:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Podczas korzystania z składnika szablonu parametry szablonu można określić przy`TableHeader` użyciu `RowTemplate` elementów podrzędnych, które pasują do nazw parametrów ( i w poniższym przykładzie):

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
> Ogólne ograniczenia typu będą obsługiwane w przyszłej wersji. Aby uzyskać więcej informacji, zobacz [Zezwalanie na ogólne ograniczenia typu (#8433 dotnet/aspnetcore).](https://github.com/dotnet/aspnetcore/issues/8433)

## <a name="template-context-parameters"></a>Parametry kontekstu szablonu

Argumenty składnika `RenderFragment<T>` typu przekazywane jako elementy `context` mają niejawny parametr o `@context.PetId`nazwie (na przykład z `Context` poprzedniego przykładu kodu), ale można zmienić nazwę parametru przy użyciu atrybutu elementu podrzędnego. W poniższym przykładzie `RowTemplate` `Context` atrybut elementu określa `pet` parametr:

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

Alternatywnie można określić `Context` atrybut elementu komponentu. Określony `Context` atrybut ma zastosowanie do wszystkich określonych parametrów szablonu. Może to być przydatne, gdy chcesz określić nazwę parametru zawartości dla niejawnej zawartości podrzędnej (bez zawijania elementu podrzędnego). W poniższym przykładzie `Context` atrybut pojawia `TableTemplate` się w elemencie i ma zastosowanie do wszystkich parametrów szablonu:

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

## <a name="generic-typed-components"></a>Składniki typu ogólnego

Składniki szablonów są często typizowane ogólnie. Na przykład składnik `ListViewTemplate` ogólny może służyć `IEnumerable<T>` do renderowania wartości. Aby zdefiniować składnik ogólny, należy użyć dyrektywy, [`@typeparam`](xref:mvc/views/razor#typeparam) aby określić parametry typu:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

W przypadku używania składników typu ogólnego, parametr typu jest wywnioskowany, jeśli to możliwe:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

W przeciwnym razie parametr type musi być jawnie określony przy użyciu atrybutu, który pasuje do nazwy parametru typu. W poniższym `TItem="Pet"` przykładzie określa typ:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
