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
# <a name="aspnet-core-no-locblazor-component-rendering"></a><span data-ttu-id="3128f-103">BlazorRenderowanie składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3128f-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="3128f-104">[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="3128f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="3128f-105">Składniki *muszą być* renderowane, gdy są one najpierw dodawane do hierarchii składników przez ich składnik nadrzędny.</span><span class="sxs-lookup"><span data-stu-id="3128f-105">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="3128f-106">Jest to jedyny czas, który musi być renderowany przez składnik.</span><span class="sxs-lookup"><span data-stu-id="3128f-106">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="3128f-107">Składniki *mogą* wybrać opcję renderowania w dowolnym innym czasie zgodnie z ich własnymi regułami i konwencjami.</span><span class="sxs-lookup"><span data-stu-id="3128f-107">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="3128f-108">Konwencje dla `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="3128f-108">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="3128f-109">Domyślnie Razor składniki ( `.razor` ) dziedziczą z <xref:Microsoft.AspNetCore.Components.ComponentBase> klasy podstawowej, która zawiera logikę do wyzwalania ponownej renderowania w następujących godzinach:</span><span class="sxs-lookup"><span data-stu-id="3128f-109">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="3128f-110">Po zastosowaniu zaktualizowanego zestawu parametrów ze składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="3128f-110">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="3128f-111">Po zastosowaniu zaktualizowanej wartości dla parametru kaskadowego.</span><span class="sxs-lookup"><span data-stu-id="3128f-111">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="3128f-112">Po powiadomieniu o zdarzeniu i wywołaniu jednego z jego własnych programów obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="3128f-112">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="3128f-113">Po wywołaniu własnej <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="3128f-113">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="3128f-114">Składniki dziedziczone z <xref:Microsoft.AspNetCore.Components.ComponentBase> pomijania są przerenderowane z powodu aktualizacji parametrów, jeśli spełniony jest jeden z następujących warunków:</span><span class="sxs-lookup"><span data-stu-id="3128f-114">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="3128f-115">Wszystkie wartości parametrów są znanymi niezmiennymi typami pierwotnymi (na przykład, `int` , `string` , `DateTime` ) i nie zostały zmienione od momentu ustawienia poprzedniego zestawu parametrów.</span><span class="sxs-lookup"><span data-stu-id="3128f-115">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="3128f-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>Metoda składnika zwraca wartość `false` .</span><span class="sxs-lookup"><span data-stu-id="3128f-116">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="3128f-117">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , zobacz <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender> .</span><span class="sxs-lookup"><span data-stu-id="3128f-117">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="3128f-118">Sterowanie przepływem renderowania</span><span class="sxs-lookup"><span data-stu-id="3128f-118">Control the rendering flow</span></span>

