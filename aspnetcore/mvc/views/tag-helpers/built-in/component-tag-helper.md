---
title: Pomocnik znaczników składników w ASP.NET rdzeniu
author: guardrex
ms.author: riande
description: Dowiedz się, jak używać pomocnika ASP.NET Core Component Tag Helper do renderowania składników Razor na stronach i widokach.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123427"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="3d4ce-103">Pomocnik znaczników składników w ASP.NET rdzeniu</span><span class="sxs-lookup"><span data-stu-id="3d4ce-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3d4ce-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d4ce-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3d4ce-105">Aby renderować składnik ze strony lub widoku, użyj [pomocnika znacznika składnika](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="3d4ce-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3d4ce-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3d4ce-106">Prerequisites</span></span>

<span data-ttu-id="3d4ce-107">Postępuj zgodnie ze wskazówkami w *przygotowaniu aplikacji do używania składników w* sekcji strony i widoki <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> artykułu.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="3d4ce-108">Pomocnik znaczników składników</span><span class="sxs-lookup"><span data-stu-id="3d4ce-108">Component Tag Helper</span></span>

<span data-ttu-id="3d4ce-109">Pomocnik znacznika składnika renderuje składnik na `Counter` stronie lub w widoku:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="3d4ce-110">W poprzednim przykładzie przyjęto założenie, że `Counter` składnik znajduje się w folderze *Strony* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="3d4ce-111">Pomocnik znaczników składnika może również przekazywać parametry do komponentów.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="3d4ce-112">Należy wziąć `ColorfulCheckbox` pod uwagę następujący składnik, który ustawia kolor i rozmiar etykiety pola wyboru:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="3d4ce-113">Parametry `Size` `int` [komponentu](xref:blazor/components#component-parameters) `string`( ) i `Color` ( ) mogą być ustawiane przez pomocnika znacznika komponentu:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="3d4ce-114">W poprzednim przykładzie przyjęto założenie, że `ColorfulCheckbox` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="3d4ce-115">Na stronie lub w widoku renderowany jest następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="3d4ce-116">Przekazywanie cytowanego ciągu wymaga [jawnego wyrażenia Razor](xref:mvc/views/razor#explicit-razor-expressions), jak pokazano `param-Color` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="3d4ce-117">Razor analizowanie zachowanie dla `string` wartości typu nie ma `param-*` zastosowania do atrybutu, `object` ponieważ atrybut jest typem.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="3d4ce-118">Typ parametru musi być serializowalny JSON, co zazwyczaj oznacza, że typ musi mieć domyślny konstruktor i właściwości settable.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="3d4ce-119">Na przykład można określić `Size` wartość `Color` dla i w poprzednim `Size` przykładzie, ponieważ typy i `Color` są typami pierwotnymi (`int` i `string`), które są obsługiwane przez serializatorA JSON.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="3d4ce-120">W poniższym przykładzie obiekt klasy jest przekazywany do składnika:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="3d4ce-121">*MyClass.cs:*</span><span class="sxs-lookup"><span data-stu-id="3d4ce-121">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="3d4ce-122">**Klasa musi mieć publiczny konstruktor bez parametrów.**</span><span class="sxs-lookup"><span data-stu-id="3d4ce-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="3d4ce-123">*Udostępnione/MyComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-123">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="3d4ce-124">*Strony/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-124">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="3d4ce-125">W poprzednim przykładzie przyjęto założenie, że `MyComponent` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="3d4ce-126">`MyClass`znajduje się w obszarze nazw`{APP ASSEMBLY}`aplikacji ( ).</span><span class="sxs-lookup"><span data-stu-id="3d4ce-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="3d4ce-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="3d4ce-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3d4ce-128">Jest prerendered do strony.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="3d4ce-129">Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="3d4ce-130">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="3d4ce-130">Render Mode</span></span> | <span data-ttu-id="3d4ce-131">Opis</span><span class="sxs-lookup"><span data-stu-id="3d4ce-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3d4ce-132">Renderuje składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3d4ce-133">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3d4ce-134">Renderuje znacznik aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3d4ce-135">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-135">Output from the component isn't included.</span></span> <span data-ttu-id="3d4ce-136">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3d4ce-137">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3d4ce-138">Podczas gdy strony i widoki mogą używać składników, odwrotność nie jest prawdziwa.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="3d4ce-139">Składniki nie mogą używać funkcji specyficznych dla widoku i strony, takich jak widoki częściowe i sekcje.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="3d4ce-140">Aby użyć logiki z widoku częściowego w komponencie, należy uwzględnić logikę widoku częściowego w składniku.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="3d4ce-141">Renderowanie składników serwera ze statycznej strony HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3d4ce-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d4ce-142">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3d4ce-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
