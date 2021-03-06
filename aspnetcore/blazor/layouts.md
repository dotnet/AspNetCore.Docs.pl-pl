---
title: ASP.NET Core Blazor układy
author: guardrex
description: Dowiedz się, jak tworzyć składniki układu wielokrotnego użytku dla Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2021
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
uid: blazor/layouts
ms.openlocfilehash: 9f3400b766a043fdf3872838bffe392eddba4049
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102394953"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="db890-103">ASP.NET Core Blazor układy</span><span class="sxs-lookup"><span data-stu-id="db890-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="db890-104">Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnej prezentacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db890-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall presentation.</span></span> <span data-ttu-id="db890-105">Umieszczenie kopii znaczników dla tych elementów we wszystkich składnikach aplikacji nie jest wydajne.</span><span class="sxs-lookup"><span data-stu-id="db890-105">Placing a copy of the markup for these elements into all of the components of an app isn't efficient.</span></span> <span data-ttu-id="db890-106">Za każdym razem, gdy jeden z tych elementów jest aktualizowany, każdy składnik, który używa tego elementu, musi zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="db890-106">Every time that one of these elements is updated, every component that uses the element must be updated.</span></span> <span data-ttu-id="db890-107">To podejście jest kosztowne do utrzymania i może prowadzić do niespójnej zawartości w przypadku braku aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="db890-107">This approach is costly to maintain and can lead to inconsistent content if an update is missed.</span></span> <span data-ttu-id="db890-108">*Układy* rozwiązują te problemy.</span><span class="sxs-lookup"><span data-stu-id="db890-108">*Layouts* solve these problems.</span></span>

<span data-ttu-id="db890-109">BlazorUkład to Razor składnik, który współużytkuje znaczniki ze składnikami, które odwołują się do niego.</span><span class="sxs-lookup"><span data-stu-id="db890-109">A Blazor layout is a Razor component that shares markup with components that reference it.</span></span> <span data-ttu-id="db890-110">Układy mogą używać [powiązań danych](xref:blazor/components/data-binding), [iniekcji zależności](xref:blazor/fundamentals/dependency-injection)i innych funkcji składników.</span><span class="sxs-lookup"><span data-stu-id="db890-110">Layouts can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other features of components.</span></span>

## <a name="layout-components"></a><span data-ttu-id="db890-111">Składniki układu</span><span class="sxs-lookup"><span data-stu-id="db890-111">Layout components</span></span>

### <a name="create-a-layout-component"></a><span data-ttu-id="db890-112">Tworzenie składnika układu</span><span class="sxs-lookup"><span data-stu-id="db890-112">Create a layout component</span></span>

<span data-ttu-id="db890-113">Aby utworzyć składnik układu:</span><span class="sxs-lookup"><span data-stu-id="db890-113">To create a layout component:</span></span>

