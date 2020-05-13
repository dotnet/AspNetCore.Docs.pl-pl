---
title: Tworzenie i używanie Razor składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak tworzyć i używać Razor składników, w tym jak powiązać z danymi, obsługiwać zdarzenia i zarządzać cyklem życia składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: a7009bf1cf99a15f3617b47a904d52f5787b9ce1
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153513"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="47dc6-103">Tworzenie i używanie Razor składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47dc6-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="47dc6-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="47dc6-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="47dc6-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47dc6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="47dc6-106">aplikacje są kompilowane przy użyciu *składników*programu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-106"> apps are built using *components*.</span></span> <span data-ttu-id="47dc6-107">Składnik jest niezależnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz.</span><span class="sxs-lookup"><span data-stu-id="47dc6-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="47dc6-108">Składnik zawiera znaczniki HTML i logikę przetwarzania wymagane do iniekcji danych lub reagowania na zdarzenia interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="47dc6-109">Składniki są elastyczne i lekkie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="47dc6-110">Mogą być zagnieżdżane, ponownie używane i udostępniane między projektami.</span><span class="sxs-lookup"><span data-stu-id="47dc6-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="47dc6-111">Klasy składników</span><span class="sxs-lookup"><span data-stu-id="47dc6-111">Component classes</span></span>

<span data-ttu-id="47dc6-112">Składniki są zaimplementowane w [Razor](xref:mvc/views/razor) plikach składników (*. Razor*) przy użyciu kombinacji języka C# i znaczników HTML.</span><span class="sxs-lookup"><span data-stu-id="47dc6-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="47dc6-113">Składnik w programie Blazor jest formalnie nazywany \* Razor składnikiem\*.</span><span class="sxs-lookup"><span data-stu-id="47dc6-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="47dc6-114">Nazwa składnika musi rozpoczynać się wielką literą.</span><span class="sxs-lookup"><span data-stu-id="47dc6-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="47dc6-115">Na przykład *MyCoolComponent. Razor* jest prawidłowy, a *MyCoolComponent. Razor* jest nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="47dc6-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="47dc6-116">Interfejs użytkownika dla składnika jest definiowany przy użyciu języka HTML.</span><span class="sxs-lookup"><span data-stu-id="47dc6-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="47dc6-117">Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="47dc6-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="47dc6-118">Po skompilowaniu aplikacji logika kodu HTML i renderowania języka C# jest konwertowana na klasę składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="47dc6-119">Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="47dc6-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="47dc6-120">Elementy członkowskie klasy składnika są zdefiniowane w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="47dc6-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="47dc6-121">W `@code` bloku stan składnika (właściwości, pola) jest określany przy użyciu metod obsługi zdarzeń lub definiowania innej logiki składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="47dc6-122">Dozwolony jest więcej niż jeden `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="47dc6-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="47dc6-123">Składowe składnika mogą być używane jako część logiki renderowania składnika przy użyciu wyrażeń języka C#, które zaczynają się od `@` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="47dc6-124">Na przykład pole C# jest renderowane przez utworzenie prefiksu `@` do nazwy pola.</span><span class="sxs-lookup"><span data-stu-id="47dc6-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="47dc6-125">Poniższy przykład szacuje i renderuje:</span><span class="sxs-lookup"><span data-stu-id="47dc6-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="47dc6-126">`headingFontStyle`na wartość właściwości CSS dla elementu `font-style` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="47dc6-127">`headingText`do zawartości `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="47dc6-128">Po pierwszym wyrenderowaniu składnika składnik generuje jego drzewo renderowania w odpowiedzi na zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="47dc6-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="47dc6-129">Następnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje Document Object Model przeglądarki (DOM).</span><span class="sxs-lookup"><span data-stu-id="47dc6-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="47dc6-130">Składniki to zwykłe klasy języka C# i można je umieścić w dowolnym miejscu w projekcie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="47dc6-131">Składniki, które generują strony sieci Web, zwykle znajdują się w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="47dc6-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="47dc6-132">Składniki niestronicowe są często umieszczane w folderze *udostępnionym* lub w folderze niestandardowym dodanym do projektu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="47dc6-133">Zazwyczaj przestrzeń nazw składnika pochodzi od głównej przestrzeni nazw aplikacji i lokalizacji składnika (folderu) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="47dc6-134">Jeśli główna przestrzeń nazw aplikacji jest `BlazorApp` i znajduje się `Counter` w folderze *stron* :</span><span class="sxs-lookup"><span data-stu-id="47dc6-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="47dc6-135">`Counter`Przestrzeń nazw składnika to `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="47dc6-136">W pełni kwalifikowana nazwa typu składnika to `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="47dc6-137">W przypadku folderów niestandardowych, które zawierają składniki, Dodaj `using` instrukcję do składnika nadrzędnego lub pliku *_Imports. Razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-137">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="47dc6-138">W poniższym przykładzie składniki w folderze *Components* są dostępne:</span><span class="sxs-lookup"><span data-stu-id="47dc6-138">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="47dc6-139">Alternatywnie można odwoływać się bezpośrednio do składnika:</span><span class="sxs-lookup"><span data-stu-id="47dc6-139">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="47dc6-140">Aby uzyskać więcej informacji, zobacz sekcję [Importowanie składników](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="47dc6-140">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="static-assets"></a><span data-ttu-id="47dc6-141">Statyczne zasoby</span><span class="sxs-lookup"><span data-stu-id="47dc6-141">Static assets</span></span>

