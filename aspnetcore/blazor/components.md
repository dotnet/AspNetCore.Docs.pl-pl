---
title: Tworzenie i używanie ASP.NET podstawowych komponentów maszynki do golenia
author: guardrex
description: Dowiedz się, jak tworzyć składniki razor i używać ich, w tym jak wiązać się z danymi, obsługiwać zdarzenia i zarządzać cyklami życia komponentu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791487"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="fc15f-103">Tworzenie i używanie ASP.NET podstawowych komponentów maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="fc15f-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="fc15f-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="fc15f-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="fc15f-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc15f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="fc15f-106">aplikacje są tworzone przy użyciu *składników*.</span><span class="sxs-lookup"><span data-stu-id="fc15f-106"> apps are built using *components*.</span></span> <span data-ttu-id="fc15f-107">Składnik jest samodzielnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz.</span><span class="sxs-lookup"><span data-stu-id="fc15f-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="fc15f-108">Składnik zawiera znaczników HTML i logiki przetwarzania wymagane do wstrzykiwania danych lub reagowania na zdarzenia interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="fc15f-109">Komponenty są elastyczne i lekkie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="fc15f-110">Mogą być zagnieżdżone, ponownie i udostępniane między projektami.</span><span class="sxs-lookup"><span data-stu-id="fc15f-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="fc15f-111">Klasy komponentów</span><span class="sxs-lookup"><span data-stu-id="fc15f-111">Component classes</span></span>

<span data-ttu-id="fc15f-112">Składniki są implementowane w plikach komponentów [Razor](xref:mvc/views/razor) (*.brzytwa*) przy użyciu kombinacji znaczników C# i HTML.</span><span class="sxs-lookup"><span data-stu-id="fc15f-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="fc15f-113">Składnik w Blazor jest formalnie określany jako *składnik Razor*.</span><span class="sxs-lookup"><span data-stu-id="fc15f-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="fc15f-114">Nazwa komponentu musi zaczynać się od znaku wielkim.</span><span class="sxs-lookup"><span data-stu-id="fc15f-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="fc15f-115">Na przykład *MyCoolComponent.brzytwa* jest prawidłowa, a *myCoolComponent.brzytwa* jest nieprawidłowa.</span><span class="sxs-lookup"><span data-stu-id="fc15f-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="fc15f-116">Interfejs użytkownika składnika jest zdefiniowany przy użyciu kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="fc15f-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="fc15f-117">Dynamiczna logika renderowania (na przykład pętle, warunki, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="fc15f-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="fc15f-118">Podczas kompilowania aplikacji, znaczników HTML i logiki renderowania Języka C# są konwertowane na klasę składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="fc15f-119">Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="fc15f-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="fc15f-120">Elementy członkowskie klasy komponentu `@code` są definiowane w bloku.</span><span class="sxs-lookup"><span data-stu-id="fc15f-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="fc15f-121">W `@code` bloku stan składnika (właściwości, pola) jest określony za pomocą metod obsługi zdarzeń lub definiowania innej logiki składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="fc15f-122">Dopuszczalne `@code` jest więcej niż jeden blok.</span><span class="sxs-lookup"><span data-stu-id="fc15f-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="fc15f-123">Elementy członkowskie składnika mogą być używane jako część logiki renderowania `@`składnika przy użyciu wyrażeń Języka C#, które zaczynają się od .</span><span class="sxs-lookup"><span data-stu-id="fc15f-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="fc15f-124">Na przykład pole C# jest renderowane `@` przez prefiks do nazwy pola.</span><span class="sxs-lookup"><span data-stu-id="fc15f-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="fc15f-125">Poniższy przykład ocenia i renderuje:</span><span class="sxs-lookup"><span data-stu-id="fc15f-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="fc15f-126">`_headingFontStyle`do wartości właściwości CSS dla `font-style`.</span><span class="sxs-lookup"><span data-stu-id="fc15f-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="fc15f-127">`_headingText`do treści `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="fc15f-128">Po składnik jest początkowo renderowane, składnik ponownie generuje jego drzewa renderowania w odpowiedzi na zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="fc15f-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="fc15f-129">następnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje modelu obiektu dokumentu przeglądarki (DOM).</span><span class="sxs-lookup"><span data-stu-id="fc15f-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="fc15f-130">Składniki są zwykłe klasy C# i mogą być umieszczane w dowolnym miejscu w ramach projektu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="fc15f-131">Składniki, które tworzą strony sieci Web, zwykle znajdują się w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="fc15f-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="fc15f-132">Składniki niebędące stronicowymi są często umieszczane w folderze *udostępnionym* lub folderze niestandardowym dodawanym do projektu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="fc15f-133">Zazwyczaj obszar nazw składnika pochodzi z głównego obszaru nazw aplikacji i lokalizacji (folderu) składnika w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="fc15f-134">Jeśli główny obszar nazw aplikacji `BlazorApp` jest `Counter` i składnik znajduje się w folderze *Strony:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="fc15f-135">Obszar `Counter` nazw składnika `BlazorApp.Pages`jest .</span><span class="sxs-lookup"><span data-stu-id="fc15f-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="fc15f-136">W pełni kwalifikowana nazwa `BlazorApp.Pages.Counter`typu komponentu to .</span><span class="sxs-lookup"><span data-stu-id="fc15f-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="fc15f-137">Aby uzyskać więcej informacji, zobacz sekcję [Importowanie składników.](#import-components)</span><span class="sxs-lookup"><span data-stu-id="fc15f-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="fc15f-138">Aby użyć folderu niestandardowego, dodaj obszar nazw folderu niestandardowego do składnika nadrzędnego lub do pliku *_Imports.brzytwa* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="fc15f-139">Na przykład następujący obszar nazw udostępnia składniki w folderze *Składniki,* gdy `BlazorApp`głównym obszarem nazw aplikacji jest:</span><span class="sxs-lookup"><span data-stu-id="fc15f-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="fc15f-140">Aktywa statyczne</span><span class="sxs-lookup"><span data-stu-id="fc15f-140">Static assets</span></span>

