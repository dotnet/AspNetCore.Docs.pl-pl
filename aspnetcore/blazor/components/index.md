---
title: Tworzenie i używanie Razor składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak tworzyć i używać Razor składników, w tym jak powiązać z danymi, obsługiwać zdarzenia i zarządzać cyklem życia składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 7048fe7107fb71d632bf12df9d53e6f168d5a0db
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587466"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="00703-103">Tworzenie i używanie Razor składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00703-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="00703-104">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="00703-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="00703-105">Blazor aplikacje są kompilowane przy użyciu *składników* programu.</span><span class="sxs-lookup"><span data-stu-id="00703-105">Blazor apps are built using *components*.</span></span> <span data-ttu-id="00703-106">Składnik jest niezależnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz.</span><span class="sxs-lookup"><span data-stu-id="00703-106">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="00703-107">Składnik zawiera znaczniki HTML i logikę przetwarzania wymagane do iniekcji danych lub reagowania na zdarzenia interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="00703-107">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="00703-108">Składniki są elastyczne i lekkie.</span><span class="sxs-lookup"><span data-stu-id="00703-108">Components are flexible and lightweight.</span></span> <span data-ttu-id="00703-109">Mogą być zagnieżdżane, ponownie używane i udostępniane między projektami.</span><span class="sxs-lookup"><span data-stu-id="00703-109">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="00703-110">Klasy składników</span><span class="sxs-lookup"><span data-stu-id="00703-110">Component classes</span></span>

<span data-ttu-id="00703-111">Składniki są implementowane w [Razor](xref:mvc/views/razor) plikach składników ( `.razor` ) przy użyciu kombinacji języka C# i znaczników HTML.</span><span class="sxs-lookup"><span data-stu-id="00703-111">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="00703-112">Składnik w programie Blazor jest formalnie nazywany *Razor składnikiem*.</span><span class="sxs-lookup"><span data-stu-id="00703-112">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="00703-113">Razor obowiązuje</span><span class="sxs-lookup"><span data-stu-id="00703-113">Razor syntax</span></span>

<span data-ttu-id="00703-114">Razor składniki w Blazor aplikacjach szeroko wykorzystują Razor składnię.</span><span class="sxs-lookup"><span data-stu-id="00703-114">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="00703-115">Jeśli nie znasz Razor języka znaczników, zalecamy odczytywanie [ Razor odwołań składni dla ASP.NET Core](xref:mvc/views/razor) przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="00703-115">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="00703-116">Podczas uzyskiwania dostępu do zawartości w Razor składni należy zwrócić szczególną uwagę na następujące sekcje:</span><span class="sxs-lookup"><span data-stu-id="00703-116">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="00703-117">[Dyrektywy](xref:mvc/views/razor#directives): `@` — wstępnie ustalone zastrzeżone słowa kluczowe, które zwykle zmieniają sposób, w jaki znaczniki składnika są analizowane lub działają.</span><span class="sxs-lookup"><span data-stu-id="00703-117">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="00703-118">[Atrybuty dyrektywy](xref:mvc/views/razor#directive-attributes): `@` — wstępnie ustalone zastrzeżone słowa kluczowe, które zwykle zmieniają sposób analizowania lub działania elementów składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-118">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="00703-119">Nazwy</span><span class="sxs-lookup"><span data-stu-id="00703-119">Names</span></span>

<span data-ttu-id="00703-120">Nazwa składnika musi rozpoczynać się wielką literą.</span><span class="sxs-lookup"><span data-stu-id="00703-120">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="00703-121">Na przykład `MyCoolComponent.razor` prawidłowe i `myCoolComponent.razor` jest nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="00703-121">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="00703-122">Routing</span><span class="sxs-lookup"><span data-stu-id="00703-122">Routing</span></span>

<span data-ttu-id="00703-123">Routing w programie jest realizowany Blazor przez dostarczenie szablonu trasy do każdego dostępnego składnika w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00703-123">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="00703-124">Gdy Razor plik z [`@page`][9] dyrektywą jest kompilowany, wygenerowana Klasa ma określony <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="00703-124">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="00703-125">W czasie wykonywania router szuka klas składników za pomocą <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> i renderuje, w zależności od tego, który składnik ma szablon trasy zgodny z żądanym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="00703-125">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="00703-126">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="00703-126">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="00703-127">Znaczniki</span><span class="sxs-lookup"><span data-stu-id="00703-127">Markup</span></span>

<span data-ttu-id="00703-128">Interfejs użytkownika dla składnika jest definiowany przy użyciu języka HTML.</span><span class="sxs-lookup"><span data-stu-id="00703-128">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="00703-129">Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie *Razor* .</span><span class="sxs-lookup"><span data-stu-id="00703-129">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="00703-130">Po skompilowaniu aplikacji logika kodu HTML i renderowania języka C# jest konwertowana na klasę składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-130">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="00703-131">Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="00703-131">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="00703-132">Elementy członkowskie klasy składnika są zdefiniowane w [`@code`][1] bloku.</span><span class="sxs-lookup"><span data-stu-id="00703-132">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="00703-133">W [`@code`][1] bloku stan składnika (właściwości, pola) jest określany przy użyciu metod obsługi zdarzeń lub definiowania innej logiki składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-133">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="00703-134">Dozwolony jest więcej niż jeden [`@code`][1] blok.</span><span class="sxs-lookup"><span data-stu-id="00703-134">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="00703-135">Składowe składnika mogą być używane jako część logiki renderowania składnika przy użyciu wyrażeń języka C#, które zaczynają się od `@` .</span><span class="sxs-lookup"><span data-stu-id="00703-135">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="00703-136">Na przykład pole C# jest renderowane przez utworzenie prefiksu `@` do nazwy pola.</span><span class="sxs-lookup"><span data-stu-id="00703-136">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="00703-137">Poniższy przykład szacuje i renderuje:</span><span class="sxs-lookup"><span data-stu-id="00703-137">The following example evaluates and renders:</span></span>

* <span data-ttu-id="00703-138">`headingFontStyle` na wartość właściwości CSS dla elementu `font-style` .</span><span class="sxs-lookup"><span data-stu-id="00703-138">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="00703-139">`headingText` do zawartości `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="00703-139">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="00703-140">Po pierwszym wyrenderowaniu składnika składnik generuje jego drzewo renderowania w odpowiedzi na zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="00703-140">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="00703-141">Blazor Następnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje Document Object Model przeglądarki (DOM).</span><span class="sxs-lookup"><span data-stu-id="00703-141">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="00703-142">Dodatkowe szczegóły znajdują się w temacie <xref:blazor/components/rendering> .</span><span class="sxs-lookup"><span data-stu-id="00703-142">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="00703-143">Składniki to zwykłe klasy języka C# i można je umieścić w dowolnym miejscu w projekcie.</span><span class="sxs-lookup"><span data-stu-id="00703-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="00703-144">Składniki, które generują strony sieci Web, zwykle znajdują się w `Pages` folderze.</span><span class="sxs-lookup"><span data-stu-id="00703-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="00703-145">Składniki niestronicowe są często umieszczane w `Shared` folderze lub do folderu niestandardowego dodanego do projektu.</span><span class="sxs-lookup"><span data-stu-id="00703-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="00703-146">Przestrzenie nazw</span><span class="sxs-lookup"><span data-stu-id="00703-146">Namespaces</span></span>

<span data-ttu-id="00703-147">Zazwyczaj przestrzeń nazw składnika pochodzi od głównej przestrzeni nazw aplikacji i lokalizacji składnika (folderu) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00703-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="00703-148">Jeśli główna przestrzeń nazw aplikacji jest `BlazorSample` i znajduje się `Counter` w `Pages` folderze:</span><span class="sxs-lookup"><span data-stu-id="00703-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="00703-149">`Counter`Przestrzeń nazw składnika to `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="00703-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="00703-150">W pełni kwalifikowana nazwa typu składnika to `BlazorSample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="00703-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="00703-151">W przypadku folderów niestandardowych, które zawierają składniki, Dodaj [`@using`][2] dyrektywę do składnika nadrzędnego lub do `_Imports.razor` pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00703-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="00703-152">W poniższym przykładzie są dostępne składniki w `Components` folderze:</span><span class="sxs-lookup"><span data-stu-id="00703-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="00703-153">Do składników można także odwoływać się za pomocą ich w pełni kwalifikowanych nazw, które nie wymagają [`@using`][2] dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="00703-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="00703-154">Przestrzeń nazw składnika utworzona w programie Razor jest oparta na (w kolejności priorytetu):</span><span class="sxs-lookup"><span data-stu-id="00703-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="00703-155">[`@namespace`][8] oznaczenie w Razor pliku ( `.razor` ) Markup ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="00703-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="00703-156">Projekt znajduje się `RootNamespace` w pliku projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="00703-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="00703-157">Nazwa projektu, pobrana z pliku projektu nazwa pliku ( `.csproj` ) i ścieżka z katalogu głównego projektu do składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="00703-158">Na przykład struktura rozpoznaje `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) do przestrzeni nazw `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="00703-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="00703-159">Składniki przestrzegają reguł powiązań nazw języka C#.</span><span class="sxs-lookup"><span data-stu-id="00703-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="00703-160">W przypadku `Index` składnika w tym przykładzie składniki należące do zakresu są wszystkich składników:</span><span class="sxs-lookup"><span data-stu-id="00703-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="00703-161">W tym samym folderze, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="00703-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="00703-162">Składniki w katalogu głównym projektu, które nie określają jawnie innej przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="00703-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="00703-163">`global::`Kwalifikacja nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="00703-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="00703-164">Importowanie składników przy użyciu instrukcji z aliasami [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (np `@using Foo = Bar` .) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="00703-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="00703-165">Częściowo kwalifikowane nazwy nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="00703-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="00703-166">Na przykład dodawanie `@using BlazorSample` i odwoływanie się do `NavMenu` składnika ( `NavMenu.razor` ) za pomocą `<Shared.NavMenu></Shared.NavMenu>` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="00703-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="00703-167">Obsługa klasy częściowej</span><span class="sxs-lookup"><span data-stu-id="00703-167">Partial class support</span></span>

<span data-ttu-id="00703-168">Razor składniki są generowane jako klasy częściowe.</span><span class="sxs-lookup"><span data-stu-id="00703-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="00703-169">Razor składniki są tworzone przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="00703-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="00703-170">Kod C# jest zdefiniowany w [`@code`][1] bloku z oznaczeniem HTML i Razor kodem w pojedynczym pliku.</span><span class="sxs-lookup"><span data-stu-id="00703-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="00703-171">Blazor Szablony definiują ich Razor składniki przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="00703-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="00703-172">Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako Klasa częściowa.</span><span class="sxs-lookup"><span data-stu-id="00703-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="00703-173">Poniższy przykład pokazuje `Counter` składnik domyślny z [`@code`][1] blokiem w aplikacji wygenerowanej na podstawie Blazor szablonu.</span><span class="sxs-lookup"><span data-stu-id="00703-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="00703-174">Znaczniki HTML, Razor kod i kod C# są w tym samym pliku:</span><span class="sxs-lookup"><span data-stu-id="00703-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="00703-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="00703-176">`Counter`Składnik można również utworzyć przy użyciu pliku związanego z kodem z klasą częściową:</span><span class="sxs-lookup"><span data-stu-id="00703-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="00703-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="00703-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="00703-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
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

<span data-ttu-id="00703-179">W razie potrzeby dodaj wszystkie wymagane przestrzenie nazw do pliku klasy częściowej.</span><span class="sxs-lookup"><span data-stu-id="00703-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="00703-180">Typowe przestrzenie nazw używane przez Razor składniki obejmują:</span><span class="sxs-lookup"><span data-stu-id="00703-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="00703-181">[`@using`][2] dyrektywy w `_Imports.razor` pliku są stosowane tylko do Razor plików ( `.razor` ), nie plików C# ( `.cs` ).</span><span class="sxs-lookup"><span data-stu-id="00703-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="00703-182">Określ klasę bazową</span><span class="sxs-lookup"><span data-stu-id="00703-182">Specify a base class</span></span>

<span data-ttu-id="00703-183">[`@inherits`][6]Dyrektywa może służyć do określania klasy bazowej dla składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="00703-184">Poniższy przykład pokazuje, jak składnik może dziedziczyć klasę bazową, `BlazorRocksBase` , aby zapewnić właściwości i metody składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="00703-185">Klasa bazowa powinna pochodzić od <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="00703-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="00703-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="00703-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="00703-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="00703-188">Używanie składników</span><span class="sxs-lookup"><span data-stu-id="00703-188">Use components</span></span>

<span data-ttu-id="00703-189">Składniki mogą zawierać inne składniki, deklarując je za pomocą składni elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="00703-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="00703-190">Znaczniki użycia składnika wyglądają jak tag HTML, gdzie nazwa znacznika jest typem składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="00703-191">Następujące znaczniki w programie `Pages/Index.razor` renderuje `HeadingComponent` wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="00703-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="00703-192">`Shared/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-192">`Shared/HeadingComponent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-index/HeadingComponent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-index/HeadingComponent.razor)]

