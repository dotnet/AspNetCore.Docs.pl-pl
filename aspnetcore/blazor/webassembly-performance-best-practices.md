---
title: 'ASP.NET Core Blazor WebAssembly najlepszych rozwiązań dotyczących wydajności'
author: pranavkm
description: 'Wskazówki dotyczące zwiększania wydajności Blazor WebAssembly aplikacji ASP.NET Core i unikania typowych problemów z wydajnością.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 423745d734d8da2b8f3f974f9b4dd1a0265d4877
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054739"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="e1645-103">ASP.NET Core Blazor WebAssembly najlepszych rozwiązań dotyczących wydajności</span><span class="sxs-lookup"><span data-stu-id="e1645-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="e1645-104">Autorzy [Pranav Krishnamoorthy](https://github.com/pranavkm) i [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="e1645-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="e1645-105">Blazor WebAssembly jest starannie zaprojektowany i zoptymalizowany pod kątem wysokiej wydajności w najbardziej realistycznych scenariuszach interfejsu użytkownika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1645-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="e1645-106">Jednak generowanie najlepszych wyników zależy od deweloperów korzystających z odpowiednich wzorców i funkcji.</span><span class="sxs-lookup"><span data-stu-id="e1645-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="e1645-107">Należy wziąć pod uwagę następujące aspekty:</span><span class="sxs-lookup"><span data-stu-id="e1645-107">Consider the following aspects:</span></span>

* <span data-ttu-id="e1645-108">**Przepływność środowiska uruchomieniowego** : kod platformy .NET działa w interpreterze środowiska uruchomieniowego webassembly, więc PRZEPŁYWNOŚĆ procesora CPU jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="e1645-108">**Runtime throughput** : The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="e1645-109">W wymagających scenariuszach aplikacja korzysta z [optymalizacji szybkości renderowania](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="e1645-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="e1645-110">**Czas uruchamiania** : aplikacja przenosi środowisko uruchomieniowe platformy .NET do przeglądarki, dlatego ważne jest, aby użyć funkcji [minimalizujących rozmiar pobieranych aplikacji](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="e1645-110">**Startup time** : The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="e1645-111">Optymalizuj szybkość renderowania</span><span class="sxs-lookup"><span data-stu-id="e1645-111">Optimize rendering speed</span></span>

<span data-ttu-id="e1645-112">Poniższe sekcje zawierają zalecenia dotyczące minimalizowania obciążeń renderowania i zwiększania czasu odpowiedzi interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="e1645-113">Poniższe porady mogą łatwo wprowadzić *dziesięć lub większą poprawę* w zakresie szybkości renderowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="e1645-114">Unikaj niepotrzebnego renderowania poddrzewa składników</span><span class="sxs-lookup"><span data-stu-id="e1645-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="e1645-115">W środowisku uruchomieniowym składniki istnieją jako hierarchia.</span><span class="sxs-lookup"><span data-stu-id="e1645-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="e1645-116">Składnik główny ma składniki podrzędne.</span><span class="sxs-lookup"><span data-stu-id="e1645-116">A root component has child components.</span></span> <span data-ttu-id="e1645-117">Z kolei dzieci mają własne składniki podrzędne i tak dalej.</span><span class="sxs-lookup"><span data-stu-id="e1645-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="e1645-118">W przypadku wystąpienia zdarzenia, takiego jak użytkownik, który wybiera przycisk, w ten sposób Blazor decyduje o tym, które składniki mają być przerenderowane:</span><span class="sxs-lookup"><span data-stu-id="e1645-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

 1. <span data-ttu-id="e1645-119">Samo zdarzenie jest wysyłane do składnika, który wyrenderuje procedurę obsługi zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="e1645-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="e1645-120">Po wykonaniu procedury obsługi zdarzeń ten składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="e1645-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="e1645-121">Zawsze, gdy dowolny składnik jest przerenderowany, dostarcza nową kopię wartości parametrów do poszczególnych składników podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="e1645-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="e1645-122">W przypadku otrzymania nowego zestawu wartości parametrów każdy składnik wybiera, czy ma być przerenderowany.</span><span class="sxs-lookup"><span data-stu-id="e1645-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="e1645-123">Domyślnie składniki są rerenderowane, jeśli wartości parametrów mogły ulec zmianie (na przykład, jeśli są to obiekty modyfikowalne).</span><span class="sxs-lookup"><span data-stu-id="e1645-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="e1645-124">Ostatnie dwa kroki tej sekwencji kontynuują rekursywnie hierarchię składników.</span><span class="sxs-lookup"><span data-stu-id="e1645-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="e1645-125">W wielu przypadkach całe poddrzewo jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="e1645-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="e1645-126">Oznacza to, że zdarzenia ukierunkowane na składniki wyższego poziomu mogą spowodować kosztowne procesy ponownego renderowania, ponieważ wszystko poniżej tego punktu musi być renderowane.</span><span class="sxs-lookup"><span data-stu-id="e1645-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="e1645-127">Jeśli chcesz przerwać ten proces i zapobiec obsłużeniu rekursji do określonego poddrzewa, możesz:</span><span class="sxs-lookup"><span data-stu-id="e1645-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="e1645-128">Upewnij się, że wszystkie parametry określonego składnika są typami pierwotnymi (na przykład,,,, `string` `int` `bool` `DateTime` i innych).</span><span class="sxs-lookup"><span data-stu-id="e1645-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="e1645-129">Wbudowana logika do wykrywania zmian automatycznie pomija ponowne renderowanie, jeśli żadna z tych wartości parametrów nie została zmieniona.</span><span class="sxs-lookup"><span data-stu-id="e1645-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="e1645-130">Jeśli renderuje składnik podrzędny z `<Customer CustomerId="@item.CustomerId" />` , gdzie `CustomerId` jest `int` wartością, wówczas nie jest on ponownie renderowany z wyjątkiem `item.CustomerId` zmian.</span><span class="sxs-lookup"><span data-stu-id="e1645-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="e1645-131">Jeśli musisz zaakceptować wartości parametrów niepierwotnych, takie jak niestandardowe typy modeli, wywołania zwrotne zdarzeń lub <xref:Microsoft.AspNetCore.Components.RenderFragment> wartości, możesz przesłonić, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Aby kontrolować decyzję o tym, czy należy renderować, co zostało opisane w sekcji [ `ShouldRender` użycie](#use-of-shouldrender) .</span><span class="sxs-lookup"><span data-stu-id="e1645-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="e1645-132">Po pominięciu odrenderowania całych poddrzew może być możliwe usunięcie ogromnej większości kosztów renderowania w przypadku wystąpienia zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="e1645-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="e1645-133">W celu pominięcia odwzorowania tej części interfejsu użytkownika warto uwzględnić składniki podrzędne.</span><span class="sxs-lookup"><span data-stu-id="e1645-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="e1645-134">Jest to prawidłowy sposób zmniejszenia kosztów renderowania składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="e1645-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="e1645-135">Korzystanie z `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="e1645-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="e1645-136">W przypadku tworzenia składnika tylko interfejsu użytkownika, który nigdy nie zmienia się po początkowym renderowaniu (bez względu na wartości parametrów), skonfiguruj, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Aby zwrócić `false` :</span><span class="sxs-lookup"><span data-stu-id="e1645-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="e1645-137">Jeśli składnik wymaga ponownej renderowania, gdy jego wartości parametrów są zmieniane w określony sposób, można użyć pól prywatnych do śledzenia niezbędnych informacji w celu wykrycia zmian.</span><span class="sxs-lookup"><span data-stu-id="e1645-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="e1645-138">W poniższym przykładzie `shouldRender` jest oparta na sprawdzaniu dowolnego rodzaju zmiany lub mutacji, które powinny monitować o przerenderowanie.</span><span class="sxs-lookup"><span data-stu-id="e1645-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="e1645-139">`prevOutboundFlightId` i `prevInboundFlightId` Śledź informacje dotyczące następnej potencjalnej aktualizacji:</span><span class="sxs-lookup"><span data-stu-id="e1645-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="e1645-140">W powyższym kodzie, program obsługi zdarzeń może być również ustawiony `shouldRender` na `true` tak, aby składnik został przerenderowany po zdarzeniu.</span><span class="sxs-lookup"><span data-stu-id="e1645-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="e1645-141">W przypadku większości składników ten poziom kontroli ręcznej nie jest konieczny.</span><span class="sxs-lookup"><span data-stu-id="e1645-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="e1645-142">Należy tylko zależeć od pomijania poddrzew renderowania, jeśli te poddrzewa są szczególnie kosztowne do renderowania i powodują opóźnienia interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="e1645-143">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="e1645-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="e1645-144">Wirtualizacja</span><span class="sxs-lookup"><span data-stu-id="e1645-144">Virtualization</span></span>

<span data-ttu-id="e1645-145">Podczas renderowania dużych ilości interfejsu użytkownika w obrębie pętli, na przykład lista lub siatka z tysiącami wpisów, zawiera ilość operacji renderowania może prowadzić do opóźnienia w renderowaniu interfejsu użytkownika i w ten sposób słabe środowisko użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="e1645-146">W przypadku, gdy użytkownik widzi tylko niewielką liczbę elementów jednocześnie bez przewijania, wydaje się, że wastefule to wiele czasu renderowania elementów, które nie są obecnie widoczne.</span><span class="sxs-lookup"><span data-stu-id="e1645-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="e1645-147">Aby rozwiązać ten krok, program Blazor udostępnia wbudowany [ `<Virtualize>` składnik](xref:blazor/components/virtualization) , który tworzy wygląd i zachowanie przewijania z arbitralnie dużej listy, ale w rzeczywistości renderuje tylko elementy listy, które znajdują się w bieżącym okienku ekranu przewijania.</span><span class="sxs-lookup"><span data-stu-id="e1645-147">To address this, Blazor provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="e1645-148">Na przykład oznacza to, że aplikacja może mieć listę z 100 000 wpisów, ale płacisz kosztem renderowania 20 elementów, które są widoczne w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="e1645-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="e1645-149">Użycie `<Virtualize>` składnika może skalować wydajność interfejsu użytkownika według kolejności wielkości.</span><span class="sxs-lookup"><span data-stu-id="e1645-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="e1645-150">`<Virtualize>` można użyć, gdy:</span><span class="sxs-lookup"><span data-stu-id="e1645-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="e1645-151">Renderowanie zestawu elementów danych w pętli.</span><span class="sxs-lookup"><span data-stu-id="e1645-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="e1645-152">Większość elementów nie jest widoczna z powodu przewijania.</span><span class="sxs-lookup"><span data-stu-id="e1645-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="e1645-153">Renderowane elementy mają dokładnie taki sam rozmiar.</span><span class="sxs-lookup"><span data-stu-id="e1645-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="e1645-154">Gdy użytkownik przewija do dowolnego punktu, składnik może obliczyć widoczne elementy do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="e1645-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="e1645-155">Poniżej przedstawiono przykład niezwirtualizowanej listy:</span><span class="sxs-lookup"><span data-stu-id="e1645-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="e1645-156">Jeśli `allFlights` Kolekcja zawiera 10 000 elementów, tworzy wystąpienie i renderuje wystąpienia 10 000 `<FlightSummary>` składników.</span><span class="sxs-lookup"><span data-stu-id="e1645-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="e1645-157">W porównaniu poniżej przedstawiono przykład listy zwirtualizowanej:</span><span class="sxs-lookup"><span data-stu-id="e1645-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="e1645-158">Mimo że wynikowy interfejs użytkownika wygląda tak samo dla użytkownika, w tle, tylko tworzy wystąpienie składnika i renderuje jako wiele `<FlightSummary>` wystąpień wymagane do wypełnienia regionu przewijania.</span><span class="sxs-lookup"><span data-stu-id="e1645-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="e1645-159">Zestaw `<FlightSummary>` wyświetlanych wystąpień jest ponownie obliczany i renderowany podczas przewijania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="e1645-160">`<Virtualize>` ma także inne korzyści.</span><span class="sxs-lookup"><span data-stu-id="e1645-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="e1645-161">Na przykład gdy składnik żąda danych z zewnętrznego interfejsu API, `<Virtualize>` zezwala składnikowi na pobieranie tylko wycinków rekordów, które odpowiadają bieżącemu widocznemu regionowi, zamiast pobierać wszystkie dane z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e1645-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="e1645-162">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="e1645-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="e1645-163">Twórz lekkie, zoptymalizowane składniki</span><span class="sxs-lookup"><span data-stu-id="e1645-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="e1645-164">Większość Blazor składników nie wymaga agresywnej optymalizacji.</span><span class="sxs-lookup"><span data-stu-id="e1645-164">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="e1645-165">Wynika to z faktu, że większość składników nie jest często powtarzana w interfejsie użytkownika i nie jest uruchamiana z dużą częstotliwością.</span><span class="sxs-lookup"><span data-stu-id="e1645-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="e1645-166">Na przykład `@page` składniki i składniki reprezentujące jednopoziomowe elementy interfejsu użytkownika, takie jak okna dialogowe lub formularze, najprawdopodobniej pojawiają się tylko jeden w czasie i ponownie renderują w odpowiedzi na gest użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="e1645-167">Te składniki nie tworzą obciążenia o wysokim poziomie renderowania, dzięki czemu można swobodnie korzystać z dowolnej kombinacji potrzebnych funkcji, bez obaw o wydajność renderowania.</span><span class="sxs-lookup"><span data-stu-id="e1645-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="e1645-168">Istnieją jednak również typowe scenariusze, w których można tworzyć składniki, które muszą być powtórzone na dużą skalę.</span><span class="sxs-lookup"><span data-stu-id="e1645-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="e1645-169">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1645-169">For example:</span></span>

 * <span data-ttu-id="e1645-170">Duże zagnieżdżone formularze mogą mieć setki poszczególnych wejść, etykiet i innych elementów.</span><span class="sxs-lookup"><span data-stu-id="e1645-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="e1645-171">Siatki mogą zawierać tysiące komórek.</span><span class="sxs-lookup"><span data-stu-id="e1645-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="e1645-172">Wykresy punktowe mogą mieć miliony punktów danych.</span><span class="sxs-lookup"><span data-stu-id="e1645-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="e1645-173">Jeśli modeluje każdą jednostkę jako oddzielne wystąpienia składnika, będzie wiele z nich, aby wydajność renderowania stała się krytyczna.</span><span class="sxs-lookup"><span data-stu-id="e1645-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="e1645-174">Ta sekcja zawiera wskazówki dotyczące podejmowania takich składników w sposób uproszczony, co sprawia, że interfejs użytkownika pozostanie szybko i będzie odpowiadać.</span><span class="sxs-lookup"><span data-stu-id="e1645-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="e1645-175">Unikaj tysięcy wystąpień składników</span><span class="sxs-lookup"><span data-stu-id="e1645-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="e1645-176">Każdy składnik jest oddzielną wyspa, która może być niezależna od elementów nadrzędnych i podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="e1645-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="e1645-177">Wybierając sposób dzielenia interfejsu użytkownika na hierarchię składników, można przejąć kontrolę nad szczegółowością renderowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="e1645-178">Może to być dobre lub złe w przypadku wydajności.</span><span class="sxs-lookup"><span data-stu-id="e1645-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="e1645-179">Dzieląc interfejs użytkownika na więcej składników, można przetworzyć mniejsze części interfejsu użytkownika, gdy wystąpią zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="e1645-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="e1645-180">Na przykład gdy użytkownik kliknie przycisk w wierszu tabeli, może być możliwe tylko przerenderowanie pojedynczego wiersza zamiast całej strony lub tabeli.</span><span class="sxs-lookup"><span data-stu-id="e1645-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="e1645-181">Jednak każdy dodatkowy składnik wiąże się z dodatkowym obciążeniem pamięci i procesora, aby zająć się niezależnym stanem i wyrenderowaniem.</span><span class="sxs-lookup"><span data-stu-id="e1645-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="e1645-182">Podczas dostrajania wydajności programu Blazor WebAssembly .NET 5 mierzy się obciążenie renderowania wokół 0,06 MS na wystąpienie składnika.</span><span class="sxs-lookup"><span data-stu-id="e1645-182">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="e1645-183">Jest to oparte na prostym składniku, który akceptuje trzy parametry działające na typowym laptopie.</span><span class="sxs-lookup"><span data-stu-id="e1645-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="e1645-184">Wewnętrznie narzuty są duże ze względu na pobieranie stanu poszczególnych składników ze słowników oraz przekazywanie i otrzymywanie parametrów.</span><span class="sxs-lookup"><span data-stu-id="e1645-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="e1645-185">Dzięki mnożenia można zobaczyć, że dodanie 2 000 dodatkowych wystąpień składników spowodowałoby dodanie 0,12 sekund do czasu renderowania, a interfejs użytkownika byłby wolny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="e1645-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="e1645-186">Istnieje możliwość, że składniki są bardziej uproszczone, dzięki czemu można uzyskać więcej z nich, ale często bardziej wydajną techniką nie jest to wiele składników.</span><span class="sxs-lookup"><span data-stu-id="e1645-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="e1645-187">W poniższych sekcjach opisano dwa sposoby.</span><span class="sxs-lookup"><span data-stu-id="e1645-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="e1645-188">Wbudowane składniki podrzędne do ich elementów nadrzędnych</span><span class="sxs-lookup"><span data-stu-id="e1645-188">Inline child components into their parents</span></span>

<span data-ttu-id="e1645-189">Rozważmy następujący składnik, który renderuje sekwencję składników podrzędnych:</span><span class="sxs-lookup"><span data-stu-id="e1645-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="e1645-190">Dla poprzedniego przykładowego kodu `<ChatMessageDisplay>` składnik jest zdefiniowany w pliku `ChatMessageDisplay.razor` zawierającym:</span><span class="sxs-lookup"><span data-stu-id="e1645-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="e1645-191">Powyższy przykład działa prawidłowo i sprawdza, czy tylko tysiące wiadomości nie są wyświetlane jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="e1645-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="e1645-192">Aby jednocześnie pokazać tysiące komunikatów, *należy rozważyć* rozważenia oddzielnego `ChatMessageDisplay` składnika.</span><span class="sxs-lookup"><span data-stu-id="e1645-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="e1645-193">Zamiast tego Renderuj wbudowane bezpośrednio do obiektu nadrzędnego:</span><span class="sxs-lookup"><span data-stu-id="e1645-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="e1645-194">Pozwala to uniknąć narzutu na składnik renderowania, tak aby wiele składników podrzędnych była kosztem niemożliwości ponownego renderowania każdego z nich.</span><span class="sxs-lookup"><span data-stu-id="e1645-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="e1645-195">Zdefiniuj wielokrotne użycie `RenderFragments` kodu</span><span class="sxs-lookup"><span data-stu-id="e1645-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="e1645-196">Składniki podrzędne mogą być rozprowadzone wyłącznie jako sposób użycia logiki renderowania.</span><span class="sxs-lookup"><span data-stu-id="e1645-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="e1645-197">W takim przypadku nadal można ponownie używać logiki renderowania bez deklarowania rzeczywistych składników.</span><span class="sxs-lookup"><span data-stu-id="e1645-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="e1645-198">W bloku dowolnego składnika `@code` można zdefiniować <xref:Microsoft.AspNetCore.Components.RenderFragment> , który EMITUJE interfejs użytkownika i może być wywoływany z dowolnego miejsca:</span><span class="sxs-lookup"><span data-stu-id="e1645-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="e1645-199">Jak demonstated w poprzednim przykładzie, składniki mogą emitować znaczniki z kodu w obrębie ich `@code` bloku i poza nim.</span><span class="sxs-lookup"><span data-stu-id="e1645-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="e1645-200">To podejście definiuje <xref:Microsoft.AspNetCore.Components.RenderFragment> delegata, który można renderować wewnątrz zwykłych wyników renderowania składnika, opcjonalnie w wielu miejscach.</span><span class="sxs-lookup"><span data-stu-id="e1645-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="e1645-201">Jest to konieczne, aby delegat zaakceptował parametr o nazwie `__builder` typu, <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> Aby Razor kompilator mógł generować instrukcje renderowania dla niego.</span><span class="sxs-lookup"><span data-stu-id="e1645-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="e1645-202">Jeśli chcesz umożliwić wielokrotne użycie wielu składników, rozważ zadeklarowanie go jako `public static` członka:</span><span class="sxs-lookup"><span data-stu-id="e1645-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="e1645-203">Ta funkcja może teraz zostać wywołana z niepowiązanego składnika.</span><span class="sxs-lookup"><span data-stu-id="e1645-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="e1645-204">Ta technika jest przydatna do tworzenia bibliotek wstawek znaczników wielokrotnego użytku, które są renderowane bez jakichkolwiek obciążeń dla składników.</span><span class="sxs-lookup"><span data-stu-id="e1645-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="e1645-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> Delegaty mogą również akceptować parametry.</span><span class="sxs-lookup"><span data-stu-id="e1645-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="e1645-206">Aby utworzyć odpowiednik `ChatMessageDisplay` składnika z wcześniejszego przykładu:</span><span class="sxs-lookup"><span data-stu-id="e1645-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="e1645-207">Takie podejście umożliwia ponowne korzystanie z logiki renderowania bez narzutu na składnik.</span><span class="sxs-lookup"><span data-stu-id="e1645-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="e1645-208">Nie ma jednak możliwości odświeżenia poddrzewa interfejsu użytkownika niezależnie od tego, czy nie ma możliwości pominięcia renderowania tego poddrzewa interfejsu użytkownika podczas jego nadrzędnego renderowania, ponieważ nie ma granicy składnika.</span><span class="sxs-lookup"><span data-stu-id="e1645-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="e1645-209">Nie odbieraj zbyt wielu parametrów</span><span class="sxs-lookup"><span data-stu-id="e1645-209">Don't receive too many parameters</span></span>

<span data-ttu-id="e1645-210">Jeśli składnik powtarza się bardzo często, na przykład setki lub tysiące razy, należy pamiętać, że narzuty dotyczące przekazywania i otrzymywania każdego parametru kompiluje.</span><span class="sxs-lookup"><span data-stu-id="e1645-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="e1645-211">Jest to rzadkie, że zbyt wiele parametrów poważnie ogranicza wydajność, ale może być czynnikiem.</span><span class="sxs-lookup"><span data-stu-id="e1645-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="e1645-212">Dla `<TableCell>` składnika, który renderuje 1 000 razy w obrębie siatki, każdy dodatkowy parametr, który przeszedł do niego, może dodać około 15 MS do całkowitego kosztu renderowania.</span><span class="sxs-lookup"><span data-stu-id="e1645-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="e1645-213">Jeśli każda komórka zaakceptowała 10 parametrów, przekazywanie parametrów zajmie około 150 MS na składnik renderowania i w ten sposób prawdopodobnie 150 000 MS (150 s) i z własnej przyczyny jest interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="e1645-214">Aby zmniejszyć obciążenie, można powiązać wiele parametrów za pośrednictwem klas niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="e1645-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="e1645-215">Na przykład `<TableCell>` składnik może zaakceptować:</span><span class="sxs-lookup"><span data-stu-id="e1645-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="e1645-216">W poprzednim przykładzie `Data` różni się dla każdej komórki, ale `Options` jest wspólne dla wszystkich.</span><span class="sxs-lookup"><span data-stu-id="e1645-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="e1645-217">Oczywiście może być udoskonaleniem, aby nie mieć `<TableCell>` składnika i zamiast niego wbudowany w jego logikę w składnik nadrzędny.</span><span class="sxs-lookup"><span data-stu-id="e1645-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="e1645-218">Upewnij się, że parametry kaskadowe zostały naprawione</span><span class="sxs-lookup"><span data-stu-id="e1645-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="e1645-219">`<CascadingValue>`Składnik ma opcjonalny parametr o nazwie `IsFixed` .</span><span class="sxs-lookup"><span data-stu-id="e1645-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="e1645-220">Jeśli `IsFixed` wartość jest `false` (domyślnie), a następnie każdy odbiorca wartości kaskadowej skonfiguruje subskrypcję do odbierania powiadomień o zmianach.</span><span class="sxs-lookup"><span data-stu-id="e1645-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="e1645-221">W takim przypadku każda z nich `[CascadingParameter]` jest **znacznie droższa** niż regularna `[Parameter]` ze względu na śledzenie subskrypcji.</span><span class="sxs-lookup"><span data-stu-id="e1645-221">In this case, each each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="e1645-222">Jeśli `IsFixed` wartość jest `true` (na przykład `<CascadingValue Value="@someValue" IsFixed="true">` ), adresaci Pobiera wartość początkową, ale *nie* konfiguruje żadnej subskrypcji do odbierania aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="e1645-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="e1645-223">W takim przypadku każdy `[CascadingParameter]` jest lekki i **nie jest droższy** od zwykłego `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="e1645-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="e1645-224">Tak, gdzie to możliwe, należy używać `IsFixed="true"` na wartościach kaskadowych.</span><span class="sxs-lookup"><span data-stu-id="e1645-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="e1645-225">Można to zrobić zawsze, gdy wartość jest podawana nie zmienia się w czasie.</span><span class="sxs-lookup"><span data-stu-id="e1645-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="e1645-226">We wspólnym wzorcu, w którym składnik przechodzi `this` jako wartość kaskadowo, należy użyć `IsFixed="true"` :</span><span class="sxs-lookup"><span data-stu-id="e1645-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="e1645-227">Jest to bardzo istotna różnica, jeśli istnieje duża liczba innych składników, które otrzymują kaskadową wartość.</span><span class="sxs-lookup"><span data-stu-id="e1645-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="e1645-228">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="e1645-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="e1645-229">Unikaj korzystając atrybutów z `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="e1645-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="e1645-230">Składniki mogą wybrać otrzymywanie niedopasowanych wartości parametrów przy użyciu <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flagi:</span><span class="sxs-lookup"><span data-stu-id="e1645-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="e1645-231">Takie podejście umożliwia przechodzenie przez dowolne dodatkowe atrybuty do elementu.</span><span class="sxs-lookup"><span data-stu-id="e1645-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="e1645-232">Jednak jest on również dość kosztowny, ponieważ moduł renderujący musi:</span><span class="sxs-lookup"><span data-stu-id="e1645-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="e1645-233">Dopasowuje wszystkie podane parametry do zestawu znanych parametrów w celu skompilowania słownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="e1645-234">Śledź, jak wiele kopii tego samego atrybutu zastępuje siebie nawzajem.</span><span class="sxs-lookup"><span data-stu-id="e1645-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="e1645-235">Korzystaj z bezpłatnych <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> składników niezwiązanych z wydajnością, takich jak te, które nie są często powtarzane.</span><span class="sxs-lookup"><span data-stu-id="e1645-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="e1645-236">Jednak w przypadku składników, które są renderowane na dużą skalę, takich jak każdy element na dużej liście lub w komórkach w siatce, spróbuj uniknąć korzystając atrybutu.</span><span class="sxs-lookup"><span data-stu-id="e1645-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="e1645-237">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="e1645-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="e1645-238">Zaimplementuj `SetParametersAsync` ręcznie</span><span class="sxs-lookup"><span data-stu-id="e1645-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="e1645-239">Jednym z głównych aspektów narzutu na składnik renderowania jest zapisanie przychodzących wartości parametrów do `[Parameter]` właściwości.</span><span class="sxs-lookup"><span data-stu-id="e1645-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="e1645-240">Aby to zrobić, moduł renderowania musi używać odbicia.</span><span class="sxs-lookup"><span data-stu-id="e1645-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="e1645-241">Mimo że jest to nieco zoptymalizowane, brak obsługi JIT w środowisku uruchomieniowym webassembly nakłada limity.</span><span class="sxs-lookup"><span data-stu-id="e1645-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="e1645-242">W niektórych ekstremalnych przypadkach warto uniknąć odbicia i zaimplementować własną logikę ustawienia parametru ręcznie.</span><span class="sxs-lookup"><span data-stu-id="e1645-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="e1645-243">Może to być stosowane w przypadku:</span><span class="sxs-lookup"><span data-stu-id="e1645-243">This may be applicable when:</span></span>

 * <span data-ttu-id="e1645-244">Masz składnik, który renderuje bardzo często (na przykład, w interfejsie użytkownika znajdują się setki lub tysiące kopii tego elementu).</span><span class="sxs-lookup"><span data-stu-id="e1645-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="e1645-245">Akceptuje wiele parametrów.</span><span class="sxs-lookup"><span data-stu-id="e1645-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="e1645-246">Okaże się, że narzuty odbioru parametrów mają zauważalny wpływ na czas odpowiedzi interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="e1645-247">W takich przypadkach można przesłonić metodę wirtualną składnika <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> i wdrożyć własną logikę specyficzną dla danego składnika.</span><span class="sxs-lookup"><span data-stu-id="e1645-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="e1645-248">W poniższym przykładzie zamierzone jest uniknięcie przeszukiwania słownika:</span><span class="sxs-lookup"><span data-stu-id="e1645-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="e1645-249">W poprzednim kodzie zwracające klasę bazową <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> działają normalne metody cyklu życia bez ponownego przypisywania parametrów.</span><span class="sxs-lookup"><span data-stu-id="e1645-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="e1645-250">Jak widać w powyższym kodzie, zastępowanie <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> i dostarczanie logiki niestandardowej jest skomplikowane i pracochłonne, dlatego nie zalecamy ogólnie tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="e1645-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="e1645-251">W skrajnych przypadkach może zwiększyć wydajność renderowania o 20-25%, ale rozwiązanie to należy wziąć pod uwagę tylko w scenariuszach wymienionych powyżej.</span><span class="sxs-lookup"><span data-stu-id="e1645-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="e1645-252">Nie Wyzwalaj zbyt szybko zdarzeń</span><span class="sxs-lookup"><span data-stu-id="e1645-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="e1645-253">Niektóre zdarzenia przeglądarki są niezwykle często wyzwalane, na przykład `onmousemove` i `onscroll` , które mogą być uruchamiane dziesiątki lub setki razy na sekundę.</span><span class="sxs-lookup"><span data-stu-id="e1645-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="e1645-254">W większości przypadków nie trzeba często wykonywać aktualizacji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="e1645-255">Jeśli użytkownik spróbuje to zrobić, może to spowodować szkody czas odpowiedzi interfejsu użytkownika lub nadmierne wykorzystanie procesora CPU.</span><span class="sxs-lookup"><span data-stu-id="e1645-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="e1645-256">Zamiast używać natywnych `@onmousemove` lub `@onscroll` zdarzeń, warto użyć kodu js Interop do zarejestrowania wywołania zwrotnego, które jest generowane rzadziej.</span><span class="sxs-lookup"><span data-stu-id="e1645-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="e1645-257">Na przykład poniższy składnik ( `MyComponent.razor` ) wyświetla pozycję myszy, ale tylko aktualizuje się co najwyżej raz na 500 MS:</span><span class="sxs-lookup"><span data-stu-id="e1645-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="e1645-258">Odpowiedni kod JavaScript, który może być umieszczony na `index.html` stronie lub załadowany jako moduł ES6, rejestruje rzeczywisty odbiornik zdarzeń dom.</span><span class="sxs-lookup"><span data-stu-id="e1645-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="e1645-259">W tym przykładzie odbiornik zdarzeń używa [ `throttle` funkcji Lodash](https://lodash.com/docs/4.17.15#throttle) , aby ograniczyć szybkość wywołań:</span><span class="sxs-lookup"><span data-stu-id="e1645-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="e1645-260">Ta technika może być jeszcze bardziej ważna dla Blazor Server , ponieważ każde wywołanie zdarzenia obejmuje dostarczenie komunikatu przez sieć.</span><span class="sxs-lookup"><span data-stu-id="e1645-260">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="e1645-261">Jest to przydatne w przypadku, gdy Blazor WebAssembly może znacznie zmniejszyć ilość pracy renderowania.</span><span class="sxs-lookup"><span data-stu-id="e1645-261">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="e1645-262">Optymalizuj szybkość międzyoperacyjności kodu JavaScript</span><span class="sxs-lookup"><span data-stu-id="e1645-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="e1645-263">Wywołania między programami .NET i JavaScript obejmują kilka dodatkowych kosztów, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="e1645-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="e1645-264">Domyślnie wywołania są asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="e1645-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="e1645-265">Domyślnie parametry i zwracane wartości są serializowane w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="e1645-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="e1645-266">Ma to na celu zapewnienie łatwego w zrozumieniu mechanizmu konwersji między typami .NET i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e1645-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="e1645-267">Ponadto Blazor Server te wywołania są przesyłane przez sieć.</span><span class="sxs-lookup"><span data-stu-id="e1645-267">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="e1645-268">Unikaj nadmiernie szczegółowych wywołań</span><span class="sxs-lookup"><span data-stu-id="e1645-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="e1645-269">Ponieważ każde wywołanie wiąże się z pewnym obciążeniem, może być cenne, aby zmniejszyć liczbę wywołań.</span><span class="sxs-lookup"><span data-stu-id="e1645-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="e1645-270">Rozważmy następujący kod, który przechowuje kolekcję elementów w `localStorage` sklepie przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="e1645-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="e1645-271">W powyższym przykładzie jest to oddzielne wywołanie międzyoperacyjna JS dla każdego elementu.</span><span class="sxs-lookup"><span data-stu-id="e1645-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="e1645-272">Zamiast tego, następujące podejście zmniejsza międzyoperacyjność elementu JS do pojedynczego wywołania:</span><span class="sxs-lookup"><span data-stu-id="e1645-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="e1645-273">Odpowiednia funkcja języka JavaScript zdefiniowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e1645-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="e1645-274">W przypadku Blazor WebAssembly , zwykle jest to kwestia tylko wtedy, gdy wykonujesz dużą liczbę wywołań międzyoperacyjnych w języku js.</span><span class="sxs-lookup"><span data-stu-id="e1645-274">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="e1645-275">Rozważ możliwość wykonywania wywołań synchronicznych</span><span class="sxs-lookup"><span data-stu-id="e1645-275">Consider making synchronous calls</span></span>

<span data-ttu-id="e1645-276">Wywołania międzyoperacyjne JavaScript są domyślnie asynchroniczne, bez względu na to, czy kod jest wywoływany synchronicznie, czy asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="e1645-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="e1645-277">Ma to na celu zapewnienie, że składniki są zgodne z systemami Blazor WebAssembly i Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e1645-277">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="e1645-278">W systemie Blazor Server wszystkie wywołania międzyoperacyjne języka JavaScript muszą być asynchroniczne, ponieważ są wysyłane za pośrednictwem połączenia sieciowego.</span><span class="sxs-lookup"><span data-stu-id="e1645-278">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="e1645-279">Jeśli masz pewność, że aplikacja została kiedykolwiek uruchomiona tylko w programie Blazor WebAssembly , możesz wybrać opcję wykonywania synchronicznych wywołań w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e1645-279">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="e1645-280">Ta funkcja ma nieco mniej obciążenia niż wywołania asynchroniczne i może powodować mniejszą liczbę cykli renderowania, ponieważ nie ma stanu pośredniego podczas oczekiwania na wyniki.</span><span class="sxs-lookup"><span data-stu-id="e1645-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="e1645-281">Aby wykonać synchroniczne wywołanie z platformy .NET do języka JavaScript, Rzutowanie <xref:Microsoft.JSInterop.IJSRuntime> na <xref:Microsoft.JSInterop.IJSInProcessRuntime> :</span><span class="sxs-lookup"><span data-stu-id="e1645-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e1645-282">Podczas pracy z programem `IJSObjectReference` można wykonać wywołanie synchroniczne przez rzutowanie na `IJSInProcessObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="e1645-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="e1645-283">Aby wykonać synchroniczne wywołanie z JavaScript do .NET, użyj `DotNet.invokeMethod` zamiast `DotNet.invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="e1645-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="e1645-284">Wywołania synchroniczne działają, jeśli:</span><span class="sxs-lookup"><span data-stu-id="e1645-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="e1645-285">Aplikacja działa w systemie Blazor WebAssembly , nie Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e1645-285">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="e1645-286">Wywołana funkcja zwraca wartość synchronicznie (nie jest to `async` Metoda i nie zwraca platformy .NET <xref:System.Threading.Tasks.Task> ani JavaScript `Promise` ).</span><span class="sxs-lookup"><span data-stu-id="e1645-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="e1645-287">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="e1645-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="e1645-288">Rozważ utworzenie nieskierowanych wywołań</span><span class="sxs-lookup"><span data-stu-id="e1645-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="e1645-289">W przypadku uruchamiania w systemie Blazor WebAssembly można wykonać nieskierowanie wywołania z platformy .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e1645-289">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="e1645-290">Są to wywołania synchroniczne, które nie wykonują serializacji JSON argumentów lub zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="e1645-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="e1645-291">Wszystkie aspekty związane z zarządzaniem pamięcią i tłumaczeniami między środowiskami .NET i JavaScript są pozostawiane do dewelopera.</span><span class="sxs-lookup"><span data-stu-id="e1645-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="e1645-292">Podczas używania `IJSUnmarshalledRuntime` ma najmniejsze obciążenie związane z interfejsem js międzyoperacyjności, interfejsy API języka JavaScript wymagane do współdziałania z tymi interfejsy API są obecnie nieudokumentowane i mogą ulegać zmianom w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="e1645-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="e1645-293">Minimalizuj rozmiar pobierania aplikacji</span><span class="sxs-lookup"><span data-stu-id="e1645-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="e1645-294">Przycinanie języka pośredniego (IL)</span><span class="sxs-lookup"><span data-stu-id="e1645-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="e1645-295">[Przycinanie nieużywanych zestawów z Blazor WebAssembly aplikacji](xref:blazor/host-and-deploy/configure-trimmer) zmniejsza rozmiar aplikacji przez usunięcie nieużywanego kodu w danych binarnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1645-295">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="e1645-296">Domyślnie element dostosowujący jest wykonywany podczas publikowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1645-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="e1645-297">Aby skorzystać z przycinania, Opublikuj aplikację do wdrożenia przy użyciu [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z opcją [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) ustawioną na `Release` :</span><span class="sxs-lookup"><span data-stu-id="e1645-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="e1645-298">Tworzenie łączy języka pośredniego (IL)</span><span class="sxs-lookup"><span data-stu-id="e1645-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="e1645-299">[Łączenie Blazor WebAssembly aplikacji](xref:blazor/host-and-deploy/configure-linker) zmniejsza rozmiar aplikacji przez przycinanie nieużywanego kodu w plikach binarnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1645-299">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="e1645-300">Domyślnie konsolidator języka pośredniego (IL) jest włączony tylko w przypadku kompilowania w `Release` konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="e1645-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="e1645-301">Aby z tego skorzystać, Opublikuj aplikację do wdrożenia przy użyciu [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z opcją [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) ustawioną na `Release` :</span><span class="sxs-lookup"><span data-stu-id="e1645-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="e1645-302">Użyj System.Text.Jsna</span><span class="sxs-lookup"><span data-stu-id="e1645-302">Use System.Text.Json</span></span>

<span data-ttu-id="e1645-303">Blazorimplementacja elementu webinterop w języku JS polega na tym <xref:System.Text.Json> , że jest to biblioteka serializacji JSON o wysokiej wydajności z alokacją małej ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="e1645-303">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="e1645-304">Użycie nie powoduje, że <xref:System.Text.Json> rozmiar ładunku aplikacji jest większy niż dodanie co najmniej jednej alternatywnej biblioteki JSON.</span><span class="sxs-lookup"><span data-stu-id="e1645-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="e1645-305">Aby uzyskać wskazówki dotyczące migracji, zobacz [Jak przeprowadzić migrację z `Newtonsoft.Json` do programu `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="e1645-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="e1645-306">Zestawy ładowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="e1645-306">Lazy load assemblies</span></span>

<span data-ttu-id="e1645-307">Ładuj zestawy w czasie wykonywania, gdy zestawy są wymagane przez trasę.</span><span class="sxs-lookup"><span data-stu-id="e1645-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="e1645-308">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="e1645-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="e1645-309">Kompresja</span><span class="sxs-lookup"><span data-stu-id="e1645-309">Compression</span></span>

<span data-ttu-id="e1645-310">Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="e1645-310">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="e1645-311">Blazor opiera się na serwerze w celu przeprowadzenia negotation zawartości i obkompresji plików skompresowanych statycznie.</span><span class="sxs-lookup"><span data-stu-id="e1645-311">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="e1645-312">Po wdrożeniu aplikacji Sprawdź, czy aplikacja obsługuje skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="e1645-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="e1645-313">Zbadaj kartę Sieć w Narzędzia deweloperskie przeglądarki i sprawdź, czy pliki są obsługiwane przez program `Content-Encoding: br` lub `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="e1645-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="e1645-314">Jeśli host nie obsługuje skompresowanych plików, postępuj zgodnie z instrukcjami w temacie <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="e1645-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="e1645-315">Wyłącz nieużywane funkcje</span><span class="sxs-lookup"><span data-stu-id="e1645-315">Disable unused features</span></span>

<span data-ttu-id="e1645-316">Blazor WebAssemblyśrodowisko uruchomieniowe obejmuje następujące funkcje platformy .NET, które można wyłączyć, jeśli aplikacja nie wymaga ich dla mniejszego rozmiaru ładunku:</span><span class="sxs-lookup"><span data-stu-id="e1645-316">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="e1645-317">Plik danych jest uwzględniany w celu poprawnego wprowadzania informacji o strefie czasowej.</span><span class="sxs-lookup"><span data-stu-id="e1645-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="e1645-318">Jeśli aplikacja nie wymaga tej funkcji, rozważ wyłączenie jej przez ustawienie właściwości programu `BlazorEnableTimeZoneSupport` MSBuild w pliku projektu aplikacji na `false` :</span><span class="sxs-lookup"><span data-stu-id="e1645-318">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e1645-319">Domyślnie Blazor WebAssembly zasoby globalizacji są wymagane do wyświetlania wartości, takich jak daty i waluta, w kulturze użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1645-319">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="e1645-320">Jeśli aplikacja nie wymaga lokalizacji, można [skonfigurować aplikację do obsługi niezmiennej kultury](xref:blazor/globalization-localization), która jest oparta na `en-US` kulturze:</span><span class="sxs-lookup"><span data-stu-id="e1645-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e1645-321">Informacje o sortowaniu są uwzględniane w celu <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> poprawnego działania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="e1645-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="e1645-322">Jeśli masz pewność, że aplikacja nie wymaga danych sortowania, rozważ wyłączenie jej przez ustawienie `BlazorWebAssemblyPreserveCollationData` Właściwości programu MSBuild w pliku projektu aplikacji na `false` :</span><span class="sxs-lookup"><span data-stu-id="e1645-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