Blazor<span data-ttu-id="47dc6-142">zgodnie z Konwencją ASP.NET Core aplikacje umieszczające statyczne zasoby w [folderze katalogu głównego sieci Web (wwwroot)](xref:fundamentals/index#web-root)projektu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-142"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="47dc6-143">Użyj ścieżki względnej (), `/` Aby odwołać się do katalogu głównego sieci Web dla statycznego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="47dc6-143">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="47dc6-144">W poniższym przykładzie *logo. png* znajduje się fizycznie w folderze *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="47dc6-144">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="47dc6-145">składniki nie **obsługują** notacji ukośnika odwrotnego ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="47dc6-145"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="47dc6-146">Aby uzyskać informacje na temat ustawiania ścieżki podstawowej aplikacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="47dc6-146">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="47dc6-147">Pomocnicy tagów nie są obsługiwani w składnikach</span><span class="sxs-lookup"><span data-stu-id="47dc6-147">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="47dc6-148">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) nie są obsługiwani w Razor składnikach (pliki *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="47dc6-148">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="47dc6-149">Aby zapewnić funkcję przypominającą pomocnik tagów w programie Blazor , należy utworzyć składnik o tej samej funkcji co pomocnik tagów i użyć składnika zamiast niego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-149">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="47dc6-150">Używanie składników</span><span class="sxs-lookup"><span data-stu-id="47dc6-150">Use components</span></span>

<span data-ttu-id="47dc6-151">Składniki mogą zawierać inne składniki, deklarując je za pomocą składni elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="47dc6-151">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="47dc6-152">Znaczniki użycia składnika wyglądają jak tag HTML, gdzie nazwa znacznika jest typem składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-152">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="47dc6-153">Poniższy znacznik w *indeksie. Razor* renderuje `HeadingComponent` wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="47dc6-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="47dc6-154">*Składniki/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-154">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="47dc6-155">Jeśli składnik zawiera element HTML z wielką literą, która nie jest zgodna z nazwą składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę.</span><span class="sxs-lookup"><span data-stu-id="47dc6-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="47dc6-156">Dodanie `@using` dyrektywy dla przestrzeni nazw składnika sprawia, że składnik jest dostępny, co rozwiązuje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-156">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="47dc6-157">Routing</span><span class="sxs-lookup"><span data-stu-id="47dc6-157">Routing</span></span>

<span data-ttu-id="47dc6-158">Routing w programie jest realizowany Blazor przez dostarczenie szablonu trasy do każdego dostępnego składnika w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-158">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="47dc6-159">Gdy Razor plik z `@page` dyrektywą jest kompilowany, wygenerowana Klasa ma określony <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="47dc6-159">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="47dc6-160">W czasie wykonywania router szuka klas składników za pomocą `RouteAttribute` i renderuje, w zależności od tego, który składnik ma szablon trasy zgodny z żądanym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="47dc6-160">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="47dc6-161">Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="47dc6-161">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="47dc6-162">Parametry</span><span class="sxs-lookup"><span data-stu-id="47dc6-162">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="47dc6-163">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="47dc6-163">Route parameters</span></span>

<span data-ttu-id="47dc6-164">Składniki mogą odbierać parametry trasy z szablonu trasy dostarczonego w `@page` dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-164">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="47dc6-165">Router używa parametrów trasy, aby wypełnić odpowiednie parametry składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-165">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="47dc6-166">*Strony/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-166">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="47dc6-167">Parametry opcjonalne nie są obsługiwane, więc dwie `@page` dyrektywy są stosowane w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-167">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="47dc6-168">Pierwszy zezwala na nawigowanie do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="47dc6-168">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="47dc6-169">Druga `@page` dyrektywa odbiera `{text}` parametr Route i przypisuje wartość do `Text` właściwości.</span><span class="sxs-lookup"><span data-stu-id="47dc6-169">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="47dc6-170">*Catch-all* parametrów ( `*` / `**` ), która przechwytuje ścieżkę między wieloma granicami folderów, **nie** jest obsługiwana w Razor składnikach (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="47dc6-170">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="47dc6-171">Parametry składnika</span><span class="sxs-lookup"><span data-stu-id="47dc6-171">Component parameters</span></span>

<span data-ttu-id="47dc6-172">Składniki mogą mieć *Parametry składnika*, które są zdefiniowane przy użyciu właściwości publicznych w klasie składnika z `[Parameter]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="47dc6-172">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="47dc6-173">Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.</span><span class="sxs-lookup"><span data-stu-id="47dc6-173">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="47dc6-174">*Składniki/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-174">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="47dc6-175">W poniższym przykładzie z przykładowej aplikacji `ParentComponent` ustawia wartość `Title` właściwości `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-175">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="47dc6-176">*Strony/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-176">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="47dc6-177">Nie należy tworzyć składników, które zapisują do własnych *parametrów składników*, zamiast tego należy użyć pola private.</span><span class="sxs-lookup"><span data-stu-id="47dc6-177">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="47dc6-178">Aby uzyskać więcej informacji, zobacz sekcję [nie twórz składników, które zapisują we własnych właściwościach parametrów](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="47dc6-178">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="47dc6-179">Zawartość podrzędna</span><span class="sxs-lookup"><span data-stu-id="47dc6-179">Child content</span></span>

<span data-ttu-id="47dc6-180">Składniki mogą ustawiać zawartość innego składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-180">Components can set the content of another component.</span></span> <span data-ttu-id="47dc6-181">Składnik Assigner zawiera zawartość między tagami, które określają składnik do odbioru.</span><span class="sxs-lookup"><span data-stu-id="47dc6-181">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="47dc6-182">W poniższym przykładzie `ChildComponent` ma `ChildContent` Właściwość, która reprezentuje element `RenderFragment` , który reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="47dc6-182">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="47dc6-183">Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana.</span><span class="sxs-lookup"><span data-stu-id="47dc6-183">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="47dc6-184">Wartość `ChildContent` jest odbierana ze składnika nadrzędnego i renderowany w panelu uruchamiania `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-184">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="47dc6-185">*Składniki/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="47dc6-186">Właściwość otrzymująca `RenderFragment` zawartość musi być nazywana `ChildContent` Konwencją.</span><span class="sxs-lookup"><span data-stu-id="47dc6-186">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="47dc6-187">`ParentComponent`W przykładowej aplikacji można dostarczyć zawartość do renderowania `ChildComponent` przez umieszczenie zawartości wewnątrz `<ChildComponent>` tagów.</span><span class="sxs-lookup"><span data-stu-id="47dc6-187">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="47dc6-188">*Strony/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-188">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="47dc6-189">Korzystając atrybutów i dowolne parametry</span><span class="sxs-lookup"><span data-stu-id="47dc6-189">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="47dc6-190">Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-190">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="47dc6-191">Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy użyciu [`@attributes`](xref:mvc/views/razor#attributes) Razor dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="47dc6-191">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="47dc6-192">Ten scenariusz jest przydatny podczas definiowania składnika, który generuje element znaczników, który obsługuje różne dostosowania.</span><span class="sxs-lookup"><span data-stu-id="47dc6-192">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="47dc6-193">Na przykład może być żmudnym do definiowania atrybutów oddzielnie dla `<input>` , który obsługuje wiele parametrów.</span><span class="sxs-lookup"><span data-stu-id="47dc6-193">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="47dc6-194">W poniższym przykładzie pierwszy `<input>` element ( `id="useIndividualParams"` ) używa pojedynczych parametrów składnika, podczas gdy drugi `<input>` element ( `id="useAttributesDict"` ) używa atrybutu korzystając:</span><span class="sxs-lookup"><span data-stu-id="47dc6-194">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="47dc6-195">Typ parametru musi być zaimplementowany `IEnumerable<KeyValuePair<string, object>>` przy użyciu kluczy ciągu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-195">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="47dc6-196">Korzystanie z programu `IReadOnlyDictionary<string, object>` jest również opcją w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-196">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="47dc6-197">Renderowane `<input>` elementy korzystające z obu metod są identyczne:</span><span class="sxs-lookup"><span data-stu-id="47dc6-197">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="47dc6-198">Aby zaakceptować dowolne atrybuty, zdefiniuj parametr składnika przy użyciu `[Parameter]` atrybutu z `CaptureUnmatchedValues` właściwością ustawioną na `true` :</span><span class="sxs-lookup"><span data-stu-id="47dc6-198">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="47dc6-199">`CaptureUnmatchedValues`Właściwość on `[Parameter]` umożliwia dopasowanie parametru do wszystkich atrybutów, które nie są zgodne z żadnym innym parametrem.</span><span class="sxs-lookup"><span data-stu-id="47dc6-199">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="47dc6-200">Składnik może definiować tylko jeden parametr z `CaptureUnmatchedValues` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-200">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="47dc6-201">Typ właściwości używany z elementem `CaptureUnmatchedValues` musi być możliwy do przypisania z `Dictionary<string, object>` klucza ciągu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-201">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="47dc6-202">`IEnumerable<KeyValuePair<string, object>>`lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-202">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="47dc6-203">Pozycja `@attributes` względem pozycji atrybutów elementu jest ważna.</span><span class="sxs-lookup"><span data-stu-id="47dc6-203">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="47dc6-204">Gdy `@attributes` są splatted w elemencie, atrybuty są przetwarzane od prawej do lewej (Ostatnia do).</span><span class="sxs-lookup"><span data-stu-id="47dc6-204">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="47dc6-205">Rozważmy następujący przykład składnika, który zużywa `Child` składnik:</span><span class="sxs-lookup"><span data-stu-id="47dc6-205">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="47dc6-206">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-206">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="47dc6-207">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-207">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="47dc6-208">`Child` `extra` Atrybut składnika jest ustawiony z prawej strony `@attributes` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-208">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="47dc6-209">`Parent`Renderowany składnik `<div>` zawiera gdy jest `extra="5"` przenoszona przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od ostatni do pierwszego):</span><span class="sxs-lookup"><span data-stu-id="47dc6-209">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="47dc6-210">W poniższym przykładzie porządek `extra` i `@attributes` jest odwrócony w `Child` składniku `<div>` :</span><span class="sxs-lookup"><span data-stu-id="47dc6-210">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="47dc6-211">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-211">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="47dc6-212">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-212">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="47dc6-213">Renderowane `<div>` w `Parent` składniku zawiera, `extra="10"` gdy jest przenoszona przez dodatkowy atrybut:</span><span class="sxs-lookup"><span data-stu-id="47dc6-213">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="47dc6-214">Przechwyć odwołania do składników</span><span class="sxs-lookup"><span data-stu-id="47dc6-214">Capture references to components</span></span>

<span data-ttu-id="47dc6-215">Odwołania do składników zapewniają sposób odwoływania się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia, takie jak `Show` lub `Reset` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-215">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="47dc6-216">Aby przechwycić odwołanie do składnika:</span><span class="sxs-lookup"><span data-stu-id="47dc6-216">To capture a component reference:</span></span>

* <span data-ttu-id="47dc6-217">Dodaj [`@ref`](xref:mvc/views/razor#ref) atrybut do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-217">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="47dc6-218">Zdefiniuj pole z tym samym typem co składnik podrzędny.</span><span class="sxs-lookup"><span data-stu-id="47dc6-218">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="47dc6-219">Gdy składnik jest renderowany, `loginDialog` pole zostanie wypełnione `MyLoginDialog` wystąpieniem składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-219">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="47dc6-220">Następnie można wywołać metody .NET w wystąpieniu składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-220">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47dc6-221">`loginDialog`Zmienna jest wypełniana tylko po wyrenderowaniu składnika, a jego wyjście zawiera `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="47dc6-221">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="47dc6-222">Do tego momentu nie ma niczego do odwołania.</span><span class="sxs-lookup"><span data-stu-id="47dc6-222">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="47dc6-223">Aby manipulować odwołaniami do składników po zakończeniu renderowania składnika, należy użyć [metody OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="47dc6-223">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="47dc6-224">Aby odwoływać się do składników w pętli, zobacz [przechwytywanie odwołań do wielu podobnych składników podrzędnych (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="47dc6-224">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="47dc6-225">Podczas przechwytywania odwołań do składników użycie podobnej składni do [przechwytywania odwołań do elementów](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest funkcją międzyoperacyjności języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="47dc6-225">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="47dc6-226">Odwołania do składników nie są przenoszone do kodu JavaScript, &mdash; są używane tylko w kodzie platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="47dc6-226">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="47dc6-227">**Nie** należy używać odwołań do składników do mutacji stanu składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="47dc6-227">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="47dc6-228">Zamiast tego należy używać zwykłych parametrów deklaratywnych do przekazywania danych do składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="47dc6-228">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="47dc6-229">Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie uruchamiają się w prawidłowym czasie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-229">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="47dc6-230">Wywołaj metody składnika zewnętrznie, aby zaktualizować stan</span><span class="sxs-lookup"><span data-stu-id="47dc6-230">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="47dc6-231">używa kontekstu synchronizacji ( `SynchronizationContext` ) w celu wymuszenia pojedynczego wątku logicznego wykonywania.</span><span class="sxs-lookup"><span data-stu-id="47dc6-231"> uses a synchronization context (`SynchronizationContext`) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="47dc6-232">[Metody cyklu życia](xref:blazor/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane przez Blazor są wykonywane w kontekście synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-232">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="47dc6-233">Kontekst synchronizacji serwera próbuje emulować środowisko jednowątkowe, aby dokładnie pasowało do modelu webassembly w przeglądarce, który jest pojedynczym wątkiem.</span><span class="sxs-lookup"><span data-stu-id="47dc6-233"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="47dc6-234">W dowolnym momencie prace są wykonywane na dokładnie jednym wątku, co daje wrażenie pojedynczego wątku logicznego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-234">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="47dc6-235">Nie wykonano jednocześnie dwóch operacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-235">No two operations execute concurrently.</span></span>

<span data-ttu-id="47dc6-236">W przypadku zdarzenia składnik należy zaktualizować w oparciu o zdarzenie zewnętrzne, takie jak czasomierz lub inne powiadomienia, przy użyciu `InvokeAsync` metody, która będzie wysyłana do Blazor kontekstu synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-236">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's synchronization context.</span></span> <span data-ttu-id="47dc6-237">Rozważmy na przykład *usługę powiadamiania* , która może powiadomić dowolny składnik nasłuchujący zaktualizowanego stanu:</span><span class="sxs-lookup"><span data-stu-id="47dc6-237">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="47dc6-238">Zarejestruj `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="47dc6-238">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="47dc6-239">W obszarze Blazor zestaw webassembly Zarejestruj usługę w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="47dc6-239">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="47dc6-240">W obszarze Blazor serwer Zarejestruj usługę w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="47dc6-240">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="47dc6-241">`NotifierService`Aby zaktualizować składnik, użyj programu:</span><span class="sxs-lookup"><span data-stu-id="47dc6-241">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="47dc6-242">W poprzednim przykładzie `NotifierService` wywołuje `OnNotify` metodę składnika poza Blazor kontekstem synchronizacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-242">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="47dc6-243">`InvokeAsync`służy do przełączania do poprawnego kontekstu i kolejki renderowania.</span><span class="sxs-lookup"><span data-stu-id="47dc6-243">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="47dc6-244">Użyj \@ klawisza, aby kontrolować zachowywanie elementów i składników</span><span class="sxs-lookup"><span data-stu-id="47dc6-244">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="47dc6-245">Gdy renderuje listę elementów lub składników, a następnie zmienia się elementy lub składniki, Blazor algorytm różnicowania musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny być na nich mapowane.</span><span class="sxs-lookup"><span data-stu-id="47dc6-245">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="47dc6-246">Zwykle ten proces jest automatyczny i można go zignorować, ale istnieją przypadki, w których może być konieczne sterowanie procesem.</span><span class="sxs-lookup"><span data-stu-id="47dc6-246">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="47dc6-247">Rozważmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="47dc6-247">Consider the following example:</span></span>

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

<span data-ttu-id="47dc6-248">Zawartość `People` kolekcji może ulec zmianie z wstawionymi, usuniętymi lub z ponownymi zamówieniami.</span><span class="sxs-lookup"><span data-stu-id="47dc6-248">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="47dc6-249">Gdy składnik jest przerenderowany, `<DetailsEditor>` składnik może ulec zmianie, aby otrzymywać różne `Details` wartości parametrów.</span><span class="sxs-lookup"><span data-stu-id="47dc6-249">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="47dc6-250">Może to spowodować bardziej złożone odwzorowanie niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="47dc6-250">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="47dc6-251">W niektórych przypadkach odzyskanie może prowadzić do zauważalnych różnic w zachowaniu, takich jak brak fokusu elementu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-251">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="47dc6-252">Proces mapowania można kontrolować przy użyciu [`@key`](xref:mvc/views/razor#key) atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="47dc6-252">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="47dc6-253">`@key`powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza:</span><span class="sxs-lookup"><span data-stu-id="47dc6-253">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="47dc6-254">Po `People` zmianie kolekcji algorytm różnicowy zachowuje skojarzenie między `<DetailsEditor>` wystąpieniami i `person` wystąpieniami:</span><span class="sxs-lookup"><span data-stu-id="47dc6-254">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="47dc6-255">Jeśli element `Person` zostanie usunięty z `People` listy, tylko odpowiednie `<DetailsEditor>` wystąpienie zostanie usunięte z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-255">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="47dc6-256">Inne wystąpienia pozostaną bez zmian.</span><span class="sxs-lookup"><span data-stu-id="47dc6-256">Other instances are left unchanged.</span></span>
* <span data-ttu-id="47dc6-257">Jeśli na `Person` liście zostanie wstawiony w pewnym miejscu, jedno nowe `<DetailsEditor>` wystąpienie zostanie wstawione w odpowiednim położeniu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-257">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="47dc6-258">Inne wystąpienia pozostaną bez zmian.</span><span class="sxs-lookup"><span data-stu-id="47dc6-258">Other instances are left unchanged.</span></span>
* <span data-ttu-id="47dc6-259">`Person`W przypadku ponownego uporządkowania wpisów odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i uporządkowane w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-259">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="47dc6-260">W niektórych scenariuszach użycie programu `@key` minimalizuje złożoność operacji renderowania i pozwala uniknąć potencjalnych problemów związanych ze stanem częściowych elementów modelu dom, takich jak pozycja fokusu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-260">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47dc6-261">Klucze są lokalne dla każdego elementu kontenera lub składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-261">Keys are local to each container element or component.</span></span> <span data-ttu-id="47dc6-262">Klucze nie są porównywane globalnie w całym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-262">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="47dc6-263">Kiedy używać \@ klucza</span><span class="sxs-lookup"><span data-stu-id="47dc6-263">When to use \@key</span></span>

<span data-ttu-id="47dc6-264">Zazwyczaj warto używać `@key` zawsze, gdy lista jest renderowana (na przykład w `@foreach` bloku) i odpowiednia wartość istnieje do zdefiniowania `@key` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-264">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="47dc6-265">Można również użyć, `@key` Aby uniemożliwić Blazor zachowanie poddrzewa elementu lub składnika po zmianie obiektu:</span><span class="sxs-lookup"><span data-stu-id="47dc6-265">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="47dc6-266">Jeśli `@currentPerson` zmiany, `@key` dyrektywa Attribute wymusza Blazor odrzucanie całości `<div>` i jego obiektów podrzędnych oraz odbudowa poddrzewa w interfejsie użytkownika o nowych elementach i składnikach.</span><span class="sxs-lookup"><span data-stu-id="47dc6-266">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="47dc6-267">Może to być przydatne, jeśli zachodzi konieczność zagwarantowania, że stan interfejsu użytkownika nie jest zachowywany w przypadku `@currentPerson` zmiany.</span><span class="sxs-lookup"><span data-stu-id="47dc6-267">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="47dc6-268">Kiedy nie używać \@ klucza</span><span class="sxs-lookup"><span data-stu-id="47dc6-268">When not to use \@key</span></span>

<span data-ttu-id="47dc6-269">W przypadku różnicowania w programie występuje koszt wydajności `@key` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-269">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="47dc6-270">Koszt wydajności nie jest duży, ale określa tylko, `@key` czy kontrolowanie reguł utrwalania elementu lub składnika przynosi korzyści dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-270">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="47dc6-271">Nawet jeśli `@key` nie jest używany, program Blazor zachowuje elementy podrzędne i wystąpienia składników tak jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="47dc6-271">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="47dc6-272">Jedyną zaletą korzystania z programu `@key` jest kontrola nad *sposobem* , w jaki wystąpienia modelu są mapowane na zachowane wystąpienia składników, zamiast algorytmu różnicowego, wybierając mapowanie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-272">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="47dc6-273">Wartości, które mają być używane dla \@ klucza</span><span class="sxs-lookup"><span data-stu-id="47dc6-273">What values to use for \@key</span></span>

<span data-ttu-id="47dc6-274">Ogólnie rzecz biorąc, warto podać jeden z następujących rodzajów wartości dla `@key` :</span><span class="sxs-lookup"><span data-stu-id="47dc6-274">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="47dc6-275">Wystąpienia obiektów modelu (na przykład `Person` wystąpienie takie jak w poprzednim przykładzie).</span><span class="sxs-lookup"><span data-stu-id="47dc6-275">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="47dc6-276">Zapewnia to zachowywanie na podstawie równości odwołań do obiektów.</span><span class="sxs-lookup"><span data-stu-id="47dc6-276">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="47dc6-277">Unikatowe identyfikatory (na przykład wartości klucza podstawowego typu `int` , `string` lub `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="47dc6-277">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="47dc6-278">Upewnij się, że wartości używane do `@key` nie kolidują.</span><span class="sxs-lookup"><span data-stu-id="47dc6-278">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="47dc6-279">Jeśli w tym samym elemencie nadrzędnym zostaną wykryte wartości powodujące konflikt, program Blazor zgłosi wyjątek, ponieważ nie może on w sposób jednoznaczny zmapować starych elementów lub składników na nowe elementy lub składniki.</span><span class="sxs-lookup"><span data-stu-id="47dc6-279">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="47dc6-280">Używaj tylko odrębnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-280">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="47dc6-281">Nie twórz składników, które zapisują do własnych właściwości parametrów</span><span class="sxs-lookup"><span data-stu-id="47dc6-281">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="47dc6-282">Parametry są zastępowane w następujących warunkach:</span><span class="sxs-lookup"><span data-stu-id="47dc6-282">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="47dc6-283">Zawartość składnika podrzędnego jest renderowana przy użyciu `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-283">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="47dc6-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest wywoływana w składniku nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="47dc6-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="47dc6-285">Parametry są resetowane, ponieważ składnik nadrzędny jest ponownie renderowany, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana, i nowe wartości parametrów są dostarczane do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-285">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="47dc6-286">Rozważmy następujący `Expander` składnik:</span><span class="sxs-lookup"><span data-stu-id="47dc6-286">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="47dc6-287">Renderuje zawartość podrzędną.</span><span class="sxs-lookup"><span data-stu-id="47dc6-287">Renders child content.</span></span>
* <span data-ttu-id="47dc6-288">Włącza lub wyłącza wyświetlanie zawartości podrzędnej za pomocą parametru składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-288">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="47dc6-289">`Expander`Składnik jest dodawany do składnika nadrzędnego, który może wywołać `StateHasChanged` :</span><span class="sxs-lookup"><span data-stu-id="47dc6-289">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="47dc6-290">Początkowo `Expander` składniki działają niezależnie, gdy ich `Expanded` właściwości są przełączane.</span><span class="sxs-lookup"><span data-stu-id="47dc6-290">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="47dc6-291">Składniki podrzędne utrzymują swoje Stany zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="47dc6-291">The child components maintain their states as expected.</span></span> <span data-ttu-id="47dc6-292">Gdy `StateHasChanged` jest wywoływana w obiekcie nadrzędnym, `Expanded` parametr pierwszego składnika podrzędnego jest resetowany z powrotem do jego wartości początkowej ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="47dc6-292">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="47dc6-293">Wartość drugiego `Expander` składnika `Expanded` nie zostanie zresetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.</span><span class="sxs-lookup"><span data-stu-id="47dc6-293">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="47dc6-294">Aby zachować stan w poprzednim scenariuszu, użyj *pola prywatnego* w `Expander` składniku, aby zachować stan przełączenia.</span><span class="sxs-lookup"><span data-stu-id="47dc6-294">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="47dc6-295">Następujący `Expander` składnik:</span><span class="sxs-lookup"><span data-stu-id="47dc6-295">The following `Expander` component:</span></span>

* <span data-ttu-id="47dc6-296">Akceptuje `Expanded` wartość parametru składnika z elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-296">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="47dc6-297">Przypisuje wartość parametru składnika do *pola prywatnego* ( `expanded` ) w [zdarzeniu OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="47dc6-297">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="47dc6-298">Używa prywatnego pola do utrzymania stanu wewnętrznego przełączania.</span><span class="sxs-lookup"><span data-stu-id="47dc6-298">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="partial-class-support"></a><span data-ttu-id="47dc6-299">Obsługa klasy częściowej</span><span class="sxs-lookup"><span data-stu-id="47dc6-299">Partial class support</span></span>

Razor<span data-ttu-id="47dc6-300">składniki są generowane jako klasy częściowe.</span><span class="sxs-lookup"><span data-stu-id="47dc6-300"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="47dc6-301">składniki są tworzone przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="47dc6-301"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="47dc6-302">Kod C# jest zdefiniowany w [`@code`](xref:mvc/views/razor#code) bloku z oznaczeniem HTML i Razor kodem w pojedynczym pliku.</span><span class="sxs-lookup"><span data-stu-id="47dc6-302">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="47dc6-303">Szablony definiują ich Razor składniki przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="47dc6-303"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="47dc6-304">Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako Klasa częściowa.</span><span class="sxs-lookup"><span data-stu-id="47dc6-304">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="47dc6-305">Poniższy przykład pokazuje `Counter` składnik domyślny z `@code` blokiem w aplikacji wygenerowanej na podstawie Blazor szablonu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-305">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="47dc6-306">Znaczniki HTML, Razor kod i kod C# są w tym samym pliku:</span><span class="sxs-lookup"><span data-stu-id="47dc6-306">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="47dc6-307">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-307">*Counter.razor*:</span></span>

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

<span data-ttu-id="47dc6-308">`Counter`Składnik można również utworzyć przy użyciu pliku związanego z kodem z klasą częściową:</span><span class="sxs-lookup"><span data-stu-id="47dc6-308">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="47dc6-309">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-309">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="47dc6-310">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-310">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="47dc6-311">W razie potrzeby dodaj wszystkie wymagane przestrzenie nazw do pliku klasy częściowej.</span><span class="sxs-lookup"><span data-stu-id="47dc6-311">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="47dc6-312">Typowe przestrzenie nazw używane przez Razor składniki obejmują:</span><span class="sxs-lookup"><span data-stu-id="47dc6-312">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="47dc6-313">Określ klasę bazową</span><span class="sxs-lookup"><span data-stu-id="47dc6-313">Specify a base class</span></span>

<span data-ttu-id="47dc6-314">[`@inherits`](xref:mvc/views/razor#inherits)Dyrektywa może służyć do określania klasy bazowej dla składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-314">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="47dc6-315">Poniższy przykład pokazuje, jak składnik może dziedziczyć klasę bazową, `BlazorRocksBase` , aby zapewnić właściwości i metody składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-315">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="47dc6-316">Klasa bazowa powinna pochodzić od `ComponentBase` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-316">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="47dc6-317">*Strony/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-317">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="47dc6-318">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-318">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="47dc6-319">Określ atrybut</span><span class="sxs-lookup"><span data-stu-id="47dc6-319">Specify an attribute</span></span>

<span data-ttu-id="47dc6-320">Atrybuty można określić w Razor składnikach [`@attribute`](xref:mvc/views/razor#attribute) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="47dc6-320">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="47dc6-321">Poniższy przykład stosuje `[Authorize]` atrybut do klasy składnika:</span><span class="sxs-lookup"><span data-stu-id="47dc6-321">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="47dc6-322">Importuj składniki</span><span class="sxs-lookup"><span data-stu-id="47dc6-322">Import components</span></span>

<span data-ttu-id="47dc6-323">Przestrzeń nazw składnika utworzona w programie Razor jest oparta na (w kolejności priorytetu):</span><span class="sxs-lookup"><span data-stu-id="47dc6-323">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="47dc6-324">[`@namespace`](xref:mvc/views/razor#namespace)oznaczenie w Razor pliku (*. Razor*) Markup ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="47dc6-324">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="47dc6-325">Projekt znajduje się `RootNamespace` w pliku projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="47dc6-325">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="47dc6-326">Nazwa projektu, pobrana z nazwy pliku projektu (*. csproj*) i ścieżka z katalogu głównego projektu do składnika.</span><span class="sxs-lookup"><span data-stu-id="47dc6-326">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="47dc6-327">Na przykład struktura rozpoznaje *{Project root}/Pages/index.Razor* (*BlazorSample. csproj*) do przestrzeni nazw `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-327">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="47dc6-328">Składniki przestrzegają reguł powiązań nazw języka C#.</span><span class="sxs-lookup"><span data-stu-id="47dc6-328">Components follow C# name binding rules.</span></span> <span data-ttu-id="47dc6-329">W przypadku `Index` składnika w tym przykładzie składniki należące do zakresu są wszystkich składników:</span><span class="sxs-lookup"><span data-stu-id="47dc6-329">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="47dc6-330">W tym samym folderze *strony*.</span><span class="sxs-lookup"><span data-stu-id="47dc6-330">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="47dc6-331">Składniki w katalogu głównym projektu, które nie określają jawnie innej przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="47dc6-331">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="47dc6-332">Składniki zdefiniowane w innej przestrzeni nazw są wprowadzane do zakresu za Razor pomocą [`@using`](xref:mvc/views/razor#using) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="47dc6-332">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="47dc6-333">Jeśli inny składnik, `NavMenu.razor` ,,, istnieje w *BlazorSample/Shared/* folder, można użyć w programie `Index.razor` z następującą `@using` instrukcją:</span><span class="sxs-lookup"><span data-stu-id="47dc6-333">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="47dc6-334">Do składników można także odwoływać się za pomocą ich w pełni kwalifikowanych nazw, które nie wymagają [`@using`](xref:mvc/views/razor#using) dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="47dc6-334">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="47dc6-335">`global::`Kwalifikacja nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="47dc6-335">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="47dc6-336">Importowanie składników przy użyciu instrukcji z aliasami `using` (np `@using Foo = Bar` .) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="47dc6-336">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="47dc6-337">Częściowo kwalifikowane nazwy nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="47dc6-337">Partially qualified names aren't supported.</span></span> <span data-ttu-id="47dc6-338">Na przykład dodawanie `@using BlazorSample` i odwoływanie `NavMenu.razor` się za pomocą `<Shared.NavMenu></Shared.NavMenu>` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="47dc6-338">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="47dc6-339">Warunkowe atrybuty elementu HTML</span><span class="sxs-lookup"><span data-stu-id="47dc6-339">Conditional HTML element attributes</span></span>

<span data-ttu-id="47dc6-340">Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET.</span><span class="sxs-lookup"><span data-stu-id="47dc6-340">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="47dc6-341">Jeśli wartość jest `false` lub `null` , atrybut nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="47dc6-341">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="47dc6-342">Jeśli wartość to `true` , atrybut jest renderowany jako zminimalizowany.</span><span class="sxs-lookup"><span data-stu-id="47dc6-342">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="47dc6-343">W poniższym przykładzie, `IsCompleted` określa, czy `checked` jest renderowany w znacznikach elementu:</span><span class="sxs-lookup"><span data-stu-id="47dc6-343">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="47dc6-344">Jeśli `IsCompleted` jest `true` , pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="47dc6-344">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="47dc6-345">Jeśli `IsCompleted` jest `false` , pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="47dc6-345">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="47dc6-346">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="47dc6-346">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="47dc6-347">Niektóre atrybuty HTML, takie jak [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nie działają prawidłowo, gdy typem .NET jest `bool` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-347">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="47dc6-348">W takich przypadkach należy użyć `string` typu zamiast `bool` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-348">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="47dc6-349">Nieprzetworzony kod HTML</span><span class="sxs-lookup"><span data-stu-id="47dc6-349">Raw HTML</span></span>

<span data-ttu-id="47dc6-350">Ciągi są zwykle renderowane przy użyciu węzłów tekstowych DOM, co oznacza, że wszystkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst literału.</span><span class="sxs-lookup"><span data-stu-id="47dc6-350">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="47dc6-351">Aby renderować nieprzetworzony kod HTML, zawiń zawartość HTML w `MarkupString` wartości.</span><span class="sxs-lookup"><span data-stu-id="47dc6-351">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="47dc6-352">Wartość jest analizowana jako plik HTML lub SVG i wstawiona do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="47dc6-352">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="47dc6-353">Renderowanie nieprzetworzonego kodu HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy je unikać!</span><span class="sxs-lookup"><span data-stu-id="47dc6-353">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="47dc6-354">W poniższym przykładzie pokazano, jak za pomocą `MarkupString` typu dodać blok statycznej zawartości HTML do renderowanego danych wyjściowych składnika:</span><span class="sxs-lookup"><span data-stu-id="47dc6-354">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="47dc6-355">Wartości kaskadowe i parametry</span><span class="sxs-lookup"><span data-stu-id="47dc6-355">Cascading values and parameters</span></span>

<span data-ttu-id="47dc6-356">W niektórych scenariuszach nie można przepływać danych z składnika nadrzędnego do składnika potomnego przy użyciu [parametrów składnika](#component-parameters), zwłaszcza gdy istnieje kilka warstw składników.</span><span class="sxs-lookup"><span data-stu-id="47dc6-356">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="47dc6-357">Wartości kaskadowe i parametry rozwiązują ten problem, zapewniając wygodną metodę dla składnika nadrzędnego, aby zapewnić wartość wszystkim jej składnikom potomnym.</span><span class="sxs-lookup"><span data-stu-id="47dc6-357">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="47dc6-358">Kaskadowe wartości i parametry również zapewniają podejście do współrzędnych składników.</span><span class="sxs-lookup"><span data-stu-id="47dc6-358">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="47dc6-359">Przykład motywu</span><span class="sxs-lookup"><span data-stu-id="47dc6-359">Theme example</span></span>

<span data-ttu-id="47dc6-360">W poniższym przykładzie z przykładowej aplikacji `ThemeInfo` Klasa określa informacje o motywie, aby przetworzyć hierarchię składników w taki sposób, aby wszystkie przyciski w danej części aplikacji miały ten sam styl.</span><span class="sxs-lookup"><span data-stu-id="47dc6-360">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="47dc6-361">*UIThemeClasses/themeinfo wskazuje. cs*:</span><span class="sxs-lookup"><span data-stu-id="47dc6-361">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="47dc6-362">Składnik nadrzędny może zapewnić kaskadową wartość przy użyciu składnika wartości kaskadowych.</span><span class="sxs-lookup"><span data-stu-id="47dc6-362">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="47dc6-363">`CascadingValue`Składnik otacza poddrzewo hierarchii składników i dostarcza jedną wartość do wszystkich składników w tym poddrzewie.</span><span class="sxs-lookup"><span data-stu-id="47dc6-363">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="47dc6-364">Przykładowo aplikacja Przykładowa określa informacje o motywie ( `ThemeInfo` ) w jednej z układów aplikacji jako parametr kaskadowy dla wszystkich składników, które tworzą treść układu `@Body` właściwości.</span><span class="sxs-lookup"><span data-stu-id="47dc6-364">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="47dc6-365">`ButtonClass`ma przypisaną wartość `btn-success` w składniku układu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-365">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="47dc6-366">Każdy składnik podrzędny może wykorzystać tę właściwość za pomocą `ThemeInfo` obiektu kaskadowego.</span><span class="sxs-lookup"><span data-stu-id="47dc6-366">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="47dc6-367">`CascadingValuesParametersLayout`składnika</span><span class="sxs-lookup"><span data-stu-id="47dc6-367">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="47dc6-368">Aby korzystać z wartości kaskadowych, składniki deklarują kaskadowe parametry przy użyciu `[CascadingParameter]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-368">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="47dc6-369">Wartości kaskadowe są powiązane z parametrami kaskadowymi według typu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-369">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="47dc6-370">W przykładowej aplikacji `CascadingValuesParametersTheme` składnik wiąże `ThemeInfo` wartość kaskadową z parametrem kaskadowym.</span><span class="sxs-lookup"><span data-stu-id="47dc6-370">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="47dc6-371">Parametr służy do ustawiania klasy CSS dla jednego z przycisków wyświetlanych przez składnik.</span><span class="sxs-lookup"><span data-stu-id="47dc6-371">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="47dc6-372">`CascadingValuesParametersTheme`składnika</span><span class="sxs-lookup"><span data-stu-id="47dc6-372">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="47dc6-373">Aby przetworzyć kaskadowo wiele wartości tego samego typu w ramach tego samego poddrzewa, podaj unikatowy `Name` ciąg dla każdego `CascadingValue` składnika i odpowiadający mu `CascadingParameter` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-373">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="47dc6-374">W poniższym przykładzie dwa `CascadingValue` składniki kaskaduje różne wystąpienia o `MyCascadingType` nazwie:</span><span class="sxs-lookup"><span data-stu-id="47dc6-374">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="47dc6-375">W składniku potomnym, kaskadowe parametry odbierają swoje wartości z odpowiednich kaskadowych wartości w składniku nadrzędnym według nazwy:</span><span class="sxs-lookup"><span data-stu-id="47dc6-375">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="47dc6-376">Przykład TabSet</span><span class="sxs-lookup"><span data-stu-id="47dc6-376">TabSet example</span></span>

<span data-ttu-id="47dc6-377">Parametry kaskadowe umożliwiają również współdziałanie składników w hierarchii składników.</span><span class="sxs-lookup"><span data-stu-id="47dc6-377">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="47dc6-378">Rozważmy na przykład następujący przykład *TabSet* w aplikacji przykładowej.</span><span class="sxs-lookup"><span data-stu-id="47dc6-378">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="47dc6-379">Przykładowa aplikacja ma `ITab` interfejs, który implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="47dc6-379">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="47dc6-380">`CascadingValuesParametersTabSet`Składnik używa `TabSet` składnika, który zawiera kilka `Tab` składników:</span><span class="sxs-lookup"><span data-stu-id="47dc6-380">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="47dc6-381">Składniki podrzędne `Tab` nie są jawnie przenoszone jako parametry do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-381">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="47dc6-382">Zamiast tego składniki podrzędne `Tab` są częścią zawartości elementu podrzędnego `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="47dc6-382">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="47dc6-383">Jednak `TabSet` nadal musi wiedzieć o każdym `Tab` składniku, aby można było renderować nagłówki i aktywną kartę. Aby umożliwić tę koordynację bez konieczności stosowania dodatkowego kodu, `TabSet` składnik *może stanowić wartość kaskadową* , która następnie jest wybierana przez `Tab` składniki potomne.</span><span class="sxs-lookup"><span data-stu-id="47dc6-383">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="47dc6-384">`TabSet`składnika</span><span class="sxs-lookup"><span data-stu-id="47dc6-384">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="47dc6-385">Składniki potomne `Tab` przechwytują zawierający `TabSet` jako parametr kaskadowy, więc `Tab` składniki dodają się do `TabSet` i koordynują, na której karcie jest aktywna.</span><span class="sxs-lookup"><span data-stu-id="47dc6-385">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="47dc6-386">`Tab`składnika</span><span class="sxs-lookup"><span data-stu-id="47dc6-386">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="47dc6-387">przystawki</span><span class="sxs-lookup"><span data-stu-id="47dc6-387"> templates</span></span>

<span data-ttu-id="47dc6-388">Fragmenty renderowania można definiować przy użyciu Razor składni szablonu.</span><span class="sxs-lookup"><span data-stu-id="47dc6-388">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="47dc6-389">Szablony są sposobem definiowania fragmentu interfejsu użytkownika i przyjmuje następujący format:</span><span class="sxs-lookup"><span data-stu-id="47dc6-389"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="47dc6-390">Poniższy przykład ilustruje sposób określania `RenderFragment` i `RenderFragment<T>` wartości oraz renderowania szablonów bezpośrednio w składniku.</span><span class="sxs-lookup"><span data-stu-id="47dc6-390">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="47dc6-391">Fragmenty renderowania mogą być również przekazane jako argumenty do [składników z szablonem](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="47dc6-391">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="47dc6-392">Renderowane dane wyjściowe poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="47dc6-392">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="47dc6-393">Skalowalne obrazy wektorowe (SVG)</span><span class="sxs-lookup"><span data-stu-id="47dc6-393">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="47dc6-394">Ponieważ Blazor renderuje HTML, obrazy obsługiwane przez przeglądarkę, w tym obrazy SVG (Scalable Vector Graphics *)*, są obsługiwane przez `<img>` tag:</span><span class="sxs-lookup"><span data-stu-id="47dc6-394">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="47dc6-395">Podobnie Obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów (*CSS*):</span><span class="sxs-lookup"><span data-stu-id="47dc6-395">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="47dc6-396">Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="47dc6-396">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="47dc6-397">Jeśli umieścisz `<svg>` tag bezpośrednio w pliku składnika (*. Razor*), podstawowe renderowanie obrazu jest obsługiwane, ale wiele scenariuszy zaawansowanych nie jest jeszcze obsługiwanych.</span><span class="sxs-lookup"><span data-stu-id="47dc6-397">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="47dc6-398">Na przykład `<use>` tagi nie są obecnie przestrzegane i `@bind` nie mogą być używane z niektórymi tagami SVG.</span><span class="sxs-lookup"><span data-stu-id="47dc6-398">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="47dc6-399">Oczekujemy, że te ograniczenia są opisane w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="47dc6-399">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47dc6-400">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="47dc6-400">Additional resources</span></span>

* <span data-ttu-id="47dc6-401"><xref:security/blazor/server/threat-mitigation>&ndash;Zawiera wskazówki dotyczące tworzenia Blazor Aplikacje serwera, które muszą będą konkurować o z wyczerpaniem zasobów.</span><span class="sxs-lookup"><span data-stu-id="47dc6-401"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
