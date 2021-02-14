---
title: ASP.NET Core Blazor kaskadowe wartości i parametry
author: guardrex
description: Dowiedz się, jak przepływać dane z składnika nadrzędnego do składników potomnych.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280234"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor kaskadowe wartości i parametry

*Wartości kaskadowe i parametry* zapewniają convienent sposób przepływu danych w dół hierarchii składników z składnika nadrzędnego do dowolnej liczby składników decendent. W przeciwieństwie do [parametrów składnika](xref:blazor/components/index#component-parameters), wartości kaskadowych i parametrów nie wymagają przypisania atrybutu dla każdego składnika potomnego, w którym są używane dane. Kaskadowe wartości i parametry umożliwiają również współrzędną ze sobą składników w hierarchii składników.

## <a name="cascadingvalue-component"></a>`CascadingValue` cm6long

Składnik nadrzędny udostępnia wartość kaskadową przy użyciu Blazor [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) składnika platformy, który zawija poddrzewo hierarchii składników i dostarcza jedną wartość do wszystkich składników w ramach poddrzewa.

Poniższy przykład ilustruje przepływ informacji o motywie w dół hierarchii składników składnika układu, aby udostępnić klasy stylu CSS do przycisków w składnikach podrzędnych.

Następująca `ThemeInfo` Klasa języka C# jest umieszczana w folderze o nazwie `UIThemeClasses` i określa informacje o motywie.

> [!NOTE]
> W przypadku przykładów w tej sekcji przestrzeń nazw aplikacji to `BlazorSample` . Podczas eksperymentowania z kodem we własnej przykładowej aplikacji Zmień przestrzeń nazw aplikacji na przestrzeń nazw przykładowej aplikacji.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

Poniższy [składnik układu](xref:blazor/layouts) określa informacje o motywie ( `ThemeInfo` ) jako kaskadową wartość dla wszystkich składników, które tworzą treść układu <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> właściwości. `ButtonClass` ma przypisaną wartość [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) , która jest stylem przycisku ładowania początkowego. Każdy składnik podrzędny w hierarchii składników może użyć `ButtonClass` właściwości za pomocą `ThemeInfo` wartości kaskadowych.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>Atrybut `[CascadingParameter]`

Aby korzystać z wartości kaskadowych, składniki potomne deklarują kaskadowe parametry przy użyciu [ `[CascadingParameter]` atrybutu](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). Wartości kaskadowe są powiązane z parametrami kaskadowymi **według typu**. Kaskadowe łączenie wielu wartości tego samego typu jest omówione w sekcji [kaskadowe wiele wartości](#cascade-multiple-values) w dalszej części tego artykułu.

Poniższy składnik wiąże `ThemeInfo` wartość kaskadową z parametrem kaskadowym, opcjonalnie używając tej samej nazwy `ThemeInfo` . Parametr służy do ustawiania klasy CSS **`Increment Counter (Themed)`** przycisku.

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>Kaskadowe wiele wartości

Aby przetworzyć kaskadowo wiele wartości tego samego typu w ramach tego samego poddrzewa, podaj unikatowy <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> ciąg dla każdego [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) składnika i ich odpowiednie [ `[CascadingParameter]` atrybuty](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).

W poniższym przykładzie dwa [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) składniki kaskaduje różne wystąpienia `CascadingType` :

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

W składniku potomnym, kaskadowe parametry otrzymują swoje kaskadowe wartości z składnika nadrzędnego przez <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> :

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>Przekazywanie danych w hierarchii składników

Parametry kaskadowe umożliwiają również składnikom przekazywanie danych w hierarchii składników. Rozważmy następujący przykład zestawu kart interfejsu użytkownika, gdzie składnik zestawu kart utrzymuje serię poszczególnych kart.

> [!NOTE]
> W przypadku przykładów w tej sekcji przestrzeń nazw aplikacji to `BlazorSample` . Podczas eksperymentowania z kodem we własnej przykładowej aplikacji Zmień przestrzeń nazw na przestrzeń nazw przykładowej aplikacji.

Utwórz `ITab` interfejs, który umożliwia zaimplementowanie kart w folderze o nazwie `UIInterfaces` .

`UIInterfaces/ITab.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

Poniższy `TabSet` składnik utrzymuje zestaw kart. Składniki zestawu kart `Tab` , które są tworzone w dalszej części tej sekcji, dostarczają listę elementów ( `<li>...</li>` ) dla listy ( `<ul>...</ul>` ).

`Tab`Składniki podrzędne nie są jawnie przenoszone jako parametry do `TabSet` . Zamiast tego składniki podrzędne `Tab` są częścią zawartości elementu podrzędnego `TabSet` . Jednak `TabSet` nadal wymaga odwołania do każdego składnika, `Tab` Aby można było renderować nagłówki i aktywną kartę. Aby umożliwić tę koordynację bez konieczności stosowania dodatkowego kodu, `TabSet` składnik *może stanowić wartość kaskadową* , która następnie jest wybierana przez `Tab` składniki potomne.

`Shared/TabSet.razor`:

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

`Tab`Składniki potomne przechwytują zawierającego je `TabSet` jako parametr kaskadowy. `Tab`Składniki dodają siebie do `TabSet` i koordynują, aby ustawić aktywną kartę.

`Shared/Tab.razor`:

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

Poniższy `ExampleTabSet` składnik używa `TabSet` składnika, który zawiera trzy `Tab` składniki.

`Pages/ExampleTabSet.razor`:

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
