---
title: Układ w ASP.NET Core
author: ardalis
description: Dowiedz się, jak używać typowych układów, udostępniać dyrektywy i uruchamiać wspólny kod przed renderowaniem widoków w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/layout
ms.openlocfilehash: fbae94f315c1bb49f1b04be7e71c841f46826216
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766488"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="56c71-103">Układ w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56c71-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="56c71-104">[Steve Kowalski](https://ardalis.com/) i [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="56c71-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="56c71-105">Strony i widoki często udostępniają wizualizacje i programistyczne elementy.</span><span class="sxs-lookup"><span data-stu-id="56c71-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="56c71-106">W tym artykule pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="56c71-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="56c71-107">Użyj wspólnych układów.</span><span class="sxs-lookup"><span data-stu-id="56c71-107">Use common layouts.</span></span>
* <span data-ttu-id="56c71-108">Dyrektywy udostępniania.</span><span class="sxs-lookup"><span data-stu-id="56c71-108">Share directives.</span></span>
* <span data-ttu-id="56c71-109">Uruchom wspólny kod przed renderowaniem stron lub widoków.</span><span class="sxs-lookup"><span data-stu-id="56c71-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="56c71-110">W tym dokumencie omówiono układy dla dwóch różnych metod ASP.NET Core MVC: Razor Pages i controllers z widokami.</span><span class="sxs-lookup"><span data-stu-id="56c71-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="56c71-111">W tym temacie różnice są minimalne:</span><span class="sxs-lookup"><span data-stu-id="56c71-111">For this topic, the differences are minimal:</span></span>

* Razor<span data-ttu-id="56c71-112">Strony znajdują się w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="56c71-112"> Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="56c71-113">Kontrolery z widokami używają folderu *widoki* dla widoków.</span><span class="sxs-lookup"><span data-stu-id="56c71-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="56c71-114">Co to jest układ</span><span class="sxs-lookup"><span data-stu-id="56c71-114">What is a Layout</span></span>

<span data-ttu-id="56c71-115">Większość aplikacji sieci Web ma wspólny układ, który zapewnia użytkownikom spójne środowisko, które przechodzi ze strony do strony.</span><span class="sxs-lookup"><span data-stu-id="56c71-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="56c71-116">Układ zazwyczaj obejmuje typowe elementy interfejsu użytkownika, takie jak nagłówek aplikacji, Nawigacja lub elementy menu oraz stopka.</span><span class="sxs-lookup"><span data-stu-id="56c71-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Przykład układu strony](layout/_static/page-layout.png)

