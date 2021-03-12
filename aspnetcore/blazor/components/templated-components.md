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
# <a name="aspnet-core-blazor-templated-components"></a>BlazorSkładniki szablonu ASP.NET Core

Składniki z szablonami są składnikami, które akceptują jeden lub więcej szablonów interfejsu użytkownika jako parametry, które mogą być następnie używane jako część logiki renderowania składnika. Składniki z szablonami umożliwiają tworzenie składników wyższego poziomu, które są większe niż zwykłe składniki. Oto kilka przykładów:

* Składnik tabeli, który umożliwia użytkownikowi określenie szablonów dla nagłówka, wierszy i stopki tabeli.
* Składnik listy, który umożliwia użytkownikowi określenie szablonu do renderowania elementów na liście.

Składnik szablonu jest definiowany przez określenie co najmniej jednego parametru składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment> lub <xref:Microsoft.AspNetCore.Components.RenderFragment%601> . Fragment renderowania reprezentuje segment interfejsu użytkownika do renderowania. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> przyjmuje parametr typu, który można określić podczas wywoływania fragmentu renderowania.

Często składniki z szablonami są typowo wpisane, jak pokazano w poniższym `TableTemplate` składniku. Typ ogólny `<T>` w tym przykładzie służy do renderowania `IReadOnlyList<T>` wartości, które w tym przypadku to seria wierszy PET w składniku wyświetlającym tabelę zwierząt domowych.

`Shared/TableTemplate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

W przypadku korzystania z składnika z szablonem parametry szablonu można określić za pomocą elementów podrzędnych, które pasują do nazw parametrów. W poniższym przykładzie `<TableHeader>...</TableHeader>` i `<RowTemplate>...<RowTemplate>` Podaj <xref:Microsoft.AspNetCore.Components.RenderFragment%601> Szablony dla `TableHeader` i `RowTemplate` `TableTemplate` składnika.

Określ `Context` atrybut w elemencie składnika, gdy chcesz określić nazwę parametru zawartości dla niejawnej zawartości podrzędnej (bez żadnego otoki elementu podrzędnego). W poniższym przykładzie `Context` atrybut jest wyświetlany w `TableTemplate` elemencie i ma zastosowanie do wszystkich <xref:Microsoft.AspNetCore.Components.RenderFragment%601> parametrów szablonu.

`Pages/Pets.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

Alternatywnie można zmienić nazwę parametru przy użyciu `Context` atrybutu dla <xref:Microsoft.AspNetCore.Components.RenderFragment%601> elementu podrzędnego. W poniższym przykładzie `Context` ustawiono wartość `RowTemplate` zamiast `TableTemplate` :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

Argumenty składnika typu <xref:Microsoft.AspNetCore.Components.RenderFragment%601> mają niejawny parametr o nazwie `context` , który może być używany. W poniższym przykładzie `Context` nie ustawiono. `@context.{PROPERTY}` dostarcza wartości PET do szablonu, gdzie `{PROPERTY}` jest `Pet` właściwością:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

W przypadku używania składników o typie ogólnym parametr typu jest wnioskowany, jeśli jest to możliwe. Można jednak jawnie określić typ z atrybutem, który ma nazwę zgodną z parametrem typu, który znajduje się `TItem` w poprzednim przykładzie:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a>Ograniczenia typu ogólnego

> [!NOTE]
> Ograniczenia typu ogólnego będą obsługiwane w przyszłych wydaniach. Aby uzyskać więcej informacji, zobacz [Zezwalanie na ograniczenia typu ogólnego (#8433 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
