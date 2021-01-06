---
title: BlazorZaawansowane scenariusze ASP.NET Core
author: guardrex
description: Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056559"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>BlazorZaawansowane scenariusze ASP.NET Core

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server Procedura obsługi obwodu

Blazor Server umożliwia kodowi Definiowanie *procedury obsługi obwodu*, która umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika. Procedura obsługi obwodu jest implementowana przez wyprowadzanie z `CircuitHandler` i rejestrowanie klasy w kontenerze usługi aplikacji. Poniższy przykład obsługi obwodu śledzi otwarte SignalR połączenia:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Procedury obsługi obwodu są rejestrowane przy użyciu funkcji DI. Wystąpienia w zakresie są tworzone na wystąpienie obwodu. Korzystając z `TrackingCircuitHandler` powyższego przykładu, tworzona jest usługa singleton, ponieważ stan wszystkich obwodów musi być śledzony:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Jeśli metody obsługi niestandardowego obwodu zgłaszają nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor Server obwodu. Aby tolerować wyjątki w kodzie programu obsługi lub metodach wywoływanych, zawiń kod w jednej lub kilku [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcjach z obsługą błędów i rejestrowaniem.

Gdy obwód kończy się, ponieważ użytkownik odłączył się i struktura czyści stan obwodu, struktura usuwa zakres DI obwodu. Oddysponowanie zakresu polega na usunięciu wszelkich usług DI-Scope w zakresie, które implementują <xref:System.IDisposable?displayProperty=fullName> . Jeśli jakakolwiek usługa nie zgłasza nieobsłużonego wyjątku podczas usuwania, struktura rejestruje wyjątek.

## <a name="manual-rendertreebuilder-logic"></a>Ręczna logika RenderTreeBuilder

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> zapewnia metody manipulowania składnikami i elementami, w tym ręczne Kompilowanie składników w kodzie C#.

> [!NOTE]
> Korzystanie z programu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> do tworzenia składników jest zaawansowanym scenariuszem. Nieprawidłowo sformułowany składnik (na przykład niezamknięty tag znacznika) może spowodować niezdefiniowane zachowanie.

Rozważmy następujący `PetDetails` składnik, który można ręcznie utworzyć w innym składniku:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

W poniższym przykładzie pętla w `CreateComponent` metodzie generuje trzy `PetDetails` składniki. W <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metodach z numerem sekwencyjnym numery sekwencji są numerami wierszy kodu źródłowego. BlazorAlgorytm różnic polega na numerach sekwencji odpowiadających odrębnym wierszom kodu, a nie odrębnym wywoływaniu wywołań. Podczas tworzenia składnika przy użyciu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod umieszczaj argumenty dla numerów sekwencji. **Użycie obliczenia lub licznika do wygenerowania numeru sekwencji może prowadzić do niskiej wydajności.** Aby uzyskać więcej informacji, zobacz sekcję [numery sekwencji powiązane z numerami wierszy kodu i kolejnością niewykonania](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent` składnika

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Typy w programie <xref:Microsoft.AspNetCore.Components.RenderTree> umożliwiają przetwarzanie *wyników* operacji renderowania. Są to wewnętrzne szczegóły Blazor implementacji platformy. Te typy powinny być uznawane za *niestabilne* i mogą ulec zmianie w przyszłych wersjach.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Numery sekwencji odnoszą się do numerów wierszy kodu, a nie kolejności wykonywania

Razor Pliki składników ( `.razor` ) są zawsze kompilowane. Kompilacja jest potencjalną możliwością przekroczenia interpretacji kodu, ponieważ krok kompilacji może służyć do iniekcji informacji, które zwiększają wydajność aplikacji w czasie wykonywania.

Najważniejszym przykładem tych ulepszeń są *numery sekwencji*. Numery sekwencji wskazują na środowisko uruchomieniowe, które pochodzą z różnych i uporządkowanych wierszy kodu. Środowisko uruchomieniowe używa tych informacji do generowania wydajnych różnic drzewa w czasie liniowym, które są znacznie szybsze niż zwykle jest to możliwe dla algorytmu różnicowego drzewa ogólnego.

Weź pod uwagę następujący Razor plik składnika ( `.razor` ):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Poprzedni kod kompiluje się w taki sposób, aby wyglądał następująco:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Gdy kod jest wykonywany po raz pierwszy, jeśli `someFlag` jest `true` , Konstruktor odbiera:

| Sequence | Typ      | Dane   |
| :------: | --------- | :----: |
| 0        | Węzeł tekstu | Pierwsze  |
| 1        | Węzeł tekstu | Second |

Wyobraź sobie `someFlag` , że zostanie ona `false` przerenderowana, a znaczniki są renderowane ponownie. Tym razem Konstruktor odbiera:

| Sequence | Typ       | Dane   |
| :------: | ---------- | :----: |
| 1        | Węzeł tekstu  | Second |

Gdy środowisko uruchomieniowe wykonuje porównanie, zobaczy, że element w sekwencji `0` został usunięty, więc generuje następujący *skrypt* uproszczonej edycji:

* Usuń pierwszy węzeł tekstu.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problem z programowo generowanymi numerami sekwencji

Wyobraź sobie, że została zapisana następująca logika konstruktora drzewa renderowania:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Teraz pierwsze dane wyjściowe to:

| Sequence | Typ      | Dane   |
| :------: | --------- | :----: |
| 0        | Węzeł tekstu | Pierwsze  |
| 1        | Węzeł tekstu | Second |

Ten wynik jest identyczny z poprzednim przypadkiem, dlatego nie istnieją żadne negatywne problemy. `someFlag` znajduje się `false` na drugim renderingu, a dane wyjściowe:

| Sequence | Typ      | Dane   |
| :------: | --------- | ------ |
| 0        | Węzeł tekstu | Second |

Tym razem algorytm diff widzi, że pojawiły się *dwie* zmiany, a algorytm generuje następujący skrypt edycji:

* Zmień wartość pierwszego węzła tekstowego na `Second` .
* Usuń drugi węzeł tekstu.

Generowanie numerów sekwencji utraciło wszystkie przydatne informacje o tym, gdzie `if/else` znajdują się gałęzie i pętle w oryginalnym kodzie. Wynikiem tego jest różnica **dwa razy** , tak długo, jak wcześniej.

Jest to prosty przykład. W bardziej realistycznych przypadkach ze złożonymi i głęboko zagnieżdżonymi strukturami, w szczególności z pętlami, koszt wydajności jest zwykle wyższy. Zamiast natychmiastowego identyfikowania, które bloki lub gałęzie pętli zostały wstawione lub usunięte, algorytm diff musi odnosił się do drzewa renderowania. Zwykle polega to na konieczności kompilowania dłużej edytowanych skryptów, ponieważ algorytm różnicowy jest niewiadome o tym, jak stare i nowe struktury odnoszą się do siebie.

### <a name="guidance-and-conclusions"></a>Wskazówki i wnioski

* Wydajność aplikacji ma wpływ na to, że numery sekwencji są generowane dynamicznie.
* Struktura nie może automatycznie tworzyć własnych numerów sekwencji w czasie wykonywania, ponieważ niezbędne informacje nie istnieją, chyba że są przechwytywane w czasie kompilacji.
* Nie zapisuj długich bloków logiki wykonywanej ręcznie <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> . Preferuj `.razor` pliki i Zezwalaj kompilatorowi na rozpatruje numery sekwencji. Jeśli nie możesz uniknąć ręcznej <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logiki, Podziel długie bloki kodu na mniejsze fragmenty opakowane <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> . Każdy region ma własne oddzielne miejsce numerów sekwencyjnych, więc można uruchomić ponownie od zera (lub dowolnego innego numeru) w każdym regionie.
* Jeśli numery sekwencji są stałee, algorytm diff wymaga tylko zwiększenia wartości sekwencji. Początkowa wartość i przerwy są nieistotne. Jedną z wiarygodnych opcji jest użycie numeru wiersza kodu jako numeru sekwencyjnego lub rozpoczęcie od zera i zwiększenie według wartości lub setek (lub dowolnego preferowanego interwału). 
* Blazor używa numerów sekwencji, podczas gdy inne struktury interfejsu użytkownika rozróżniania drzewa nie są używane. Różnica jest znacznie szybsza, gdy są używane numery sekwencyjne i Blazor ma zalety krok kompilacji, który zajmuje się automatycznie numerami sekwencyjnymi dla deweloperów tworzących `.razor` pliki.
