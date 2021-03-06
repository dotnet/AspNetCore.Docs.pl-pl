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
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor układy

Niektóre elementy aplikacji, takie jak menu, wiadomości o prawach autorskich i logo firmy, są zwykle częścią ogólnej prezentacji aplikacji. Umieszczenie kopii znaczników dla tych elementów we wszystkich składnikach aplikacji nie jest wydajne. Za każdym razem, gdy jeden z tych elementów jest aktualizowany, każdy składnik, który używa tego elementu, musi zostać zaktualizowany. To podejście jest kosztowne do utrzymania i może prowadzić do niespójnej zawartości w przypadku braku aktualizacji. *Układy* rozwiązują te problemy.

BlazorUkład to Razor składnik, który współużytkuje znaczniki ze składnikami, które odwołują się do niego. Układy mogą używać [powiązań danych](xref:blazor/components/data-binding), [iniekcji zależności](xref:blazor/fundamentals/dependency-injection)i innych funkcji składników.

## <a name="layout-components"></a>Składniki układu

### <a name="create-a-layout-component"></a>Tworzenie składnika układu

Aby utworzyć składnik układu:

* Utwórz Razor składnik zdefiniowany przez Razor szablon lub kod C#. Składniki układu oparte na Razor szablonie używają `.razor` rozszerzenia pliku podobnie jak zwykłe Razor składniki. Ze względu na to, że składniki układu są udostępniane między składnikami aplikacji, są zwykle umieszczane w `Shared` folderze aplikacji. Układy można jednak umieścić w dowolnej lokalizacji dostępnej dla składników, które go używają. Na przykład układ może być umieszczony w tym samym folderze co składniki, które go używają.
* Dziedzicz składnik <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> . <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>Definiuje <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> Właściwość ( <xref:Microsoft.AspNetCore.Components.RenderFragment> Typ) dla renderowanej zawartości wewnątrz układu.
* Użyj Razor składni, `@Body` Aby określić lokalizację w znacznikach układu, w którym jest renderowana zawartość.

Poniższy `DoctorWhoLayout` składnik pokazuje Razor szablon składnika układu. Układ dziedziczy <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> i ustawia `@Body` między paskiem nawigacyjnym ( `<nav>...</nav>` ) i stopką ( `<footer>...</footer>` ).

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a>`MainLayout` cm6long