::: moniker-end

<span data-ttu-id="00703-193">Jeśli składnik zawiera element HTML z wielką literą, która nie jest zgodna z nazwą składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę.</span><span class="sxs-lookup"><span data-stu-id="00703-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="00703-194">Dodanie [`@using`][2] dyrektywy dla przestrzeni nazw składnika sprawia, że składnik jest dostępny, co rozwiązuje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="00703-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="00703-195">Parametry</span><span class="sxs-lookup"><span data-stu-id="00703-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="00703-196">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="00703-196">Route parameters</span></span>

<span data-ttu-id="00703-197">Składniki mogą odbierać parametry trasy z szablonu trasy dostarczonego w [`@page`][9] dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="00703-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="00703-198">Router używa parametrów trasy, aby wypełnić odpowiednie parametry składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="00703-199">Parametry opcjonalne są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="00703-199">Optional parameters are supported.</span></span> <span data-ttu-id="00703-200">W poniższym przykładzie `text` opcjonalny parametr przypisuje wartość segmentu trasy do `Text` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="00703-201">Jeśli segment nie istnieje, wartość `Text` jest ustawiana na `fantastic` .</span><span class="sxs-lookup"><span data-stu-id="00703-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="00703-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-index/RouteParameter.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="00703-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-index/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="00703-204">Parametry opcjonalne nie są obsługiwane, więc dwie [`@page`][9] dyrektywy są stosowane w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="00703-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="00703-205">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="00703-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="00703-206">Druga [`@page`][9] dyrektywa odbiera `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="00703-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="00703-207">Aby uzyskać informacje dotyczące przechwytywania wszystkich parametrów trasy ( `{*pageRoute}` ), które przechwytują ścieżki między wieloma granicami folderów, zobacz <xref:blazor/fundamentals/routing#catch-all-route-parameters> .</span><span class="sxs-lookup"><span data-stu-id="00703-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="00703-208">Parametry składnika</span><span class="sxs-lookup"><span data-stu-id="00703-208">Component parameters</span></span>

<span data-ttu-id="00703-209">Składniki mogą zawierać *Parametry składnika*, które są zdefiniowane przy użyciu publicznej prostej lub złożonej właściwości w klasie składnika z [ `[Parameter]` atrybutem](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="00703-209">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="00703-210">Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.</span><span class="sxs-lookup"><span data-stu-id="00703-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="00703-211">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-211">`Shared/ChildComponent.razor`:</span></span>

```razor
<div class="panel panel-default">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>

    <button class="btn btn-primary" @onclick="OnClickCallback">
        Trigger a Parent component method
    </button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
}
```

<span data-ttu-id="00703-212">Do parametrów składnika można przypisać wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="00703-212">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="00703-213">W poniższym przykładzie z przykładowej aplikacji `ParentComponent` ustawia wartość `Title` właściwości `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="00703-213">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="00703-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-214">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>
```

<span data-ttu-id="00703-215">Przypisz pola, właściwości i metody w języku C# do parametrów składnika jako wartości atrybutów HTML przy użyciu [ Razor zastrzeżonego `@` symbolu](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="00703-215">Assign C# fields, properties, and methods to component parameters as HTML attribute values using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="00703-216">Aby przypisać pole składnika nadrzędnego, właściwość lub metodę do parametru składnika podrzędnego, prefiks pola, właściwości lub nazwy metody z `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="00703-216">To assign a parent component's field, property, or method to a child component's parameter, prefix the field, property, or method name with the `@` symbol.</span></span> <span data-ttu-id="00703-217">Aby przypisać wynik [niejawnego wyrażenia języka C#](xref:mvc/views/razor#implicit-razor-expressions) do parametru, poprzedź wyrażenie niejawne `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="00703-217">To assign the result of an [implicit C# expression](xref:mvc/views/razor#implicit-razor-expressions) to a parameter, prefix the implicit expression with an `@` symbol.</span></span>

  <span data-ttu-id="00703-218">Poniższy składnik nadrzędny wyświetla cztery wystąpienia poprzedniego `ChildComponent` składnika i ustawia ich `Title` wartości parametrów na:</span><span class="sxs-lookup"><span data-stu-id="00703-218">The following parent component displays four instances of the preceding `ChildComponent` component and sets their `Title` parameter values to:</span></span>

  * <span data-ttu-id="00703-219">Wartość `title` pola.</span><span class="sxs-lookup"><span data-stu-id="00703-219">The value of the `title` field.</span></span>
  * <span data-ttu-id="00703-220">Wynik `GetTitle` metody języka C#.</span><span class="sxs-lookup"><span data-stu-id="00703-220">The result of the `GetTitle` C# method.</span></span>
  * <span data-ttu-id="00703-221">Bieżąca data lokalna w formacie długim <xref:System.DateTime.ToLongDateString%2A> .</span><span class="sxs-lookup"><span data-stu-id="00703-221">The current local date in long format with <xref:System.DateTime.ToLongDateString%2A>.</span></span>
  * <span data-ttu-id="00703-222">`person` `Name` Właściwość obiektu.</span><span class="sxs-lookup"><span data-stu-id="00703-222">The `person` object's `Name` property.</span></span>

  <span data-ttu-id="00703-223">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-223">`Pages/ParentComponent.razor`:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from field.
  </ChildComponent>
  
  <ChildComponent Title="@GetTitle()">
      Title from method.
  </ChildComponent>
  
  <ChildComponent Title="@DateTime.Now.ToLongDateString()">
      Title from implicit Razor expression.
  </ChildComponent>
  
  <ChildComponent Title="@person.Name">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title = "Panel Title from Parent";
      private Person person = new Person();
      
      private string GetTitle()
      {
          return "Panel Title from Parent";
      }
      
      private class Person
      {
          public string Name { get; set; } = "Dr. Who";
      }
  }
  ```
  
  <span data-ttu-id="00703-224">W przeciwieństwie do Razor stron ( `.cshtml` ), Blazor nie można wykonać asynchronicznej pracy w Razor wyrażeniu podczas renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-224">Unlike in Razor pages (`.cshtml`), Blazor can't perform asynchronous work in a Razor expression while rendering a component.</span></span> <span data-ttu-id="00703-225">Jest to spowodowane tym, że Blazor jest przeznaczony do renderowania interakcyjnych interfejsów użytkownika.</span><span class="sxs-lookup"><span data-stu-id="00703-225">This is because Blazor is designed for rendering interactive UIs.</span></span> <span data-ttu-id="00703-226">W interaktywnym interfejsie użytkownika ekran musi zawsze wyświetlać coś, dlatego nie ma sensu blokowania przepływu renderowania.</span><span class="sxs-lookup"><span data-stu-id="00703-226">In an interactive UI, the screen must always display something, so it doesn't make sense to block the rendering flow.</span></span> <span data-ttu-id="00703-227">Zamiast tego zadania asynchroniczne są wykonywane w ramach jednego z [asynchronicznych zdarzeń cyklu życia](xref:blazor/components/lifecycle).</span><span class="sxs-lookup"><span data-stu-id="00703-227">Instead, asynchronous work is performed during one of the [asynchronous lifecycle events](xref:blazor/components/lifecycle).</span></span> <span data-ttu-id="00703-228">Po każdym asynchronicznym zdarzeniu cyklu życia składnik może być ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="00703-228">After each asynchronous lifecycle event, the component may render again.</span></span> <span data-ttu-id="00703-229">Następująca Razor składnia **nie** jest obsługiwana:</span><span class="sxs-lookup"><span data-stu-id="00703-229">The following Razor syntax is **not** supported:</span></span>
  
  ```razor
  <ChildComponent Title="@await ...">
      ...
  </ChildComponent>
  ```
  
  <span data-ttu-id="00703-230">Kod w powyższym przykładzie generuje *błąd kompilatora* , jeśli aplikacja została skompilowana:</span><span class="sxs-lookup"><span data-stu-id="00703-230">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="00703-231">Operatora "await" można używać tylko w metodzie asynchronicznej.</span><span class="sxs-lookup"><span data-stu-id="00703-231">The 'await' operator can only be used within an async method.</span></span> <span data-ttu-id="00703-232">Rozważ oznaczenie tej metody za pomocą modyfikatora "Async" i zmianę zwracanego typu na "Task".</span><span class="sxs-lookup"><span data-stu-id="00703-232">Consider marking this method with the 'async' modifier and changing its return type to 'Task'.</span></span>

  <span data-ttu-id="00703-233">Aby uzyskać wartość `Title` parametru w powyższym przykładzie asynchronicznie, składnik może użyć [ `OnInitializedAsync` zdarzenia cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods), jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="00703-233">To obtain a value for the `Title` parameter in the preceding example asychronously, the component can use the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods), as the following example demonstrates:</span></span>
  
  ```razor
  <ChildComponent Title="@title">
      Title from implicit Razor expression.
  </ChildComponent>
  
  @code {
      private string title;
      
      protected override async Task OnInitializedAsync()
      {
          title = await ...;
      }
  }
  ```
  
* <span data-ttu-id="00703-234">Aby przypisać wynik [jawnego wyrażenia języka C#](xref:mvc/views/razor#explicit-razor-expressions) w składniku nadrzędnym do parametru składnika podrzędnego, umieść wyrażenie w nawiasach z `@` prefiksem symbolu.</span><span class="sxs-lookup"><span data-stu-id="00703-234">To assign the result of an [explicit C# expression](xref:mvc/views/razor#explicit-razor-expressions) in the parent component to a child component's parameter, surround the expression in parentheses with an `@` symbol prefix.</span></span>

  <span data-ttu-id="00703-235">Poniższy składnik podrzędny ma <xref:System.DateTime> parametr składnika `ShowItemsSinceDate` .</span><span class="sxs-lookup"><span data-stu-id="00703-235">The following child component has a <xref:System.DateTime> component parameter, `ShowItemsSinceDate`.</span></span>
  
  <span data-ttu-id="00703-236">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-236">`Shared/ChildComponent.razor`:</span></span>
  
  ```razor
  <div class="panel panel-default">
      <div class="panel-heading">Explicit DateTime Expression Example</div>
      <div class="panel-body">
          <p>@ChildContent</p>
          <p>One week ago date: @ShowItemsSinceDate</p>
      </div>
  </div>

  @code {
      [Parameter]
      public DateTime ShowItemsSinceDate { get; set; }

      [Parameter]
      public RenderFragment ChildContent { get; set; }
  }
  ```
  
  <span data-ttu-id="00703-237">Poniższy składnik nadrzędny oblicza datę z jawnym wyrażeniem języka C#, które jest tydzień w przeszłości do przypisania do parametru elementu podrzędnego `ShowItemsSinceDate` .</span><span class="sxs-lookup"><span data-stu-id="00703-237">The following parent component calculates a date with an explicit C# expression that's one week in the past for assignment to the child's `ShowItemsSinceDate` parameter.</span></span>
  
  <span data-ttu-id="00703-238">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-238">`Pages/ParentComponent.razor`:</span></span>

  ```razor
  <ChildComponent ShowItemsSinceDate="@(DateTime.Now - TimeSpan.FromDays(7))">
      Title from explicit Razor expression.
  </ChildComponent>
  ```

  <span data-ttu-id="00703-239">Użycie wyrażenia Explicit do łączenia tekstu z wynikiem wyrażenia w celu przypisania do parametru **nie** jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="00703-239">Use of an explicit expression to concatenate text with an expression result for assignment to a parameter is **not** supported.</span></span> <span data-ttu-id="00703-240">Poniższy przykład dąży do łączenia tekstu "SKU-" z numerem zapasowym produktu ( `SKU` Właściwość "jednostka składowania") udostępnianą przez obiekt składnika nadrzędnego `product` .</span><span class="sxs-lookup"><span data-stu-id="00703-240">The following example seeks to concatenate the text "SKU-" with a product stock number (`SKU` property, "Stock Keeping Unit") provided by a parent component's `product` object.</span></span> <span data-ttu-id="00703-241">Chociaż ta składnia jest obsługiwana na Razor stronie ( `.cshtml` ), nie jest ona prawidłowa do przypisania do parametru elementu podrzędnego `Title` .</span><span class="sxs-lookup"><span data-stu-id="00703-241">Although this syntax is supported in a Razor page (`.cshtml`), it isn't valid for assignment to the child's `Title` parameter.</span></span>
  
  ```razor
  <ChildComponent Title="SKU-@(product.SKU)">
      Title from composed Razor expression. This doesn't compile.
  </ChildComponent>
  ```
  
  <span data-ttu-id="00703-242">Kod w powyższym przykładzie generuje *błąd kompilatora* , jeśli aplikacja została skompilowana:</span><span class="sxs-lookup"><span data-stu-id="00703-242">The code in the preceding example generates a *compiler error* if the app is built:</span></span>
  
  > <span data-ttu-id="00703-243">Atrybuty składnika nie obsługują złożonej zawartości (mieszanego języka C# i znaczników).</span><span class="sxs-lookup"><span data-stu-id="00703-243">Component attributes do not support complex content (mixed C# and markup).</span></span>
  
  <span data-ttu-id="00703-244">Aby obsługiwać przypisanie wartości złożonej, użyj metody, pola lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="00703-244">To support the assignment of a composed value, use a method, field, or property.</span></span> <span data-ttu-id="00703-245">W poniższym przykładzie jest wykonywana concatination "SKU-" oraz numeru giełdowego produktu w metodzie C# `GetTitle` :</span><span class="sxs-lookup"><span data-stu-id="00703-245">The following example performs the concatination of "SKU-" and a product's stock number in the C# method `GetTitle`:</span></span>
  
  ```razor
  <ChildComponent Title="@GetTitle()">
      Composed title from method.
  </ChildComponent>
  
  @code {
      private Product product = new Product();

      private string GetTitle() => $"SKU-{product.SKU}";
      
      private class Product
      {
          public string SKU { get; set; } = "12345";
      }
  }
  ```
  
<span data-ttu-id="00703-246">Aby uzyskać więcej informacji, zobacz [ Razor Informacje o składni dla ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="00703-246">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="00703-247">Nie należy tworzyć składników, które zapisują do własnych *parametrów składników*, zamiast tego należy użyć pola private.</span><span class="sxs-lookup"><span data-stu-id="00703-247">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="00703-248">Aby uzyskać więcej informacji, zobacz sekcję [zastępowanie parametrów](#overwritten-parameters) .</span><span class="sxs-lookup"><span data-stu-id="00703-248">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

#### <a name="component-parameters-should-be-auto-properties"></a><span data-ttu-id="00703-249">Parametry składnika powinny być właściwościami autowypełnianymi</span><span class="sxs-lookup"><span data-stu-id="00703-249">Component parameters should be auto-properties</span></span>

<span data-ttu-id="00703-250">Parametry składnika powinny być zadeklarowane jako *Właściwości autoproperties*, co oznacza, że nie powinny zawierać logiki niestandardowej w swoich metodach pobierających lub setter.</span><span class="sxs-lookup"><span data-stu-id="00703-250">Component parameters should be declared as *auto-properties*, meaning that they shouldn't contain custom logic in their getters or setters.</span></span> <span data-ttu-id="00703-251">Na przykład następująca `StartData` Właściwość jest właściwością autoproperty:</span><span class="sxs-lookup"><span data-stu-id="00703-251">For example, the following `StartData` property is an auto-property:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

<span data-ttu-id="00703-252">Nie umieszczaj logiki niestandardowej w `get` `set` metodzie lub, ponieważ parametry składnika są przeznaczone wyłącznie do użycia jako kanał składnika nadrzędnego do przepływu informacji do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-252">Don't place custom logic in the `get` or `set` accessor because component parameters are purely intended for use as a channel for a parent component to flow information to a child component.</span></span> <span data-ttu-id="00703-253">Jeśli Metoda ustawiająca właściwości składnika podrzędnego zawiera logikę powodującą odtwarzanie składnika nadrzędnego, nieskończona pętla renderowania.</span><span class="sxs-lookup"><span data-stu-id="00703-253">If a setter of a child component property contains logic that causes rerendering of the parent component, an infinite rendering loop results.</span></span>

<span data-ttu-id="00703-254">Jeśli konieczne jest przekształcenie odebranej wartości parametru:</span><span class="sxs-lookup"><span data-stu-id="00703-254">If you need to transform a received parameter value:</span></span>

* <span data-ttu-id="00703-255">Pozostaw Właściwość parametru jako czystą Właściwość autoproperty, aby reprezentować dostarczone dane pierwotne.</span><span class="sxs-lookup"><span data-stu-id="00703-255">Leave the parameter property as a pure auto-property to represent the supplied raw data.</span></span>
* <span data-ttu-id="00703-256">Utwórz inną właściwość lub metodę, która dostarcza przekształcone dane na podstawie właściwości parametru.</span><span class="sxs-lookup"><span data-stu-id="00703-256">Create some other property or method that supplies the transformed data based on the parameter property.</span></span>

<span data-ttu-id="00703-257">Można przesłonić, `OnParametersSetAsync` Jeśli chcesz przekształcić otrzymany parametr za każdym razem, gdy zostaną odebrane nowe dane.</span><span class="sxs-lookup"><span data-stu-id="00703-257">You can override `OnParametersSetAsync` if you want to transform a received parameter each time new data is received.</span></span>

## <a name="child-content"></a><span data-ttu-id="00703-258">Zawartość podrzędna</span><span class="sxs-lookup"><span data-stu-id="00703-258">Child content</span></span>

<span data-ttu-id="00703-259">Składniki mogą ustawiać zawartość innego składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-259">Components can set the content of another component.</span></span> <span data-ttu-id="00703-260">Składnik Assigner zawiera zawartość między tagami, które określają składnik do odbioru.</span><span class="sxs-lookup"><span data-stu-id="00703-260">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="00703-261">W poniższym przykładzie `ChildComponent` ma `ChildContent` Właściwość, która reprezentuje element <xref:Microsoft.AspNetCore.Components.RenderFragment> , który reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="00703-261">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="00703-262">Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana.</span><span class="sxs-lookup"><span data-stu-id="00703-262">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="00703-263">Wartość `ChildContent` jest odbierana ze składnika nadrzędnego i renderowany w panelu uruchamiania `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="00703-263">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="00703-264">`Shared/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-264">`Shared/ChildComponent.razor`:</span></span>

```razor
<div class="panel panel-default">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>

    <button class="btn btn-primary" @onclick="OnClickCallback">
        Trigger a Parent component method
    </button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public EventCallback<MouseEventArgs> OnClickCallback { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="00703-265">Właściwość otrzymująca <xref:Microsoft.AspNetCore.Components.RenderFragment> zawartość musi być nazywana `ChildContent` Konwencją.</span><span class="sxs-lookup"><span data-stu-id="00703-265">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="00703-266">`ParentComponent`W przykładowej aplikacji można dostarczyć zawartość do renderowania `ChildComponent` przez umieszczenie zawartości wewnątrz `<ChildComponent>` tagów.</span><span class="sxs-lookup"><span data-stu-id="00703-266">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="00703-267">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-267">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>
```

<span data-ttu-id="00703-268">Ze względu na sposób, w jaki Blazor renderuje zawartość podrzędną, składniki renderowania wewnątrz `for` pętli wymagają zmiennej lokalnego indeksu, jeśli zmienna pętli zwiększania jest używana w zawartości składnika podrzędnego:</span><span class="sxs-lookup"><span data-stu-id="00703-268">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="00703-269">Alternatywnie możesz użyć `foreach` pętli z <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="00703-269">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="00703-270">Aby uzyskać informacje na temat sposobu <xref:Microsoft.AspNetCore.Components.RenderFragment> użycia jako szablonu dla Razor interfejsu użytkownika składnika, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="00703-270">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="00703-271">Korzystając atrybutów i dowolne parametry</span><span class="sxs-lookup"><span data-stu-id="00703-271">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="00703-272">Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-272">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="00703-273">Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy użyciu [`@attributes`][3] Razor dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="00703-273">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="00703-274">Ten scenariusz jest przydatny podczas definiowania składnika, który generuje element znaczników, który obsługuje różne dostosowania.</span><span class="sxs-lookup"><span data-stu-id="00703-274">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="00703-275">Na przykład może być żmudnym do definiowania atrybutów oddzielnie dla `<input>` , który obsługuje wiele parametrów.</span><span class="sxs-lookup"><span data-stu-id="00703-275">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="00703-276">W poniższym przykładzie pierwszy `<input>` element ( `id="useIndividualParams"` ) używa pojedynczych parametrów składnika, podczas gdy drugi `<input>` element ( `id="useAttributesDict"` ) używa atrybutu korzystając:</span><span class="sxs-lookup"><span data-stu-id="00703-276">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

<span data-ttu-id="00703-277">Typ parametru musi być zaimplementowany `IEnumerable<KeyValuePair<string, object>>` lub `IReadOnlyDictionary<string, object>` z kluczami ciągu.</span><span class="sxs-lookup"><span data-stu-id="00703-277">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="00703-278">Renderowane `<input>` elementy korzystające z obu metod są identyczne:</span><span class="sxs-lookup"><span data-stu-id="00703-278">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="00703-279">Aby zaakceptować dowolne atrybuty, zdefiniuj parametr składnika przy użyciu [ `[Parameter]` atrybutu](xref:Microsoft.AspNetCore.Components.ParameterAttribute) z <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> właściwością ustawioną na `true` :</span><span class="sxs-lookup"><span data-stu-id="00703-279">To accept arbitrary attributes, define a component parameter using the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="00703-280"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Właściwość on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) umożliwia dopasowanie parametru do wszystkich atrybutów, które nie są zgodne z żadnym innym parametrem.</span><span class="sxs-lookup"><span data-stu-id="00703-280">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="00703-281">Składnik może definiować tylko jeden parametr z <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="00703-281">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="00703-282">Typ właściwości używany z elementem <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> musi być możliwy do przypisania z `Dictionary<string, object>` klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="00703-282">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="00703-283">`IEnumerable<KeyValuePair<string, object>>` lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="00703-283">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="00703-284">Pozycja [`@attributes`][3] względem pozycji atrybutów elementu jest ważna.</span><span class="sxs-lookup"><span data-stu-id="00703-284">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="00703-285">Gdy [`@attributes`][3] są splatted w elemencie, atrybuty są przetwarzane od prawej do lewej (Ostatnia do).</span><span class="sxs-lookup"><span data-stu-id="00703-285">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="00703-286">Rozważmy następujący przykład składnika, który zużywa `Child` składnik:</span><span class="sxs-lookup"><span data-stu-id="00703-286">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="00703-287">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-287">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="00703-288">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-288">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="00703-289">`Child` `extra` Atrybut składnika jest ustawiony z prawej strony [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="00703-289">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="00703-290">`Parent`Renderowany składnik `<div>` zawiera gdy jest `extra="5"` przenoszona przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od ostatni do pierwszego):</span><span class="sxs-lookup"><span data-stu-id="00703-290">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="00703-291">W poniższym przykładzie porządek `extra` i [`@attributes`][3] jest odwrócony w `Child` składniku `<div>` :</span><span class="sxs-lookup"><span data-stu-id="00703-291">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="00703-292">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-292">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="00703-293">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="00703-293">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="00703-294">Renderowane `<div>` w `Parent` składniku zawiera, `extra="10"` gdy jest przenoszona przez dodatkowy atrybut:</span><span class="sxs-lookup"><span data-stu-id="00703-294">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="00703-295">Przechwyć odwołania do składników</span><span class="sxs-lookup"><span data-stu-id="00703-295">Capture references to components</span></span>

<span data-ttu-id="00703-296">Odwołania do składników zapewniają sposób odwoływania się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia, takie jak `Show` lub `Reset` .</span><span class="sxs-lookup"><span data-stu-id="00703-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="00703-297">Aby przechwycić odwołanie do składnika:</span><span class="sxs-lookup"><span data-stu-id="00703-297">To capture a component reference:</span></span>

* <span data-ttu-id="00703-298">Dodaj [`@ref`][4] atrybut do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-298">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="00703-299">Zdefiniuj pole z tym samym typem co składnik podrzędny.</span><span class="sxs-lookup"><span data-stu-id="00703-299">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="00703-300">Gdy składnik jest renderowany, `loginDialog` pole zostanie wypełnione `CustomLoginDialog` wystąpieniem składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-300">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="00703-301">Następnie można wywołać metody .NET w wystąpieniu składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="00703-302">`loginDialog`Zmienna jest wypełniana tylko po wyrenderowaniu składnika, a jego wyjście zawiera `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="00703-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="00703-303">Dopóki składnik nie zostanie renderowany, nie ma niczego do odwołania.</span><span class="sxs-lookup"><span data-stu-id="00703-303">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="00703-304">Aby manipulować odwołaniami do składników po zakończeniu renderowania składnika, użyj [ `OnAfterRenderAsync` `OnAfterRender` metod lub](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="00703-304">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="00703-305">Aby użyć zmiennej odniesienia z obsługą zdarzeń, należy użyć wyrażenia lambda lub przypisać delegata obsługi zdarzeń w [ `OnAfterRenderAsync` `OnAfterRender` metodach lub](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="00703-305">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="00703-306">Daje to pewność, że zmienna odwołania zostanie przypisana przed przypisaniem programu obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="00703-306">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="00703-307">Aby odwoływać się do składników w pętli, zobacz [przechwytywanie odwołań do wielu podobnych składników podrzędnych (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="00703-307">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="00703-308">Podczas przechwytywania odwołań do składników użycie podobnej składni do [przechwytywania odwołań do elementów](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest funkcją międzyoperacyjności języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="00703-308">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="00703-309">Odwołania do składników nie są przesyłane do kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="00703-309">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="00703-310">Odwołania do składników są używane tylko w kodzie platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="00703-310">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="00703-311">**Nie** należy używać odwołań do składników do mutacji stanu składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="00703-311">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="00703-312">Zamiast tego należy używać zwykłych parametrów deklaratywnych do przekazywania danych do składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="00703-312">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="00703-313">Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie uruchamiają się w prawidłowym czasie.</span><span class="sxs-lookup"><span data-stu-id="00703-313">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="00703-314">Kontekst synchronizacji</span><span class="sxs-lookup"><span data-stu-id="00703-314">Synchronization context</span></span>

<span data-ttu-id="00703-315">Blazor używa kontekstu synchronizacji ( <xref:System.Threading.SynchronizationContext> ) w celu wymuszenia pojedynczego wątku logicznego wykonywania.</span><span class="sxs-lookup"><span data-stu-id="00703-315">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="00703-316">[Metody cyklu życia](xref:blazor/components/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane przez Blazor są wykonywane w kontekście synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="00703-316">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="00703-317">Blazor Serverkontekst synchronizacji programu próbuje emulować środowisko jednowątkowe, aby dokładnie pasowało do modelu webassembly w przeglądarce, który jest pojedynczym wątkiem.</span><span class="sxs-lookup"><span data-stu-id="00703-317">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="00703-318">W dowolnym momencie prace są wykonywane na dokładnie jednym wątku, co daje wrażenie pojedynczego wątku logicznego.</span><span class="sxs-lookup"><span data-stu-id="00703-318">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="00703-319">Nie wykonano jednocześnie dwóch operacji.</span><span class="sxs-lookup"><span data-stu-id="00703-319">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="00703-320">Unikaj wywołań blokowania wątków</span><span class="sxs-lookup"><span data-stu-id="00703-320">Avoid thread-blocking calls</span></span>

<span data-ttu-id="00703-321">Ogólnie rzecz biorąc nie należy wywoływać następujących metod.</span><span class="sxs-lookup"><span data-stu-id="00703-321">Generally, don't call the following methods.</span></span> <span data-ttu-id="00703-322">Poniższe metody blokują wątek i w ten sposób blokują pracę aplikacji przed jej <xref:System.Threading.Tasks.Task> ukończeniem:</span><span class="sxs-lookup"><span data-stu-id="00703-322">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="00703-323">Wywołaj metody składnika zewnętrznie, aby zaktualizować stan</span><span class="sxs-lookup"><span data-stu-id="00703-323">Invoke component methods externally to update state</span></span>

<span data-ttu-id="00703-324">W przypadku zdarzenia składnika należy zaktualizować na podstawie zdarzenia zewnętrznego, takiego jak czasomierz lub inne powiadomienia, użyj `InvokeAsync` metody, która jest wysyłana do Blazor kontekstu synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="00703-324">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="00703-325">Rozważmy na przykład *usługę powiadamiania* , która może powiadomić dowolny składnik nasłuchujący zaktualizowanego stanu:</span><span class="sxs-lookup"><span data-stu-id="00703-325">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="00703-326">Zarejestruj `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="00703-326">Register the `NotifierService`:</span></span>

* <span data-ttu-id="00703-327">W programie Blazor WebAssembly Zarejestruj usługę jako pojedynczą w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="00703-327">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="00703-328">W programie Blazor Server Zarejestruj usługę jako objętą zakresem `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="00703-328">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="00703-329">`NotifierService`Aby zaktualizować składnik, użyj programu:</span><span class="sxs-lookup"><span data-stu-id="00703-329">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="00703-330">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="00703-330">In the preceding example:</span></span>

* <span data-ttu-id="00703-331">`NotifierService` wywołuje `OnNotify` metodę składnika poza Blazor kontekstem synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="00703-331">`NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="00703-332">`InvokeAsync` służy do przełączania do poprawnego kontekstu i kolejki renderowania.</span><span class="sxs-lookup"><span data-stu-id="00703-332">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="00703-333">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="00703-333">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="00703-334">Składnik implementuje <xref:System.IDisposable> i `OnNotify` delegat nie jest subskrybowany w `Dispose` metodzie, która jest wywoływana przez platformę, gdy składnik zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="00703-334">The component implements <xref:System.IDisposable>, and the `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="00703-335">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="00703-335">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="00703-336">Użyj \@ klawisza, aby kontrolować zachowywanie elementów i składników</span><span class="sxs-lookup"><span data-stu-id="00703-336">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="00703-337">Gdy renderuje listę elementów lub składników, a następnie zmienia się elementy lub składniki, Blazor algorytm różnicowania musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny być na nich mapowane.</span><span class="sxs-lookup"><span data-stu-id="00703-337">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="00703-338">Zwykle ten proces jest automatyczny i można go zignorować, ale istnieją przypadki, w których może być konieczne sterowanie procesem.</span><span class="sxs-lookup"><span data-stu-id="00703-338">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="00703-339">Rozpatrzmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="00703-339">Consider the following example:</span></span>

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

<span data-ttu-id="00703-340">Zawartość `People` kolekcji może ulec zmianie z wstawionymi, usuniętymi lub z ponownymi zamówieniami.</span><span class="sxs-lookup"><span data-stu-id="00703-340">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="00703-341">Gdy składnik jest przerenderowany, `<DetailsEditor>` składnik może ulec zmianie, aby otrzymywać różne `Details` wartości parametrów.</span><span class="sxs-lookup"><span data-stu-id="00703-341">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="00703-342">Może to spowodować bardziej złożone odwzorowanie niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="00703-342">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="00703-343">W niektórych przypadkach odzyskanie może prowadzić do zauważalnych różnic w zachowaniu, takich jak brak fokusu elementu.</span><span class="sxs-lookup"><span data-stu-id="00703-343">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="00703-344">Proces mapowania można kontrolować przy użyciu [`@key`][5] atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="00703-344">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="00703-345">[`@key`][5] powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza:</span><span class="sxs-lookup"><span data-stu-id="00703-345">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="00703-346">Po `People` zmianie kolekcji algorytm różnicowy zachowuje skojarzenie między `<DetailsEditor>` wystąpieniami i `person` wystąpieniami:</span><span class="sxs-lookup"><span data-stu-id="00703-346">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="00703-347">Jeśli element `Person` zostanie usunięty z `People` listy, tylko odpowiednie `<DetailsEditor>` wystąpienie zostanie usunięte z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="00703-347">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="00703-348">Inne wystąpienia pozostaną bez zmian.</span><span class="sxs-lookup"><span data-stu-id="00703-348">Other instances are left unchanged.</span></span>
* <span data-ttu-id="00703-349">Jeśli na `Person` liście zostanie wstawiony w pewnym miejscu, jedno nowe `<DetailsEditor>` wystąpienie zostanie wstawione w odpowiednim położeniu.</span><span class="sxs-lookup"><span data-stu-id="00703-349">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="00703-350">Inne wystąpienia pozostaną bez zmian.</span><span class="sxs-lookup"><span data-stu-id="00703-350">Other instances are left unchanged.</span></span>
* <span data-ttu-id="00703-351">`Person`W przypadku ponownego uporządkowania wpisów odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i uporządkowane w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="00703-351">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="00703-352">W niektórych scenariuszach użycie programu [`@key`][5] minimalizuje złożoność operacji renderowania i pozwala uniknąć potencjalnych problemów związanych ze stanem częściowych elementów modelu dom, takich jak pozycja fokusu.</span><span class="sxs-lookup"><span data-stu-id="00703-352">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="00703-353">Klucze są lokalne dla każdego elementu kontenera lub składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-353">Keys are local to each container element or component.</span></span> <span data-ttu-id="00703-354">Klucze nie są porównywane globalnie w całym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="00703-354">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="00703-355">Kiedy używać \@ klucza</span><span class="sxs-lookup"><span data-stu-id="00703-355">When to use \@key</span></span>

<span data-ttu-id="00703-356">Zazwyczaj warto używać [`@key`][5] zawsze, gdy lista jest renderowana (na przykład w bloku [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ), a odpowiednia wartość istnieje do zdefiniowania [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="00703-356">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="00703-357">Można również użyć, [`@key`][5] Aby uniemożliwić Blazor zachowanie poddrzewa elementu lub składnika po zmianie obiektu:</span><span class="sxs-lookup"><span data-stu-id="00703-357">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="00703-358">Jeśli `@currentPerson` zmiany, [`@key`][5] dyrektywa Attribute wymusza Blazor odrzucanie całości `<div>` i jego obiektów podrzędnych oraz odbudowa poddrzewa w interfejsie użytkownika o nowych elementach i składnikach.</span><span class="sxs-lookup"><span data-stu-id="00703-358">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="00703-359">Może to być przydatne, jeśli zachodzi konieczność zagwarantowania, że stan interfejsu użytkownika nie jest zachowywany w przypadku `@currentPerson` zmiany.</span><span class="sxs-lookup"><span data-stu-id="00703-359">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="00703-360">Kiedy nie używać \@ klucza</span><span class="sxs-lookup"><span data-stu-id="00703-360">When not to use \@key</span></span>

<span data-ttu-id="00703-361">W przypadku różnicowania w programie występuje koszt wydajności [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="00703-361">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="00703-362">Koszt wydajności nie jest duży, ale określa tylko, [`@key`][5] czy kontrolowanie reguł utrwalania elementu lub składnika przynosi korzyści dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00703-362">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="00703-363">Nawet jeśli [`@key`][5] nie jest używany, program Blazor zachowuje elementy podrzędne i wystąpienia składników tak jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="00703-363">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="00703-364">Jedyną zaletą korzystania z programu [`@key`][5] jest kontrola nad *sposobem* , w jaki wystąpienia modelu są mapowane na zachowane wystąpienia składników, zamiast algorytmu różnicowego, wybierając mapowanie.</span><span class="sxs-lookup"><span data-stu-id="00703-364">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="00703-365">Wartości, które mają być używane dla \@ klucza</span><span class="sxs-lookup"><span data-stu-id="00703-365">What values to use for \@key</span></span>

<span data-ttu-id="00703-366">Ogólnie rzecz biorąc, warto podać jeden z następujących rodzajów wartości dla [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="00703-366">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="00703-367">Wystąpienia obiektów modelu (na przykład `Person` wystąpienie takie jak w poprzednim przykładzie).</span><span class="sxs-lookup"><span data-stu-id="00703-367">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="00703-368">Zapewnia to zachowywanie na podstawie równości odwołań do obiektów.</span><span class="sxs-lookup"><span data-stu-id="00703-368">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="00703-369">Unikatowe identyfikatory (na przykład wartości klucza podstawowego typu `int` , `string` lub `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="00703-369">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="00703-370">Upewnij się, że wartości używane do [`@key`][5] nie kolidują.</span><span class="sxs-lookup"><span data-stu-id="00703-370">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="00703-371">Jeśli w tym samym elemencie nadrzędnym zostaną wykryte wartości powodujące konflikt, program Blazor zgłosi wyjątek, ponieważ nie może on w sposób jednoznaczny zmapować starych elementów lub składników na nowe elementy lub składniki.</span><span class="sxs-lookup"><span data-stu-id="00703-371">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="00703-372">Używaj tylko odrębnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="00703-372">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="00703-373">Zastępowanie parametrów</span><span class="sxs-lookup"><span data-stu-id="00703-373">Overwritten parameters</span></span>

<span data-ttu-id="00703-374">BlazorStruktura zazwyczaj nakłada bezpieczne przypisanie parametrów nadrzędny-do-podrzędnego:</span><span class="sxs-lookup"><span data-stu-id="00703-374">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="00703-375">Parametry nie są nieoczekiwanie zapisywane.</span><span class="sxs-lookup"><span data-stu-id="00703-375">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="00703-376">Efekty uboczne są zminimalizowane.</span><span class="sxs-lookup"><span data-stu-id="00703-376">Side-effects are minimized.</span></span> <span data-ttu-id="00703-377">Na przykład można uniknąć dodatkowych renderowanych, ponieważ mogą one tworzyć nieskończone pętle renderowania.</span><span class="sxs-lookup"><span data-stu-id="00703-377">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="00703-378">Składnik podrzędny otrzymuje nowe wartości parametrów, które prawdopodobnie zastąpią istniejące wartości, gdy składnik nadrzędny zostanie przerenderowany.</span><span class="sxs-lookup"><span data-stu-id="00703-378">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="00703-379">Przypadkowe zastępowanie wartości parametrów w składniku podrzędnym często występuje podczas tworzenia składnika z co najmniej jednym parametrem związanym z danymi i zapisem dewelopera bezpośrednio do parametru w elemencie podrzędnym:</span><span class="sxs-lookup"><span data-stu-id="00703-379">Accidentally overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="00703-380">Składnik podrzędny jest renderowany z co najmniej jedną wartością parametru ze składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-380">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="00703-381">Element podrzędny zapisuje bezpośrednio do wartości parametru.</span><span class="sxs-lookup"><span data-stu-id="00703-381">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="00703-382">Składnik nadrzędny ponownie renderuje i zastępuje wartość parametru elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-382">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="00703-383">Możliwość zastępowania wartości parametrów rozciąga się również na metody ustawiające właściwości składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-383">The potential for overwriting parameter values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="00703-384">**Nasze ogólne wskazówki nie umożliwiają tworzenia składników, które bezpośrednio zapisują do własnych parametrów.**</span><span class="sxs-lookup"><span data-stu-id="00703-384">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="00703-385">Rozważmy następujący wadliwy `Expander` składnik, który:</span><span class="sxs-lookup"><span data-stu-id="00703-385">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="00703-386">Renderuje zawartość podrzędną.</span><span class="sxs-lookup"><span data-stu-id="00703-386">Renders child content.</span></span>
* <span data-ttu-id="00703-387">Włącza lub wyłącza wyświetlanie zawartości podrzędnej za pomocą parametru składnika ( `Expanded` ).</span><span class="sxs-lookup"><span data-stu-id="00703-387">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="00703-388">Składnik zapisuje bezpośrednio do `Expanded` parametru, który pokazuje problem z nadpisaniem parametrów i należy go unikać.</span><span class="sxs-lookup"><span data-stu-id="00703-388">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
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

<span data-ttu-id="00703-389">`Expander`Składnik jest dodawany do składnika nadrzędnego, który może wywołać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="00703-389">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="00703-390">Początkowo `Expander` składniki działają niezależnie, gdy ich `Expanded` właściwości są przełączane.</span><span class="sxs-lookup"><span data-stu-id="00703-390">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="00703-391">Składniki podrzędne utrzymują swoje Stany zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="00703-391">The child components maintain their states as expected.</span></span> <span data-ttu-id="00703-392">Gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana w obiekcie nadrzędnym, `Expanded` parametr pierwszego składnika podrzędnego jest resetowany z powrotem do jego wartości początkowej ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="00703-392">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="00703-393">Wartość drugiego `Expander` składnika `Expanded` nie zostanie zresetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.</span><span class="sxs-lookup"><span data-stu-id="00703-393">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="00703-394">Aby zachować stan w poprzednim scenariuszu, użyj *pola prywatnego* w `Expander` składniku, aby zachować stan przełączenia.</span><span class="sxs-lookup"><span data-stu-id="00703-394">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="00703-395">Następujący zmieniony `Expander` składnik:</span><span class="sxs-lookup"><span data-stu-id="00703-395">The following revised `Expander` component:</span></span>

* <span data-ttu-id="00703-396">Akceptuje `Expanded` wartość parametru składnika z elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="00703-396">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="00703-397">Przypisuje wartość parametru składnika do *pola prywatnego* ( `expanded` ) w [zdarzeniu OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="00703-397">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="00703-398">Używa pola private do obsługi wewnętrznego stanu przełączania, który pokazuje, jak uniknąć pisania bezpośrednio do parametru.</span><span class="sxs-lookup"><span data-stu-id="00703-398">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

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

<span data-ttu-id="00703-399">Aby uzyskać dodatkowe informacje, zobacz [ Blazor dwukierunkowy błąd powiązania (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="00703-399">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="00703-400">Zastosuj atrybut</span><span class="sxs-lookup"><span data-stu-id="00703-400">Apply an attribute</span></span>

<span data-ttu-id="00703-401">Atrybuty mogą być stosowane do Razor składników z [`@attribute`][7] dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="00703-401">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="00703-402">Poniższy przykład stosuje [ `[Authorize]` atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) do klasy składnika:</span><span class="sxs-lookup"><span data-stu-id="00703-402">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="00703-403">Warunkowe atrybuty elementu HTML</span><span class="sxs-lookup"><span data-stu-id="00703-403">Conditional HTML element attributes</span></span>

<span data-ttu-id="00703-404">Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET.</span><span class="sxs-lookup"><span data-stu-id="00703-404">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="00703-405">Jeśli wartość jest `false` lub `null` , atrybut nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="00703-405">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="00703-406">Jeśli wartość to `true` , atrybut jest renderowany jako zminimalizowany.</span><span class="sxs-lookup"><span data-stu-id="00703-406">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="00703-407">W poniższym przykładzie, `IsCompleted` określa, czy `checked` jest renderowany w znacznikach elementu:</span><span class="sxs-lookup"><span data-stu-id="00703-407">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="00703-408">Jeśli `IsCompleted` jest `true` , pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="00703-408">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="00703-409">Jeśli `IsCompleted` jest `false` , pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="00703-409">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="00703-410">Aby uzyskać więcej informacji, zobacz [ Razor Informacje o składni dla ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="00703-410">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="00703-411">Niektóre atrybuty HTML, takie jak [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , nie działają prawidłowo, gdy typem .NET jest `bool` .</span><span class="sxs-lookup"><span data-stu-id="00703-411">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="00703-412">W takich przypadkach należy użyć `string` typu zamiast `bool` .</span><span class="sxs-lookup"><span data-stu-id="00703-412">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="00703-413">Nieprzetworzony kod HTML</span><span class="sxs-lookup"><span data-stu-id="00703-413">Raw HTML</span></span>

<span data-ttu-id="00703-414">Ciągi są zwykle renderowane przy użyciu węzłów tekstowych DOM, co oznacza, że wszystkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst literału.</span><span class="sxs-lookup"><span data-stu-id="00703-414">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="00703-415">Aby renderować nieprzetworzony kod HTML, zawiń zawartość HTML w `MarkupString` wartości.</span><span class="sxs-lookup"><span data-stu-id="00703-415">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="00703-416">Wartość jest analizowana jako plik HTML lub SVG i wstawiona do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="00703-416">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="00703-417">Renderowanie nieprzetworzonego kodu HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy je unikać!</span><span class="sxs-lookup"><span data-stu-id="00703-417">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="00703-418">W poniższym przykładzie pokazano, jak za pomocą `MarkupString` typu dodać blok statycznej zawartości HTML do renderowanego danych wyjściowych składnika:</span><span class="sxs-lookup"><span data-stu-id="00703-418">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="00703-419">Razor przystawki</span><span class="sxs-lookup"><span data-stu-id="00703-419">Razor templates</span></span>

<span data-ttu-id="00703-420">Fragmenty renderowania można definiować przy użyciu Razor składni szablonu.</span><span class="sxs-lookup"><span data-stu-id="00703-420">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="00703-421">Razor Szablony są sposobem definiowania fragmentu interfejsu użytkownika i przyjmuje następujący format:</span><span class="sxs-lookup"><span data-stu-id="00703-421">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="00703-422">Poniższy przykład ilustruje sposób określania <xref:Microsoft.AspNetCore.Components.RenderFragment> i <xref:Microsoft.AspNetCore.Components.RenderFragment%601> wartości oraz renderowania szablonów bezpośrednio w składniku.</span><span class="sxs-lookup"><span data-stu-id="00703-422">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="00703-423">Fragmenty renderowania mogą być również przekazane jako argumenty do [składników z szablonem](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="00703-423">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="00703-424">Renderowane dane wyjściowe poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="00703-424">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="00703-425">Statyczne zasoby</span><span class="sxs-lookup"><span data-stu-id="00703-425">Static assets</span></span>

<span data-ttu-id="00703-426">Blazorzgodnie z Konwencją ASP.NET Core aplikacje umieszczające statyczne zasoby w [ `web root (wwwroot)` folderze](xref:fundamentals/index#web-root)projektu.</span><span class="sxs-lookup"><span data-stu-id="00703-426">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="00703-427">Użyj ścieżki względnej (), `/` Aby odwołać się do katalogu głównego sieci Web dla statycznego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="00703-427">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="00703-428">W poniższym przykładzie znajduje się `logo.png` fizycznie w `{PROJECT ROOT}/wwwroot/images` folderze:</span><span class="sxs-lookup"><span data-stu-id="00703-428">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="00703-429">Razor składniki nie **obsługują** notacji ukośnika odwrotnego ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="00703-429">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="00703-430">Aby uzyskać informacje na temat ustawiania ścieżki podstawowej aplikacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="00703-430">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="00703-431">Pomocnicy tagów nie są obsługiwani w składnikach</span><span class="sxs-lookup"><span data-stu-id="00703-431">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="00703-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) nie są obsługiwane w Razor składnikach ( `.razor` pliki).</span><span class="sxs-lookup"><span data-stu-id="00703-432">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="00703-433">Aby zapewnić funkcję przypominającą pomocnik tagów w programie Blazor , należy utworzyć składnik o tej samej funkcji co pomocnik tagów i użyć składnika zamiast niego.</span><span class="sxs-lookup"><span data-stu-id="00703-433">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="00703-434">Skalowalne obrazy wektorowe (SVG)</span><span class="sxs-lookup"><span data-stu-id="00703-434">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="00703-435">Ponieważ Blazor renderuje HTML, obrazy obsługiwane przez przeglądarkę, w tym obrazy Scalable Vector Graphics (SVG) `.svg` , są obsługiwane przez `<img>` tag:</span><span class="sxs-lookup"><span data-stu-id="00703-435">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="00703-436">Podobnie Obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów ( `.css` ):</span><span class="sxs-lookup"><span data-stu-id="00703-436">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="00703-437">Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="00703-437">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="00703-438">Jeśli umieścisz `<svg>` tag bezpośrednio w pliku składnika ( `.razor` ), podstawowe renderowanie obrazu jest obsługiwane, ale wiele scenariuszy zaawansowanych nie jest jeszcze obsługiwanych.</span><span class="sxs-lookup"><span data-stu-id="00703-438">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="00703-439">Na przykład `<use>` tagi nie są obecnie przestrzegane i [`@bind`][10] nie mogą być używane z niektórymi tagami SVG.</span><span class="sxs-lookup"><span data-stu-id="00703-439">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="00703-440">Aby uzyskać więcej informacji, zobacz [Obsługa SVG w Blazor (#18271 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="00703-440">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="00703-441">Zachowanie renderowania odstępów</span><span class="sxs-lookup"><span data-stu-id="00703-441">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="00703-442">O ile [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) dyrektywa nie jest używana z wartością `true` , dodatkowe odstępy są usuwane domyślnie, jeśli:</span><span class="sxs-lookup"><span data-stu-id="00703-442">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="00703-443">Początkowe lub końcowe w obrębie elementu.</span><span class="sxs-lookup"><span data-stu-id="00703-443">Leading or trailing within an element.</span></span>
* <span data-ttu-id="00703-444">Interlinia lub kończąca się w `RenderFragment` parametrze.</span><span class="sxs-lookup"><span data-stu-id="00703-444">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="00703-445">Na przykład zawartość podrzędna została przeniesiona do innego składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-445">For example, child content passed to another component.</span></span>
* <span data-ttu-id="00703-446">Poprzedza lub następuje blok kodu w języku C#, taki jak `@if` lub `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="00703-446">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="00703-447">Usunięcie odstępu może mieć wpływ na renderowane dane wyjściowe podczas korzystania z reguły CSS, np `white-space: pre` ..</span><span class="sxs-lookup"><span data-stu-id="00703-447">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="00703-448">Aby wyłączyć tę optymalizację wydajności i zachować odstęp, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="00703-448">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="00703-449">Dodaj `@preservewhitespace true` dyrektywę w górnej części `.razor` pliku, aby zastosować preferencję do określonego składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-449">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="00703-450">Dodaj `@preservewhitespace true` dyrektywę wewnątrz pliku, `_Imports.razor` Aby zastosować preferencję do całego podkatalogu lub całego projektu.</span><span class="sxs-lookup"><span data-stu-id="00703-450">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="00703-451">W większości przypadków żadna akcja nie jest wymagana, ponieważ aplikacje zwykle nadal zachowują się normalnie (ale szybciej).</span><span class="sxs-lookup"><span data-stu-id="00703-451">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="00703-452">W przypadku wyłączania odstępu powoduje dowolnego problemu dla określonego składnika, użyj `@preservewhitespace true` w tym składniku, aby wyłączyć tę optymalizację.</span><span class="sxs-lookup"><span data-stu-id="00703-452">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="00703-453">Biały znak jest zachowywany w kodzie źródłowym składnika.</span><span class="sxs-lookup"><span data-stu-id="00703-453">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="00703-454">Odstępy — renderowanie tekstu tylko w Document Object Model przeglądarki (DOM), nawet gdy nie ma efektu wizualnego.</span><span class="sxs-lookup"><span data-stu-id="00703-454">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="00703-455">Rozważmy następujący Razor Kod składnika:</span><span class="sxs-lookup"><span data-stu-id="00703-455">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="00703-456">W poprzednim przykładzie pokazano następujący niezbędny odstęp:</span><span class="sxs-lookup"><span data-stu-id="00703-456">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="00703-457">Poza `@foreach` blokiem kodu.</span><span class="sxs-lookup"><span data-stu-id="00703-457">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="00703-458">Wokół `<li>` elementu.</span><span class="sxs-lookup"><span data-stu-id="00703-458">Around the `<li>` element.</span></span>
* <span data-ttu-id="00703-459">Wokół `@item.Text` danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="00703-459">Around the `@item.Text` output.</span></span>

<span data-ttu-id="00703-460">Lista zawierająca 100 elementów powoduje 402 obszary odstępu, a żaden z dodatkowych białych znaków nie ma wizualnego wpływu na renderowane dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="00703-460">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="00703-461">Podczas renderowania statycznego kodu HTML dla składników, odstęp wewnątrz znacznika nie jest zachowywany.</span><span class="sxs-lookup"><span data-stu-id="00703-461">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="00703-462">Na przykład Wyświetl źródło następującego składnika w renderowanych danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="00703-462">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="00703-463">Odstęp nie jest zachowywany w powyższym Razor znaczniku:</span><span class="sxs-lookup"><span data-stu-id="00703-463">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="00703-464">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="00703-464">Additional resources</span></span>

* <span data-ttu-id="00703-465"><xref:blazor/security/server/threat-mitigation>: Zawiera wskazówki dotyczące tworzenia Blazor Server aplikacji, które muszą będą konkurować o z wyczerpaniem zasobów.</span><span class="sxs-lookup"><span data-stu-id="00703-465"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[2]: <xref:mvc/views/razor#using> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[3]: <xref:mvc/views/razor#attributes> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[4]: <xref:mvc/views/razor#ref> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[5]: <xref:mvc/views/razor#key> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[6]: <xref:mvc/views/razor#inherits> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[7]: <xref:mvc/views/razor#attribute> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[8]: <xref:mvc/views/razor#namespace> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[9]: <xref:mvc/views/razor#page> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
[10]: <xref:mvc/views/razor#bind> "::: No-Loc (Razor)::: odwołanie do składni dla ASP.NET Core"
