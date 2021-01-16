---
title: BlazorRenderowanie składników ASP.NET Core
author: guardrex
description: Dowiedz się więcej o Razor renderowaniu składników w Blazor aplikacjach ASP.NET Core, w tym o czasie wywoływania StateHasChanged.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253975"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a>BlazorRenderowanie składników ASP.NET Core

[Steve Sanderson](https://github.com/SteveSandersonMS)

Składniki *muszą być* renderowane, gdy są one najpierw dodawane do hierarchii składników przez ich składnik nadrzędny. Jest to jedyny czas, który musi być renderowany przez składnik.

Składniki *mogą* wybrać opcję renderowania w dowolnym innym czasie zgodnie z ich własnymi regułami i konwencjami.

## <a name="conventions-for-componentbase"></a>Konwencje dla `ComponentBase`

Domyślnie Razor składniki ( `.razor` ) dziedziczą z <xref:Microsoft.AspNetCore.Components.ComponentBase> klasy podstawowej, która zawiera logikę do wyzwalania ponownej renderowania w następujących godzinach:

* Po zastosowaniu zaktualizowanego zestawu parametrów ze składnika nadrzędnego.
* Po zastosowaniu zaktualizowanej wartości dla parametru kaskadowego.
* Po powiadomieniu o zdarzeniu i wywołaniu jednego z jego własnych programów obsługi zdarzeń.
* Po wywołaniu własnej <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> metody.

Składniki dziedziczone z <xref:Microsoft.AspNetCore.Components.ComponentBase> pomijania są przerenderowane z powodu aktualizacji parametrów, jeśli spełniony jest jeden z następujących warunków:

* Wszystkie wartości parametrów są znanymi niezmiennymi typami pierwotnymi (na przykład, `int` , `string` , `DateTime` ) i nie zostały zmienione od momentu ustawienia poprzedniego zestawu parametrów.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>Metoda składnika zwraca wartość `false` .

Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , zobacz <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender> .

## <a name="control-the-rendering-flow"></a>Sterowanie przepływem renderowania

W większości przypadków <xref:Microsoft.AspNetCore.Components.ComponentBase> konwencje powodują poprawność podzbioru składnika po wystąpieniu zdarzenia. Deweloperzy nie są zwykle zobowiązani do zapewnienia ręcznej logiki, aby określić, które składniki mają być renderowane i kiedy mają być renderowane. Ogólny wpływ konwencji platformy polega na tym, że składnik otrzymujący zdarzenie rerenderuje siebie, co rekurencyjnie wyzwala odwzorowanie składników podrzędnych, których wartości parametrów mogły zostać zmienione.

Aby uzyskać więcej informacji o wpływie na wydajność konwencji platformy i sposobach optymalizacji hierarchii składników aplikacji, zobacz <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> .

## <a name="when-to-call-statehaschanged"></a>Kiedy należy wywołać `StateHasChanged`

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>Metoda umożliwia wyzwolenie renderowania w dowolnym momencie. Jednak należy zachować ostrożność, aby nie wywoływać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> niepotrzebnie, która jest powszechnym błędem, ponieważ nakłada niepotrzebne koszty renderowania.

*Nie* należy wywoływać, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> gdy:

* Rutynowe obsługiwanie zdarzeń, zarówno synchronicznie, jak i asynchronicznie, ponieważ <xref:Microsoft.AspNetCore.Components.ComponentBase> wyzwala Render dla większości rutynowych programów obsługi zdarzeń.
* Implementacja typowej logiki cyklu życia, takiej jak [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) lub [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) , synchonrously lub asynchronicznie, od momentu <xref:Microsoft.AspNetCore.Components.ComponentBase> wyzwalania renderowania dla typowych zdarzeń cyklu życia.

Jednak warto wywoływać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> w przypadku przypadków opisanych w następujących sekcjach:

* [Procedura obsługi asynchronicznej obejmuje wiele faz asynchronicznych](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Otrzymywanie wywołania od czegoś zewnętrznego do Blazor systemu obsługi renderowania i zdarzeń](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Aby renderować składnik spoza poddrzewa, które jest renderowane przez określone zdarzenie](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Procedura obsługi asynchronicznej obejmuje wiele faz asynchronicznych

Rozważmy następujący `Counter` składnik, który aktualizuje liczbę cztery razy przy każdym kliknięciu.

`Pages/Counter.razor`:

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

Ze względu na sposób, w jaki zadania są zdefiniowane w programie .NET, odbiorca <xref:System.Threading.Tasks.Task> może obserwować tylko ostateczne zakończenie, a nie pośrednie Stany asynchroniczne. W związku z tym, <xref:Microsoft.AspNetCore.Components.ComponentBase> można wyzwolić ponowne renderowanie tylko wtedy, gdy <xref:System.Threading.Tasks.Task> jest on zwracany po raz pierwszy i kiedy <xref:System.Threading.Tasks.Task> kończy. Nie może on wiedzieć, aby przerenderować w innych punktach pośrednich. Jeśli chcesz, aby były renderowane w punktach pośrednich, użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a>Otrzymywanie wywołania od czegoś zewnętrznego do Blazor systemu obsługi renderowania i zdarzeń

<xref:Microsoft.AspNetCore.Components.ComponentBase> wie tylko o własnych metodach cyklu życia i Blazor zdarzeniach wyzwalanych przez użytkownika. <xref:Microsoft.AspNetCore.Components.ComponentBase> nie wie o innych zdarzeniach, które mogą wystąpić w kodzie. Na przykład wszystkie zdarzenia w języku C# wywoływane przez niestandardowy magazyn danych są nieznane do Blazor . Aby zdarzenia wyzwalające ponowne renderowanie w celu wyświetlenia zaktualizowanych wartości w interfejsie użytkownika, użyj polecenia <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .

W innym przypadku użycia należy wziąć pod uwagę Poniższy `Counter` składnik, który używa <xref:System.Timers.Timer?displayProperty=fullName> programu, aby zaktualizować licznik w regularnych odstępach czasu i wywołania <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> w celu zaktualizowania interfejsu użytkownika.

`Pages/Counter.razor`:

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

W poprzednim przykładzie, `OnTimerCallback` musi wywołać, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ponieważ Blazor nie ma informacji o zmianach `currentCount` w wywołaniu wywołania zwrotnego. `OnTimerCallback` działa poza Blazor zarządzanym przepływem renderowania lub powiadomieniem o zdarzeniu.

Podobnie, ponieważ wywołanie zwrotne jest wywoływane poza Blazor kontekstem synchronizacji, konieczne jest Zawijanie logiki w programie, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> Aby przenieść ją do kontekstu synchronizacji modułu renderowania. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> można wywołać tylko z kontekstu synchronizacji modułu renderowania i zgłasza wyjątek w przeciwnym razie. Jest to równoważne kierowaniu do wątku interfejsu użytkownika w innych strukturach interfejsu użytkownika.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Aby renderować składnik spoza poddrzewa, które jest renderowane przez określone zdarzenie

Interfejs użytkownika może wymagać wysłania zdarzenia do jednego składnika, zmiany pewnego stanu i konieczności odwzorowania całkowicie innego składnika, który nie jest obiektem podrzędnym jednego z nich.

Jednym ze sposobów na zaradzenie sobie z tym scenariuszem jest posiadanie klasy *zarządzania stanem* , takiej jak di usługa, która jest wstrzykiwana do wielu składników. Gdy jeden składnik wywołuje metodę w Menedżerze stanu, Menedżer stanu może zgłosić zdarzenie języka C#, które następnie odbiera przez niezależny składnik.

Ponieważ te zdarzenia języka C# znajdują się poza Blazor potokiem renderowania, należy wywołać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> inne składniki, które mają być renderowane w odpowiedzi na zdarzenia Menedżera stanu.

Jest to podobne do wcześniejszego przypadku <xref:System.Timers.Timer?displayProperty=fullName> w sekcji w [poprzedniej](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) sekcji. Ponieważ stos wywołań wykonywania zwykle pozostaje w kontekście synchronizacji modułu renderowania, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> nie jest zazwyczaj wymagany. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> jest wymagana tylko wtedy, gdy logika wyprowadza kontekst synchronizacji, na przykład wywołanie metody <xref:System.Threading.Tasks.Task.ContinueWith%2A> <xref:System.Threading.Tasks.Task> lub oczekiwanie na <xref:System.Threading.Tasks.Task> [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .
