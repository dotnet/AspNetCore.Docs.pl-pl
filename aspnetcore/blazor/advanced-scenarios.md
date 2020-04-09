---
title: zaawansowane scenariusze Blazor ASP.NET Core
author: guardrex
description: Dowiedz się więcej Blazoro zaawansowanych scenariuszach w , w tym jak włączyć ręczną logikę RenderTreeBuilder do aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659454"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="33673-103">zaawansowane scenariusze ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="33673-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="33673-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="33673-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="33673-105">Obsługa obwodów serwera Blazor</span><span class="sxs-lookup"><span data-stu-id="33673-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="33673-106">Blazor Server pozwala na zdefiniowanie programu *obsługi obwodów,* który umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="33673-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="33673-107">Program obsługi obwodu jest implementowany przez wyprowadzanie `CircuitHandler` i rejestrowanie klasy w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="33673-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="33673-108">Poniższy przykład obsługi obwodu śledzi otwarte połączenia SignalR:</span><span class="sxs-lookup"><span data-stu-id="33673-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="33673-109">Programy obsługi obwodów są rejestrowane przy użyciu DI.</span><span class="sxs-lookup"><span data-stu-id="33673-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="33673-110">Wystąpienia o określonym zakresie są tworzone na wystąpienie obwodu.</span><span class="sxs-lookup"><span data-stu-id="33673-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="33673-111">`TrackingCircuitHandler` W poprzednim przykładzie tworzona jest usługa singleton, ponieważ stan wszystkich obwodów musi być śledzony:</span><span class="sxs-lookup"><span data-stu-id="33673-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="33673-112">Jeśli metody niestandardowego programu obsługi obwodów zgłaszają nieobsługiowany wyjątek, wyjątek jest krytyczny dla obwodu serwera Blazor.</span><span class="sxs-lookup"><span data-stu-id="33673-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="33673-113">Aby tolerować wyjątki w kodzie programu obsługi lub metody wywoływane, zawiń kod w co najmniej jednej [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługi błędów i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="33673-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="33673-114">Po zakończeniu obwodu, ponieważ użytkownik został odłączony, a struktura czyści stan obwodu, struktura usuwa zakres DI obwodu.</span><span class="sxs-lookup"><span data-stu-id="33673-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="33673-115">Pozbywanie się zakresu usuwa wszystkie usługi DI <xref:System.IDisposable?displayProperty=fullName>o zakresie obwodu, które implementują .</span><span class="sxs-lookup"><span data-stu-id="33673-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="33673-116">Jeśli dowolna usługa DI zgłasza nieobsługiowany wyjątek podczas likwidacji, struktura rejestruje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="33673-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="33673-117">Ręczna logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="33673-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="33673-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`udostępnia metody manipulowania składników i elementów, w tym składników budynku ręcznie w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="33673-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="33673-119">Użycie `RenderTreeBuilder` do tworzenia składników jest scenariuszem zaawansowanym.</span><span class="sxs-lookup"><span data-stu-id="33673-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="33673-120">Nieprawidłowo sformułowany składnik (na przykład niezamknięty znacznik znaczników) może spowodować niezdefiniowane zachowanie.</span><span class="sxs-lookup"><span data-stu-id="33673-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="33673-121">Należy wziąć `PetDetails` pod uwagę następujący składnik, który może być ręcznie wbudowany w inny składnik:</span><span class="sxs-lookup"><span data-stu-id="33673-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="33673-122">W poniższym przykładzie pętla w `CreateComponent` `PetDetails` metodzie generuje trzy składniki.</span><span class="sxs-lookup"><span data-stu-id="33673-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="33673-123">Podczas `RenderTreeBuilder` wywoływania metod tworzenia`OpenComponent` `AddAttribute`składników ( i ), numery sekwencji są numery wierszy kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="33673-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="33673-124">Algorytm różnicy Blazor opiera się na numery sekwencji odpowiadające różnych wierszy kodu, a nie różne wywołania wywołania.</span><span class="sxs-lookup"><span data-stu-id="33673-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="33673-125">Podczas tworzenia składnika `RenderTreeBuilder` z metodami, hardcode argumenty dla numerów sekwencji.</span><span class="sxs-lookup"><span data-stu-id="33673-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="33673-126">**Przy użyciu obliczeń lub licznika do generowania numeru sekwencyjnyego może prowadzić do niskiej wydajności.**</span><span class="sxs-lookup"><span data-stu-id="33673-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="33673-127">Aby uzyskać więcej informacji, zobacz [numery sekwencji odnoszą się do numerów wierszy kodu, a nie do](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) sekcji zlecenia wykonania.</span><span class="sxs-lookup"><span data-stu-id="33673-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="33673-128">`BuiltContent`Składnik:</span><span class="sxs-lookup"><span data-stu-id="33673-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="33673-129">Typy `Microsoft.AspNetCore.Components.RenderTree` w zezwalają na przetwarzanie *wyników* operacji renderowania.</span><span class="sxs-lookup"><span data-stu-id="33673-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="33673-130">Są to wewnętrzne szczegóły implementacji ram Blazor.</span><span class="sxs-lookup"><span data-stu-id="33673-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="33673-131">Te typy należy uznać za *niestabilne* i mogą ulec zmianie w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="33673-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="33673-132">Numery sekwencji odnoszą się do numerów wierszy kodu, a nie do zlecenia wykonania</span><span class="sxs-lookup"><span data-stu-id="33673-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="33673-133">Pliki komponentów razor (*.brzytwa*) są zawsze kompilowane.</span><span class="sxs-lookup"><span data-stu-id="33673-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="33673-134">Kompilacja jest potencjalną przewagą nad interpretacją kodu, ponieważ krok kompilacji może służyć do wstrzykiwania informacji, które poprawia wydajność aplikacji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="33673-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="33673-135">Kluczowym przykładem tych ulepszeń są *numery sekwencji*.</span><span class="sxs-lookup"><span data-stu-id="33673-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="33673-136">Numery sekwencji wskazują do środowiska wykonawczego, które dane wyjściowe pochodzą z których różne i uporządkowane wiersze kodu.</span><span class="sxs-lookup"><span data-stu-id="33673-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="33673-137">Środowisko wykonawcze używa tych informacji do generowania efektywnych różnic kursowych drzewa w czasie liniowym, co jest znacznie szybsze niż zwykle jest to możliwe dla ogólnego algorytmu różnicowego drzewa.</span><span class="sxs-lookup"><span data-stu-id="33673-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="33673-138">Rozważmy następujący plik komponentu Razor (*.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="33673-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="33673-139">Poprzedni kod kompiluje się do czegoś następującego:</span><span class="sxs-lookup"><span data-stu-id="33673-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="33673-140">Gdy kod jest wykonywany po raz `someFlag` `true`pierwszy, jeśli jest , konstruktor odbiera:</span><span class="sxs-lookup"><span data-stu-id="33673-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="33673-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="33673-141">Sequence</span></span> | <span data-ttu-id="33673-142">Typ</span><span class="sxs-lookup"><span data-stu-id="33673-142">Type</span></span>      | <span data-ttu-id="33673-143">Dane</span><span class="sxs-lookup"><span data-stu-id="33673-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="33673-144">0</span><span class="sxs-lookup"><span data-stu-id="33673-144">0</span></span>        | <span data-ttu-id="33673-145">Węzeł tekstowy</span><span class="sxs-lookup"><span data-stu-id="33673-145">Text node</span></span> | <span data-ttu-id="33673-146">First</span><span class="sxs-lookup"><span data-stu-id="33673-146">First</span></span>  |
| <span data-ttu-id="33673-147">1</span><span class="sxs-lookup"><span data-stu-id="33673-147">1</span></span>        | <span data-ttu-id="33673-148">Węzeł tekstowy</span><span class="sxs-lookup"><span data-stu-id="33673-148">Text node</span></span> | <span data-ttu-id="33673-149">Sekunda</span><span class="sxs-lookup"><span data-stu-id="33673-149">Second</span></span> |

<span data-ttu-id="33673-150">Wyobraź `someFlag` sobie, że staje się `false`, a znaczniki są renderowane ponownie.</span><span class="sxs-lookup"><span data-stu-id="33673-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="33673-151">Tym razem budowniczy otrzymuje:</span><span class="sxs-lookup"><span data-stu-id="33673-151">This time, the builder receives:</span></span>

| <span data-ttu-id="33673-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="33673-152">Sequence</span></span> | <span data-ttu-id="33673-153">Typ</span><span class="sxs-lookup"><span data-stu-id="33673-153">Type</span></span>       | <span data-ttu-id="33673-154">Dane</span><span class="sxs-lookup"><span data-stu-id="33673-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="33673-155">1</span><span class="sxs-lookup"><span data-stu-id="33673-155">1</span></span>        | <span data-ttu-id="33673-156">Węzeł tekstowy</span><span class="sxs-lookup"><span data-stu-id="33673-156">Text node</span></span>  | <span data-ttu-id="33673-157">Sekunda</span><span class="sxs-lookup"><span data-stu-id="33673-157">Second</span></span> |

<span data-ttu-id="33673-158">Gdy środowisko wykonawcze wykonuje diff, widzi, że `0` element w sekwencji został usunięty, więc generuje następujący skrypt *edycji*trywialne:</span><span class="sxs-lookup"><span data-stu-id="33673-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="33673-159">Usuń pierwszy węzeł tekstowy.</span><span class="sxs-lookup"><span data-stu-id="33673-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="33673-160">Problem z programowym generowaniem numerów sekwencyjnych</span><span class="sxs-lookup"><span data-stu-id="33673-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="33673-161">Wyobraź sobie, że zamiast tego napisałeś następującą logikę konstruktora drzew renderowania:</span><span class="sxs-lookup"><span data-stu-id="33673-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="33673-162">Teraz pierwsze wyjście to:</span><span class="sxs-lookup"><span data-stu-id="33673-162">Now, the first output is:</span></span>

| <span data-ttu-id="33673-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="33673-163">Sequence</span></span> | <span data-ttu-id="33673-164">Typ</span><span class="sxs-lookup"><span data-stu-id="33673-164">Type</span></span>      | <span data-ttu-id="33673-165">Dane</span><span class="sxs-lookup"><span data-stu-id="33673-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="33673-166">0</span><span class="sxs-lookup"><span data-stu-id="33673-166">0</span></span>        | <span data-ttu-id="33673-167">Węzeł tekstowy</span><span class="sxs-lookup"><span data-stu-id="33673-167">Text node</span></span> | <span data-ttu-id="33673-168">First</span><span class="sxs-lookup"><span data-stu-id="33673-168">First</span></span>  |
| <span data-ttu-id="33673-169">1</span><span class="sxs-lookup"><span data-stu-id="33673-169">1</span></span>        | <span data-ttu-id="33673-170">Węzeł tekstowy</span><span class="sxs-lookup"><span data-stu-id="33673-170">Text node</span></span> | <span data-ttu-id="33673-171">Sekunda</span><span class="sxs-lookup"><span data-stu-id="33673-171">Second</span></span> |

<span data-ttu-id="33673-172">Wynik ten jest identyczny z poprzednim przypadkiem, więc nie istnieją żadne negatywne problemy.</span><span class="sxs-lookup"><span data-stu-id="33673-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="33673-173">`someFlag`znajduje `false` się na drugim renderowaniu, a dane wyjściowe są następujące:</span><span class="sxs-lookup"><span data-stu-id="33673-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="33673-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="33673-174">Sequence</span></span> | <span data-ttu-id="33673-175">Typ</span><span class="sxs-lookup"><span data-stu-id="33673-175">Type</span></span>      | <span data-ttu-id="33673-176">Dane</span><span class="sxs-lookup"><span data-stu-id="33673-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="33673-177">0</span><span class="sxs-lookup"><span data-stu-id="33673-177">0</span></span>        | <span data-ttu-id="33673-178">Węzeł tekstowy</span><span class="sxs-lookup"><span data-stu-id="33673-178">Text node</span></span> | <span data-ttu-id="33673-179">Sekunda</span><span class="sxs-lookup"><span data-stu-id="33673-179">Second</span></span> |

<span data-ttu-id="33673-180">Tym razem algorytm różnicowy widzi, że wystąpiły *dwie* zmiany, a algorytm generuje następujący skrypt edycji:</span><span class="sxs-lookup"><span data-stu-id="33673-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="33673-181">Zmień wartość pierwszego węzła `Second`tekstowego na .</span><span class="sxs-lookup"><span data-stu-id="33673-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="33673-182">Usuń drugi węzeł tekstowy.</span><span class="sxs-lookup"><span data-stu-id="33673-182">Remove the second text node.</span></span>

<span data-ttu-id="33673-183">Generowanie numerów sekwencji utracił wszystkie przydatne `if/else` informacje o tym, gdzie gałęzie i pętle były obecne w oryginalnym kodzie.</span><span class="sxs-lookup"><span data-stu-id="33673-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="33673-184">Powoduje to dyferwę **dwa razy dłużej** niż wcześniej.</span><span class="sxs-lookup"><span data-stu-id="33673-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="33673-185">To trywialny przykład.</span><span class="sxs-lookup"><span data-stu-id="33673-185">This is a trivial example.</span></span> <span data-ttu-id="33673-186">W bardziej realistycznych przypadkach ze złożonymi i głęboko zagnieżdżonymi strukturami, a zwłaszcza w przypadku pętli, koszt wydajności jest zwykle wyższy.</span><span class="sxs-lookup"><span data-stu-id="33673-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="33673-187">Zamiast natychmiast zidentyfikować, które bloki pętli lub gałęzie zostały wstawione lub usunięte, algorytm różnicujący musi ponownie wciągać się głęboko w drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="33673-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="33673-188">Zwykle powoduje to konieczność tworzenia dłuższych skryptów edycji, ponieważ algorytm różnicowy jest błędnie poinformowany o tym, jak stare i nowe struktury odnoszą się do siebie nawzajem.</span><span class="sxs-lookup"><span data-stu-id="33673-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="33673-189">Wytyczne i wnioski</span><span class="sxs-lookup"><span data-stu-id="33673-189">Guidance and conclusions</span></span>

* <span data-ttu-id="33673-190">Wydajność aplikacji cierpi, jeśli numery sekwencji są generowane dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="33673-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="33673-191">Struktura nie można utworzyć własne numery sekwencji automatycznie w czasie wykonywania, ponieważ niezbędne informacje nie istnieje, chyba że jest przechwycony w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="33673-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="33673-192">Nie zapisuj długich bloków logiki implementowanych `RenderTreeBuilder` ręcznie.</span><span class="sxs-lookup"><span data-stu-id="33673-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="33673-193">Preferuj pliki *.brzytwa* i pozwól kompilatorowi radzić sobie z numerami sekwencji.</span><span class="sxs-lookup"><span data-stu-id="33673-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="33673-194">Jeśli nie możesz uniknąć `RenderTreeBuilder` logiki ręcznej, podziel długie bloki `OpenRegion` / `CloseRegion` kodu na mniejsze kawałki zawinięte w wywołania.</span><span class="sxs-lookup"><span data-stu-id="33673-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="33673-195">Każdy region ma własną oddzielną przestrzeń numerów sekwencyjnych, dzięki czemu można ponownie uruchomić od zera (lub dowolną inną dowolną liczbę) wewnątrz każdego regionu.</span><span class="sxs-lookup"><span data-stu-id="33673-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="33673-196">Jeśli numery sekwencji są zakodowane na stałe, algorytm różnicowy wymaga tylko, aby numery sekwencji zwiększyć wartość.</span><span class="sxs-lookup"><span data-stu-id="33673-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="33673-197">Wartość początkowa i luki są nieistotne.</span><span class="sxs-lookup"><span data-stu-id="33673-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="33673-198">Jedną z legalnych opcji jest użycie numeru wiersza kodu jako numeru sekwencyjnyego lub rozpoczęcia od zera i zwiększenia o jeden lub setki (lub dowolnego preferowanego interwału).</span><span class="sxs-lookup"><span data-stu-id="33673-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="33673-199">używa numerów sekwencji, podczas gdy inne struktury interfejsu użytkownika różnicujące drzewa nie używają ich.</span><span class="sxs-lookup"><span data-stu-id="33673-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="33673-200">Diffing jest znacznie szybsze, gdy Blazor numery sekwencji są używane i ma tę zaletę, krok kompilacji, który zajmuje się numery sekwencji automatycznie dla programistów tworzenia plików *.brzytwa.*</span><span class="sxs-lookup"><span data-stu-id="33673-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="33673-201">Wykonywanie dużych transferów danych w Blazor aplikacjach serwera</span><span class="sxs-lookup"><span data-stu-id="33673-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="33673-202">W niektórych scenariuszach duże ilości danych muszą być Blazorprzesyłane między javascript i .</span><span class="sxs-lookup"><span data-stu-id="33673-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="33673-203">Zazwyczaj duże transfery danych występują, gdy:</span><span class="sxs-lookup"><span data-stu-id="33673-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="33673-204">Interfejsy API systemu plików przeglądarki służą do przekazywania lub pobierania pliku.</span><span class="sxs-lookup"><span data-stu-id="33673-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="33673-205">Wymagany jest interop z biblioteką innej firmy.</span><span class="sxs-lookup"><span data-stu-id="33673-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="33673-206">W Blazor serwerze ograniczenie jest w miejscu, aby zapobiec przekazywaniu pojedynczych dużych wiadomości, które mogą powodować problemy z wydajnością.</span><span class="sxs-lookup"><span data-stu-id="33673-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="33673-207">Podczas opracowywania kodu, który przesyła dane Blazormiędzy javascript i:</span><span class="sxs-lookup"><span data-stu-id="33673-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="33673-208">Podziel dane na mniejsze kawałki i wysyłaj segmenty danych sekwencyjnie, aż wszystkie dane nie będą odbierane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="33673-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="33673-209">Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.</span><span class="sxs-lookup"><span data-stu-id="33673-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="33673-210">Nie blokuj głównego wątku interfejsu użytkownika przez długi czas podczas wysyłania lub odbierania danych.</span><span class="sxs-lookup"><span data-stu-id="33673-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="33673-211">Zwolnić pamięć zużytą po zakończeniu lub anulowaniu procesu.</span><span class="sxs-lookup"><span data-stu-id="33673-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="33673-212">Wymuszaj następujące dodatkowe wymagania ze względów bezpieczeństwa:</span><span class="sxs-lookup"><span data-stu-id="33673-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="33673-213">Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przekazany.</span><span class="sxs-lookup"><span data-stu-id="33673-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="33673-214">Zadeklarować minimalną szybkość przekazywania z klienta do serwera.</span><span class="sxs-lookup"><span data-stu-id="33673-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="33673-215">Po odebraniu danych przez serwer dane mogą być:</span><span class="sxs-lookup"><span data-stu-id="33673-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="33673-216">Tymczasowo przechowywane w buforze pamięci, dopóki nie zostaną zebrane wszystkie segmenty.</span><span class="sxs-lookup"><span data-stu-id="33673-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="33673-217">Zużyte natychmiast.</span><span class="sxs-lookup"><span data-stu-id="33673-217">Consumed immediately.</span></span> <span data-ttu-id="33673-218">Na przykład dane mogą być natychmiast przechowywane w bazie danych lub zapisywane na dysku podczas odnoszenia każdego segmentu.</span><span class="sxs-lookup"><span data-stu-id="33673-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="33673-219">Następująca klasa przekazywania plików obsługuje JS interop z klientem.</span><span class="sxs-lookup"><span data-stu-id="33673-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="33673-220">Klasa przesyłająca używa JS interop do:</span><span class="sxs-lookup"><span data-stu-id="33673-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="33673-221">Sonduj klienta, aby wysłać segment danych.</span><span class="sxs-lookup"><span data-stu-id="33673-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="33673-222">Przerwij transakcję, jeśli limit czasu sondowania.</span><span class="sxs-lookup"><span data-stu-id="33673-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="33673-223">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="33673-223">In the preceding example:</span></span>

* <span data-ttu-id="33673-224">Jest `_maxBase64SegmentSize` ustawiona `8192`na , `_maxBase64SegmentSize = _segmentSize * 4 / 3`który jest obliczany od .</span><span class="sxs-lookup"><span data-stu-id="33673-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="33673-225">Interfejsy API zarządzania pamięcią .NET Core niskiego poziomu służą `_uploadedSegments`do przechowywania segmentów pamięci na serwerze w programie .</span><span class="sxs-lookup"><span data-stu-id="33673-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="33673-226">Metoda `ReceiveFile` jest używana do obsługi przekazywania za pośrednictwem js interop:</span><span class="sxs-lookup"><span data-stu-id="33673-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="33673-227">Rozmiar pliku jest określany w bajtach `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`za pośrednictwem JS interop z .</span><span class="sxs-lookup"><span data-stu-id="33673-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="33673-228">Liczba segmentów do odebrania jest `numberOfSegments`obliczana i przechowywana w pliku .</span><span class="sxs-lookup"><span data-stu-id="33673-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="33673-229">Segmenty są wymagane w `for` pętli przez JS `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`interop z .</span><span class="sxs-lookup"><span data-stu-id="33673-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="33673-230">Wszystkie segmenty, ale ostatni musi być 8,192 bajtów przed dekodowaniem.</span><span class="sxs-lookup"><span data-stu-id="33673-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="33673-231">Klient jest zmuszony do wysyłania danych w skuteczny sposób.</span><span class="sxs-lookup"><span data-stu-id="33673-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="33673-232">Dla każdego odebranego segmentu kontrole <xref:System.Convert.TryFromBase64String*>są wykonywane przed dekodowaniem za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="33673-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="33673-233">Strumień z danymi jest zwracany <xref:System.IO.Stream> `SegmentedStream`jako nowy ( ) po zakończeniu przekazywania.</span><span class="sxs-lookup"><span data-stu-id="33673-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="33673-234">Klasa strumienia segmentowego udostępnia listę segmentów jako nieszukalną <xref:System.IO.Stream>tylko w przypadku odczytu:</span><span class="sxs-lookup"><span data-stu-id="33673-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="33673-235">Następujący kod implementuje funkcje JavaScript do odbierania danych:</span><span class="sxs-lookup"><span data-stu-id="33673-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