* <span data-ttu-id="db890-114">Utwórz Razor składnik zdefiniowany przez Razor szablon lub kod C#.</span><span class="sxs-lookup"><span data-stu-id="db890-114">Create a Razor component defined by a Razor template or C# code.</span></span> <span data-ttu-id="db890-115">Składniki układu oparte na Razor szablonie używają `.razor` rozszerzenia pliku podobnie jak zwykłe Razor składniki.</span><span class="sxs-lookup"><span data-stu-id="db890-115">Layout components based on a Razor template use the `.razor` file extension just like ordinary Razor components.</span></span> <span data-ttu-id="db890-116">Ze względu na to, że składniki układu są udostępniane między składnikami aplikacji, są zwykle umieszczane w `Shared` folderze aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db890-116">Because layout components are shared across an app's components, they're usually placed in the app's `Shared` folder.</span></span> <span data-ttu-id="db890-117">Układy można jednak umieścić w dowolnej lokalizacji dostępnej dla składników, które go używają.</span><span class="sxs-lookup"><span data-stu-id="db890-117">However, layouts can be placed in any location accessible to the components that use it.</span></span> <span data-ttu-id="db890-118">Na przykład układ może być umieszczony w tym samym folderze co składniki, które go używają.</span><span class="sxs-lookup"><span data-stu-id="db890-118">For example, a layout can be placed in the same folder as the components that use it.</span></span>
* <span data-ttu-id="db890-119">Dziedzicz składnik <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="db890-119">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="db890-120"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Definiuje <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> Właściwość ( <xref:Microsoft.AspNetCore.Components.RenderFragment> Typ) dla renderowanej zawartości wewnątrz układu.</span><span class="sxs-lookup"><span data-stu-id="db890-120">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property (<xref:Microsoft.AspNetCore.Components.RenderFragment> type) for the rendered content inside the layout.</span></span>
* <span data-ttu-id="db890-121">Użyj Razor składni, `@Body` Aby określić lokalizację w znacznikach układu, w którym jest renderowana zawartość.</span><span class="sxs-lookup"><span data-stu-id="db890-121">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="db890-122">Poniższy `DoctorWhoLayout` składnik pokazuje Razor szablon składnika układu.</span><span class="sxs-lookup"><span data-stu-id="db890-122">The following `DoctorWhoLayout` component shows the Razor template of a layout component.</span></span> <span data-ttu-id="db890-123">Układ dziedziczy <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> i ustawia `@Body` między paskiem nawigacyjnym ( `<nav>...</nav>` ) i stopką ( `<footer>...</footer>` ).</span><span class="sxs-lookup"><span data-stu-id="db890-123">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar (`<nav>...</nav>`) and the footer (`<footer>...</footer>`).</span></span>

<span data-ttu-id="db890-124">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-124">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a><span data-ttu-id="db890-125">`MainLayout` cm6long</span><span class="sxs-lookup"><span data-stu-id="db890-125">`MainLayout` component</span></span>

