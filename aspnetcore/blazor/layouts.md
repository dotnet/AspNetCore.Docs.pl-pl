---
title: ASP.NET Core Blazor układy
author: guardrex
description: Dowiedz się, jak tworzyć składniki układu wielokrotnego użytku dla Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 09cca9c4af23c35fdbc2ee92169913c960b0a68d
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424333"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="c1459-103">ASP.NET Core Blazor układy</span><span class="sxs-lookup"><span data-stu-id="c1459-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="c1459-104">Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1459-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c1459-105">Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnego układu aplikacji i są używane przez każdy składnik w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1459-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="c1459-106">Kopiowanie kodu tych elementów do wszystkich składników aplikacji nie jest efektywnym podejściem.</span><span class="sxs-lookup"><span data-stu-id="c1459-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="c1459-107">Za każdym razem, gdy jeden z elementów wymaga aktualizacji, należy zaktualizować każdy składnik.</span><span class="sxs-lookup"><span data-stu-id="c1459-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="c1459-108">Taka duplikacja jest trudna do utrzymania i może prowadzić do niespójnej zawartości z upływem czasu.</span><span class="sxs-lookup"><span data-stu-id="c1459-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="c1459-109">*Układy* rozwiązują ten problem.</span><span class="sxs-lookup"><span data-stu-id="c1459-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="c1459-110">Technicznie, układ jest tylko innym składnikiem.</span><span class="sxs-lookup"><span data-stu-id="c1459-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="c1459-111">Układ jest zdefiniowany w Razor szablonie lub w kodzie C# i może używać [powiązań danych](xref:blazor/data-binding), [iniekcji zależności](xref:blazor/dependency-injection)i innych scenariuszy składników.</span><span class="sxs-lookup"><span data-stu-id="c1459-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="c1459-112">Aby przekształcić *składnik* do *układu*, składnik:</span><span class="sxs-lookup"><span data-stu-id="c1459-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="c1459-113">Dziedziczy z `LayoutComponentBase` , który definiuje `Body` Właściwość dla renderowanej zawartości wewnątrz układu.</span><span class="sxs-lookup"><span data-stu-id="c1459-113">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="c1459-114">Używa Razor składni `@Body` do określenia lokalizacji w znaczniku układu, w którym jest renderowana zawartość.</span><span class="sxs-lookup"><span data-stu-id="c1459-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="c1459-115">Poniższy przykładowy kod przedstawia Razor szablon składnika układu, *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="c1459-115">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="c1459-116">Układ dziedziczy `LayoutComponentBase` i ustawia `@Body` między paskiem nawigacyjnym i stopką:</span><span class="sxs-lookup"><span data-stu-id="c1459-116">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="c1459-117">W aplikacji opartej na jednym z Blazor szablonów aplikacji `MainLayout` składnik (*MainLayout. Razor*) znajduje się w folderze *udostępnionym* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1459-117">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="c1459-118">Układ domyślny</span><span class="sxs-lookup"><span data-stu-id="c1459-118">Default layout</span></span>

<span data-ttu-id="c1459-119">Określ domyślny układ aplikacji w `Router` składniku w pliku *App. Razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1459-119">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="c1459-120">Poniższy `Router` składnik, który jest dostarczany przez Blazor Szablony domyślne, ustawia domyślny układ `MainLayout` składnika:</span><span class="sxs-lookup"><span data-stu-id="c1459-120">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="c1459-121">Aby podać domyślny układ `NotFound` zawartości, określ `LayoutView` dla `NotFound` zawartości:</span><span class="sxs-lookup"><span data-stu-id="c1459-121">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="c1459-122">Aby uzyskać więcej informacji na temat `Router` składnika, zobacz <xref:blazor/routing> .</span><span class="sxs-lookup"><span data-stu-id="c1459-122">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="c1459-123">Określanie układu jako domyślnego układu w routerze jest przydatnym rozwiązaniem, ponieważ może być zastąpione dla poszczególnych składników lub folderów.</span><span class="sxs-lookup"><span data-stu-id="c1459-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="c1459-124">Preferuj użycie routera do ustawienia domyślnego układu aplikacji, ponieważ jest to najbardziej ogólna technika.</span><span class="sxs-lookup"><span data-stu-id="c1459-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="c1459-125">Określanie układu w składniku</span><span class="sxs-lookup"><span data-stu-id="c1459-125">Specify a layout in a component</span></span>

