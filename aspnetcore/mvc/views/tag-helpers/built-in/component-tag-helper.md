---
title: Pomocnik tagu składnika w ASP.NET Core
author: guardrex
ms.author: riande
description: 'Dowiedz się, jak używać pomocnika tagów składnika ASP.NET Core, aby renderować :::no-loc(Razor)::: składniki na stronach i widokach.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595457"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="a4757-103">Pomocnik tagu składnika w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4757-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="a4757-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4757-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4757-105">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a4757-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a4757-106">Postępuj zgodnie ze wskazówkami w sekcji *konfiguracji* dla następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="a4757-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a4757-107">Postępuj zgodnie ze wskazówkami w sekcji *Konfiguracja* <xref:blazor/components/prerendering-and-integration?pivots=server> artykułu.</span><span class="sxs-lookup"><span data-stu-id="a4757-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="a4757-108">Pomocnik tagów składnika</span><span class="sxs-lookup"><span data-stu-id="a4757-108">Component Tag Helper</span></span>

<span data-ttu-id="a4757-109">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag).</span><span class="sxs-lookup"><span data-stu-id="a4757-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="a4757-110">Integrowanie :::no-loc(Razor)::: składników na :::no-loc(Razor)::: stronach i aplikacjach MVC w *hostowanej :::no-loc(Blazor WebAssembly)::: aplikacji* jest obsługiwane w ASP.NET Core na platformie .NET 5,0 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="a4757-110">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="a4757-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="a4757-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="a4757-112">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="a4757-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="a4757-113">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia :::no-loc(Blazor)::: aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a4757-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a4757-114">:::no-loc(Blazor WebAssembly)::: w poniższej tabeli przedstawiono tryby renderowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-114">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="a4757-115">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="a4757-115">Render Mode</span></span> | <span data-ttu-id="a4757-116">Opis</span><span class="sxs-lookup"><span data-stu-id="a4757-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="a4757-117">Renderuje znacznik dla :::no-loc(Blazor WebAssembly)::: aplikacji w celu uwzględnienia składnika interaktywnego, gdy jest ładowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a4757-117">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="a4757-118">Składnik nie jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="a4757-118">The component isn't prerendered.</span></span> <span data-ttu-id="a4757-119">Ta opcja ułatwia renderowanie różnych :::no-loc(Blazor WebAssembly)::: składników na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="a4757-119">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="a4757-120">Wstępnie renderuje składnik do statycznego kodu HTML i zawiera znacznik dla :::no-loc(Blazor WebAssembly)::: aplikacji w celu późniejszego użycia w celu zapewnienia, że składnik będzie interaktywny po załadowaniu w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a4757-120">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="a4757-121">:::no-loc(Blazor Server)::: w poniższej tabeli przedstawiono tryby renderowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-121">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="a4757-122">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="a4757-122">Render Mode</span></span> | <span data-ttu-id="a4757-123">Opis</span><span class="sxs-lookup"><span data-stu-id="a4757-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="a4757-124">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-124">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="a4757-125">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania :::no-loc(Blazor)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-125">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="a4757-126">Renderuje znacznik dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-126">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="a4757-127">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="a4757-127">Output from the component isn't included.</span></span> <span data-ttu-id="a4757-128">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania :::no-loc(Blazor)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-128">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="a4757-129">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="a4757-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a4757-130">:::no-loc(Blazor Server)::: w poniższej tabeli przedstawiono tryby renderowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-130">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="a4757-131">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="a4757-131">Render Mode</span></span> | <span data-ttu-id="a4757-132">Opis</span><span class="sxs-lookup"><span data-stu-id="a4757-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="a4757-133">Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-133">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="a4757-134">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania :::no-loc(Blazor)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-134">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="a4757-135">Renderuje znacznik dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-135">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="a4757-136">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="a4757-136">Output from the component isn't included.</span></span> <span data-ttu-id="a4757-137">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania :::no-loc(Blazor)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-137">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="a4757-138">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="a4757-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="a4757-139">Dodatkowe cechy obejmują:</span><span class="sxs-lookup"><span data-stu-id="a4757-139">Additional characteristics include:</span></span>

