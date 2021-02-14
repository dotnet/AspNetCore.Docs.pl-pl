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
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="3bc2a-103">ASP.NET Core Blazor kaskadowe wartości i parametry</span><span class="sxs-lookup"><span data-stu-id="3bc2a-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="3bc2a-104">*Wartości kaskadowe i parametry* zapewniają convienent sposób przepływu danych w dół hierarchii składników z składnika nadrzędnego do dowolnej liczby składników decendent.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="3bc2a-105">W przeciwieństwie do [parametrów składnika](xref:blazor/components/index#component-parameters), wartości kaskadowych i parametrów nie wymagają przypisania atrybutu dla każdego składnika potomnego, w którym są używane dane.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="3bc2a-106">Kaskadowe wartości i parametry umożliwiają również współrzędną ze sobą składników w hierarchii składników.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="3bc2a-107">`CascadingValue` cm6long</span><span class="sxs-lookup"><span data-stu-id="3bc2a-107">`CascadingValue` component</span></span>

<span data-ttu-id="3bc2a-108">Składnik nadrzędny udostępnia wartość kaskadową przy użyciu Blazor [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) składnika platformy, który zawija poddrzewo hierarchii składników i dostarcza jedną wartość do wszystkich składników w ramach poddrzewa.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="3bc2a-109">Poniższy przykład ilustruje przepływ informacji o motywie w dół hierarchii składników składnika układu, aby udostępnić klasy stylu CSS do przycisków w składnikach podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="3bc2a-110">Następująca `ThemeInfo` Klasa języka C# jest umieszczana w folderze o nazwie `UIThemeClasses` i określa informacje o motywie.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="3bc2a-111">W przypadku przykładów w tej sekcji przestrzeń nazw aplikacji to `BlazorSample` .</span><span class="sxs-lookup"><span data-stu-id="3bc2a-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="3bc2a-112">Podczas eksperymentowania z kodem we własnej przykładowej aplikacji Zmień przestrzeń nazw aplikacji na przestrzeń nazw przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="3bc2a-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="3bc2a-114">Poniższy [składnik układu](xref:blazor/layouts) określa informacje o motywie ( `ThemeInfo` ) jako kaskadową wartość dla wszystkich składników, które tworzą treść układu <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> właściwości.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="3bc2a-115">`ButtonClass` ma przypisaną wartość [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) , która jest stylem przycisku ładowania początkowego.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="3bc2a-116">Każdy składnik podrzędny w hierarchii składników może użyć `ButtonClass` właściwości za pomocą `ThemeInfo` wartości kaskadowych.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="3bc2a-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="3bc2a-118">Atrybut `[CascadingParameter]`</span><span class="sxs-lookup"><span data-stu-id="3bc2a-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="3bc2a-119">Aby korzystać z wartości kaskadowych, składniki potomne deklarują kaskadowe parametry przy użyciu [ `[CascadingParameter]` atrybutu](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="3bc2a-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="3bc2a-120">Wartości kaskadowe są powiązane z parametrami kaskadowymi **według typu**.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="3bc2a-121">Kaskadowe łączenie wielu wartości tego samego typu jest omówione w sekcji [kaskadowe wiele wartości](#cascade-multiple-values) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="3bc2a-122">Poniższy składnik wiąże `ThemeInfo` wartość kaskadową z parametrem kaskadowym, opcjonalnie używając tej samej nazwy `ThemeInfo` .</span><span class="sxs-lookup"><span data-stu-id="3bc2a-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="3bc2a-123">Parametr służy do ustawiania klasy CSS **`Increment Counter (Themed)`** przycisku.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="3bc2a-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="3bc2a-125">Kaskadowe wiele wartości</span><span class="sxs-lookup"><span data-stu-id="3bc2a-125">Cascade multiple values</span></span>

<span data-ttu-id="3bc2a-126">Aby przetworzyć kaskadowo wiele wartości tego samego typu w ramach tego samego poddrzewa, podaj unikatowy <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> ciąg dla każdego [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) składnika i ich odpowiednie [ `[CascadingParameter]` atrybuty](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="3bc2a-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="3bc2a-127">W poniższym przykładzie dwa [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) składniki kaskaduje różne wystąpienia `CascadingType` :</span><span class="sxs-lookup"><span data-stu-id="3bc2a-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

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

<span data-ttu-id="3bc2a-128">W składniku potomnym, kaskadowe parametry otrzymują swoje kaskadowe wartości z składnika nadrzędnego przez <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> :</span><span class="sxs-lookup"><span data-stu-id="3bc2a-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="3bc2a-129">Przekazywanie danych w hierarchii składników</span><span class="sxs-lookup"><span data-stu-id="3bc2a-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="3bc2a-130">Parametry kaskadowe umożliwiają również składnikom przekazywanie danych w hierarchii składników.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="3bc2a-131">Rozważmy następujący przykład zestawu kart interfejsu użytkownika, gdzie składnik zestawu kart utrzymuje serię poszczególnych kart.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="3bc2a-132">W przypadku przykładów w tej sekcji przestrzeń nazw aplikacji to `BlazorSample` .</span><span class="sxs-lookup"><span data-stu-id="3bc2a-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="3bc2a-133">Podczas eksperymentowania z kodem we własnej przykładowej aplikacji Zmień przestrzeń nazw na przestrzeń nazw przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="3bc2a-134">Utwórz `ITab` interfejs, który umożliwia zaimplementowanie kart w folderze o nazwie `UIInterfaces` .</span><span class="sxs-lookup"><span data-stu-id="3bc2a-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="3bc2a-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-135">`UIInterfaces/ITab.cs`:</span></span>

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

<span data-ttu-id="3bc2a-136">Poniższy `TabSet` składnik utrzymuje zestaw kart.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="3bc2a-137">Składniki zestawu kart `Tab` , które są tworzone w dalszej części tej sekcji, dostarczają listę elementów ( `<li>...</li>` ) dla listy ( `<ul>...</ul>` ).</span><span class="sxs-lookup"><span data-stu-id="3bc2a-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="3bc2a-138">`Tab`Składniki podrzędne nie są jawnie przenoszone jako parametry do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="3bc2a-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="3bc2a-139">Zamiast tego składniki podrzędne `Tab` są częścią zawartości elementu podrzędnego `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="3bc2a-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="3bc2a-140">Jednak `TabSet` nadal wymaga odwołania do każdego składnika, `Tab` Aby można było renderować nagłówki i aktywną kartę. Aby umożliwić tę koordynację bez konieczności stosowania dodatkowego kodu, `TabSet` składnik *może stanowić wartość kaskadową* , która następnie jest wybierana przez `Tab` składniki potomne.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="3bc2a-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-141">`Shared/TabSet.razor`:</span></span>

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

<span data-ttu-id="3bc2a-142">`Tab`Składniki potomne przechwytują zawierającego je `TabSet` jako parametr kaskadowy.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="3bc2a-143">`Tab`Składniki dodają siebie do `TabSet` i koordynują, aby ustawić aktywną kartę.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="3bc2a-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-144">`Shared/Tab.razor`:</span></span>

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

<span data-ttu-id="3bc2a-145">Poniższy `ExampleTabSet` składnik używa `TabSet` składnika, który zawiera trzy `Tab` składniki.</span><span class="sxs-lookup"><span data-stu-id="3bc2a-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="3bc2a-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="3bc2a-146">`Pages/ExampleTabSet.razor`:</span></span>

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
