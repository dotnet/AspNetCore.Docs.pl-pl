---
title: ASP.NET Core Blazor układy
author: guardrex
description: Dowiedz się, jak tworzyć składniki układu wielokrotnego użytku dla Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
ms.openlocfilehash: d1f3e2028ca120b5901aca0b24802d872ae52597
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279733"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor układy

Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnego układu aplikacji i są używane przez każdy składnik w aplikacji. Kopiowanie kodu tych elementów do wszystkich składników aplikacji nie jest efektywnym podejściem. Za każdym razem, gdy jeden z elementów wymaga aktualizacji, należy zaktualizować każdy składnik. Taka duplikacja jest trudna do utrzymania i może prowadzić do niespójnej zawartości z upływem czasu. *Układy* rozwiązują ten problem.

Technicznie, układ jest tylko innym składnikiem. Układ jest zdefiniowany w Razor szablonie lub w kodzie C# i może używać [powiązań danych](xref:blazor/components/data-binding), [iniekcji zależności](xref:blazor/fundamentals/dependency-injection)i innych scenariuszy składników. Układy stosują się tylko do składników rutowanych Razor , które mają [`@page`](xref:mvc/views/razor#page) dyrektywy.

Aby skonwertować składnik do układu:

* Dziedzicz składnik <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> . <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Definiuje <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> Właściwość dla renderowanej zawartości wewnątrz układu.
* Użyj Razor składni, `@Body` Aby określić lokalizację w znacznikach układu, w którym jest renderowana zawartość.

Poniższy przykład kodu pokazuje Razor szablon składnika układu `MainLayout.razor` . Układ dziedziczy <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> i ustawia `@Body` między paskiem nawigacyjnym i stopką:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a>`MainLayout` cm6long

W aplikacji opartej na jednym z Blazor szablonów projektu `MainLayout` składnik ( `MainLayout.razor` ) znajduje się w `Shared` folderze aplikacji:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a>Układ domyślny

Określ domyślny układ aplikacji w <xref:Microsoft.AspNetCore.Components.Routing.Router> składniku w `App.razor` pliku aplikacji. Poniższy <xref:Microsoft.AspNetCore.Components.Routing.Router> składnik, który jest dostarczany przez Blazor Szablony domyślne, ustawia domyślny układ `MainLayout` składnika:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Aby podać domyślny układ <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, określ <xref:Microsoft.AspNetCore.Components.LayoutView> dla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika, zobacz <xref:blazor/fundamentals/routing> .

Określanie układu jako domyślnego układu w routerze jest przydatnym rozwiązaniem, ponieważ może być zastąpione dla poszczególnych składników lub folderów. Preferuj użycie routera do ustawienia domyślnego układu aplikacji, ponieważ jest to najbardziej ogólna technika.

## <a name="specify-a-layout-in-a-component"></a>Określanie układu w składniku

Użyj [`@layout`](xref:mvc/views/razor#layout) Razor dyrektywy, aby zastosować układ do Razor składnika obsługującego Routing, który ma także [`@page`](xref:mvc/views/razor#page) dyrektywę. Kompilator konwertuje `@layout` do <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , który jest stosowany do klasy składnika.

Zawartość następującego `MasterList` składnika jest wstawiana do `MasterLayout` pozycji `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Określanie układu bezpośrednio w składniku zastępuje domyślny zestaw *układów* w routerze lub `@layout` dyrektywę zaimportowaną z `_Imports.razor` .

## <a name="centralized-layout-selection"></a>Scentralizowany wybór układu

Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie `_Imports.razor` . Kompilator zawiera dyrektywy określone w pliku Imports we wszystkich Razor szablonach w tym samym folderze i rekursywnie we wszystkich jego podfolderach. W związku z tym `_Imports.razor` plik zawierający `@layout MyCoolLayout` wszystkie składniki w folderze jest używany `MyCoolLayout` . Nie trzeba wielokrotnie dodawać `@layout MyCoolLayout` do wszystkich `.razor` plików w folderze i podfolderach. `@using` dyrektywy są również stosowane do składników w ten sam sposób.

Następujące `_Imports.razor` Importy plików:

* `MyCoolLayout`.
* Wszystkie Razor składniki w tym samym folderze i wszystkie podfoldery.
* `BlazorApp1.Data`Przestrzeń nazw.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

`_Imports.razor`Plik jest podobny do [pliku _ViewImports. cshtml dla Razor widoków i stron](xref:mvc/views/layout#importing-shared-directives) , ale jest stosowany w odniesieniu do Razor plików składników.

Określanie układu w programie `_Imports.razor` przesłania układ określony jako *domyślny układ* routera.

> [!WARNING]
> **Nie** dodawaj Razor `@layout` dyrektywy do `_Imports.razor` pliku głównego, co spowoduje nieskończoną pętlę układów w aplikacji. Aby kontrolować domyślny układ aplikacji, określ układ w `Router` składniku. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [układu domyślnego](#default-layout) .

> [!NOTE]
> [`@layout`](xref:mvc/views/razor#layout) Razor Dyrektywa stosuje tylko układ do obsługi Razor składników w [`@page`](xref:mvc/views/razor#page) dyrektywach.

## <a name="nested-layouts"></a>Układy zagnieżdżone

Aplikacje mogą składać się z zagnieżdżonych układów. Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu. Na przykład zagnieżdżanie układów służy do tworzenia struktury menu wielopoziomowego.

Poniższy przykład pokazuje, jak używać układów zagnieżdżonych. `EpisodesComponent.razor`Plik jest składnikiem do wyświetlenia. Składnik odwołuje się do `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

`MasterListLayout.razor`Plik zawiera `MasterListLayout` . Układ odwołuje się do innego układu, `MasterLayout` w którym jest renderowany. `EpisodesComponent` jest renderowany w miejscu, gdzie `@Body` pojawia się:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Na koniec `MasterLayout` w programie `MasterLayout.razor` znajdują się elementy układu najwyższego poziomu, takie jak nagłówek, menu główne i stopka. `MasterListLayout` z `EpisodesComponent` renderowanym miejscem, `@Body` gdzie pojawia się:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Udostępnianie Razor układu stron ze składnikami zintegrowanymi

Gdy składniki routingu są zintegrowane Razor z aplikacją Pages, można używać współużytkowanego układu aplikacji ze składnikami. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/prerendering-and-integration>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/layout>