<span data-ttu-id="3128f-119">W większości przypadków <xref:Microsoft.AspNetCore.Components.ComponentBase> konwencje powodują poprawność podzbioru składnika po wystąpieniu zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="3128f-119">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="3128f-120">Deweloperzy nie są zwykle zobowiązani do zapewnienia ręcznej logiki, aby określić, które składniki mają być renderowane i kiedy mają być renderowane.</span><span class="sxs-lookup"><span data-stu-id="3128f-120">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="3128f-121">Ogólny wpływ konwencji platformy polega na tym, że składnik otrzymujący zdarzenie rerenderuje siebie, co rekurencyjnie wyzwala odwzorowanie składników podrzędnych, których wartości parametrów mogły zostać zmienione.</span><span class="sxs-lookup"><span data-stu-id="3128f-121">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="3128f-122">Aby uzyskać więcej informacji o wpływie na wydajność konwencji platformy i sposobach optymalizacji hierarchii składników aplikacji, zobacz <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> .</span><span class="sxs-lookup"><span data-stu-id="3128f-122">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="3128f-123">Kiedy należy wywołać `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="3128f-123">When to call `StateHasChanged`</span></span>

<span data-ttu-id="3128f-124"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>Metoda umożliwia wyzwolenie renderowania w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="3128f-124">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="3128f-125">Jednak należy zachować ostrożność, aby nie wywoływać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> niepotrzebnie, która jest powszechnym błędem, ponieważ nakłada niepotrzebne koszty renderowania.</span><span class="sxs-lookup"><span data-stu-id="3128f-125">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="3128f-126">*Nie* należy wywoływać, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> gdy:</span><span class="sxs-lookup"><span data-stu-id="3128f-126">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="3128f-127">Rutynowe obsługiwanie zdarzeń, zarówno synchronicznie, jak i asynchronicznie, ponieważ <xref:Microsoft.AspNetCore.Components.ComponentBase> wyzwala Render dla większości rutynowych programów obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="3128f-127">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="3128f-128">Implementacja typowej logiki cyklu życia, takiej jak [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) lub [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) , synchonrously lub asynchronicznie, od momentu <xref:Microsoft.AspNetCore.Components.ComponentBase> wyzwalania renderowania dla typowych zdarzeń cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="3128f-128">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="3128f-129">Jednak warto wywoływać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> w przypadku przypadków opisanych w następujących sekcjach:</span><span class="sxs-lookup"><span data-stu-id="3128f-129">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="3128f-130">Procedura obsługi asynchronicznej obejmuje wiele faz asynchronicznych</span><span class="sxs-lookup"><span data-stu-id="3128f-130">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="3128f-131">Otrzymywanie wywołania od czegoś zewnętrznego do Blazor systemu obsługi renderowania i zdarzeń</span><span class="sxs-lookup"><span data-stu-id="3128f-131">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="3128f-132">Aby renderować składnik spoza poddrzewa, które jest renderowane przez określone zdarzenie</span><span class="sxs-lookup"><span data-stu-id="3128f-132">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="3128f-133">Procedura obsługi asynchronicznej obejmuje wiele faz asynchronicznych</span><span class="sxs-lookup"><span data-stu-id="3128f-133">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="3128f-134">Rozważmy następujący `Counter` składnik, który aktualizuje liczbę cztery razy przy każdym kliknięciu.</span><span class="sxs-lookup"><span data-stu-id="3128f-134">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="3128f-135">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="3128f-135">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="3128f-136">Ze względu na sposób, w jaki zadania są zdefiniowane w programie .NET, odbiorca <xref:System.Threading.Tasks.Task> może obserwować tylko ostateczne zakończenie, a nie pośrednie Stany asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="3128f-136">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="3128f-137">W związku z tym, <xref:Microsoft.AspNetCore.Components.ComponentBase> można wyzwolić ponowne renderowanie tylko wtedy, gdy <xref:System.Threading.Tasks.Task> jest on zwracany po raz pierwszy i kiedy <xref:System.Threading.Tasks.Task> kończy.</span><span class="sxs-lookup"><span data-stu-id="3128f-137">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="3128f-138">Nie może on wiedzieć, aby przerenderować w innych punktach pośrednich.</span><span class="sxs-lookup"><span data-stu-id="3128f-138">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="3128f-139">Jeśli chcesz, aby były renderowane w punktach pośrednich, użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="3128f-139">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a><span data-ttu-id="3128f-140">Otrzymywanie wywołania od czegoś zewnętrznego do Blazor systemu obsługi renderowania i zdarzeń</span><span class="sxs-lookup"><span data-stu-id="3128f-140">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="3128f-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> wie tylko o własnych metodach cyklu życia i Blazor zdarzeniach wyzwalanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3128f-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="3128f-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> nie wie o innych zdarzeniach, które mogą wystąpić w kodzie.</span><span class="sxs-lookup"><span data-stu-id="3128f-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="3128f-143">Na przykład wszystkie zdarzenia w języku C# wywoływane przez niestandardowy magazyn danych są nieznane do Blazor .</span><span class="sxs-lookup"><span data-stu-id="3128f-143">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="3128f-144">Aby zdarzenia wyzwalające ponowne renderowanie w celu wyświetlenia zaktualizowanych wartości w interfejsie użytkownika, użyj polecenia <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> .</span><span class="sxs-lookup"><span data-stu-id="3128f-144">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="3128f-145">W innym przypadku użycia należy wziąć pod uwagę Poniższy `Counter` składnik, który używa <xref:System.Timers.Timer?displayProperty=fullName> programu, aby zaktualizować licznik w regularnych odstępach czasu i wywołania <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> w celu zaktualizowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3128f-145">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="3128f-146">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="3128f-146">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="3128f-147">W poprzednim przykładzie, `OnTimerCallback` musi wywołać, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ponieważ Blazor nie ma informacji o zmianach `currentCount` w wywołaniu wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="3128f-147">In the preceding example, `OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="3128f-148">`OnTimerCallback` działa poza Blazor zarządzanym przepływem renderowania lub powiadomieniem o zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="3128f-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>