* <span data-ttu-id="a4757-140">Wiele pomocników tagów składnika renderuje wiele :::no-loc(Razor)::: składników jest dozwolonych.</span><span class="sxs-lookup"><span data-stu-id="a4757-140">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="a4757-141">Nie można dynamicznie renderować składników po rozpoczęciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="a4757-142">Podczas gdy strony i widoki mogą korzystać ze składników, wartość nie jest równa "true".</span><span class="sxs-lookup"><span data-stu-id="a4757-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="a4757-143">Składniki nie mogą korzystać z funkcji specjalnych, takich jak widoki częściowe i sekcje.</span><span class="sxs-lookup"><span data-stu-id="a4757-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="a4757-144">Aby użyć logiki z widoku częściowego w składniku, należy rozłożyć logikę widoku częściowego na składnik.</span><span class="sxs-lookup"><span data-stu-id="a4757-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="a4757-145">Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a4757-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="a4757-146">Poniższy pomocnik tagów składnika renderuje `Counter` składnik na stronie lub widoku w :::no-loc(Blazor Server)::: aplikacji za pomocą `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="a4757-146">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="a4757-147">W poprzednim przykładzie przyjęto założenie, że `Counter` składnik znajduje się w folderze *strony* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="a4757-148">Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji (na przykład `@using :::no-loc(Blazor):::Sample.Pages` lub `@using :::no-loc(Blazor):::Sample.Client.Pages` w rozwiązaniu hostowanym :::no-loc(Blazor)::: ).</span><span class="sxs-lookup"><span data-stu-id="a4757-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="a4757-149">Pomocnik tagów składnika może również przekazywać parametry do składników.</span><span class="sxs-lookup"><span data-stu-id="a4757-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="a4757-150">Rozważmy poniższy `ColorfulCheckbox` składnik, który ustawia kolor i rozmiar etykiety pola wyboru:</span><span class="sxs-lookup"><span data-stu-id="a4757-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="a4757-151">`Size` `int` Parametry składnika () `Color` i `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) mogą być ustawiane przez pomocnika tagów składnika:</span><span class="sxs-lookup"><span data-stu-id="a4757-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="a4757-152">W poprzednim przykładzie przyjęto założenie, że `ColorfulCheckbox` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="a4757-153">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="a4757-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="a4757-154">Następujący kod HTML jest renderowany na stronie lub w widoku:</span><span class="sxs-lookup"><span data-stu-id="a4757-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="a4757-155">Przekazywanie ciągu w cudzysłowie wymaga [ :::no-loc(Razor)::: wyrażenia jawnego](xref:mvc/views/razor#explicit-razor-expressions), jak pokazano `param-Color` w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a4757-155">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="a4757-156">:::no-loc(Razor):::Zachowanie analizy dla `string` wartości typu nie ma zastosowania do atrybutu, `param-*` ponieważ atrybut jest `object` typem.</span><span class="sxs-lookup"><span data-stu-id="a4757-156">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="a4757-157">Wszystkie typy parametrów są obsługiwane, z wyjątkiem:</span><span class="sxs-lookup"><span data-stu-id="a4757-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="a4757-158">Parametry ogólne.</span><span class="sxs-lookup"><span data-stu-id="a4757-158">Generic parameters.</span></span>
* <span data-ttu-id="a4757-159">Parametry, których nie można serializować.</span><span class="sxs-lookup"><span data-stu-id="a4757-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="a4757-160">Dziedziczenie w parametrach kolekcji.</span><span class="sxs-lookup"><span data-stu-id="a4757-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="a4757-161">Parametry, których typ jest zdefiniowany poza :::no-loc(Blazor WebAssembly)::: aplikacją lub w ramach zestawu załadowanego przez opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="a4757-161">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="a4757-162">Typ parametru musi być możliwy do serializacji JSON, co oznacza, że typ musi mieć domyślny Konstruktor i właściwości settable.</span><span class="sxs-lookup"><span data-stu-id="a4757-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="a4757-163">Na przykład można określić wartość dla `Size` i `Color` w poprzednim przykładzie, ponieważ typy `Size` i `Color` są typami pierwotnymi ( `int` i `string` ), które są obsługiwane przez serializator JSON.</span><span class="sxs-lookup"><span data-stu-id="a4757-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="a4757-164">W poniższym przykładzie obiekt klasy jest przenoszona do składnika:</span><span class="sxs-lookup"><span data-stu-id="a4757-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="a4757-165">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="a4757-165">*MyClass.cs* :</span></span>

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

<span data-ttu-id="a4757-166">**Klasa musi mieć publiczny Konstruktor bez parametrów.**</span><span class="sxs-lookup"><span data-stu-id="a4757-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="a4757-167">*Shared/webcomponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="a4757-167">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="a4757-168">*Strony/Moje strony. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a4757-168">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="a4757-169">W poprzednim przykładzie przyjęto założenie, że `MyComponent` składnik znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="a4757-170">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using :::no-loc(Blazor):::Sample` i `@using :::no-loc(Blazor):::Sample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="a4757-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="a4757-171">`MyClass` znajduje się w przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4757-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4757-172">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a4757-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
