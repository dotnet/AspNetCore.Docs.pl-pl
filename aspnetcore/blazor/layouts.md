---
title: ASP.NET układy Blazor rdzenia
author: guardrex
description: Dowiedz się, jak tworzyć składniki Blazor układu wielokrotnego użytku dla aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660413"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="0118a-103">ASP.NET układy Blazor rdzenia</span><span class="sxs-lookup"><span data-stu-id="0118a-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="0118a-104">Przez [Rainer Stropek](https://www.timecockpit.com) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0118a-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0118a-105">Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnego układu aplikacji i są używane przez każdy składnik w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0118a-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="0118a-106">Kopiowanie kodu tych elementów do wszystkich składników aplikacji nie jest&mdash;skuteczne podejście za każdym razem, gdy jeden z elementów wymaga aktualizacji, każdy składnik musi zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="0118a-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="0118a-107">Takie powielanie jest trudne do utrzymania i może prowadzić do niespójnej zawartości w czasie.</span><span class="sxs-lookup"><span data-stu-id="0118a-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="0118a-108">*Układy* rozwiązują ten problem.</span><span class="sxs-lookup"><span data-stu-id="0118a-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="0118a-109">Technicznie układ jest po prostu kolejnym elementem.</span><span class="sxs-lookup"><span data-stu-id="0118a-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="0118a-110">Układ jest zdefiniowany w szablonie Razor lub w kodzie Języka C# i może używać [powiązania danych,](xref:blazor/data-binding) [iniekcji zależności](xref:blazor/dependency-injection)i innych scenariuszy składników.</span><span class="sxs-lookup"><span data-stu-id="0118a-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="0118a-111">Aby przekształcić *komponent* w *układ*, komponent:</span><span class="sxs-lookup"><span data-stu-id="0118a-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="0118a-112">Dziedziczy `LayoutComponentBase`z , który `Body` definiuje właściwość dla renderowanych treści wewnątrz układu.</span><span class="sxs-lookup"><span data-stu-id="0118a-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="0118a-113">Używa składni `@Body` Razor, aby określić lokalizację w znacznikach układu, w której zawartość jest renderowana.</span><span class="sxs-lookup"><span data-stu-id="0118a-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="0118a-114">Poniższy przykład kodu przedstawia szablon Razor składnika *układu, MainLayout.brzytwa*.</span><span class="sxs-lookup"><span data-stu-id="0118a-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="0118a-115">Układ dziedziczy `LayoutComponentBase` i `@Body` ustawia między paskiem nawigacyjnym a stopki:</span><span class="sxs-lookup"><span data-stu-id="0118a-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="0118a-116">W aplikacji opartej na Blazor jednym z `MainLayout` szablonów aplikacji składnik *(MainLayout.brzytwa)* znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0118a-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="0118a-117">Układ domyślny</span><span class="sxs-lookup"><span data-stu-id="0118a-117">Default layout</span></span>

<span data-ttu-id="0118a-118">Określ domyślny układ `Router` aplikacji w składniku w pliku *App.razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0118a-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="0118a-119">Następujący `Router` składnik, który jest dostarczany Blazor przez szablony domyślne, `MainLayout` ustawia domyślny układ na składnik:</span><span class="sxs-lookup"><span data-stu-id="0118a-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="0118a-120">Aby podać domyślny `NotFound` układ zawartości, określ `LayoutView` zawartość: `NotFound`</span><span class="sxs-lookup"><span data-stu-id="0118a-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="0118a-121">Aby uzyskać więcej `Router` informacji <xref:blazor/routing>na temat komponentu, zobacz .</span><span class="sxs-lookup"><span data-stu-id="0118a-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="0118a-122">Określenie układu jako układu domyślnego w routerze jest użyteczną praktyką, ponieważ można go zastąpić na podstawie składnika lub dla folderu.</span><span class="sxs-lookup"><span data-stu-id="0118a-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="0118a-123">Wolisz używać routera, aby ustawić domyślny układ aplikacji, ponieważ jest to najbardziej ogólna technika.</span><span class="sxs-lookup"><span data-stu-id="0118a-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="0118a-124">Określanie układu w komponencie</span><span class="sxs-lookup"><span data-stu-id="0118a-124">Specify a layout in a component</span></span>

<span data-ttu-id="0118a-125">Użyj dyrektywy `@layout` Razor, aby zastosować układ do składnika.</span><span class="sxs-lookup"><span data-stu-id="0118a-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="0118a-126">Kompilator `@layout` konwertuje `LayoutAttribute`na , który jest stosowany do klasy składnika.</span><span class="sxs-lookup"><span data-stu-id="0118a-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="0118a-127">Zawartość następującego `MasterList` komponentu jest wstawiana `MasterLayout` `@Body`do pozycji:</span><span class="sxs-lookup"><span data-stu-id="0118a-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="0118a-128">Określenie układu bezpośrednio w komponencie zastępuje *domyślny układ* ustawiony `@layout` w routerze lub dyrektywę zaimportowany z *_Imports.brzytwa*.</span><span class="sxs-lookup"><span data-stu-id="0118a-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="0118a-129">Wybór układu scentralizowanego</span><span class="sxs-lookup"><span data-stu-id="0118a-129">Centralized layout selection</span></span>

<span data-ttu-id="0118a-130">Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="0118a-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="0118a-131">Kompilator zawiera dyrektywy określone w pliku importu we wszystkich szablonach Razor w tym samym folderze i rekursywnie we wszystkich jego podfolderach.</span><span class="sxs-lookup"><span data-stu-id="0118a-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="0118a-132">W związku z tym *plik _Imports.brzytwa* zawierający `@layout MyCoolLayout` zapewnia, że `MyCoolLayout`wszystkie składniki w folderze używać .</span><span class="sxs-lookup"><span data-stu-id="0118a-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="0118a-133">Nie ma potrzeby wielokrotnego `@layout MyCoolLayout` dodawania do wszystkich plików *.brzytwa* w folderze i podfolderach.</span><span class="sxs-lookup"><span data-stu-id="0118a-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="0118a-134">`@using`dyrektywy są również stosowane do komponentów w ten sam sposób.</span><span class="sxs-lookup"><span data-stu-id="0118a-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="0118a-135">Importowanie plików *_Imports.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="0118a-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="0118a-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="0118a-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="0118a-137">Wszystkie komponenty Razor w tym samym folderze i wszystkie podfoldery.</span><span class="sxs-lookup"><span data-stu-id="0118a-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="0118a-138">Obszar `BlazorApp1.Data` nazw.</span><span class="sxs-lookup"><span data-stu-id="0118a-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="0118a-139">Plik *_Imports.razor* jest podobny do [pliku _ViewImports.cshtml dla widoków i stron Razor,](xref:mvc/views/layout#importing-shared-directives) ale stosuje się specjalnie do plików komponentów Razor.</span><span class="sxs-lookup"><span data-stu-id="0118a-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="0118a-140">Określenie układu w *_Imports.razor* zastępuje układ określony jako *domyślny układ*routera .</span><span class="sxs-lookup"><span data-stu-id="0118a-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="0118a-141">Układy zagnieżdżone</span><span class="sxs-lookup"><span data-stu-id="0118a-141">Nested layouts</span></span>

<span data-ttu-id="0118a-142">Aplikacje mogą składać się z układów zagnieżdżonych.</span><span class="sxs-lookup"><span data-stu-id="0118a-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="0118a-143">Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu.</span><span class="sxs-lookup"><span data-stu-id="0118a-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="0118a-144">Na przykład układy zagnieżdżania są używane do tworzenia wielopoziomowej struktury menu.</span><span class="sxs-lookup"><span data-stu-id="0118a-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="0118a-145">W poniższym przykładzie pokazano, jak używać układów zagnieżdżonych.</span><span class="sxs-lookup"><span data-stu-id="0118a-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="0118a-146">*Plik EpisodesComponent.razor* jest składnikiem do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="0118a-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="0118a-147">Komponent odwołuje się `MasterListLayout`do:</span><span class="sxs-lookup"><span data-stu-id="0118a-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="0118a-148">Plik *MasterListLayout.razor* zapewnia `MasterListLayout`plik .</span><span class="sxs-lookup"><span data-stu-id="0118a-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="0118a-149">Układ odwołuje się do `MasterLayout`innego układu, gdzie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="0118a-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="0118a-150">`EpisodesComponent`jest renderowany `@Body` w miejscu, w którym się pojawia:</span><span class="sxs-lookup"><span data-stu-id="0118a-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="0118a-151">Na koniec `MasterLayout` w *masterlayout.brzytwa* zawiera elementy układu najwyższego poziomu, takie jak nagłówek, menu główne i stopka.</span><span class="sxs-lookup"><span data-stu-id="0118a-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="0118a-152">`MasterListLayout`z `EpisodesComponent` renderowanym renderem, w którym `@Body` się pojawia:</span><span class="sxs-lookup"><span data-stu-id="0118a-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="0118a-153">Udostępnianie układu Strony Razor ze zintegrowanymi komponentami</span><span class="sxs-lookup"><span data-stu-id="0118a-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="0118a-154">Gdy składniki rutowalne są zintegrowane z aplikacją Razor Pages, udostępnionego układu aplikacji można używać ze składnikami.</span><span class="sxs-lookup"><span data-stu-id="0118a-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="0118a-155">Aby uzyskać więcej informacji, zobacz <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="0118a-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0118a-156">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0118a-156">Additional resources</span></span>

* <xref:mvc/views/layout>