<span data-ttu-id="3128f-149">Podobnie, ponieważ wywołanie zwrotne jest wywoływane poza Blazor kontekstem synchronizacji, konieczne jest Zawijanie logiki w programie, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> Aby przenieść ją do kontekstu synchronizacji modułu renderowania.</span><span class="sxs-lookup"><span data-stu-id="3128f-149">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="3128f-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> można wywołać tylko z kontekstu synchronizacji modułu renderowania i zgłasza wyjątek w przeciwnym razie.</span><span class="sxs-lookup"><span data-stu-id="3128f-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="3128f-151">Jest to równoważne kierowaniu do wątku interfejsu użytkownika w innych strukturach interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3128f-151">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="3128f-152">Aby renderować składnik spoza poddrzewa, które jest renderowane przez określone zdarzenie</span><span class="sxs-lookup"><span data-stu-id="3128f-152">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="3128f-153">Interfejs użytkownika może wymagać wysłania zdarzenia do jednego składnika, zmiany pewnego stanu i konieczności odwzorowania całkowicie innego składnika, który nie jest obiektem podrzędnym jednego z nich.</span><span class="sxs-lookup"><span data-stu-id="3128f-153">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="3128f-154">Jednym ze sposobów na zaradzenie sobie z tym scenariuszem jest posiadanie klasy *zarządzania stanem* , takiej jak di usługa, która jest wstrzykiwana do wielu składników.</span><span class="sxs-lookup"><span data-stu-id="3128f-154">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="3128f-155">Gdy jeden składnik wywołuje metodę w Menedżerze stanu, Menedżer stanu może zgłosić zdarzenie języka C#, które następnie odbiera przez niezależny składnik.</span><span class="sxs-lookup"><span data-stu-id="3128f-155">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="3128f-156">Ponieważ te zdarzenia języka C# znajdują się poza Blazor potokiem renderowania, należy wywołać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> inne składniki, które mają być renderowane w odpowiedzi na zdarzenia Menedżera stanu.</span><span class="sxs-lookup"><span data-stu-id="3128f-156">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="3128f-157">Jest to podobne do wcześniejszego przypadku <xref:System.Timers.Timer?displayProperty=fullName> w sekcji w [poprzedniej](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) sekcji.</span><span class="sxs-lookup"><span data-stu-id="3128f-157">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="3128f-158">Ponieważ stos wywołań wykonywania zwykle pozostaje w kontekście synchronizacji modułu renderowania, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> nie jest zazwyczaj wymagany.</span><span class="sxs-lookup"><span data-stu-id="3128f-158">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="3128f-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> jest wymagana tylko wtedy, gdy logika wyprowadza kontekst synchronizacji, na przykład wywołanie metody <xref:System.Threading.Tasks.Task.ContinueWith%2A> <xref:System.Threading.Tasks.Task> lub oczekiwanie na <xref:System.Threading.Tasks.Task> [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) .</span><span class="sxs-lookup"><span data-stu-id="3128f-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
