---
title: Tworzenie i używanie Razor składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak tworzyć i używać Razor składników, w tym jak powiązać z danymi, obsługiwać zdarzenia i zarządzać cyklem życia składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: d25dc3441c2373655558dfc101b899252a280814
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944448"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="8a84a-103">Tworzenie i używanie Razor składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a84a-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="8a84a-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="8a84a-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="8a84a-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/../common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a84a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/../common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="8a84a-106">aplikacje są kompilowane przy użyciu *składników*programu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-106"> apps are built using *components*.</span></span> <span data-ttu-id="8a84a-107">Składnik jest niezależnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz.</span><span class="sxs-lookup"><span data-stu-id="8a84a-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="8a84a-108">Składnik zawiera znaczniki HTML i logikę przetwarzania wymagane do iniekcji danych lub reagowania na zdarzenia interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="8a84a-109">Składniki są elastyczne i lekkie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="8a84a-110">Mogą być zagnieżdżane, ponownie używane i udostępniane między projektami.</span><span class="sxs-lookup"><span data-stu-id="8a84a-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="8a84a-111">Klasy składników</span><span class="sxs-lookup"><span data-stu-id="8a84a-111">Component classes</span></span>

<span data-ttu-id="8a84a-112">Składniki są implementowane w [Razor](xref:mvc/views/razor) plikach składników ( `.razor` ) przy użyciu kombinacji języka C# i znaczników HTML.</span><span class="sxs-lookup"><span data-stu-id="8a84a-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="8a84a-113">Składnik w programie Blazor jest formalnie nazywany \* Razor składnikiem\*.</span><span class="sxs-lookup"><span data-stu-id="8a84a-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a>Razor<span data-ttu-id="8a84a-114">obowiązuje</span><span class="sxs-lookup"><span data-stu-id="8a84a-114"> syntax</span></span>

