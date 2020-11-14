---
title: Pomocnik tagu składnika w ASP.NET Core
author: guardrex
ms.author: riande
description: 'Dowiedz się, jak używać pomocnika tagów składnika ASP.NET Core, aby renderować Razor składniki na stronach i widokach.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431046"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="53271-103">Pomocnik tagu składnika w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53271-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="53271-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="53271-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="53271-105">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="53271-105">Prerequisites</span></span>

<span data-ttu-id="53271-106">Postępuj zgodnie ze wskazówkami w sekcji *konfiguracji* dla następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="53271-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="53271-107">Pomocnik tagów składnika</span><span class="sxs-lookup"><span data-stu-id="53271-107">Component Tag Helper</span></span>

<span data-ttu-id="53271-108">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag).</span><span class="sxs-lookup"><span data-stu-id="53271-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="53271-109">Integrowanie Razor składników na Razor stronach i aplikacjach MVC w *hostowanej Blazor WebAssembly aplikacji* jest obsługiwane w ASP.NET Core na platformie .NET 5,0 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="53271-109">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="53271-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="53271-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="53271-111">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="53271-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="53271-112">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="53271-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="53271-113">Blazor WebAssembly w poniższej tabeli przedstawiono tryby renderowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-113">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="53271-114">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="53271-114">Render Mode</span></span> | <span data-ttu-id="53271-115">Opis</span><span class="sxs-lookup"><span data-stu-id="53271-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="53271-116">Renderuje znacznik dla Blazor WebAssembly aplikacji w celu uwzględnienia składnika interaktywnego, gdy jest ładowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="53271-116">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="53271-117">Składnik nie jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="53271-117">The component isn't prerendered.</span></span> <span data-ttu-id="53271-118">Ta opcja ułatwia renderowanie różnych Blazor WebAssembly składników na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="53271-118">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="53271-119">Wstępnie renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor WebAssembly aplikacji w celu późniejszego użycia w celu zapewnienia, że składnik będzie interaktywny po załadowaniu w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="53271-119">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="53271-120">Blazor Server w poniższej tabeli przedstawiono tryby renderowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-120">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="53271-121">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="53271-121">Render Mode</span></span> | <span data-ttu-id="53271-122">Opis</span><span class="sxs-lookup"><span data-stu-id="53271-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="53271-123">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-123">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="53271-124">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="53271-125">Renderuje znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-125">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="53271-126">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="53271-126">Output from the component isn't included.</span></span> <span data-ttu-id="53271-127">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-127">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="53271-128">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="53271-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="53271-129">Blazor Server w poniższej tabeli przedstawiono tryby renderowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-129">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="53271-130">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="53271-130">Render Mode</span></span> | <span data-ttu-id="53271-131">Opis</span><span class="sxs-lookup"><span data-stu-id="53271-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="53271-132">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="53271-133">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="53271-134">Renderuje znacznik dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="53271-135">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="53271-135">Output from the component isn't included.</span></span> <span data-ttu-id="53271-136">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="53271-137">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="53271-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="53271-138">Dodatkowe cechy obejmują:</span><span class="sxs-lookup"><span data-stu-id="53271-138">Additional characteristics include:</span></span>

* <span data-ttu-id="53271-139">Wiele pomocników tagów składnika renderuje wiele Razor składników jest dozwolonych.</span><span class="sxs-lookup"><span data-stu-id="53271-139">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="53271-140">Nie można dynamicznie renderować składników po rozpoczęciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="53271-141">Podczas gdy strony i widoki mogą korzystać ze składników, wartość nie jest równa "true".</span><span class="sxs-lookup"><span data-stu-id="53271-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="53271-142">Składniki nie mogą korzystać z funkcji specjalnych, takich jak widoki częściowe i sekcje.</span><span class="sxs-lookup"><span data-stu-id="53271-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="53271-143">Aby użyć logiki z widoku częściowego w składniku, należy rozłożyć logikę widoku częściowego na składnik.</span><span class="sxs-lookup"><span data-stu-id="53271-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="53271-144">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="53271-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="53271-145">Poniższy pomocnik tagów składnika renderuje `Counter` składnik na stronie lub widoku w Blazor Server aplikacji za pomocą `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="53271-145">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="53271-146">W poprzednim przykładzie przyjęto założenie, że `Counter` składnik znajduje się w folderze *strony* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="53271-147">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji (na przykład `@using BlazorSample.Pages` lub `@using BlazorSample.Client.Pages` w rozwiązaniu hostowanym Blazor ).</span><span class="sxs-lookup"><span data-stu-id="53271-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="53271-148">Pomocnik tagów składnika może również przekazywać parametry do składników.</span><span class="sxs-lookup"><span data-stu-id="53271-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="53271-149">Rozważmy poniższy `ColorfulCheckbox` składnik, który ustawia kolor i rozmiar etykiety pola wyboru:</span><span class="sxs-lookup"><span data-stu-id="53271-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="53271-150">`Size` `int` Parametry składnika () `Color` i `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) mogą być ustawiane przez pomocnika tagów składnika:</span><span class="sxs-lookup"><span data-stu-id="53271-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="53271-151">W poprzednim przykładzie przyjęto założenie, że `ColorfulCheckbox` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="53271-152">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="53271-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="53271-153">Następujący kod HTML jest renderowany na stronie lub w widoku:</span><span class="sxs-lookup"><span data-stu-id="53271-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="53271-154">Przekazywanie ciągu w cudzysłowie wymaga [ Razor wyrażenia jawnego](xref:mvc/views/razor#explicit-razor-expressions), jak pokazano `param-Color` w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="53271-154">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="53271-155">RazorZachowanie analizy dla `string` wartości typu nie ma zastosowania do atrybutu, `param-*` ponieważ atrybut jest `object` typem.</span><span class="sxs-lookup"><span data-stu-id="53271-155">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="53271-156">Wszystkie typy parametrów są obsługiwane, z wyjątkiem:</span><span class="sxs-lookup"><span data-stu-id="53271-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="53271-157">Parametry ogólne.</span><span class="sxs-lookup"><span data-stu-id="53271-157">Generic parameters.</span></span>
* <span data-ttu-id="53271-158">Parametry, których nie można serializować.</span><span class="sxs-lookup"><span data-stu-id="53271-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="53271-159">Dziedziczenie w parametrach kolekcji.</span><span class="sxs-lookup"><span data-stu-id="53271-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="53271-160">Parametry, których typ jest zdefiniowany poza Blazor WebAssembly aplikacją lub w ramach zestawu załadowanego przez opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="53271-160">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="53271-161">Typ parametru musi być możliwy do serializacji JSON, co oznacza, że typ musi mieć domyślny Konstruktor i właściwości settable.</span><span class="sxs-lookup"><span data-stu-id="53271-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="53271-162">Na przykład można określić wartość dla `Size` i `Color` w poprzednim przykładzie, ponieważ typy `Size` i `Color` są typami pierwotnymi ( `int` i `string` ), które są obsługiwane przez serializator JSON.</span><span class="sxs-lookup"><span data-stu-id="53271-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="53271-163">W poniższym przykładzie obiekt klasy jest przenoszona do składnika:</span><span class="sxs-lookup"><span data-stu-id="53271-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="53271-164">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="53271-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="53271-165">**Klasa musi mieć publiczny Konstruktor bez parametrów.**</span><span class="sxs-lookup"><span data-stu-id="53271-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="53271-166">*Shared/webcomponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="53271-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="53271-167">*Strony/Moje strony. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="53271-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="53271-168">W poprzednim przykładzie przyjęto założenie, że `MyComponent` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="53271-169">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample` i `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="53271-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="53271-170">`MyClass` znajduje się w przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="53271-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53271-171">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="53271-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