W aplikacji utworzonej na podstawie [ Blazor szablonu projektu](xref:blazor/project-structure) `MainLayout` składnik jest [domyślnym układem](#apply-a-default-layout-to-an-app)aplikacji.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

[ Blazor Funkcja izolacji CSS](xref:blazor/components/css-isolation) stosuje odizolowane Style CSS do `MainLayout` składnika. Zgodnie z Konwencją, style są dostarczane przez towarzyszący arkusz stylów o tej samej nazwie `Shared/MainLayout.razor.css` . Implementacja ASP.NET Core Framework arkusza stylów jest dostępna do inspekcji w [ASP.NET Core źródle odwołania (repozytorium dotnet/aspnetcore w witrynie GitHub)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a>Zastosuj Układ

### <a name="apply-a-layout-to-a-component"></a>Zastosuj układ do składnika

Użyj [`@layout`](xref:mvc/views/razor#layout) Razor dyrektywy, aby zastosować układ do Razor składnika obsługującego Routing, który ma [`@page`](xref:mvc/views/razor#page) dyrektywę. Kompilator konwertuje do `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> i stosuje atrybut do klasy składnika.

Zawartość następującego `Episodes` składnika jest wstawiana do obiektu `DoctorWhoLayout` na pozycji `@Body` .

`Pages/Episodes.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

Następujące renderowane znaczniki HTML są tworzone przez poprzednią `DoctorWhoLayout` i `Episodes` składnik. Niezależne znaczniki nie są wyświetlane, aby skoncentrować się na zawartości dostarczonej przez dwa objęte składniki:

* **Lekarz, który ma nagłówek &trade; bazy danych** ( `<h1>...</h1>` ) w nagłówku ( `<header>...</header>` ), pasku nawigacyjnym ( `<nav>...</nav>` ) i elemencie informacji o znakach towarowych ( `<div>...</div>` ) w stopce () `<footer>...</footer>` pochodzi ze `DoctorWhoLayout` składnika.
* Pozycja **odcinków** ( `<h2>...</h2>` ) i lista epizodów ( `<ul>...</ul>` ) pochodzą z `Episodes` składnika.

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

Określanie układu bezpośrednio w składniku przesłania *domyślny układ*:

* Ustawiana przez `@layout` dyrektywę zaimportowaną ze `_Imports` składnika ( `_Imports.razor` ), zgodnie z opisem w poniższej sekcji [Zastosuj układ do folderu składników](#apply-a-layout-to-a-folder-of-components) .
* Ustaw jako domyślny układ aplikacji, zgodnie z opisem w sekcji [Zastosuj Układ domyślny do aplikacji](#apply-a-default-layout-to-an-app) w dalszej części tego artykułu.

### <a name="apply-a-layout-to-a-folder-of-components"></a>Zastosuj układ do folderu składników

Każdy folder aplikacji może opcjonalnie zawierać plik szablonu o nazwie `_Imports.razor` . Kompilator zawiera dyrektywy określone w pliku Imports we wszystkich Razor szablonach w tym samym folderze i rekursywnie we wszystkich jego podfolderach. W związku z tym `_Imports.razor` plik zawierający `@layout DoctorWhoLayout` pewność, że wszystkie składniki w folderze używają `DoctorWhoLayout` składnika. Nie trzeba wielokrotnie dodawać `@layout DoctorWhoLayout` do wszystkich Razor składników ( `.razor` ) w folderze i podfolderach.

`_Imports.razor`:

```razor
@layout DoctorWhoLayout
...
```

`_Imports.razor`Plik jest podobny do [pliku _ViewImports. cshtml dla Razor widoków i stron](xref:mvc/views/layout#importing-shared-directives) , ale jest stosowany w odniesieniu do Razor plików składników.

Określanie układu w programie `_Imports.razor` przesłania układ określony jako [domyślny układ aplikacji](#apply-a-default-layout-to-an-app)routera, który jest opisany w następnej sekcji.

> [!WARNING]
> **Nie** dodawaj Razor `@layout` dyrektywy do `_Imports.razor` pliku głównego, co powoduje nieskończoną pętlę układów. Aby kontrolować domyślny układ aplikacji, określ układ w `Router` składniku. Aby uzyskać więcej informacji, zobacz następujące [zastosowania układu domyślnego do aplikacji](#apply-a-default-layout-to-an-app) .

> [!NOTE]
> [`@layout`](xref:mvc/views/razor#layout) Razor Dyrektywa stosuje tylko układ do routingu Razor składników za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.

### <a name="apply-a-default-layout-to-an-app"></a>Stosowanie układu domyślnego do aplikacji

Określ domyślny układ aplikacji w `App` <xref:Microsoft.AspNetCore.Components.Routing.Router> składniku składnika. Poniższy przykład z aplikacji opartej na [ Blazor szablonie projektu](xref:blazor/project-structure) ustawia domyślny układ `MainLayout` składnika.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika, zobacz <xref:blazor/fundamentals/routing> .

Określanie układu jako domyślnego układu w `Router` składniku jest użyteczną metodą, ponieważ można przesłonić układ dla poszczególnych składników lub folderów, zgodnie z opisem w poprzednich sekcjach tego artykułu. Zalecamy używanie `Router` składnika do ustawienia domyślnego układu aplikacji, ponieważ jest to najbardziej ogólne i elastyczne podejście do korzystania z układów.

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a>Zastosuj układ do dowolnej zawartości ( `LayoutView` składnik)

Aby ustawić układ dla dowolnej Razor zawartości szablonu, określ układ ze <xref:Microsoft.AspNetCore.Components.LayoutView> składnikiem. Można użyć <xref:Microsoft.AspNetCore.Components.LayoutView> w dowolnym Razor składniku. Poniższy przykład ustawia składnik układu o nazwie `ErrorLayout` dla `MainLayout` <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> szablonu składnika ( `<NotFound>...</NotFound>` ).

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a>Układy zagnieżdżone

Składnik może odwoływać się do układu, który z kolei odwołuje się do innego układu. Na przykład, układy zagnieżdżone są używane do tworzenia struktur menu wielopoziomowego.

Poniższy przykład pokazuje, jak używać układów zagnieżdżonych. `Episodes`Składnik wyświetlany w sekcji [Zastosuj układ do składnika](#apply-a-layout-to-a-component) jest składnikiem do wyświetlenia. Składnik odwołuje się do `DoctorWhoLayout` składnika.

Poniższy `DoctorWhoLayout` składnik jest zmodyfikowaną wersją przykładu przedstawioną wcześniej w tym artykule. Elementy nagłówka i stopki są usuwane, a układ odwołuje się do innego układu `ProductionsLayout` . `Episodes`Składnik jest renderowany, gdzie `@Body` pojawia się w `DoctorWhoLayout` .

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

`ProductionsLayout`Składnik zawiera elementy układu najwyższego poziomu, w których `<header>...</header>` teraz znajdują się elementy Header () i stopka ( `<footer>...</footer>` ). `DoctorWhoLayout` `Episodes` Element with jest renderowany, gdzie `@Body` pojawia się.

`Shared/ProductionsLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

Poprzednia zagnieżdżony układ jest tworzony za pomocą powyższego kodu HTML. Niezależne znaczniki nie są wyświetlane w celu skoncentrowania się na zagnieżdżonej zawartości dostarczonej przez trzy składniki:

* Element Header ( `<header>...</header>` ), produkcyjny pasek nawigacyjny ( `<nav>...</nav>` ) i stopka ( `<footer>...</footer>` ) i ich zawartość pochodzą ze `ProductionsLayout` składnika.
* Lekarz, który przejdzie z nagłówka **&trade; bazy danych** ( `<h1>...</h1>` ), pasek nawigacyjny odcinka ( `<nav>...</nav>` ) i element informacji o znakach towarowych ( `<div>...</div>` ) pochodzi od `DoctorWhoLayout` składnika.
* Pozycja **odcinków** ( `<h2>...</h2>` ) i lista epizodów ( `<ul>...</ul>` ) pochodzą z `Episodes` składnika.

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

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Udostępnianie Razor układu stron ze składnikami zintegrowanymi

Gdy składniki routingu są zintegrowane Razor z aplikacją Pages, można używać współużytkowanego układu aplikacji ze składnikami. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/prerendering-and-integration>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:mvc/views/layout>