Razor<span data-ttu-id="8a84a-115">składniki w Blazor aplikacjach szeroko wykorzystują Razor składnię.</span><span class="sxs-lookup"><span data-stu-id="8a84a-115"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="8a84a-116">Jeśli nie znasz Razor języka znaczników, zalecamy przeczytanie <xref:mvc/views/razor> przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="8a84a-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="8a84a-117">Podczas uzyskiwania dostępu do zawartości w Razor składni należy zwrócić szczególną uwagę na następujące sekcje:</span><span class="sxs-lookup"><span data-stu-id="8a84a-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="8a84a-118">[Dyrektywy](xref:mvc/views/razor#directives): `@` — wstępnie ustalone zastrzeżone słowa kluczowe, które zwykle zmieniają sposób, w jaki znaczniki składnika są analizowane lub działają.</span><span class="sxs-lookup"><span data-stu-id="8a84a-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="8a84a-119">[Atrybuty dyrektywy](xref:mvc/views/razor#directive-attributes): `@` — wstępnie ustalone zastrzeżone słowa kluczowe, które zwykle zmieniają sposób analizowania lub działania elementów składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="8a84a-120">Nazwy</span><span class="sxs-lookup"><span data-stu-id="8a84a-120">Names</span></span>

<span data-ttu-id="8a84a-121">Nazwa składnika musi rozpoczynać się wielką literą.</span><span class="sxs-lookup"><span data-stu-id="8a84a-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="8a84a-122">Na przykład `MyCoolComponent.razor` prawidłowe i `myCoolComponent.razor` jest nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="8a84a-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="8a84a-123">Routing</span><span class="sxs-lookup"><span data-stu-id="8a84a-123">Routing</span></span>

<span data-ttu-id="8a84a-124">Routing w programie jest realizowany Blazor przez dostarczenie szablonu trasy do każdego dostępnego składnika w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="8a84a-125">Gdy Razor plik z [`@page`][9] dyrektywą jest kompilowany, wygenerowana Klasa ma określony <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="8a84a-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="8a84a-126">W czasie wykonywania router szuka klas składników za pomocą <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> i renderuje, w zależności od tego, który składnik ma szablon trasy zgodny z żądanym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="8a84a-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="8a84a-127">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="8a84a-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="8a84a-128">Znaczniki</span><span class="sxs-lookup"><span data-stu-id="8a84a-128">Markup</span></span>

<span data-ttu-id="8a84a-129">Interfejs użytkownika dla składnika jest definiowany przy użyciu języka HTML.</span><span class="sxs-lookup"><span data-stu-id="8a84a-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="8a84a-130">Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie *Razor* .</span><span class="sxs-lookup"><span data-stu-id="8a84a-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="8a84a-131">Po skompilowaniu aplikacji logika kodu HTML i renderowania języka C# jest konwertowana na klasę składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="8a84a-132">Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="8a84a-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="8a84a-133">Elementy członkowskie klasy składnika są zdefiniowane w [`@code`][1] bloku.</span><span class="sxs-lookup"><span data-stu-id="8a84a-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="8a84a-134">W [`@code`][1] bloku stan składnika (właściwości, pola) jest określany przy użyciu metod obsługi zdarzeń lub definiowania innej logiki składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="8a84a-135">Dozwolony jest więcej niż jeden [`@code`][1] blok.</span><span class="sxs-lookup"><span data-stu-id="8a84a-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="8a84a-136">Składowe składnika mogą być używane jako część logiki renderowania składnika przy użyciu wyrażeń języka C#, które zaczynają się od `@` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="8a84a-137">Na przykład pole C# jest renderowane przez utworzenie prefiksu `@` do nazwy pola.</span><span class="sxs-lookup"><span data-stu-id="8a84a-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="8a84a-138">Poniższy przykład szacuje i renderuje:</span><span class="sxs-lookup"><span data-stu-id="8a84a-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="8a84a-139">`headingFontStyle`na wartość właściwości CSS dla elementu `font-style` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="8a84a-140">`headingText`do zawartości `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="8a84a-141">Po pierwszym wyrenderowaniu składnika składnik generuje jego drzewo renderowania w odpowiedzi na zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="8a84a-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="8a84a-142">Następnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje Document Object Model przeglądarki (DOM).</span><span class="sxs-lookup"><span data-stu-id="8a84a-142"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="8a84a-143">Składniki to zwykłe klasy języka C# i można je umieścić w dowolnym miejscu w projekcie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="8a84a-144">Składniki, które generują strony sieci Web, zwykle znajdują się w `Pages` folderze.</span><span class="sxs-lookup"><span data-stu-id="8a84a-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="8a84a-145">Składniki niestronicowe są często umieszczane w `Shared` folderze lub do folderu niestandardowego dodanego do projektu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="8a84a-146">Przestrzenie nazw</span><span class="sxs-lookup"><span data-stu-id="8a84a-146">Namespaces</span></span>

<span data-ttu-id="8a84a-147">Zazwyczaj przestrzeń nazw składnika pochodzi od głównej przestrzeni nazw aplikacji i lokalizacji składnika (folderu) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="8a84a-148">Jeśli główna przestrzeń nazw aplikacji jest `BlazorApp` i znajduje się `Counter` w `Pages` folderze:</span><span class="sxs-lookup"><span data-stu-id="8a84a-148">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="8a84a-149">`Counter`Przestrzeń nazw składnika to `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-149">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="8a84a-150">W pełni kwalifikowana nazwa typu składnika to `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-150">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="8a84a-151">W przypadku folderów niestandardowych, które zawierają składniki, Dodaj [`@using`][2] dyrektywę do składnika nadrzędnego lub do `_Imports.razor` pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="8a84a-152">W poniższym przykładzie są dostępne składniki w `Components` folderze:</span><span class="sxs-lookup"><span data-stu-id="8a84a-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="8a84a-153">Do składników można także odwoływać się za pomocą ich w pełni kwalifikowanych nazw, które nie wymagają [`@using`][2] dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="8a84a-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="8a84a-154">Przestrzeń nazw składnika utworzona w programie Razor jest oparta na (w kolejności priorytetu):</span><span class="sxs-lookup"><span data-stu-id="8a84a-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="8a84a-155">[`@namespace`][8]oznaczenie w Razor pliku ( `.razor` ) Markup ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="8a84a-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="8a84a-156">Projekt znajduje się `RootNamespace` w pliku projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="8a84a-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="8a84a-157">Nazwa projektu, pobrana z pliku projektu nazwa pliku ( `.csproj` ) i ścieżka z katalogu głównego projektu do składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="8a84a-158">Na przykład struktura rozpoznaje `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) do przestrzeni nazw `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="8a84a-159">Składniki przestrzegają reguł powiązań nazw języka C#.</span><span class="sxs-lookup"><span data-stu-id="8a84a-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="8a84a-160">W przypadku `Index` składnika w tym przykładzie składniki należące do zakresu są wszystkich składników:</span><span class="sxs-lookup"><span data-stu-id="8a84a-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="8a84a-161">W tym samym folderze, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="8a84a-162">Składniki w katalogu głównym projektu, które nie określają jawnie innej przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="8a84a-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="8a84a-163">`global::`Kwalifikacja nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="8a84a-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="8a84a-164">Importowanie składników przy użyciu instrukcji z aliasami [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (np `@using Foo = Bar` .) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8a84a-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="8a84a-165">Częściowo kwalifikowane nazwy nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8a84a-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="8a84a-166">Na przykład dodawanie `@using BlazorSample` i odwoływanie się do `NavMenu` składnika ( `NavMenu.razor` ) za pomocą `<Shared.NavMenu></Shared.NavMenu>` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8a84a-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="8a84a-167">Obsługa klasy częściowej</span><span class="sxs-lookup"><span data-stu-id="8a84a-167">Partial class support</span></span>

Razor<span data-ttu-id="8a84a-168">składniki są generowane jako klasy częściowe.</span><span class="sxs-lookup"><span data-stu-id="8a84a-168"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="8a84a-169">składniki są tworzone przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="8a84a-169"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="8a84a-170">Kod C# jest zdefiniowany w [`@code`][1] bloku z oznaczeniem HTML i Razor kodem w pojedynczym pliku.</span><span class="sxs-lookup"><span data-stu-id="8a84a-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="8a84a-171">Szablony definiują ich Razor składniki przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="8a84a-171"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="8a84a-172">Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako Klasa częściowa.</span><span class="sxs-lookup"><span data-stu-id="8a84a-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="8a84a-173">Poniższy przykład pokazuje `Counter` składnik domyślny z [`@code`][1] blokiem w aplikacji wygenerowanej na podstawie Blazor szablonu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="8a84a-174">Znaczniki HTML, Razor kod i kod C# są w tym samym pliku:</span><span class="sxs-lookup"><span data-stu-id="8a84a-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="8a84a-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="8a84a-176">`Counter`Składnik można również utworzyć przy użyciu pliku związanego z kodem z klasą częściową:</span><span class="sxs-lookup"><span data-stu-id="8a84a-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="8a84a-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="8a84a-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="8a84a-179">W razie potrzeby dodaj wszystkie wymagane przestrzenie nazw do pliku klasy częściowej.</span><span class="sxs-lookup"><span data-stu-id="8a84a-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="8a84a-180">Typowe przestrzenie nazw używane przez Razor składniki obejmują:</span><span class="sxs-lookup"><span data-stu-id="8a84a-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

### <a name="specify-a-base-class"></a><span data-ttu-id="8a84a-181">Określ klasę bazową</span><span class="sxs-lookup"><span data-stu-id="8a84a-181">Specify a base class</span></span>

<span data-ttu-id="8a84a-182">[`@inherits`][6]Dyrektywa może służyć do określania klasy bazowej dla składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-182">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="8a84a-183">Poniższy przykład pokazuje, jak składnik może dziedziczyć klasę bazową, `BlazorRocksBase` , aby zapewnić właściwości i metody składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-183">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="8a84a-184">Klasa bazowa powinna pochodzić od <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="8a84a-184">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="8a84a-185">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-185">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="8a84a-186">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-186">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="8a84a-187">Używanie składników</span><span class="sxs-lookup"><span data-stu-id="8a84a-187">Use components</span></span>

<span data-ttu-id="8a84a-188">Składniki mogą zawierać inne składniki, deklarując je za pomocą składni elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="8a84a-188">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="8a84a-189">Znaczniki użycia składnika wyglądają jak tag HTML, gdzie nazwa znacznika jest typem składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-189">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="8a84a-190">Następujące znaczniki w programie `Pages/Index.razor` renderuje `HeadingComponent` wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="8a84a-190">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="8a84a-191">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-191">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="8a84a-192">Jeśli składnik zawiera element HTML z wielką literą, która nie jest zgodna z nazwą składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę.</span><span class="sxs-lookup"><span data-stu-id="8a84a-192">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="8a84a-193">Dodanie [`@using`][2] dyrektywy dla przestrzeni nazw składnika sprawia, że składnik jest dostępny, co rozwiązuje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-193">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="8a84a-194">Parametry</span><span class="sxs-lookup"><span data-stu-id="8a84a-194">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="8a84a-195">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="8a84a-195">Route parameters</span></span>

<span data-ttu-id="8a84a-196">Składniki mogą odbierać parametry trasy z szablonu trasy dostarczonego w [`@page`][9] dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-196">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="8a84a-197">Router używa parametrów trasy, aby wypełnić odpowiednie parametry składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-197">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="8a84a-198">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-198">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="8a84a-199">Parametry opcjonalne nie są obsługiwane, więc dwie [`@page`][9] dyrektywy są stosowane w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-199">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="8a84a-200">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="8a84a-200">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="8a84a-201">Druga [`@page`][9] dyrektywa odbiera `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="8a84a-201">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="8a84a-202">*Catch-all* parametrów ( `*` / `**` ), która przechwytuje ścieżkę między wieloma granicami folderów, **nie** jest obsługiwana w Razor składnikach ( `.razor` ).</span><span class="sxs-lookup"><span data-stu-id="8a84a-202">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (`.razor`).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="8a84a-203">Parametry składnika</span><span class="sxs-lookup"><span data-stu-id="8a84a-203">Component parameters</span></span>

<span data-ttu-id="8a84a-204">Składniki mogą mieć *Parametry składnika*, które są zdefiniowane przy użyciu właściwości publicznych w klasie składnika z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="8a84a-204">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="8a84a-205">Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.</span><span class="sxs-lookup"><span data-stu-id="8a84a-205">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="8a84a-206">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-206">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="8a84a-207">W poniższym przykładzie z przykładowej aplikacji `ParentComponent` ustawia wartość `Title` właściwości `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-207">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="8a84a-208">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-208">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="8a84a-209">Nie należy tworzyć składników, które zapisują do własnych *parametrów składników*, zamiast tego należy użyć pola private.</span><span class="sxs-lookup"><span data-stu-id="8a84a-209">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="8a84a-210">Aby uzyskać więcej informacji, zobacz sekcję [nie twórz składników, które zapisują we własnych właściwościach parametrów](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="8a84a-210">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="8a84a-211">Zawartość podrzędna</span><span class="sxs-lookup"><span data-stu-id="8a84a-211">Child content</span></span>

<span data-ttu-id="8a84a-212">Składniki mogą ustawiać zawartość innego składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-212">Components can set the content of another component.</span></span> <span data-ttu-id="8a84a-213">Składnik Assigner zawiera zawartość między tagami, które określają składnik do odbioru.</span><span class="sxs-lookup"><span data-stu-id="8a84a-213">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="8a84a-214">W poniższym przykładzie `ChildComponent` ma `ChildContent` Właściwość, która reprezentuje element <xref:Microsoft.AspNetCore.Components.RenderFragment> , który reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="8a84a-214">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="8a84a-215">Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana.</span><span class="sxs-lookup"><span data-stu-id="8a84a-215">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="8a84a-216">Wartość `ChildContent` jest odbierana ze składnika nadrzędnego i renderowany w panelu uruchamiania `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-216">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="8a84a-217">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-217">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="8a84a-218">Właściwość otrzymująca <xref:Microsoft.AspNetCore.Components.RenderFragment> zawartość musi być nazywana `ChildContent` Konwencją.</span><span class="sxs-lookup"><span data-stu-id="8a84a-218">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="8a84a-219">`ParentComponent`W przykładowej aplikacji można dostarczyć zawartość do renderowania `ChildComponent` przez umieszczenie zawartości wewnątrz `<ChildComponent>` tagów.</span><span class="sxs-lookup"><span data-stu-id="8a84a-219">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="8a84a-220">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-220">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="8a84a-221">Korzystając atrybutów i dowolne parametry</span><span class="sxs-lookup"><span data-stu-id="8a84a-221">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="8a84a-222">Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-222">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="8a84a-223">Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy użyciu [`@attributes`][3] Razor dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="8a84a-223">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="8a84a-224">Ten scenariusz jest przydatny podczas definiowania składnika, który generuje element znaczników, który obsługuje różne dostosowania.</span><span class="sxs-lookup"><span data-stu-id="8a84a-224">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="8a84a-225">Na przykład może być żmudnym do definiowania atrybutów oddzielnie dla `<input>` , który obsługuje wiele parametrów.</span><span class="sxs-lookup"><span data-stu-id="8a84a-225">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="8a84a-226">W poniższym przykładzie pierwszy `<input>` element ( `id="useIndividualParams"` ) używa pojedynczych parametrów składnika, podczas gdy drugi `<input>` element ( `id="useAttributesDict"` ) używa atrybutu korzystając:</span><span class="sxs-lookup"><span data-stu-id="8a84a-226">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="8a84a-227">Typ parametru musi być zaimplementowany `IEnumerable<KeyValuePair<string, object>>` przy użyciu kluczy ciągu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-227">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="8a84a-228">Korzystanie z programu `IReadOnlyDictionary<string, object>` jest również opcją w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-228">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="8a84a-229">Renderowane `<input>` elementy korzystające z obu metod są identyczne:</span><span class="sxs-lookup"><span data-stu-id="8a84a-229">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="8a84a-230">Aby zaakceptować dowolne atrybuty, zdefiniuj parametr składnika przy użyciu [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atrybutu z <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> właściwością ustawioną na `true` :</span><span class="sxs-lookup"><span data-stu-id="8a84a-230">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="8a84a-231"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Właściwość on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) umożliwia dopasowanie parametru do wszystkich atrybutów, które nie są zgodne z żadnym innym parametrem.</span><span class="sxs-lookup"><span data-stu-id="8a84a-231">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="8a84a-232">Składnik może definiować tylko jeden parametr z <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="8a84a-232">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="8a84a-233">Typ właściwości używany z elementem <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> musi być możliwy do przypisania z `Dictionary<string, object>` klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-233">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="8a84a-234">`IEnumerable<KeyValuePair<string, object>>`lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-234">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="8a84a-235">Pozycja [`@attributes`][3] względem pozycji atrybutów elementu jest ważna.</span><span class="sxs-lookup"><span data-stu-id="8a84a-235">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="8a84a-236">Gdy [`@attributes`][3] są splatted w elemencie, atrybuty są przetwarzane od prawej do lewej (Ostatnia do).</span><span class="sxs-lookup"><span data-stu-id="8a84a-236">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="8a84a-237">Rozważmy następujący przykład składnika, który zużywa `Child` składnik:</span><span class="sxs-lookup"><span data-stu-id="8a84a-237">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="8a84a-238">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-238">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="8a84a-239">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-239">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="8a84a-240">`Child` `extra` Atrybut składnika jest ustawiony z prawej strony [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="8a84a-240">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="8a84a-241">`Parent`Renderowany składnik `<div>` zawiera gdy jest `extra="5"` przenoszona przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od ostatni do pierwszego):</span><span class="sxs-lookup"><span data-stu-id="8a84a-241">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="8a84a-242">W poniższym przykładzie porządek `extra` i [`@attributes`][3] jest odwrócony w `Child` składniku `<div>` :</span><span class="sxs-lookup"><span data-stu-id="8a84a-242">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="8a84a-243">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-243">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="8a84a-244">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a84a-244">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="8a84a-245">Renderowane `<div>` w `Parent` składniku zawiera, `extra="10"` gdy jest przenoszona przez dodatkowy atrybut:</span><span class="sxs-lookup"><span data-stu-id="8a84a-245">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="8a84a-246">Przechwyć odwołania do składników</span><span class="sxs-lookup"><span data-stu-id="8a84a-246">Capture references to components</span></span>

<span data-ttu-id="8a84a-247">Odwołania do składników zapewniają sposób odwoływania się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia, takie jak `Show` lub `Reset` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-247">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="8a84a-248">Aby przechwycić odwołanie do składnika:</span><span class="sxs-lookup"><span data-stu-id="8a84a-248">To capture a component reference:</span></span>

* <span data-ttu-id="8a84a-249">Dodaj [`@ref`][4] atrybut do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-249">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="8a84a-250">Zdefiniuj pole z tym samym typem co składnik podrzędny.</span><span class="sxs-lookup"><span data-stu-id="8a84a-250">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="8a84a-251">Gdy składnik jest renderowany, `loginDialog` pole zostanie wypełnione `MyLoginDialog` wystąpieniem składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-251">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="8a84a-252">Następnie można wywołać metody .NET w wystąpieniu składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-252">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a84a-253">`loginDialog`Zmienna jest wypełniana tylko po wyrenderowaniu składnika, a jego wyjście zawiera `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="8a84a-253">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="8a84a-254">Dopóki składnik nie zostanie renderowany, nie ma niczego do odwołania.</span><span class="sxs-lookup"><span data-stu-id="8a84a-254">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="8a84a-255">Aby manipulować odwołaniami do składników po zakończeniu renderowania składnika, użyj [ `OnAfterRenderAsync` `OnAfterRender` metod lub](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="8a84a-255">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="8a84a-256">Aby użyć zmiennej odniesienia z obsługą zdarzeń, należy użyć wyrażenia lambda lub przypisać delegata obsługi zdarzeń w [ `OnAfterRenderAsync` `OnAfterRender` metodach lub](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="8a84a-256">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="8a84a-257">Daje to pewność, że zmienna odwołania zostanie przypisana przed przypisaniem programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="8a84a-257">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="8a84a-258">Aby odwoływać się do składników w pętli, zobacz [przechwytywanie odwołań do wielu podobnych składników podrzędnych (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="8a84a-258">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="8a84a-259">Podczas przechwytywania odwołań do składników użycie podobnej składni do [przechwytywania odwołań do elementów](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest funkcją międzyoperacyjności języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a84a-259">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="8a84a-260">Odwołania do składników nie są przesyłane do kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a84a-260">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="8a84a-261">Odwołania do składników są używane tylko w kodzie platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="8a84a-261">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="8a84a-262">**Nie** należy używać odwołań do składników do mutacji stanu składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="8a84a-262">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="8a84a-263">Zamiast tego należy używać zwykłych parametrów deklaratywnych do przekazywania danych do składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="8a84a-263">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="8a84a-264">Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie uruchamiają się w prawidłowym czasie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-264">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="8a84a-265">Kontekst synchronizacji</span><span class="sxs-lookup"><span data-stu-id="8a84a-265">Synchronization context</span></span>

Blazor<span data-ttu-id="8a84a-266">używa kontekstu synchronizacji ( <xref:System.Threading.SynchronizationContext> ) w celu wymuszenia pojedynczego wątku logicznego wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8a84a-266"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="8a84a-267">[Metody cyklu życia](xref:blazor/components/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane przez Blazor są wykonywane w kontekście synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-267">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor Server<span data-ttu-id="8a84a-268">kontekst synchronizacji programu próbuje emulować środowisko jednowątkowe, aby dokładnie pasowało do modelu webassembly w przeglądarce, który jest pojedynczym wątkiem.</span><span class="sxs-lookup"><span data-stu-id="8a84a-268">'s synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="8a84a-269">W dowolnym momencie prace są wykonywane na dokładnie jednym wątku, co daje wrażenie pojedynczego wątku logicznego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-269">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="8a84a-270">Nie wykonano jednocześnie dwóch operacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-270">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="8a84a-271">Unikaj wywołań blokowania wątków</span><span class="sxs-lookup"><span data-stu-id="8a84a-271">Avoid thread-blocking calls</span></span>

<span data-ttu-id="8a84a-272">Ogólnie rzecz biorąc nie należy wywoływać następujących metod.</span><span class="sxs-lookup"><span data-stu-id="8a84a-272">Generally, don't call the following methods.</span></span> <span data-ttu-id="8a84a-273">Poniższe metody blokują wątek i w ten sposób blokują pracę aplikacji przed jej <xref:System.Threading.Tasks.Task> ukończeniem:</span><span class="sxs-lookup"><span data-stu-id="8a84a-273">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="8a84a-274">Wywołaj metody składnika zewnętrznie, aby zaktualizować stan</span><span class="sxs-lookup"><span data-stu-id="8a84a-274">Invoke component methods externally to update state</span></span>

<span data-ttu-id="8a84a-275">W przypadku zdarzenia składnika należy zaktualizować na podstawie zdarzenia zewnętrznego, takiego jak czasomierz lub inne powiadomienia, użyj `InvokeAsync` metody, która jest wysyłana do Blazor kontekstu synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-275">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="8a84a-276">Rozważmy na przykład *usługę powiadamiania* , która może powiadomić dowolny składnik nasłuchujący zaktualizowanego stanu:</span><span class="sxs-lookup"><span data-stu-id="8a84a-276">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="8a84a-277">Zarejestruj `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="8a84a-277">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="8a84a-278">W programie Blazor WebAssembly Zarejestruj usługę w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="8a84a-278">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="8a84a-279">W programie Blazor Server Zarejestruj usługę w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a84a-279">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="8a84a-280">`NotifierService`Aby zaktualizować składnik, użyj programu:</span><span class="sxs-lookup"><span data-stu-id="8a84a-280">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="8a84a-281">W poprzednim przykładzie `NotifierService` wywołuje `OnNotify` metodę składnika poza Blazor kontekstem synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-281">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="8a84a-282">`InvokeAsync`służy do przełączania do poprawnego kontekstu i kolejki renderowania.</span><span class="sxs-lookup"><span data-stu-id="8a84a-282">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="8a84a-283">Użyj \@ klawisza, aby kontrolować zachowywanie elementów i składników</span><span class="sxs-lookup"><span data-stu-id="8a84a-283">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="8a84a-284">Gdy renderuje listę elementów lub składników, a następnie zmienia się elementy lub składniki, Blazor algorytm różnicowania musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny być na nich mapowane.</span><span class="sxs-lookup"><span data-stu-id="8a84a-284">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="8a84a-285">Zwykle ten proces jest automatyczny i można go zignorować, ale istnieją przypadki, w których może być konieczne sterowanie procesem.</span><span class="sxs-lookup"><span data-stu-id="8a84a-285">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="8a84a-286">Rozpatrzmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="8a84a-286">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="8a84a-287">Zawartość `People` kolekcji może ulec zmianie z wstawionymi, usuniętymi lub z ponownymi zamówieniami.</span><span class="sxs-lookup"><span data-stu-id="8a84a-287">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="8a84a-288">Gdy składnik jest przerenderowany, `<DetailsEditor>` składnik może ulec zmianie, aby otrzymywać różne `Details` wartości parametrów.</span><span class="sxs-lookup"><span data-stu-id="8a84a-288">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="8a84a-289">Może to spowodować bardziej złożone odwzorowanie niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="8a84a-289">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="8a84a-290">W niektórych przypadkach odzyskanie może prowadzić do zauważalnych różnic w zachowaniu, takich jak brak fokusu elementu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-290">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="8a84a-291">Proces mapowania można kontrolować przy użyciu [`@key`][5] atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="8a84a-291">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="8a84a-292">[`@key`][5]powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza:</span><span class="sxs-lookup"><span data-stu-id="8a84a-292">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="8a84a-293">Po `People` zmianie kolekcji algorytm różnicowy zachowuje skojarzenie między `<DetailsEditor>` wystąpieniami i `person` wystąpieniami:</span><span class="sxs-lookup"><span data-stu-id="8a84a-293">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="8a84a-294">Jeśli element `Person` zostanie usunięty z `People` listy, tylko odpowiednie `<DetailsEditor>` wystąpienie zostanie usunięte z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-294">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="8a84a-295">Inne wystąpienia pozostaną bez zmian.</span><span class="sxs-lookup"><span data-stu-id="8a84a-295">Other instances are left unchanged.</span></span>
* <span data-ttu-id="8a84a-296">Jeśli na `Person` liście zostanie wstawiony w pewnym miejscu, jedno nowe `<DetailsEditor>` wystąpienie zostanie wstawione w odpowiednim położeniu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-296">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="8a84a-297">Inne wystąpienia pozostaną bez zmian.</span><span class="sxs-lookup"><span data-stu-id="8a84a-297">Other instances are left unchanged.</span></span>
* <span data-ttu-id="8a84a-298">`Person`W przypadku ponownego uporządkowania wpisów odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i uporządkowane w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-298">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="8a84a-299">W niektórych scenariuszach użycie programu [`@key`][5] minimalizuje złożoność operacji renderowania i pozwala uniknąć potencjalnych problemów związanych ze stanem częściowych elementów modelu dom, takich jak pozycja fokusu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-299">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a84a-300">Klucze są lokalne dla każdego elementu kontenera lub składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-300">Keys are local to each container element or component.</span></span> <span data-ttu-id="8a84a-301">Klucze nie są porównywane globalnie w całym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-301">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="8a84a-302">Kiedy używać \@ klucza</span><span class="sxs-lookup"><span data-stu-id="8a84a-302">When to use \@key</span></span>

<span data-ttu-id="8a84a-303">Zazwyczaj warto używać [`@key`][5] zawsze, gdy lista jest renderowana (na przykład w bloku [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ), a odpowiednia wartość istnieje do zdefiniowania [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="8a84a-303">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="8a84a-304">Można również użyć, [`@key`][5] Aby uniemożliwić Blazor zachowanie poddrzewa elementu lub składnika po zmianie obiektu:</span><span class="sxs-lookup"><span data-stu-id="8a84a-304">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="8a84a-305">Jeśli `@currentPerson` zmiany, [`@key`][5] dyrektywa Attribute wymusza Blazor odrzucanie całości `<div>` i jego obiektów podrzędnych oraz odbudowa poddrzewa w interfejsie użytkownika o nowych elementach i składnikach.</span><span class="sxs-lookup"><span data-stu-id="8a84a-305">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="8a84a-306">Może to być przydatne, jeśli zachodzi konieczność zagwarantowania, że stan interfejsu użytkownika nie jest zachowywany w przypadku `@currentPerson` zmiany.</span><span class="sxs-lookup"><span data-stu-id="8a84a-306">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="8a84a-307">Kiedy nie używać \@ klucza</span><span class="sxs-lookup"><span data-stu-id="8a84a-307">When not to use \@key</span></span>

<span data-ttu-id="8a84a-308">W przypadku różnicowania w programie występuje koszt wydajności [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="8a84a-308">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="8a84a-309">Koszt wydajności nie jest duży, ale określa tylko, [`@key`][5] czy kontrolowanie reguł utrwalania elementu lub składnika przynosi korzyści dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8a84a-309">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="8a84a-310">Nawet jeśli [`@key`][5] nie jest używany, program Blazor zachowuje elementy podrzędne i wystąpienia składników tak jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="8a84a-310">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="8a84a-311">Jedyną zaletą korzystania z programu [`@key`][5] jest kontrola nad *sposobem* , w jaki wystąpienia modelu są mapowane na zachowane wystąpienia składników, zamiast algorytmu różnicowego, wybierając mapowanie.</span><span class="sxs-lookup"><span data-stu-id="8a84a-311">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="8a84a-312">Wartości, które mają być używane dla \@ klucza</span><span class="sxs-lookup"><span data-stu-id="8a84a-312">What values to use for \@key</span></span>

<span data-ttu-id="8a84a-313">Ogólnie rzecz biorąc, warto podać jeden z następujących rodzajów wartości dla [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="8a84a-313">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="8a84a-314">Wystąpienia obiektów modelu (na przykład `Person` wystąpienie takie jak w poprzednim przykładzie).</span><span class="sxs-lookup"><span data-stu-id="8a84a-314">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="8a84a-315">Zapewnia to zachowywanie na podstawie równości odwołań do obiektów.</span><span class="sxs-lookup"><span data-stu-id="8a84a-315">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="8a84a-316">Unikatowe identyfikatory (na przykład wartości klucza podstawowego typu `int` , `string` lub `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="8a84a-316">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="8a84a-317">Upewnij się, że wartości używane do [`@key`][5] nie kolidują.</span><span class="sxs-lookup"><span data-stu-id="8a84a-317">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="8a84a-318">Jeśli w tym samym elemencie nadrzędnym zostaną wykryte wartości powodujące konflikt, program Blazor zgłosi wyjątek, ponieważ nie może on w sposób jednoznaczny zmapować starych elementów lub składników na nowe elementy lub składniki.</span><span class="sxs-lookup"><span data-stu-id="8a84a-318">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="8a84a-319">Używaj tylko odrębnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-319">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="8a84a-320">Nie twórz składników, które zapisują do własnych właściwości parametrów</span><span class="sxs-lookup"><span data-stu-id="8a84a-320">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="8a84a-321">Parametry są zastępowane w następujących warunkach:</span><span class="sxs-lookup"><span data-stu-id="8a84a-321">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="8a84a-322">Zawartość składnika podrzędnego jest renderowana przy użyciu <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="8a84a-322">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="8a84a-323"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest wywoływana w składniku nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="8a84a-323"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="8a84a-324">Parametry są resetowane, ponieważ składnik nadrzędny jest ponownie renderowany, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana, i nowe wartości parametrów są dostarczane do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-324">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="8a84a-325">Rozważmy następujący `Expander` składnik:</span><span class="sxs-lookup"><span data-stu-id="8a84a-325">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="8a84a-326">Renderuje zawartość podrzędną.</span><span class="sxs-lookup"><span data-stu-id="8a84a-326">Renders child content.</span></span>
* <span data-ttu-id="8a84a-327">Włącza lub wyłącza wyświetlanie zawartości podrzędnej za pomocą parametru składnika.</span><span class="sxs-lookup"><span data-stu-id="8a84a-327">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="8a84a-328">`Expander`Składnik jest dodawany do składnika nadrzędnego, który może wywołać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="8a84a-328">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="8a84a-329">Początkowo `Expander` składniki działają niezależnie, gdy ich `Expanded` właściwości są przełączane.</span><span class="sxs-lookup"><span data-stu-id="8a84a-329">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="8a84a-330">Składniki podrzędne utrzymują swoje Stany zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="8a84a-330">The child components maintain their states as expected.</span></span> <span data-ttu-id="8a84a-331">Gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana w obiekcie nadrzędnym, `Expanded` parametr pierwszego składnika podrzędnego jest resetowany z powrotem do jego wartości początkowej ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="8a84a-331">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="8a84a-332">Wartość drugiego `Expander` składnika `Expanded` nie zostanie zresetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.</span><span class="sxs-lookup"><span data-stu-id="8a84a-332">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="8a84a-333">Aby zachować stan w poprzednim scenariuszu, użyj *pola prywatnego* w `Expander` składniku, aby zachować stan przełączenia.</span><span class="sxs-lookup"><span data-stu-id="8a84a-333">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="8a84a-334">Następujący `Expander` składnik:</span><span class="sxs-lookup"><span data-stu-id="8a84a-334">The following `Expander` component:</span></span>

* <span data-ttu-id="8a84a-335">Akceptuje `Expanded` wartość parametru składnika z elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-335">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="8a84a-336">Przypisuje wartość parametru składnika do *pola prywatnego* ( `expanded` ) w [zdarzeniu OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="8a84a-336">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="8a84a-337">Używa prywatnego pola do utrzymania stanu wewnętrznego przełączania.</span><span class="sxs-lookup"><span data-stu-id="8a84a-337">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a><span data-ttu-id="8a84a-338">Zastosuj atrybut</span><span class="sxs-lookup"><span data-stu-id="8a84a-338">Apply an attribute</span></span>

<span data-ttu-id="8a84a-339">Atrybuty mogą być stosowane do Razor składników z [`@attribute`][7] dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="8a84a-339">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="8a84a-340">Poniższy przykład stosuje [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut do klasy składnika:</span><span class="sxs-lookup"><span data-stu-id="8a84a-340">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="8a84a-341">Warunkowe atrybuty elementu HTML</span><span class="sxs-lookup"><span data-stu-id="8a84a-341">Conditional HTML element attributes</span></span>

<span data-ttu-id="8a84a-342">Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET.</span><span class="sxs-lookup"><span data-stu-id="8a84a-342">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="8a84a-343">Jeśli wartość jest `false` lub `null` , atrybut nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="8a84a-343">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="8a84a-344">Jeśli wartość to `true` , atrybut jest renderowany jako zminimalizowany.</span><span class="sxs-lookup"><span data-stu-id="8a84a-344">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="8a84a-345">W poniższym przykładzie, `IsCompleted` określa, czy `checked` jest renderowany w znacznikach elementu:</span><span class="sxs-lookup"><span data-stu-id="8a84a-345">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="8a84a-346">Jeśli `IsCompleted` jest `true` , pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="8a84a-346">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="8a84a-347">Jeśli `IsCompleted` jest `false` , pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="8a84a-347">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="8a84a-348">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="8a84a-348">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="8a84a-349">Niektóre atrybuty HTML, takie jak [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , nie działają prawidłowo, gdy typem .NET jest `bool` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-349">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="8a84a-350">W takich przypadkach należy użyć `string` typu zamiast `bool` .</span><span class="sxs-lookup"><span data-stu-id="8a84a-350">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="8a84a-351">Nieprzetworzony kod HTML</span><span class="sxs-lookup"><span data-stu-id="8a84a-351">Raw HTML</span></span>

<span data-ttu-id="8a84a-352">Ciągi są zwykle renderowane przy użyciu węzłów tekstowych DOM, co oznacza, że wszystkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst literału.</span><span class="sxs-lookup"><span data-stu-id="8a84a-352">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="8a84a-353">Aby renderować nieprzetworzony kod HTML, zawiń zawartość HTML w `MarkupString` wartości.</span><span class="sxs-lookup"><span data-stu-id="8a84a-353">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="8a84a-354">Wartość jest analizowana jako plik HTML lub SVG i wstawiona do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="8a84a-354">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="8a84a-355">Renderowanie nieprzetworzonego kodu HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy je unikać!</span><span class="sxs-lookup"><span data-stu-id="8a84a-355">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="8a84a-356">W poniższym przykładzie pokazano, jak za pomocą `MarkupString` typu dodać blok statycznej zawartości HTML do renderowanego danych wyjściowych składnika:</span><span class="sxs-lookup"><span data-stu-id="8a84a-356">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Razor<span data-ttu-id="8a84a-357">przystawki</span><span class="sxs-lookup"><span data-stu-id="8a84a-357"> templates</span></span>

<span data-ttu-id="8a84a-358">Fragmenty renderowania można definiować przy użyciu Razor składni szablonu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-358">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="8a84a-359">Szablony są sposobem definiowania fragmentu interfejsu użytkownika i przyjmuje następujący format:</span><span class="sxs-lookup"><span data-stu-id="8a84a-359"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="8a84a-360">Poniższy przykład ilustruje sposób określania <xref:Microsoft.AspNetCore.Components.RenderFragment> i <xref:Microsoft.AspNetCore.Components.RenderFragment%601> wartości oraz renderowania szablonów bezpośrednio w składniku.</span><span class="sxs-lookup"><span data-stu-id="8a84a-360">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="8a84a-361">Fragmenty renderowania mogą być również przekazane jako argumenty do [składników z szablonem](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="8a84a-361">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="8a84a-362">Renderowane dane wyjściowe poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="8a84a-362">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="8a84a-363">Statyczne zasoby</span><span class="sxs-lookup"><span data-stu-id="8a84a-363">Static assets</span></span>

Blazor<span data-ttu-id="8a84a-364">zgodnie z Konwencją ASP.NET Core aplikacje umieszczające statyczne zasoby w [ `web root (wwwroot)` folderze](xref:fundamentals/index#web-root)projektu.</span><span class="sxs-lookup"><span data-stu-id="8a84a-364"> follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="8a84a-365">Użyj ścieżki względnej (), `/` Aby odwołać się do katalogu głównego sieci Web dla statycznego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="8a84a-365">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="8a84a-366">W poniższym przykładzie znajduje się `logo.png` fizycznie w `{PROJECT ROOT}/wwwroot/images` folderze:</span><span class="sxs-lookup"><span data-stu-id="8a84a-366">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="8a84a-367">składniki nie **obsługują** notacji ukośnika odwrotnego ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="8a84a-367"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="8a84a-368">Aby uzyskać informacje na temat ustawiania ścieżki podstawowej aplikacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="8a84a-368">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="8a84a-369">Pomocnicy tagów nie są obsługiwani w składnikach</span><span class="sxs-lookup"><span data-stu-id="8a84a-369">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="8a84a-370">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)nie są obsługiwane w Razor składnikach ( `.razor` pliki).</span><span class="sxs-lookup"><span data-stu-id="8a84a-370">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="8a84a-371">Aby zapewnić funkcję przypominającą pomocnik tagów w programie Blazor , należy utworzyć składnik o tej samej funkcji co pomocnik tagów i użyć składnika zamiast niego.</span><span class="sxs-lookup"><span data-stu-id="8a84a-371">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="8a84a-372">Skalowalne obrazy wektorowe (SVG)</span><span class="sxs-lookup"><span data-stu-id="8a84a-372">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="8a84a-373">Ponieważ Blazor renderuje HTML, obrazy obsługiwane przez przeglądarkę, w tym obrazy Scalable Vector Graphics (SVG) `.svg` , są obsługiwane przez `<img>` tag:</span><span class="sxs-lookup"><span data-stu-id="8a84a-373">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="8a84a-374">Podobnie Obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów ( `.css` ):</span><span class="sxs-lookup"><span data-stu-id="8a84a-374">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="8a84a-375">Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="8a84a-375">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="8a84a-376">Jeśli umieścisz `<svg>` tag bezpośrednio w pliku składnika ( `.razor` ), podstawowe renderowanie obrazu jest obsługiwane, ale wiele scenariuszy zaawansowanych nie jest jeszcze obsługiwanych.</span><span class="sxs-lookup"><span data-stu-id="8a84a-376">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="8a84a-377">Na przykład `<use>` tagi nie są obecnie przestrzegane i [`@bind`][10] nie mogą być używane z niektórymi tagami SVG.</span><span class="sxs-lookup"><span data-stu-id="8a84a-377">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="8a84a-378">Aby uzyskać więcej informacji, zobacz [Obsługa SVG w Blazor (#18271 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="8a84a-378">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a84a-379">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8a84a-379">Additional resources</span></span>

* <span data-ttu-id="8a84a-380"><xref:blazor/security/server/threat-mitigation>: Zawiera wskazówki dotyczące tworzenia Blazor Server aplikacji, które muszą będą konkurować o z wyczerpaniem zasobów.</span><span class="sxs-lookup"><span data-stu-id="8a84a-380"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