<span data-ttu-id="db890-126">W aplikacji utworzonej na podstawie [ Blazor szablonu projektu](xref:blazor/project-structure) `MainLayout` składnik jest [domyślnym układem](#apply-a-default-layout-to-an-app)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db890-126">In an app created from a [Blazor project template](xref:blazor/project-structure), the `MainLayout` component is the app's [default layout](#apply-a-default-layout-to-an-app).</span></span>

<span data-ttu-id="db890-127">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-127">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

<span data-ttu-id="db890-128">[ Blazor Funkcja izolacji CSS](xref:blazor/components/css-isolation) stosuje odizolowane Style CSS do `MainLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-128">[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component.</span></span> <span data-ttu-id="db890-129">Zgodnie z Konwencją, style są dostarczane przez towarzyszący arkusz stylów o tej samej nazwie `Shared/MainLayout.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="db890-129">By convention, the styles are provided by the accompanying stylesheet of the same name, `Shared/MainLayout.razor.css`.</span></span> <span data-ttu-id="db890-130">Implementacja ASP.NET Core Framework arkusza stylów jest dostępna do inspekcji w [ASP.NET Core źródle odwołania (repozytorium dotnet/aspnetcore w witrynie GitHub)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span><span class="sxs-lookup"><span data-stu-id="db890-130">The ASP.NET Core framework implementation of the stylesheet is available for inspection in the [ASP.NET Core reference source (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span></span>

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a><span data-ttu-id="db890-131">Zastosuj Układ</span><span class="sxs-lookup"><span data-stu-id="db890-131">Apply a layout</span></span>

### <a name="apply-a-layout-to-a-component"></a><span data-ttu-id="db890-132">Zastosuj układ do składnika</span><span class="sxs-lookup"><span data-stu-id="db890-132">Apply a layout to a component</span></span>

<span data-ttu-id="db890-133">Użyj [`@layout`](xref:mvc/views/razor#layout) Razor dyrektywy, aby zastosować układ do Razor składnika obsługującego Routing, który ma [`@page`](xref:mvc/views/razor#page) dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="db890-133">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="db890-134">Kompilator konwertuje do `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> i stosuje atrybut do klasy składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-134">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute> and applies the attribute to the component class.</span></span>

<span data-ttu-id="db890-135">Zawartość następującego `Episodes` składnika jest wstawiana do obiektu `DoctorWhoLayout` na pozycji `@Body` .</span><span class="sxs-lookup"><span data-stu-id="db890-135">The content of the following `Episodes` component is inserted into the `DoctorWhoLayout` at the position of `@Body`.</span></span>

<span data-ttu-id="db890-136">`Pages/Episodes.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-136">`Pages/Episodes.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="db890-137">Następujące renderowane znaczniki HTML są tworzone przez poprzednią `DoctorWhoLayout` i `Episodes` składnik.</span><span class="sxs-lookup"><span data-stu-id="db890-137">The following rendered HTML markup is produced by the preceding `DoctorWhoLayout` and `Episodes` component.</span></span> <span data-ttu-id="db890-138">Niezależne znaczniki nie są wyświetlane, aby skoncentrować się na zawartości dostarczonej przez dwa objęte składniki:</span><span class="sxs-lookup"><span data-stu-id="db890-138">Extraneous markup doesn't appear in order to focus on the content provided by the two components involved:</span></span>

* <span data-ttu-id="db890-139">**Lekarz, który ma nagłówek &trade; bazy danych** ( `<h1>...</h1>` ) w nagłówku ( `<header>...</header>` ), pasku nawigacyjnym ( `<nav>...</nav>` ) i elemencie informacji o znakach towarowych ( `<div>...</div>` ) w stopce () `<footer>...</footer>` pochodzi ze `DoctorWhoLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-139">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`) in the header (`<header>...</header>`), navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) in the footer (`<footer>...</footer>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="db890-140">Pozycja **odcinków** ( `<h2>...</h2>` ) i lista epizodów ( `<ul>...</ul>` ) pochodzą z `Episodes` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-140">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

```html
<body>
    <div id="app">
        <header>
            <h1>Doctor Who&trade; Episode Database</h1>
        </header>

        <nav>
            <a href="masterlist">Master Episode List</a>
            <a href="search">Search</a>
            <a href="new">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <footer>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </footer>
    </div>
</body>
```

<span data-ttu-id="db890-141">Określanie układu bezpośrednio w składniku przesłania *domyślny układ*:</span><span class="sxs-lookup"><span data-stu-id="db890-141">Specifying the layout directly in a component overrides a *default layout*:</span></span>

* <span data-ttu-id="db890-142">Ustawiana przez `@layout` dyrektywę zaimportowaną ze `_Imports` składnika ( `_Imports.razor` ), zgodnie z opisem w poniższej sekcji [Zastosuj układ do folderu składników](#apply-a-layout-to-a-folder-of-components) .</span><span class="sxs-lookup"><span data-stu-id="db890-142">Set by an `@layout` directive imported from an `_Imports` component (`_Imports.razor`), as described in the following [Apply a layout to a folder of components](#apply-a-layout-to-a-folder-of-components) section.</span></span>
* <span data-ttu-id="db890-143">Ustaw jako domyślny układ aplikacji, zgodnie z opisem w sekcji [Zastosuj Układ domyślny do aplikacji](#apply-a-default-layout-to-an-app) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="db890-143">Set as the app's default layout, as described in the [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section later in this article.</span></span>

### <a name="apply-a-layout-to-a-folder-of-components"></a><span data-ttu-id="db890-144">Zastosuj układ do folderu składników</span><span class="sxs-lookup"><span data-stu-id="db890-144">Apply a layout to a folder of components</span></span>

<span data-ttu-id="db890-145">Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="db890-145">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="db890-146">Kompilator zawiera dyrektywy określone w pliku Imports we wszystkich Razor szablonach w tym samym folderze i rekursywnie we wszystkich jego podfolderach.</span><span class="sxs-lookup"><span data-stu-id="db890-146">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="db890-147">W związku z tym `_Imports.razor` plik zawierający `@layout DoctorWhoLayout` pewność, że wszystkie składniki w folderze używają `DoctorWhoLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-147">Therefore, an `_Imports.razor` file containing `@layout DoctorWhoLayout` ensures that all of the components in a folder use the `DoctorWhoLayout` component.</span></span> <span data-ttu-id="db890-148">Nie trzeba wielokrotnie dodawać `@layout DoctorWhoLayout` do wszystkich Razor składników ( `.razor` ) w folderze i podfolderach.</span><span class="sxs-lookup"><span data-stu-id="db890-148">There's no need to repeatedly add `@layout DoctorWhoLayout` to all of the Razor components (`.razor`) within the folder and subfolders.</span></span>

<span data-ttu-id="db890-149">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-149">`_Imports.razor`:</span></span>

```razor
@layout DoctorWhoLayout
...
```

<span data-ttu-id="db890-150">`_Imports.razor`Plik jest podobny do [pliku _ViewImports. cshtml dla Razor widoków i stron](xref:mvc/views/layout#importing-shared-directives) , ale jest stosowany w odniesieniu do Razor plików składników.</span><span class="sxs-lookup"><span data-stu-id="db890-150">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="db890-151">Określanie układu w programie `_Imports.razor` przesłania układ określony jako [domyślny układ aplikacji](#apply-a-default-layout-to-an-app)routera, który jest opisany w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="db890-151">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's [default app layout](#apply-a-default-layout-to-an-app), which is described in the following section.</span></span>

> [!WARNING]
> <span data-ttu-id="db890-152">**Nie** dodawaj Razor `@layout` dyrektywy do `_Imports.razor` pliku głównego, co powoduje nieskończoną pętlę układów.</span><span class="sxs-lookup"><span data-stu-id="db890-152">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts.</span></span> <span data-ttu-id="db890-153">Aby kontrolować domyślny układ aplikacji, określ układ w `Router` składniku.</span><span class="sxs-lookup"><span data-stu-id="db890-153">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="db890-154">Aby uzyskać więcej informacji, zobacz następujące [zastosowania układu domyślnego do aplikacji](#apply-a-default-layout-to-an-app) .</span><span class="sxs-lookup"><span data-stu-id="db890-154">For more information, see the following [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section.</span></span>

> [!NOTE]
> <span data-ttu-id="db890-155">[`@layout`](xref:mvc/views/razor#layout) Razor Dyrektywa stosuje tylko układ do routingu Razor składników za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="db890-155">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>

### <a name="apply-a-default-layout-to-an-app"></a><span data-ttu-id="db890-156">Stosowanie układu domyślnego do aplikacji</span><span class="sxs-lookup"><span data-stu-id="db890-156">Apply a default layout to an app</span></span>

<span data-ttu-id="db890-157">Określ domyślny układ aplikacji w `App` <xref:Microsoft.AspNetCore.Components.Routing.Router> składniku składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-157">Specify the default app layout in the in the `App` component's <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="db890-158">Poniższy przykład z aplikacji opartej na [ Blazor szablonie projektu](xref:blazor/project-structure) ustawia domyślny układ `MainLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-158">The following example from an app based on a [Blazor project template](xref:blazor/project-structure) sets the default layout to the `MainLayout` component.</span></span>

<span data-ttu-id="db890-159">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-159">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="db890-160">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika, zobacz <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="db890-160">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="db890-161">Określanie układu jako domyślnego układu w `Router` składniku jest użyteczną metodą, ponieważ można przesłonić układ dla poszczególnych składników lub folderów, zgodnie z opisem w poprzednich sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="db890-161">Specifying the layout as a default layout in the `Router` component is a useful practice because you can override the layout on a per-component or per-folder basis, as described in the preceding sections of this article.</span></span> <span data-ttu-id="db890-162">Zalecamy używanie `Router` składnika do ustawienia domyślnego układu aplikacji, ponieważ jest to najbardziej ogólne i elastyczne podejście do korzystania z układów.</span><span class="sxs-lookup"><span data-stu-id="db890-162">We recommend using the `Router` component to set the app's default layout because it's the most general and flexible approach for using layouts.</span></span>

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a><span data-ttu-id="db890-163">Zastosuj układ do dowolnej zawartości ( `LayoutView` składnik)</span><span class="sxs-lookup"><span data-stu-id="db890-163">Apply a layout to arbitrary content (`LayoutView` component)</span></span>

<span data-ttu-id="db890-164">Aby ustawić układ dla dowolnej Razor zawartości szablonu, określ układ ze <xref:Microsoft.AspNetCore.Components.LayoutView> składnikiem.</span><span class="sxs-lookup"><span data-stu-id="db890-164">To set a layout for arbitrary Razor template content, specify the layout with a <xref:Microsoft.AspNetCore.Components.LayoutView> component.</span></span> <span data-ttu-id="db890-165">Można użyć <xref:Microsoft.AspNetCore.Components.LayoutView> w dowolnym Razor składniku.</span><span class="sxs-lookup"><span data-stu-id="db890-165">You can use a <xref:Microsoft.AspNetCore.Components.LayoutView> in any Razor component.</span></span> <span data-ttu-id="db890-166">Poniższy przykład ustawia składnik układu o nazwie `ErrorLayout` dla `MainLayout` <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> szablonu składnika ( `<NotFound>...</NotFound>` ).</span><span class="sxs-lookup"><span data-stu-id="db890-166">The following example sets a layout component named `ErrorLayout` for the `MainLayout` component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template (`<NotFound>...</NotFound>`).</span></span>

<span data-ttu-id="db890-167">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-167">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a><span data-ttu-id="db890-168">Układy zagnieżdżone</span><span class="sxs-lookup"><span data-stu-id="db890-168">Nested layouts</span></span>

<span data-ttu-id="db890-169">Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu.</span><span class="sxs-lookup"><span data-stu-id="db890-169">A component can reference a layout that in turn references another layout.</span></span> <span data-ttu-id="db890-170">Na przykład, układy zagnieżdżone są używane do tworzenia struktur menu wielopoziomowego.</span><span class="sxs-lookup"><span data-stu-id="db890-170">For example, nested layouts are used to create a multi-level menu structures.</span></span>

<span data-ttu-id="db890-171">Poniższy przykład pokazuje, jak używać układów zagnieżdżonych.</span><span class="sxs-lookup"><span data-stu-id="db890-171">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="db890-172">`Episodes`Składnik wyświetlany w sekcji [Zastosuj układ do składnika](#apply-a-layout-to-a-component) jest składnikiem do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="db890-172">The `Episodes` component shown in the [Apply a layout to a component](#apply-a-layout-to-a-component) section is the component to display.</span></span> <span data-ttu-id="db890-173">Składnik odwołuje się do `DoctorWhoLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-173">The component references the `DoctorWhoLayout` component.</span></span>

<span data-ttu-id="db890-174">Poniższy `DoctorWhoLayout` składnik jest zmodyfikowaną wersją przykładu przedstawioną wcześniej w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="db890-174">The following `DoctorWhoLayout` component is a modified version of the example shown earlier in this article.</span></span> <span data-ttu-id="db890-175">Elementy nagłówka i stopki są usuwane, a układ odwołuje się do innego układu `ProductionsLayout` .</span><span class="sxs-lookup"><span data-stu-id="db890-175">The header and footer elements are removed, and the layout references another layout, `ProductionsLayout`.</span></span> <span data-ttu-id="db890-176">`Episodes`Składnik jest renderowany, gdzie `@Body` pojawia się w `DoctorWhoLayout` .</span><span class="sxs-lookup"><span data-stu-id="db890-176">The `Episodes` component is rendered where `@Body` appears in the `DoctorWhoLayout`.</span></span>

<span data-ttu-id="db890-177">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-177">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

<span data-ttu-id="db890-178">`ProductionsLayout`Składnik zawiera elementy układu najwyższego poziomu, w których `<header>...</header>` teraz znajdują się elementy Header () i stopka ( `<footer>...</footer>` ).</span><span class="sxs-lookup"><span data-stu-id="db890-178">The `ProductionsLayout` component contains the top-level layout elements, where the header (`<header>...</header>`) and footer (`<footer>...</footer>`) elements now reside.</span></span> <span data-ttu-id="db890-179">`DoctorWhoLayout` `Episodes` Element with jest renderowany, gdzie `@Body` pojawia się.</span><span class="sxs-lookup"><span data-stu-id="db890-179">The `DoctorWhoLayout` with the `Episodes` component is rendered where `@Body` appears.</span></span>

<span data-ttu-id="db890-180">`Shared/ProductionsLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="db890-180">`Shared/ProductionsLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

<span data-ttu-id="db890-181">Poprzednia zagnieżdżony układ jest tworzony za pomocą powyższego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="db890-181">The following rendered HTML markup is produced by the preceding nested layout.</span></span> <span data-ttu-id="db890-182">Niezależne znaczniki nie są wyświetlane w celu skoncentrowania się na zagnieżdżonej zawartości dostarczonej przez trzy składniki:</span><span class="sxs-lookup"><span data-stu-id="db890-182">Extraneous markup doesn't appear in order to focus on the nested content provided by the three components involved:</span></span>

* <span data-ttu-id="db890-183">Element Header ( `<header>...</header>` ), produkcyjny pasek nawigacyjny ( `<nav>...</nav>` ) i stopka ( `<footer>...</footer>` ) i ich zawartość pochodzą ze `ProductionsLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-183">The header (`<header>...</header>`), production navigation bar (`<nav>...</nav>`), and footer (`<footer>...</footer>`) elements and their content come from the `ProductionsLayout` component.</span></span>
* <span data-ttu-id="db890-184">Lekarz, który przejdzie z nagłówka **&trade; bazy danych** ( `<h1>...</h1>` ), pasek nawigacyjny odcinka ( `<nav>...</nav>` ) i element informacji o znakach towarowych ( `<div>...</div>` ) pochodzi od `DoctorWhoLayout` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-184">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`), episode navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="db890-185">Pozycja **odcinków** ( `<h2>...</h2>` ) i lista epizodów ( `<ul>...</ul>` ) pochodzą z `Episodes` składnika.</span><span class="sxs-lookup"><span data-stu-id="db890-185">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

```html
<body>
    <div id="app">
        <header>
            <h1>Productions</h1>
        </header>

        <nav>
            <a href="master-production-list">Master Production List</a>
            <a href="production-search">Search</a>
            <a href="new-production">Add Production</a>
        </nav>

        <h1>Doctor Who&trade; Episode Database</h1>

        <nav>
            <a href="episode-masterlist">Master Episode List</a>
            <a href="episode-search">Search</a>
            <a href="new-episode">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <div>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </div>

        <footer>
            Footer of Productions Layout
        </footer>
    </div>
</body>
```

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="db890-186">Udostępnianie Razor układu stron ze składnikami zintegrowanymi</span><span class="sxs-lookup"><span data-stu-id="db890-186">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="db890-187">Gdy składniki routingu są zintegrowane Razor z aplikacją Pages, można używać współużytkowanego układu aplikacji ze składnikami.</span><span class="sxs-lookup"><span data-stu-id="db890-187">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="db890-188">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/prerendering-and-integration>.</span><span class="sxs-lookup"><span data-stu-id="db890-188">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db890-189">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="db890-189">Additional resources</span></span>

* <xref:mvc/views/layout>
