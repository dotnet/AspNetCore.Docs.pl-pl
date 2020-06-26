---
title: Pomocnik tagu składnika w ASP.NET Core
author: guardrex
ms.author: riande
description: Dowiedz się, jak używać pomocnika tagów składnika ASP.NET Core, aby renderować Razor składniki na stronach i widokach.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: c088cb7dd4f446b6a42c63357ccf2a080d852382
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399247"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="b69a2-103">Pomocnik tagu składnika w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b69a2-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b69a2-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b69a2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b69a2-105">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="b69a2-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b69a2-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b69a2-106">Prerequisites</span></span>

<span data-ttu-id="b69a2-107">Postępuj zgodnie ze wskazówkami zawartymi w sekcji *przygotowanie aplikacji do używania składników w stronach i widokach* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> artykułu.</span><span class="sxs-lookup"><span data-stu-id="b69a2-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="b69a2-108">Pomocnik tagów składnika</span><span class="sxs-lookup"><span data-stu-id="b69a2-108">Component Tag Helper</span></span>

<span data-ttu-id="b69a2-109">Poniższy pomocnik tagów składnika renderuje `Counter` składnik na stronie lub widoku:</span><span class="sxs-lookup"><span data-stu-id="b69a2-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="b69a2-110">W poprzednim przykładzie przyjęto założenie, że `Counter` składnik znajduje się w folderze *strony* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="b69a2-111">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="b69a2-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="b69a2-112">Pomocnik tagów składnika może również przekazywać parametry do składników.</span><span class="sxs-lookup"><span data-stu-id="b69a2-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="b69a2-113">Rozważmy poniższy `ColorfulCheckbox` składnik, który ustawia kolor i rozmiar etykiety pola wyboru:</span><span class="sxs-lookup"><span data-stu-id="b69a2-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="b69a2-114">`Size` `int` Parametry składnika () `Color` i `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) mogą być ustawiane przez pomocnika tagów składnika:</span><span class="sxs-lookup"><span data-stu-id="b69a2-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="b69a2-115">W poprzednim przykładzie przyjęto założenie, że `ColorfulCheckbox` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="b69a2-116">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="b69a2-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="b69a2-117">Następujący kod HTML jest renderowany na stronie lub w widoku:</span><span class="sxs-lookup"><span data-stu-id="b69a2-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="b69a2-118">Przekazywanie ciągu w cudzysłowie wymaga [ Razor wyrażenia jawnego](xref:mvc/views/razor#explicit-razor-expressions), jak pokazano `param-Color` w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b69a2-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="b69a2-119">RazorZachowanie analizy dla `string` wartości typu nie ma zastosowania do atrybutu, `param-*` ponieważ atrybut jest `object` typem.</span><span class="sxs-lookup"><span data-stu-id="b69a2-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="b69a2-120">Typ parametru musi być możliwy do serializacji JSON, co oznacza, że typ musi mieć domyślny Konstruktor i właściwości settable.</span><span class="sxs-lookup"><span data-stu-id="b69a2-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="b69a2-121">Na przykład można określić wartość dla `Size` i `Color` w poprzednim przykładzie, ponieważ typy `Size` i `Color` są typami pierwotnymi ( `int` i `string` ), które są obsługiwane przez serializator JSON.</span><span class="sxs-lookup"><span data-stu-id="b69a2-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="b69a2-122">W poniższym przykładzie obiekt klasy jest przenoszona do składnika:</span><span class="sxs-lookup"><span data-stu-id="b69a2-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="b69a2-123">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="b69a2-123">*MyClass.cs*:</span></span>

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

<span data-ttu-id="b69a2-124">**Klasa musi mieć publiczny Konstruktor bez parametrów.**</span><span class="sxs-lookup"><span data-stu-id="b69a2-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="b69a2-125">*Shared/webcomponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b69a2-125">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="b69a2-126">*Strony/Moje strony. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b69a2-126">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="b69a2-127">W poprzednim przykładzie przyjęto założenie, że `MyComponent` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="b69a2-128">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample` i `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="b69a2-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="b69a2-129">`MyClass`znajduje się w przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="b69a2-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="b69a2-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="b69a2-131">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="b69a2-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="b69a2-132">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b69a2-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="b69a2-133">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="b69a2-133">Render Mode</span></span> | <span data-ttu-id="b69a2-134">Opis</span><span class="sxs-lookup"><span data-stu-id="b69a2-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="b69a2-135">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b69a2-136">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="b69a2-137">Renderuje znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b69a2-138">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="b69a2-138">Output from the component isn't included.</span></span> <span data-ttu-id="b69a2-139">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b69a2-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="b69a2-140">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="b69a2-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="b69a2-141">Podczas gdy strony i widoki mogą korzystać ze składników, wartość nie jest równa "true".</span><span class="sxs-lookup"><span data-stu-id="b69a2-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="b69a2-142">Składniki nie mogą korzystać z funkcji specjalnych, takich jak widoki częściowe i sekcje.</span><span class="sxs-lookup"><span data-stu-id="b69a2-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="b69a2-143">Aby użyć logiki z widoku częściowego w składniku, należy rozłożyć logikę widoku częściowego na składnik.</span><span class="sxs-lookup"><span data-stu-id="b69a2-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="b69a2-144">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="b69a2-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b69a2-145">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b69a2-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
