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
ms.openlocfilehash: 5c6771dd7249bfb8280ba20e1ce75967f279971c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771588"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor układy

Autorzy [Rainer Stropek](https://www.timecockpit.com) i [Luke Latham](https://github.com/guardrex)

Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnego układu aplikacji i są używane przez każdy składnik w aplikacji. Kopiowanie kodu tych elementów do wszystkich składników aplikacji nie jest skutecznym podejściem&mdash;za każdym razem, gdy jeden z elementów wymaga aktualizacji, należy zaktualizować każdy składnik. Taka duplikacja jest trudna do utrzymania i może prowadzić do niespójnej zawartości z upływem czasu. *Układy* rozwiązują ten problem.

Technicznie, układ jest tylko innym składnikiem. Układ jest zdefiniowany w Razor szablonie lub w kodzie C# i może używać [powiązań danych](xref:blazor/data-binding), [iniekcji zależności](xref:blazor/dependency-injection)i innych scenariuszy składników.

Aby przekształcić *składnik* do *układu*, składnik:

* Dziedziczy z `LayoutComponentBase`, który definiuje `Body` właściwość dla renderowanej zawartości wewnątrz układu.
* Używa Razor składni `@Body` do określenia lokalizacji w znaczniku układu, w którym jest renderowana zawartość.

Poniższy przykładowy kod przedstawia Razor szablon składnika układu, *MainLayout. Razor*. Układ dziedziczy `LayoutComponentBase` i ustawia `@Body` między paskiem nawigacyjnym i stopką:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

W aplikacji opartej na jednym z szablonów Blazor aplikacji `MainLayout` składnik (*MainLayout. Razor*) znajduje się w folderze *udostępnionym* aplikacji.

## <a name="default-layout"></a>Układ domyślny

Określ domyślny układ aplikacji w `Router` składniku w pliku *App. Razor* aplikacji. Poniższy `Router` składnik, który jest dostarczany przez szablony domyślne Blazor , ustawia domyślny układ `MainLayout` składnika:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Aby podać domyślny układ `NotFound` zawartości, określ `LayoutView` dla `NotFound` zawartości:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Aby uzyskać więcej informacji na `Router` temat składnika, <xref:blazor/routing>Zobacz.

Określanie układu jako domyślnego układu w routerze jest przydatnym rozwiązaniem, ponieważ może być zastąpione dla poszczególnych składników lub folderów. Preferuj użycie routera do ustawienia domyślnego układu aplikacji, ponieważ jest to najbardziej ogólna technika.

## <a name="specify-a-layout-in-a-component"></a>Określanie układu w składniku

Użyj dyrektywy Razor `@layout` , aby zastosować układ do składnika. Kompilator konwertuje `@layout` do `LayoutAttribute`, który jest stosowany do klasy składnika.

Zawartość następującego `MasterList` składnika jest wstawiana do `MasterLayout` pozycji: `@Body`

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Określanie układu bezpośrednio w składniku zastępuje domyślny zestaw *układów* w routerze lub `@layout` dyrektywę zaimportowaną z *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Scentralizowany wybór układu

Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie *_Imports. Razor*. Kompilator zawiera dyrektywy określone w pliku Imports we wszystkich Razor szablonach w tym samym folderze i rekursywnie we wszystkich jego podfolderach. W związku z tym plik *_Imports. Razor* zawiera `@layout MyCoolLayout` pewność, że wszystkie składniki w folderze są używane `MyCoolLayout`. Nie trzeba wielokrotnie dodawać `@layout MyCoolLayout` do wszystkich plików *Razor* w folderze i podfolderach. `@using`dyrektywy są również stosowane do składników w ten sam sposób.

Następujące *_Imports.* Importy pliku Razor:

* `MyCoolLayout`.
* Wszystkie Razor składniki w tym samym folderze i wszystkie podfoldery.
* `BlazorApp1.Data` Przestrzeń nazw.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Plik *_Imports. Razor* jest podobny do [pliku _ViewImports. cshtml dla Razor widoków i stron](xref:mvc/views/layout#importing-shared-directives) , ale jest stosowany w odniesieniu do Razor plików składników.

Określanie układu w *_Imports. Razor* przesłania układ określony jako *domyślny układ*routera.

## <a name="nested-layouts"></a>Układy zagnieżdżone

Aplikacje mogą składać się z zagnieżdżonych układów. Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu. Na przykład zagnieżdżanie układów służy do tworzenia struktury menu wielopoziomowego.

Poniższy przykład pokazuje, jak używać układów zagnieżdżonych. Plik *EpisodesComponent. Razor* jest składnikiem do wyświetlenia. Składnik odwołuje się `MasterListLayout`do:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Plik *MasterListLayout. Razor* zawiera `MasterListLayout`. Układ odwołuje się do innego `MasterLayout`układu, w którym jest renderowany. `EpisodesComponent`jest renderowany w `@Body` miejscu, gdzie pojawia się:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

`MasterLayout` Na koniec w *MasterLayout. Razor* zawiera elementy układu najwyższego poziomu, takie jak nagłówek, menu główne i stopka. `MasterListLayout`z renderowanym miejscem `EpisodesComponent` , `@Body` gdzie pojawia się:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Udostępnianie układu Razor stron ze składnikami zintegrowanymi

Gdy składniki routingu są zintegrowane z aplikacją Razor Pages, można używać współużytkowanego układu aplikacji ze składnikami. Aby uzyskać więcej informacji, zobacz <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/layout>