<span data-ttu-id="c1459-126">Użyj Razor dyrektywy, `@layout` Aby zastosować układ do składnika.</span><span class="sxs-lookup"><span data-stu-id="c1459-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="c1459-127">Kompilator konwertuje `@layout` do `LayoutAttribute` , który jest stosowany do klasy składnika.</span><span class="sxs-lookup"><span data-stu-id="c1459-127">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="c1459-128">Zawartość następującego `MasterList` składnika jest wstawiana do `MasterLayout` pozycji `@Body` :</span><span class="sxs-lookup"><span data-stu-id="c1459-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="c1459-129">Określanie układu bezpośrednio w składniku zastępuje domyślny zestaw *układów* w routerze lub `@layout` dyrektywę zaimportowaną z *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="c1459-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="c1459-130">Scentralizowany wybór układu</span><span class="sxs-lookup"><span data-stu-id="c1459-130">Centralized layout selection</span></span>

<span data-ttu-id="c1459-131">Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="c1459-131">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="c1459-132">Kompilator zawiera dyrektywy określone w pliku Imports we wszystkich Razor szablonach w tym samym folderze i rekursywnie we wszystkich jego podfolderach.</span><span class="sxs-lookup"><span data-stu-id="c1459-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="c1459-133">W związku z tym plik *_Imports. Razor* zawiera `@layout MyCoolLayout` pewność, że wszystkie składniki w folderze są używane `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="c1459-133">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="c1459-134">Nie trzeba wielokrotnie dodawać `@layout MyCoolLayout` do wszystkich plików *Razor* w folderze i podfolderach.</span><span class="sxs-lookup"><span data-stu-id="c1459-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="c1459-135">`@using`dyrektywy są również stosowane do składników w ten sam sposób.</span><span class="sxs-lookup"><span data-stu-id="c1459-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="c1459-136">Następujące *_Imports.* Importy pliku Razor:</span><span class="sxs-lookup"><span data-stu-id="c1459-136">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="c1459-137">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="c1459-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="c1459-138">Wszystkie Razor składniki w tym samym folderze i wszystkie podfoldery.</span><span class="sxs-lookup"><span data-stu-id="c1459-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="c1459-139">`BlazorApp1.Data`Przestrzeń nazw.</span><span class="sxs-lookup"><span data-stu-id="c1459-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="c1459-140">Plik *_Imports. Razor* jest podobny do [pliku _ViewImports. cshtml dla Razor widoków i stron](xref:mvc/views/layout#importing-shared-directives) , ale jest stosowany w odniesieniu do Razor plików składników.</span><span class="sxs-lookup"><span data-stu-id="c1459-140">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="c1459-141">Określanie układu w *_Imports. Razor* przesłania układ określony jako *domyślny układ*routera.</span><span class="sxs-lookup"><span data-stu-id="c1459-141">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="c1459-142">Układy zagnieżdżone</span><span class="sxs-lookup"><span data-stu-id="c1459-142">Nested layouts</span></span>

<span data-ttu-id="c1459-143">Aplikacje mogą składać się z zagnieżdżonych układów.</span><span class="sxs-lookup"><span data-stu-id="c1459-143">Apps can consist of nested layouts.</span></span> <span data-ttu-id="c1459-144">Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu.</span><span class="sxs-lookup"><span data-stu-id="c1459-144">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="c1459-145">Na przykład zagnieżdżanie układów służy do tworzenia struktury menu wielopoziomowego.</span><span class="sxs-lookup"><span data-stu-id="c1459-145">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="c1459-146">Poniższy przykład pokazuje, jak używać układów zagnieżdżonych.</span><span class="sxs-lookup"><span data-stu-id="c1459-146">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="c1459-147">Plik *EpisodesComponent. Razor* jest składnikiem do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="c1459-147">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="c1459-148">Składnik odwołuje się do `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="c1459-148">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="c1459-149">Plik *MasterListLayout. Razor* zawiera `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="c1459-149">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="c1459-150">Układ odwołuje się do innego układu, `MasterLayout` w którym jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="c1459-150">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="c1459-151">`EpisodesComponent`jest renderowany w miejscu, gdzie `@Body` pojawia się:</span><span class="sxs-lookup"><span data-stu-id="c1459-151">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="c1459-152">Na koniec `MasterLayout` w *MasterLayout. Razor* zawiera elementy układu najwyższego poziomu, takie jak nagłówek, menu główne i stopka.</span><span class="sxs-lookup"><span data-stu-id="c1459-152">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="c1459-153">`MasterListLayout`z `EpisodesComponent` renderowanym miejscem, `@Body` gdzie pojawia się:</span><span class="sxs-lookup"><span data-stu-id="c1459-153">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="c1459-154">Udostępnianie Razor układu stron ze składnikami zintegrowanymi</span><span class="sxs-lookup"><span data-stu-id="c1459-154">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="c1459-155">Gdy składniki routingu są zintegrowane Razor z aplikacją Pages, można używać współużytkowanego układu aplikacji ze składnikami.</span><span class="sxs-lookup"><span data-stu-id="c1459-155">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="c1459-156">Aby uzyskać więcej informacji, zobacz <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="c1459-156">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1459-157">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c1459-157">Additional resources</span></span>

* <xref:mvc/views/layout>
