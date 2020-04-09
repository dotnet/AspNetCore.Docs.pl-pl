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
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.NET układy Blazor rdzenia

Przez [Rainer Stropek](https://www.timecockpit.com) i [Luke Latham](https://github.com/guardrex)

Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnego układu aplikacji i są używane przez każdy składnik w aplikacji. Kopiowanie kodu tych elementów do wszystkich składników aplikacji nie jest&mdash;skuteczne podejście za każdym razem, gdy jeden z elementów wymaga aktualizacji, każdy składnik musi zostać zaktualizowany. Takie powielanie jest trudne do utrzymania i może prowadzić do niespójnej zawartości w czasie. *Układy* rozwiązują ten problem.

Technicznie układ jest po prostu kolejnym elementem. Układ jest zdefiniowany w szablonie Razor lub w kodzie Języka C# i może używać [powiązania danych,](xref:blazor/data-binding) [iniekcji zależności](xref:blazor/dependency-injection)i innych scenariuszy składników.

Aby przekształcić *komponent* w *układ*, komponent:

* Dziedziczy `LayoutComponentBase`z , który `Body` definiuje właściwość dla renderowanych treści wewnątrz układu.
* Używa składni `@Body` Razor, aby określić lokalizację w znacznikach układu, w której zawartość jest renderowana.

Poniższy przykład kodu przedstawia szablon Razor składnika *układu, MainLayout.brzytwa*. Układ dziedziczy `LayoutComponentBase` i `@Body` ustawia między paskiem nawigacyjnym a stopki:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

W aplikacji opartej na Blazor jednym z `MainLayout` szablonów aplikacji składnik *(MainLayout.brzytwa)* znajduje się w folderze *udostępnionym* aplikacji.

## <a name="default-layout"></a>Układ domyślny

Określ domyślny układ `Router` aplikacji w składniku w pliku *App.razor* aplikacji. Następujący `Router` składnik, który jest dostarczany Blazor przez szablony domyślne, `MainLayout` ustawia domyślny układ na składnik:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Aby podać domyślny `NotFound` układ zawartości, określ `LayoutView` zawartość: `NotFound`

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Aby uzyskać więcej `Router` informacji <xref:blazor/routing>na temat komponentu, zobacz .

Określenie układu jako układu domyślnego w routerze jest użyteczną praktyką, ponieważ można go zastąpić na podstawie składnika lub dla folderu. Wolisz używać routera, aby ustawić domyślny układ aplikacji, ponieważ jest to najbardziej ogólna technika.

## <a name="specify-a-layout-in-a-component"></a>Określanie układu w komponencie

Użyj dyrektywy `@layout` Razor, aby zastosować układ do składnika. Kompilator `@layout` konwertuje `LayoutAttribute`na , który jest stosowany do klasy składnika.

Zawartość następującego `MasterList` komponentu jest wstawiana `MasterLayout` `@Body`do pozycji:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Określenie układu bezpośrednio w komponencie zastępuje *domyślny układ* ustawiony `@layout` w routerze lub dyrektywę zaimportowany z *_Imports.brzytwa*.

## <a name="centralized-layout-selection"></a>Wybór układu scentralizowanego

Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie *_Imports.razor*. Kompilator zawiera dyrektywy określone w pliku importu we wszystkich szablonach Razor w tym samym folderze i rekursywnie we wszystkich jego podfolderach. W związku z tym *plik _Imports.brzytwa* zawierający `@layout MyCoolLayout` zapewnia, że `MyCoolLayout`wszystkie składniki w folderze używać . Nie ma potrzeby wielokrotnego `@layout MyCoolLayout` dodawania do wszystkich plików *.brzytwa* w folderze i podfolderach. `@using`dyrektywy są również stosowane do komponentów w ten sam sposób.

Importowanie plików *_Imports.brzytwa:*

* `MyCoolLayout`.
* Wszystkie komponenty Razor w tym samym folderze i wszystkie podfoldery.
* Obszar `BlazorApp1.Data` nazw.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Plik *_Imports.razor* jest podobny do [pliku _ViewImports.cshtml dla widoków i stron Razor,](xref:mvc/views/layout#importing-shared-directives) ale stosuje się specjalnie do plików komponentów Razor.

Określenie układu w *_Imports.razor* zastępuje układ określony jako *domyślny układ*routera .

## <a name="nested-layouts"></a>Układy zagnieżdżone

Aplikacje mogą składać się z układów zagnieżdżonych. Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu. Na przykład układy zagnieżdżania są używane do tworzenia wielopoziomowej struktury menu.

W poniższym przykładzie pokazano, jak używać układów zagnieżdżonych. *Plik EpisodesComponent.razor* jest składnikiem do wyświetlenia. Komponent odwołuje się `MasterListLayout`do:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Plik *MasterListLayout.razor* zapewnia `MasterListLayout`plik . Układ odwołuje się do `MasterLayout`innego układu, gdzie jest renderowany. `EpisodesComponent`jest renderowany `@Body` w miejscu, w którym się pojawia:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Na koniec `MasterLayout` w *masterlayout.brzytwa* zawiera elementy układu najwyższego poziomu, takie jak nagłówek, menu główne i stopka. `MasterListLayout`z `EpisodesComponent` renderowanym renderem, w którym `@Body` się pojawia:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Udostępnianie układu Strony Razor ze zintegrowanymi komponentami

Gdy składniki rutowalne są zintegrowane z aplikacją Razor Pages, udostępnionego układu aplikacji można używać ze składnikami. Aby uzyskać więcej informacji, zobacz <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/layout>