Blazor<span data-ttu-id="fc15f-141">następuje konwencja ASP.NET Aplikacje Core umieszczanie zasobów statycznych w [folderze głównego internetowego projektu (wwwroot).](xref:fundamentals/index#web-root)</span><span class="sxs-lookup"><span data-stu-id="fc15f-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="fc15f-142">Użyj ścieżki względnej`/`podstawowej ( ), aby odwołać się do katalogu głównego sieci Web dla zasobu statycznego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="fc15f-143">W poniższym przykładzie *logo.png* jest fizycznie zlokalizowane w folderze *{PROJECT ROOT}/wwwroot/images:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="fc15f-144">Komponenty brzytwy **nie** obsługują notacji tyldy ().`~/`</span><span class="sxs-lookup"><span data-stu-id="fc15f-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="fc15f-145">Aby uzyskać informacje na temat ustawiania <xref:host-and-deploy/blazor/index#app-base-path>ścieżki podstawowej aplikacji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="fc15f-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="fc15f-146">Pomocników tagów nie są obsługiwane w składnikach</span><span class="sxs-lookup"><span data-stu-id="fc15f-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="fc15f-147">[Pomocników tagów](xref:mvc/views/tag-helpers/intro) nie są obsługiwane w składnikach Razor (pliki *.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="fc15f-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="fc15f-148">Aby zapewnić funkcję podobną do Blazorpomocnika znaczników w programie , utwórz składnik o takiej samej funkcjonalności jak Pomocnik znaczników i zamiast tego użyj składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="fc15f-149">Użyj komponentów</span><span class="sxs-lookup"><span data-stu-id="fc15f-149">Use components</span></span>

<span data-ttu-id="fc15f-150">Składniki mogą zawierać inne składniki, deklarując je przy użyciu składni elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="fc15f-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="fc15f-151">Znaczników przy użyciu składnika wygląda tag HTML, gdzie nazwa znacznika jest typem składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="fc15f-152">Następujące znaczniki w *index.brzytwa* `HeadingComponent` renderuje wystąpienie:</span><span class="sxs-lookup"><span data-stu-id="fc15f-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="fc15f-153">*Składniki/HeadingComponent.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="fc15f-154">Jeśli składnik zawiera element HTML z wielką literą pierwszej litery, która nie pasuje do nazwy składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę.</span><span class="sxs-lookup"><span data-stu-id="fc15f-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="fc15f-155">Dodanie `@using` dyrektywy dla obszaru nazw składnika udostępnia składnik, który rozwiązuje ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="fc15f-156">Routing</span><span class="sxs-lookup"><span data-stu-id="fc15f-156">Routing</span></span>

<span data-ttu-id="fc15f-157">Routing Blazor w uzyskuje się przez zapewnienie szablonu trasy do każdego składnika dostępne w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="fc15f-158">Po skompilowaniu `@page` pliku Razor z dyrektywą, <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> wygenerowana klasa otrzymuje określający szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="fc15f-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="fc15f-159">W czasie wykonywania router wyszukuje `RouteAttribute` klasy składników z i renderuje, który składnik ma szablon trasy, który pasuje do żądanego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="fc15f-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="fc15f-160">Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="fc15f-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="fc15f-161">Parametry</span><span class="sxs-lookup"><span data-stu-id="fc15f-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="fc15f-162">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="fc15f-162">Route parameters</span></span>

<span data-ttu-id="fc15f-163">Składniki mogą odbierać parametry trasy z `@page` szablonu trasy przedstawionego w dyrektywie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="fc15f-164">Router używa parametrów trasy do wypełniania odpowiednich parametrów komponentu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="fc15f-165">*Strony/RouteParameter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="fc15f-166">Parametry opcjonalne nie są obsługiwane, więc dwie `@page` dyrektywy są stosowane w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="fc15f-167">Pierwszy umożliwia nawigację do składnika bez parametru.</span><span class="sxs-lookup"><span data-stu-id="fc15f-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="fc15f-168">Druga `@page` dyrektywa odbiera `{text}` parametr trasy i przypisuje `Text` wartość do właściwości.</span><span class="sxs-lookup"><span data-stu-id="fc15f-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="fc15f-169">Składnia parametrów *Catch-all* (`*`/`**`), która przechwytuje ścieżkę przez wiele granic folderów, **nie** jest obsługiwana w składnikach Razor (*.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="fc15f-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="fc15f-170">Parametry komponentu</span><span class="sxs-lookup"><span data-stu-id="fc15f-170">Component parameters</span></span>

<span data-ttu-id="fc15f-171">Komponenty mogą mieć *parametry komponentu,* które są definiowane `[Parameter]` przy użyciu właściwości publicznych w klasie składnika z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="fc15f-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="fc15f-172">Atrybuty służy do określania argumentów dla składnika w znacznikach.</span><span class="sxs-lookup"><span data-stu-id="fc15f-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="fc15f-173">*Składniki/ChildComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="fc15f-174">W poniższym przykładzie z `ParentComponent` przykładowej aplikacji `Title` ustawia wartość `ChildComponent`właściwości . .</span><span class="sxs-lookup"><span data-stu-id="fc15f-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="fc15f-175">*Strony/ParentComponent.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="fc15f-176">Nie należy tworzyć komponentów, które zapisują do własnych *parametrów składnika,* zamiast tego należy użyć pola prywatnego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="fc15f-177">Aby uzyskać więcej informacji, zobacz [Nie należy tworzyć składników, które zapisują do własnych właściwości parametrów](#dont-create-components-that-write-to-their-own-parameter-properties) sekcji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="fc15f-178">Zawartość podrzędna</span><span class="sxs-lookup"><span data-stu-id="fc15f-178">Child content</span></span>

<span data-ttu-id="fc15f-179">Składniki można ustawić zawartość innego składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-179">Components can set the content of another component.</span></span> <span data-ttu-id="fc15f-180">Składnik przypisujący udostępnia zawartość między znacznikami określającymi składnik odbiorczy.</span><span class="sxs-lookup"><span data-stu-id="fc15f-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="fc15f-181">W poniższym przykładzie `ChildComponent` `ChildContent` ma właściwość, która reprezentuje `RenderFragment`, który reprezentuje segment interfejsu użytkownika do renderowania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="fc15f-182">Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana.</span><span class="sxs-lookup"><span data-stu-id="fc15f-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="fc15f-183">Wartość `ChildContent` jest odbierana z komponentu nadrzędnego i renderowana `panel-body`wewnątrz panelu Bootstrap .</span><span class="sxs-lookup"><span data-stu-id="fc15f-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="fc15f-184">*Składniki/ChildComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="fc15f-185">Właściwość odbierająca `RenderFragment` zawartość musi `ChildContent` być nazwany przez konwencję.</span><span class="sxs-lookup"><span data-stu-id="fc15f-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="fc15f-186">W `ParentComponent` przykładowej aplikacji można zapewnić zawartość `ChildComponent` do renderowania `<ChildComponent>` przez umieszczenie zawartości wewnątrz tagów.</span><span class="sxs-lookup"><span data-stu-id="fc15f-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="fc15f-187">*Strony/ParentComponent.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="fc15f-188">Podział atrybutów i dowolne parametry</span><span class="sxs-lookup"><span data-stu-id="fc15f-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="fc15f-189">Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="fc15f-190">Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy użyciu [`@attributes`](xref:mvc/views/razor#attributes) dyrektywy Razor.</span><span class="sxs-lookup"><span data-stu-id="fc15f-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="fc15f-191">Ten scenariusz jest przydatny podczas definiowania składnika, który tworzy element znaczników, który obsługuje różne dostosowania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="fc15f-192">Na przykład może być żmudne definiowanie `<input>` atrybutów oddzielnie dla, który obsługuje wiele parametrów.</span><span class="sxs-lookup"><span data-stu-id="fc15f-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="fc15f-193">W poniższym przykładzie `<input>` pierwszy`id="useIndividualParams"`element ( ) używa poszczególnych parametrów komponentu, podczas gdy drugi `<input>` element (`id="useAttributesDict"`) używa splatting atrybutu:</span><span class="sxs-lookup"><span data-stu-id="fc15f-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="fc15f-194">Typ parametru musi `IEnumerable<KeyValuePair<string, object>>` zostać zaimplementowany za pomocą kluczy ciągów.</span><span class="sxs-lookup"><span data-stu-id="fc15f-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="fc15f-195">Korzystanie `IReadOnlyDictionary<string, object>` jest również opcją w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="fc15f-196">Renderowane `<input>` elementy przy użyciu obu podejść są identyczne:</span><span class="sxs-lookup"><span data-stu-id="fc15f-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="fc15f-197">Aby zaakceptować dowolne atrybuty, `[Parameter]` należy zdefiniować parametr `true`komponentu przy użyciu atrybutu z właściwością ustawioną na: `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="fc15f-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="fc15f-198">Właściwość `CaptureUnmatchedValues` `[Parameter]` na umożliwia parametr dopasować wszystkie atrybuty, które nie pasują do innego parametru.</span><span class="sxs-lookup"><span data-stu-id="fc15f-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="fc15f-199">Komponent może definiować tylko `CaptureUnmatchedValues`jeden parametr za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="fc15f-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="fc15f-200">Typ właściwości używany `CaptureUnmatchedValues` z musi `Dictionary<string, object>` być przypisany z kluczami ciągu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="fc15f-201">`IEnumerable<KeyValuePair<string, object>>`lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="fc15f-202">Pozycja `@attributes` względem pozycji atrybutów elementu jest ważna.</span><span class="sxs-lookup"><span data-stu-id="fc15f-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="fc15f-203">Gdy `@attributes` są splatted na element, atrybuty są przetwarzane od prawej do lewej (ostatnio do pierwszego).</span><span class="sxs-lookup"><span data-stu-id="fc15f-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="fc15f-204">Rozważmy następujący przykład składnika, `Child` który zużywa składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="fc15f-205">*ParentComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="fc15f-206">*ChildComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="fc15f-207">Atrybut `Child` składnika `extra` jest ustawiony na prawo `@attributes`od .</span><span class="sxs-lookup"><span data-stu-id="fc15f-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="fc15f-208">Renderowany `Parent` `<div>` składnik zawiera `extra="5"` po przejściu przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od początku do pierwszego):</span><span class="sxs-lookup"><span data-stu-id="fc15f-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="fc15f-209">W poniższym przykładzie `extra` kolejność `@attributes` i jest `Child` odwrócona `<div>`w komponencie:</span><span class="sxs-lookup"><span data-stu-id="fc15f-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="fc15f-210">*ParentComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="fc15f-211">*ChildComponent.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="fc15f-212">Renderowane `<div>` w `Parent` składniku `extra="10"` zawiera, gdy przekazywane przez dodatkowy atrybut:</span><span class="sxs-lookup"><span data-stu-id="fc15f-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="fc15f-213">Przechwytywanie odwołań do komponentów</span><span class="sxs-lookup"><span data-stu-id="fc15f-213">Capture references to components</span></span>

<span data-ttu-id="fc15f-214">Odwołania do składników umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu `Show` `Reset`można wydawać polecenia do tego wystąpienia, takie jak lub .</span><span class="sxs-lookup"><span data-stu-id="fc15f-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="fc15f-215">Aby przechwycić odwołanie do komponentu:</span><span class="sxs-lookup"><span data-stu-id="fc15f-215">To capture a component reference:</span></span>

* <span data-ttu-id="fc15f-216">Dodaj [`@ref`](xref:mvc/views/razor#ref) atrybut do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="fc15f-217">Zdefiniuj pole o tym samym typie co komponent podrzędny.</span><span class="sxs-lookup"><span data-stu-id="fc15f-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="fc15f-218">Podczas renderowania komponentu `_loginDialog` pole jest wypełniane wystąpieniem komponentu podrzędnego. `MyLoginDialog`</span><span class="sxs-lookup"><span data-stu-id="fc15f-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="fc15f-219">Następnie można wywołać metody .NET w wystąpieniu składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fc15f-220">Zmienna `_loginDialog` jest wypełniana tylko po renderowaniu składnika, a jej dane wyjściowe zawierają `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="fc15f-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="fc15f-221">Do tego momentu nie ma się czego odwołać.</span><span class="sxs-lookup"><span data-stu-id="fc15f-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="fc15f-222">Aby manipulować odwołaniami do komponentów po zakończeniu renderowania przez komponent, należy użyć [metod OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="fc15f-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="fc15f-223">Aby odwoływać się do komponentów w pętli, zobacz [Przechwytywanie odwołań do wielu podobnych składników podrzędnych (#13358 dotnet/aspnetcore).](https://github.com/dotnet/aspnetcore/issues/13358)</span><span class="sxs-lookup"><span data-stu-id="fc15f-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="fc15f-224">Podczas przechwytywania odwołań do składników użyć podobnej składni do [przechwytywania odwołań do elementów,](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest to funkcja interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fc15f-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="fc15f-225">Odwołania do składników nie są przekazywane&mdash;do kodu JavaScript, który jest używany tylko w kodzie .NET.</span><span class="sxs-lookup"><span data-stu-id="fc15f-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="fc15f-226">**Nie** należy używać odwołań do komponentów do mutować stan komponentów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="fc15f-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="fc15f-227">Zamiast tego należy użyć normalnych parametrów deklaratywnych do przekazywania danych do składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="fc15f-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="fc15f-228">Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie rimanują w prawidłowych godzinach.</span><span class="sxs-lookup"><span data-stu-id="fc15f-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="fc15f-229">Wywoływanie metod składników zewnętrznie w celu aktualizacji stanu</span><span class="sxs-lookup"><span data-stu-id="fc15f-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="fc15f-230">używa `SynchronizationContext` do wymuszania pojedynczego wątku logicznego wykonania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="fc15f-231">[Metody cyklu życia](xref:blazor/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane przez Blazor są wykonywane na ten `SynchronizationContext`temat .</span><span class="sxs-lookup"><span data-stu-id="fc15f-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="fc15f-232">W przypadku, gdy składnik musi zostać zaktualizowany na podstawie zdarzenia zewnętrznego, takiego `InvokeAsync` jak czasomierz Blazorlub `SynchronizationContext`inne powiadomienia, użyj metody, która zostanie wysłana do 's .</span><span class="sxs-lookup"><span data-stu-id="fc15f-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="fc15f-233">Rozważmy na przykład *usługę powiadamiania,* która może powiadamiać dowolny składnik nasłuchiwania o zaktualizowanym stanie:</span><span class="sxs-lookup"><span data-stu-id="fc15f-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="fc15f-234">Zarejestruj `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="fc15f-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="fc15f-235">W Blazor WebAssembly zarejestruj usługę `Program.Main`w:</span><span class="sxs-lookup"><span data-stu-id="fc15f-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="fc15f-236">W Blazor systemie Server zarejestruj `Startup.ConfigureServices`usługę w:</span><span class="sxs-lookup"><span data-stu-id="fc15f-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="fc15f-237">Użyj, `NotifierService` aby zaktualizować składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-237">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="fc15f-238">W poprzednim przykładzie `NotifierService` wywołuje metodę składnika `OnNotify` poza Blazor's `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="fc15f-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="fc15f-239">`InvokeAsync`służy do przełączania się do prawidłowego kontekstu i kolejkowania renderowania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="fc15f-240">Użyj \@klawisza do kontrolowania zachowania elementów i komponentów</span><span class="sxs-lookup"><span data-stu-id="fc15f-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="fc15f-241">Podczas renderowania listy elementów lub składników i elementy Blazorlub składniki następnie zmienić, 's diffing algorytm musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny mapować do nich.</span><span class="sxs-lookup"><span data-stu-id="fc15f-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="fc15f-242">Zwykle ten proces jest automatyczny i może być ignorowany, ale są przypadki, w których można kontrolować proces.</span><span class="sxs-lookup"><span data-stu-id="fc15f-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="fc15f-243">Rozważmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="fc15f-243">Consider the following example:</span></span>

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

<span data-ttu-id="fc15f-244">Zawartość `People` kolekcji może ulec zmianie za pomocą wpisów wstawionych, usuniętych lub ponownie uporządkowanych.</span><span class="sxs-lookup"><span data-stu-id="fc15f-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="fc15f-245">Gdy składnik rerenders, `<DetailsEditor>` składnik może ulec `Details` zmianie, aby otrzymać różne wartości parametrów.</span><span class="sxs-lookup"><span data-stu-id="fc15f-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="fc15f-246">Może to spowodować bardziej złożone rerendering niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="fc15f-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="fc15f-247">W niektórych przypadkach rerendering może prowadzić do widocznych różnic w zachowaniu, takich jak utrata fokusu elementu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="fc15f-248">Proces mapowania można kontrolować [`@key`](xref:mvc/views/razor#key) za pomocą atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="fc15f-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="fc15f-249">`@key`powoduje, że algorytm diffing gwarantuje zachowanie elementów lub komponentów w oparciu o wartość klucza:</span><span class="sxs-lookup"><span data-stu-id="fc15f-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="fc15f-250">Po `People` zmianie kolekcji algorytm diffing zachowuje `<DetailsEditor>` skojarzenie `person` między wystąpieniami i wystąpieniami:</span><span class="sxs-lookup"><span data-stu-id="fc15f-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="fc15f-251">Jeśli `Person` a zostanie `People` usunięty z listy, tylko odpowiednie `<DetailsEditor>` wystąpienie jest usuwany z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="fc15f-252">Inne wystąpienia pozostają niezmienione.</span><span class="sxs-lookup"><span data-stu-id="fc15f-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="fc15f-253">Jeśli `Person` a jest wstawiany w jakiejś pozycji na liście, jedno nowe `<DetailsEditor>` wystąpienie jest wstawiany w tym odpowiednim położeniu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="fc15f-254">Inne wystąpienia pozostają niezmienione.</span><span class="sxs-lookup"><span data-stu-id="fc15f-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="fc15f-255">Jeśli `Person` wpisy są ponownie uporządkowane, odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i ponownie uporządkowane w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="fc15f-256">W niektórych scenariuszach `@key` użycie minimalizuje złożoność rerendering i pozwala uniknąć potencjalnych problemów ze stanowymi częściami dom zmiany, takich jak położenie fokusu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fc15f-257">Klucze są lokalne dla każdego elementu kontenera lub składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="fc15f-258">Klucze nie są porównywane globalnie w całym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="fc15f-259">Kiedy używać \@klucza</span><span class="sxs-lookup"><span data-stu-id="fc15f-259">When to use \@key</span></span>

<span data-ttu-id="fc15f-260">Zazwyczaj warto używać za `@key` każdym razem, gdy lista jest renderowana `@foreach` (na przykład w bloku) `@key`i istnieje odpowiednia wartość do zdefiniowania programu .</span><span class="sxs-lookup"><span data-stu-id="fc15f-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="fc15f-261">Można również `@key` użyć, Blazor aby zapobiec zachowaniu elementu lub poddrzewa komponentu, gdy zmienia się obiekt:</span><span class="sxs-lookup"><span data-stu-id="fc15f-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="fc15f-262">Jeśli `@currentPerson` zmiany, `@key` dyrektywa atrybut Blazor wymusza `<div>` odrzucić cały i jego elementy podrzędne i odbudować poddrzewo w interfejsie użytkownika z nowych elementów i składników.</span><span class="sxs-lookup"><span data-stu-id="fc15f-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="fc15f-263">Może to być przydatne, jeśli trzeba zagwarantować, że `@currentPerson` żaden stan interfejsu użytkownika jest zachowywany po zmianie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="fc15f-264">Kiedy nie \@używać klucza</span><span class="sxs-lookup"><span data-stu-id="fc15f-264">When not to use \@key</span></span>

<span data-ttu-id="fc15f-265">Jest koszt wydajności podczas diffing `@key`z .</span><span class="sxs-lookup"><span data-stu-id="fc15f-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="fc15f-266">Koszt wydajności nie jest duży, `@key` ale należy określić tylko wtedy, gdy kontrolowanie zasad zachowania elementu lub składnika jest korzystne dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="fc15f-267">Nawet `@key` jeśli nie jest Blazor używany, zachowuje element podrzędny i wystąpienia składników jak najwięcej.</span><span class="sxs-lookup"><span data-stu-id="fc15f-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="fc15f-268">Jedyną zaletą `@key` przy użyciu jest kontrola nad *tym, jak* wystąpienia modelu są mapowane do zachowanych wystąpień składników, zamiast algorytmu diffing wybierając mapowanie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="fc15f-269">Jakie wartości mają \@być używane dla klucza</span><span class="sxs-lookup"><span data-stu-id="fc15f-269">What values to use for \@key</span></span>

<span data-ttu-id="fc15f-270">Ogólnie rzecz biorąc, ma sens dostarczanie jednego z `@key`następujących rodzajów wartości dla:</span><span class="sxs-lookup"><span data-stu-id="fc15f-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="fc15f-271">Wystąpienia obiektu modelu (na `Person` przykład wystąpienie, jak we wcześniejszym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="fc15f-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="fc15f-272">Zapewnia to zachowanie na podstawie równości odwołania do obiektu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="fc15f-273">Unikatowe identyfikatory (na przykład wartości `int` `string`klucza `Guid`podstawowego typu , lub ).</span><span class="sxs-lookup"><span data-stu-id="fc15f-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="fc15f-274">Upewnij się, `@key` że wartości używane dla nie kolidują ze sobą.</span><span class="sxs-lookup"><span data-stu-id="fc15f-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="fc15f-275">Jeśli wartości kolizji zostaną wykryte w Blazor tym samym elemencie nadrzędnym, zgłasza wyjątek, ponieważ nie można deterministycznie mapować stare elementy lub składniki do nowych elementów lub składników.</span><span class="sxs-lookup"><span data-stu-id="fc15f-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="fc15f-276">Używaj tylko różnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="fc15f-277">Nie należy tworzyć składników, które zapisują do własnych właściwości parametrów</span><span class="sxs-lookup"><span data-stu-id="fc15f-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="fc15f-278">Parametry są zastępowane pod następującymi warunkami:</span><span class="sxs-lookup"><span data-stu-id="fc15f-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="fc15f-279">Zawartość składnika podrzędnego jest renderowana za pomocą pliku `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="fc15f-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="fc15f-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest wywoływana w komponencie nadrzędnym.</span><span class="sxs-lookup"><span data-stu-id="fc15f-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="fc15f-281">Parametry są resetowane, ponieważ składnik <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> nadrzędny rerenders, gdy jest wywoływana i nowe wartości parametrów są dostarczane do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="fc15f-282">Należy wziąć `Expander` pod uwagę następujący składnik, który:</span><span class="sxs-lookup"><span data-stu-id="fc15f-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="fc15f-283">Renderuje zawartość podrzędną.</span><span class="sxs-lookup"><span data-stu-id="fc15f-283">Renders child content.</span></span>
* <span data-ttu-id="fc15f-284">Przełączniki przedstawiające zawartość podrzędną z parametrem komponentu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-284">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="fc15f-285">Komponent `Expander` jest dodawany do składnika `StateHasChanged`nadrzędnego, który może wywołać:</span><span class="sxs-lookup"><span data-stu-id="fc15f-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="fc15f-286">Początkowo `Expander` składniki zachowują się niezależnie, gdy ich `Expanded` właściwości są przełączane.</span><span class="sxs-lookup"><span data-stu-id="fc15f-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="fc15f-287">Składniki podrzędne zachowują swoje stany zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="fc15f-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="fc15f-288">Po `StateHasChanged` wywołaniu w chyłce `Expanded` parametr pierwszego komponentu podrzędnego`true`jest resetowany z powrotem do jego wartości początkowej ( ).</span><span class="sxs-lookup"><span data-stu-id="fc15f-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="fc15f-289">Wartość `Expander` drugiego składnika `Expanded` nie jest resetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.</span><span class="sxs-lookup"><span data-stu-id="fc15f-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="fc15f-290">Aby zachować stan w poprzednim scenariuszu, `Expander` należy użyć pola *prywatnego* w składniku, aby zachować jego stan przełączania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="fc15f-291">Następujący `Expander` składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-291">The following `Expander` component:</span></span>

* <span data-ttu-id="fc15f-292">Akceptuje wartość `Expanded` parametru składnika z elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="fc15f-293">Przypisuje wartość parametru komponentu do`_expanded`pola *prywatnego* ( ) w [zdarzeniu OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="fc15f-293">Assigns the component parameter value to a *private field* (`_expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="fc15f-294">Używa pola prywatnego do zachowania jego wewnętrznego stanu przełączania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="fc15f-295">Obsługa częściowej klasy</span><span class="sxs-lookup"><span data-stu-id="fc15f-295">Partial class support</span></span>

<span data-ttu-id="fc15f-296">Komponenty maszynki do golenia są generowane jako klasy częściowe.</span><span class="sxs-lookup"><span data-stu-id="fc15f-296">Razor components are generated as partial classes.</span></span> <span data-ttu-id="fc15f-297">Komponenty maszynki do golenia są tworzyć przy użyciu jednego z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="fc15f-297">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="fc15f-298">Kod C# jest [`@code`](xref:mvc/views/razor#code) zdefiniowany w bloku z znaczników HTML i kod Razor w jednym pliku.</span><span class="sxs-lookup"><span data-stu-id="fc15f-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="fc15f-299">szablony definiują swoje składniki Razor przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="fc15f-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="fc15f-300">Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako klasa częściowa.</span><span class="sxs-lookup"><span data-stu-id="fc15f-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="fc15f-301">W poniższym przykładzie pokazano domyślny `Counter` składnik z blokiem `@code` w aplikacji wygenerowanej na podstawie szablonu. Blazor</span><span class="sxs-lookup"><span data-stu-id="fc15f-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="fc15f-302">Znaczniki HTML, kod Razor i kod C# znajdują się w tym samym pliku:</span><span class="sxs-lookup"><span data-stu-id="fc15f-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="fc15f-303">*Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-303">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="fc15f-304">Składnik `Counter` można również utworzyć przy użyciu pliku posuwu kodu z klasą częściową:</span><span class="sxs-lookup"><span data-stu-id="fc15f-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="fc15f-305">*Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="fc15f-306">*Counter.razor.cs:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-306">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="fc15f-307">W razie potrzeby dodaj wszystkie wymagane obszary nazw do pliku klasy częściowej.</span><span class="sxs-lookup"><span data-stu-id="fc15f-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="fc15f-308">Typowe przestrzenie nazw używane przez składniki Razor obejmują:</span><span class="sxs-lookup"><span data-stu-id="fc15f-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="fc15f-309">Określanie klasy podstawowej</span><span class="sxs-lookup"><span data-stu-id="fc15f-309">Specify a base class</span></span>

<span data-ttu-id="fc15f-310">Dyrektywa [`@inherits`](xref:mvc/views/razor#inherits) może służyć do określania klasy podstawowej dla składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="fc15f-311">Poniższy przykład pokazuje, jak składnik może `BlazorRocksBase`dziedziczyć klasę podstawową, aby zapewnić właściwości i metody składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="fc15f-312">Klasa podstawowa powinna `ComponentBase`pochodzić od .</span><span class="sxs-lookup"><span data-stu-id="fc15f-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="fc15f-313">*Strony/BlazorRocks.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="fc15f-314">*BlazorRocksBase.cs:*</span><span class="sxs-lookup"><span data-stu-id="fc15f-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="fc15f-315">Określanie atrybutu</span><span class="sxs-lookup"><span data-stu-id="fc15f-315">Specify an attribute</span></span>

<span data-ttu-id="fc15f-316">Atrybuty można określić w [`@attribute`](xref:mvc/views/razor#attribute) komponentach Razor z dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="fc15f-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="fc15f-317">Poniższy przykład stosuje `[Authorize]` atrybut do klasy składnika:</span><span class="sxs-lookup"><span data-stu-id="fc15f-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="fc15f-318">Importowanie składników</span><span class="sxs-lookup"><span data-stu-id="fc15f-318">Import components</span></span>

<span data-ttu-id="fc15f-319">Obszar nazw składnika chowanego za pomocą razora jest oparty na (w kolejności priorytetu):</span><span class="sxs-lookup"><span data-stu-id="fc15f-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="fc15f-320">[`@namespace`](xref:mvc/views/razor#namespace)oznaczenie w pliku Razor (*.brzytwa*) znaczników (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="fc15f-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="fc15f-321">Projekt jest `RootNamespace` w pliku projektu`<RootNamespace>BlazorSample</RootNamespace>`( ).</span><span class="sxs-lookup"><span data-stu-id="fc15f-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="fc15f-322">Nazwa projektu, zaczerpnięta z nazwy pliku projektu (*csproj*) i ścieżka z katalogu głównego projektu do składnika.</span><span class="sxs-lookup"><span data-stu-id="fc15f-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="fc15f-323">Na przykład struktura rozwiązuje *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj* `BlazorSample.Pages`) do obszaru nazw .</span><span class="sxs-lookup"><span data-stu-id="fc15f-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="fc15f-324">Składniki są zgodne z regułami wiązania nazw języka C#.</span><span class="sxs-lookup"><span data-stu-id="fc15f-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="fc15f-325">Dla `Index` składnika w tym przykładzie składniki w zakresie są wszystkie składniki:</span><span class="sxs-lookup"><span data-stu-id="fc15f-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="fc15f-326">W tym samym folderze *strony*.</span><span class="sxs-lookup"><span data-stu-id="fc15f-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="fc15f-327">Składniki w katalogu głównym projektu, które jawnie nie określają innego obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="fc15f-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="fc15f-328">Składniki zdefiniowane w innej przestrzeni nazw są wprowadzane [`@using`](xref:mvc/views/razor#using) do zakresu przy użyciu dyrektywy Razor.</span><span class="sxs-lookup"><span data-stu-id="fc15f-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="fc15f-329">Jeśli inny `NavMenu.razor`składnik, istnieje w *folderze BlazorSample/Shared/,* składnik `Index.razor` może `@using` być użyty z następującą instrukcją:</span><span class="sxs-lookup"><span data-stu-id="fc15f-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="fc15f-330">Składniki można również odwoływać się przy użyciu ich w [`@using`](xref:mvc/views/razor#using) pełni kwalifikowanych nazw, które nie wymagają dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="fc15f-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="fc15f-331">Kwalifikacje `global::` nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fc15f-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="fc15f-332">Importowanie składników z `using` aliasami instrukcji `@using Foo = Bar`(na przykład) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fc15f-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="fc15f-333">Częściowo kwalifikowane nazwy nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fc15f-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="fc15f-334">Na przykład `@using BlazorSample` dodawanie i `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` odwoływanie się do nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="fc15f-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="fc15f-335">Warunkowe atrybuty elementów HTML</span><span class="sxs-lookup"><span data-stu-id="fc15f-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="fc15f-336">Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET.</span><span class="sxs-lookup"><span data-stu-id="fc15f-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="fc15f-337">Jeśli wartość `false` jest `null`lub atrybut nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="fc15f-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="fc15f-338">Jeśli wartość `true`jest , atrybut jest renderowany zminimalizowane.</span><span class="sxs-lookup"><span data-stu-id="fc15f-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="fc15f-339">W poniższym `IsCompleted` przykładzie `checked` określa, czy jest renderowany w znacznikach elementu:</span><span class="sxs-lookup"><span data-stu-id="fc15f-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="fc15f-340">Jeśli `IsCompleted` `true`tak, to pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="fc15f-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="fc15f-341">Jeśli `IsCompleted` `false`tak, to pole wyboru jest renderowane jako:</span><span class="sxs-lookup"><span data-stu-id="fc15f-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="fc15f-342">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="fc15f-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="fc15f-343">Niektóre atrybuty HTML, takie jak [wciśnięte aria,](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)nie działają `bool`poprawnie, gdy typem .NET jest .</span><span class="sxs-lookup"><span data-stu-id="fc15f-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="fc15f-344">W takich przypadkach `string` należy użyć typu `bool`zamiast pliku .</span><span class="sxs-lookup"><span data-stu-id="fc15f-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="fc15f-345">Surowy kod HTML</span><span class="sxs-lookup"><span data-stu-id="fc15f-345">Raw HTML</span></span>

<span data-ttu-id="fc15f-346">Ciągi są zwykle renderowane przy użyciu węzłów tekstu DOM, co oznacza, że wszelkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst dosłowny.</span><span class="sxs-lookup"><span data-stu-id="fc15f-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="fc15f-347">Aby renderować nieprzetworzony `MarkupString` kod HTML, zawiń zawartość HTML w wartość.</span><span class="sxs-lookup"><span data-stu-id="fc15f-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="fc15f-348">Wartość jest analizowana jako HTML lub SVG i wstawiana do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="fc15f-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="fc15f-349">Renderowanie surowego KODU HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy ich unikać!</span><span class="sxs-lookup"><span data-stu-id="fc15f-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="fc15f-350">Poniższy przykład pokazuje, że przy użyciu `MarkupString` typu, aby dodać blok statycznej zawartości HTML do renderowanych danych wyjściowych składnika:</span><span class="sxs-lookup"><span data-stu-id="fc15f-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="fc15f-351">Kaskadowe wartości i parametry</span><span class="sxs-lookup"><span data-stu-id="fc15f-351">Cascading values and parameters</span></span>

<span data-ttu-id="fc15f-352">W niektórych scenariuszach jest niewygodne przepływ danych ze składnika nadrzędnego do składnika malejącego przy użyciu [parametrów komponentu](#component-parameters), zwłaszcza gdy istnieje kilka warstw składowych.</span><span class="sxs-lookup"><span data-stu-id="fc15f-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="fc15f-353">Kaskadowe wartości i parametry rozwiązują ten problem, zapewniając wygodny sposób dla komponentu nadrzędnego, aby zapewnić wartość dla wszystkich jego składników potomnych.</span><span class="sxs-lookup"><span data-stu-id="fc15f-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="fc15f-354">Kaskadowe wartości i parametry zapewniają również podejście do komponentów do koordynowania.</span><span class="sxs-lookup"><span data-stu-id="fc15f-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="fc15f-355">Przykład motywu</span><span class="sxs-lookup"><span data-stu-id="fc15f-355">Theme example</span></span>

<span data-ttu-id="fc15f-356">W poniższym przykładzie z `ThemeInfo` przykładowej aplikacji klasa określa informacje o motywie, które mają przepływać w dół hierarchii składników, tak aby wszystkie przyciski w danej części aplikacji współużytkować ten sam styl.</span><span class="sxs-lookup"><span data-stu-id="fc15f-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="fc15f-357">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc15f-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="fc15f-358">Składnik nadrzędny może zapewnić wartość kaskadową przy użyciu składnika Wartość kaskadowa.</span><span class="sxs-lookup"><span data-stu-id="fc15f-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="fc15f-359">Składnik `CascadingValue` zawija poddrzewo hierarchii komponentów i dostarcza pojedynczą wartość do wszystkich komponentów w tym poddrzewie.</span><span class="sxs-lookup"><span data-stu-id="fc15f-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="fc15f-360">Na przykład przykładowa aplikacja określa`ThemeInfo`informacje o motywie ( ) w jednym z układów aplikacji jako parametr `@Body` kaskadowy dla wszystkich składników, które tworzą treść układu właściwości.</span><span class="sxs-lookup"><span data-stu-id="fc15f-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="fc15f-361">`ButtonClass`jest przypisana wartość `btn-success` w komponencie układu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="fc15f-362">Każdy składnik potomny może `ThemeInfo` korzystać z tej właściwości za pośrednictwem obiektu kaskadowego.</span><span class="sxs-lookup"><span data-stu-id="fc15f-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="fc15f-363">`CascadingValuesParametersLayout`Składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-363">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="fc15f-364">Aby użyć wartości kaskadowych, składniki deklarują parametry `[CascadingParameter]` kaskadowe przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="fc15f-365">Wartości kaskadowe są powiązane z parametrami kaskadowymi według typu.</span><span class="sxs-lookup"><span data-stu-id="fc15f-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="fc15f-366">W przykładowej aplikacji `CascadingValuesParametersTheme` składnik wiąże `ThemeInfo` wartość kaskadową z parametrem kaskadowym.</span><span class="sxs-lookup"><span data-stu-id="fc15f-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="fc15f-367">Parametr służy do ustawiania klasy CSS dla jednego z przycisków wyświetlanych przez składnik.</span><span class="sxs-lookup"><span data-stu-id="fc15f-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="fc15f-368">`CascadingValuesParametersTheme`Składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-368">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

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
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="fc15f-369">Aby kaskadowo przedstawić wiele wartości tego samego typu w `Name` obrębie `CascadingValue` tego samego `CascadingParameter`poddrzewa, podaj unikatowy ciąg do każdego komponentu i odpowiadającego mu pliku .</span><span class="sxs-lookup"><span data-stu-id="fc15f-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="fc15f-370">W poniższym przykładzie dwa `CascadingValue` składniki kaskadowo różnych wystąpień `MyCascadingType` według nazwy:</span><span class="sxs-lookup"><span data-stu-id="fc15f-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="fc15f-371">W komponencie podrzędnym parametry kaskadowe otrzymują swoje wartości z odpowiednich wartości kaskadowych w komponencie nadrzędnym według nazwy:</span><span class="sxs-lookup"><span data-stu-id="fc15f-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="fc15f-372">Przykład zestawu kart</span><span class="sxs-lookup"><span data-stu-id="fc15f-372">TabSet example</span></span>

<span data-ttu-id="fc15f-373">Parametry kaskadowe umożliwiają również współpracę komponentów w hierarchii składników.</span><span class="sxs-lookup"><span data-stu-id="fc15f-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="fc15f-374">Rozważmy na przykład poniższy *przykład Zestawu tabulatorów* w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="fc15f-375">Przykładowa aplikacja `ITab` ma interfejs, który implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="fc15f-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="fc15f-376">Komponent `CascadingValuesParametersTabSet` używa składnika, `TabSet` który `Tab` zawiera kilka składników:</span><span class="sxs-lookup"><span data-stu-id="fc15f-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="fc15f-377">Składniki `Tab` podrzędne nie są jawnie przekazywane `TabSet`jako parametry do .</span><span class="sxs-lookup"><span data-stu-id="fc15f-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="fc15f-378">Zamiast tego składniki `Tab` podrzędne są częścią zawartości `TabSet`podrzędnej programu .</span><span class="sxs-lookup"><span data-stu-id="fc15f-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="fc15f-379">Jednak `TabSet` nadal musi wiedzieć o `Tab` każdym składniku, tak aby można było renderować nagłówki i aktywną kartę. Aby włączyć tę koordynację bez `TabSet` konieczności dodatkowego kodu, składnik *może zapewnić się jako wartość kaskadowa,* która jest następnie pobierana przez składniki potomne. `Tab`</span><span class="sxs-lookup"><span data-stu-id="fc15f-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="fc15f-380">`TabSet`Składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="fc15f-381">Składniki `Tab` potomne przechwytują `TabSet` zawierające jako parametr kaskadowy, dzięki czemu `Tab` komponenty dodają `TabSet` się do i współrzędnych, na których karta jest aktywna.</span><span class="sxs-lookup"><span data-stu-id="fc15f-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="fc15f-382">`Tab`Składnik:</span><span class="sxs-lookup"><span data-stu-id="fc15f-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="fc15f-383">Szablony maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="fc15f-383">Razor templates</span></span>

<span data-ttu-id="fc15f-384">Render fragmenty mogą być definiowane przy użyciu składni szablonu Razor.</span><span class="sxs-lookup"><span data-stu-id="fc15f-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="fc15f-385">Szablony maszynki do golenia są sposobem definiowania fragmentu kodu interfejsu użytkownika i zakładania następującego formatu:</span><span class="sxs-lookup"><span data-stu-id="fc15f-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="fc15f-386">Poniższy przykład ilustruje `RenderFragment` `RenderFragment<T>` sposób określania i wartości i renderowania szablonów bezpośrednio w składniku.</span><span class="sxs-lookup"><span data-stu-id="fc15f-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="fc15f-387">Fragmenty renderowania mogą być również przekazywane jako argumenty do [składników szablonów](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="fc15f-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="fc15f-388">Renderowane dane wyjściowe poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="fc15f-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="fc15f-389">Skalowalne obrazy grafiki wektorowej (SVG)</span><span class="sxs-lookup"><span data-stu-id="fc15f-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="fc15f-390">Od Blazor czasu renderowania html, obrazy obsługiwane przez przeglądarkę, `<img>` w tym scalable vector graphics (SVG) obrazy *(.svg),* są obsługiwane przez tag:</span><span class="sxs-lookup"><span data-stu-id="fc15f-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="fc15f-391">Podobnie obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów (*.css*):</span><span class="sxs-lookup"><span data-stu-id="fc15f-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="fc15f-392">Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="fc15f-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="fc15f-393">Jeśli `<svg>` umieścisz znacznik bezpośrednio w pliku komponentu (*.brzytwa*), podstawowe renderowanie obrazu jest obsługiwane, ale wiele zaawansowanych scenariuszy nie jest jeszcze obsługiwanych.</span><span class="sxs-lookup"><span data-stu-id="fc15f-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="fc15f-394">Na przykład `<use>` tagi nie są obecnie przestrzegane i `@bind` nie mogą być używane z niektórymi tagami SVG.</span><span class="sxs-lookup"><span data-stu-id="fc15f-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="fc15f-395">Oczekujemy, że zajmiemy się tymi ograniczeniami w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="fc15f-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc15f-396">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fc15f-396">Additional resources</span></span>

* <span data-ttu-id="fc15f-397"><xref:security/blazor/server>&ndash; Zawiera wskazówki Blazor dotyczące tworzenia aplikacji serwera, które muszą zmagać się z wyczerpaniem zasobów.</span><span class="sxs-lookup"><span data-stu-id="fc15f-397"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