<span data-ttu-id="56c71-118">Typowe struktury HTML, takie jak skrypty i arkusze stylów, również są często używane przez wiele stron w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56c71-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="56c71-119">Wszystkie te elementy udostępnione mogą być zdefiniowane w pliku *układu* , do którego może odwoływać się dowolny widok używany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56c71-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="56c71-120">Układy zmniejszają ilość zduplikowanego kodu w widokach.</span><span class="sxs-lookup"><span data-stu-id="56c71-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="56c71-121">Zgodnie z Konwencją domyślny układ aplikacji ASP.NET Core ma nazwę *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="56c71-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="56c71-122">Pliki układów dla nowych projektów ASP.NET Core utworzonych przy użyciu szablonów są następujące:</span><span class="sxs-lookup"><span data-stu-id="56c71-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* Razor<span data-ttu-id="56c71-123">Strony: *Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="56c71-123"> Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Folder stron w Eksplorator rozwiązań](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="56c71-125">Kontroler z widokami: *widoki/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="56c71-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Folder widoków w Eksplorator rozwiązań](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="56c71-127">Układ definiuje szablon najwyższego poziomu dla widoków w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56c71-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="56c71-128">Aplikacje nie wymagają układu.</span><span class="sxs-lookup"><span data-stu-id="56c71-128">Apps don't require a layout.</span></span> <span data-ttu-id="56c71-129">Aplikacje mogą definiować więcej niż jeden układ — różne widoki mogą określać różne układy.</span><span class="sxs-lookup"><span data-stu-id="56c71-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="56c71-130">Poniższy kod przedstawia plik układu dla szablonu utworzonego za pomocą kontrolera i widoków:</span><span class="sxs-lookup"><span data-stu-id="56c71-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="56c71-131">Określanie układu</span><span class="sxs-lookup"><span data-stu-id="56c71-131">Specifying a Layout</span></span>

Razor<span data-ttu-id="56c71-132">widoki mają `Layout` właściwość.</span><span class="sxs-lookup"><span data-stu-id="56c71-132"> views have a `Layout` property.</span></span> <span data-ttu-id="56c71-133">Poszczególne widoki określają układ, ustawiając tę właściwość:</span><span class="sxs-lookup"><span data-stu-id="56c71-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="56c71-134">Określony układ może używać pełnej ścieżki (na przykład */Pages/Shared/_Layout. cshtml* lub */views/Shared/_Layout. cshtml*) lub częściowej nazwy (przykład: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="56c71-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="56c71-135">Po podaniu częściowej nazwy aparat Razor widoku wyszukuje plik układu przy użyciu standardowego procesu odnajdywania.</span><span class="sxs-lookup"><span data-stu-id="56c71-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="56c71-136">Folder, w którym istnieje metoda obsługi (lub kontroler), jest wyszukiwany jako pierwszy, a następnie folder *udostępniony* .</span><span class="sxs-lookup"><span data-stu-id="56c71-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="56c71-137">Ten proces odnajdywania jest identyczny z procesem używanym do odnajdywania [widoków częściowych](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="56c71-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="56c71-138">Domyślnie każdy układ musi wywoływać `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="56c71-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="56c71-139">W każdym miejscu, `RenderBody` w którym jest umieszczane wywołanie, zawartość widoku będzie renderowana.</span><span class="sxs-lookup"><span data-stu-id="56c71-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="56c71-140">Sekcje</span><span class="sxs-lookup"><span data-stu-id="56c71-140">Sections</span></span>

<span data-ttu-id="56c71-141">Układ może opcjonalnie odwoływać się do co najmniej jednej *sekcji*, `RenderSection`wywołując.</span><span class="sxs-lookup"><span data-stu-id="56c71-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="56c71-142">Sekcje umożliwiają organizowanie miejsca, w którym należy umieścić pewne elementy strony.</span><span class="sxs-lookup"><span data-stu-id="56c71-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="56c71-143">Każde wywołanie programu `RenderSection` może określać, czy ta sekcja jest wymagana czy opcjonalna:</span><span class="sxs-lookup"><span data-stu-id="56c71-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="56c71-144">Jeśli wymagana sekcja nie zostanie znaleziona, zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56c71-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="56c71-145">Poszczególne widoki określają zawartość, która ma być renderowana w sekcji przy `@section` Razor użyciu składni.</span><span class="sxs-lookup"><span data-stu-id="56c71-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="56c71-146">Jeśli strona lub widok definiuje sekcję, musi być renderowana (lub wystąpił błąd).</span><span class="sxs-lookup"><span data-stu-id="56c71-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="56c71-147">Przykładowa `@section` Definicja w Razor widoku stron:</span><span class="sxs-lookup"><span data-stu-id="56c71-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="56c71-148">W powyższym kodzie *skrypt/Main. js* zostanie dodany do `scripts` sekcji na stronie lub w widoku.</span><span class="sxs-lookup"><span data-stu-id="56c71-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="56c71-149">Inne strony lub widoki w tej samej aplikacji mogą nie wymagać tego skryptu i nie będą mogły definiować sekcji skryptów.</span><span class="sxs-lookup"><span data-stu-id="56c71-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="56c71-150">W poniższym znaczniku jest używana [pomocnik tagów częściowej](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) do renderowania *_ValidationScriptsPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="56c71-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="56c71-151">Poprzednie oznakowanie zostało wygenerowane przez tworzenie [szkieletu Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="56c71-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="56c71-152">Sekcje zdefiniowane w stronie lub widoku są dostępne tylko na stronie natychmiastowego układu.</span><span class="sxs-lookup"><span data-stu-id="56c71-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="56c71-153">Nie można się do nich odwoływać ze stron częściowych, składników widoku ani innych części systemu widoku.</span><span class="sxs-lookup"><span data-stu-id="56c71-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="56c71-154">Ignorowanie sekcji</span><span class="sxs-lookup"><span data-stu-id="56c71-154">Ignoring sections</span></span>

<span data-ttu-id="56c71-155">Domyślnie treść i wszystkie sekcje na stronie zawartości muszą być renderowane na stronie układu.</span><span class="sxs-lookup"><span data-stu-id="56c71-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="56c71-156">Aparat Razor widoku wymusza to przez śledzenie, czy treść i każda z nich zostały renderowane.</span><span class="sxs-lookup"><span data-stu-id="56c71-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="56c71-157">Aby nakazać aparatowi widoku ignorowanie treści lub sekcji, wywołaj `IgnoreBody` metody `IgnoreSection` i.</span><span class="sxs-lookup"><span data-stu-id="56c71-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="56c71-158">Treść i Każda sekcja na Razor stronie muszą być renderowane lub ignorowane.</span><span class="sxs-lookup"><span data-stu-id="56c71-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="56c71-159">Importowanie wspólnych dyrektyw</span><span class="sxs-lookup"><span data-stu-id="56c71-159">Importing Shared Directives</span></span>

<span data-ttu-id="56c71-160">Widoki i strony mogą używać Razor dyrektywy do importowania przestrzeni nazw i używania [iniekcji zależności](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="56c71-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="56c71-161">Dyrektywy udostępnione przez wiele widoków można określić we wspólnym pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="56c71-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="56c71-162">`_ViewImports` Plik obsługuje następujące dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="56c71-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="56c71-163">Plik nie obsługuje innych Razor funkcji, takich jak definicje funkcji i sekcji.</span><span class="sxs-lookup"><span data-stu-id="56c71-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="56c71-164">Przykładowy `_ViewImports.cshtml` plik:</span><span class="sxs-lookup"><span data-stu-id="56c71-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="56c71-165">Plik *_ViewImports. cshtml* dla aplikacji ASP.NET Core MVC zazwyczaj znajduje się w folderze *stron* (lub *widoków*).</span><span class="sxs-lookup"><span data-stu-id="56c71-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="56c71-166">Plik *_ViewImports. cshtml* można umieścić w dowolnym folderze. w takim przypadku zostanie on zastosowany tylko do stron lub widoków w tym folderze i jego podfolderach.</span><span class="sxs-lookup"><span data-stu-id="56c71-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="56c71-167">`_ViewImports`pliki są przetwarzane, rozpoczynając od poziomu głównego, a następnie dla każdego folderu, który zaczyna się do lokalizacji strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="56c71-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="56c71-168">`_ViewImports`ustawienia określone na poziomie głównym mogą zostać zastąpione na poziomie folderu.</span><span class="sxs-lookup"><span data-stu-id="56c71-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="56c71-169">Załóżmy na przykład, że:</span><span class="sxs-lookup"><span data-stu-id="56c71-169">For example, suppose:</span></span>

* <span data-ttu-id="56c71-170">Plik poziomu głównego *_ViewImports. cshtml* zawiera `@model MyModel1` i `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="56c71-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="56c71-171">Podfolder *_ViewImports. cshtml* zawiera `@model MyModel2` i `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="56c71-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="56c71-172">Strony i widoki w podfolderze będą miały dostęp do pomocników tagów i `MyModel2` modelu.</span><span class="sxs-lookup"><span data-stu-id="56c71-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="56c71-173">Jeśli w hierarchii plików znaleziono wiele plików *_ViewImports. cshtml* , połączone zachowanie dyrektyw jest następujące:</span><span class="sxs-lookup"><span data-stu-id="56c71-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="56c71-174">`@addTagHelper`, `@removeTagHelper`: wszystkie uruchomienia, w kolejności</span><span class="sxs-lookup"><span data-stu-id="56c71-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="56c71-175">`@tagHelperPrefix`: najbliższy jeden do widoku przesłania wszystkie inne</span><span class="sxs-lookup"><span data-stu-id="56c71-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="56c71-176">`@model`: najbliższy jeden do widoku przesłania wszystkie inne</span><span class="sxs-lookup"><span data-stu-id="56c71-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="56c71-177">`@inherits`: najbliższy jeden do widoku przesłania wszystkie inne</span><span class="sxs-lookup"><span data-stu-id="56c71-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="56c71-178">`@using`: wszystkie są uwzględnione; duplikaty są ignorowane</span><span class="sxs-lookup"><span data-stu-id="56c71-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="56c71-179">`@inject`: dla każdej właściwości najbliższy jeden do widoku przesłania wszystkie inne osoby o tej samej nazwie właściwości</span><span class="sxs-lookup"><span data-stu-id="56c71-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="56c71-180">Uruchamianie kodu przed każdym widokiem</span><span class="sxs-lookup"><span data-stu-id="56c71-180">Running Code Before Each View</span></span>

<span data-ttu-id="56c71-181">Kod, który musi zostać uruchomiony przed każdym widokiem lub stroną należy umieścić w pliku *_ViewStart. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="56c71-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="56c71-182">Zgodnie z Konwencją plik *_ViewStart. cshtml* znajduje się w folderze *stron* (lub *widoków*).</span><span class="sxs-lookup"><span data-stu-id="56c71-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="56c71-183">Instrukcje wymienione w *_ViewStart. cshtml* są uruchamiane przed wszystkimi pełnymi widokami (nie układami i nieczęściowymi widokami).</span><span class="sxs-lookup"><span data-stu-id="56c71-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="56c71-184">Podobnie jak [ViewImports. cshtml](xref:mvc/views/layout#viewimports), *_ViewStart. cshtml* jest hierarchiczna.</span><span class="sxs-lookup"><span data-stu-id="56c71-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="56c71-185">Jeśli plik *_ViewStart. cshtml* jest zdefiniowany w folderze widoku lub strony, zostanie on uruchomiony po elemencie zdefiniowanym w folderze głównym folderu *stron* (lub *widoków*) (jeśli istnieje).</span><span class="sxs-lookup"><span data-stu-id="56c71-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="56c71-186">Przykładowy plik *_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="56c71-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="56c71-187">Powyższy plik określa, że wszystkie widoki będą korzystać z układu *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="56c71-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="56c71-188">*_ViewStart. cshtml* i *_ViewImports. cshtml* **nie** są zwykle umieszczane w folderze */Pages/Shared* (lub */views/Shared*).</span><span class="sxs-lookup"><span data-stu-id="56c71-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="56c71-189">Wersje tych plików na poziomie aplikacji należy umieścić bezpośrednio w folderze */Pages* (lub */views*).</span><span class="sxs-lookup"><span data-stu-id="56c71-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
