---
title: BlazorZaawansowane scenariusze ASP.NET Core
author: guardrex
description: Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: bdea9f2fe5c552b56414bb49588733c8dc2a34db
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400222"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="6c4b6-103">BlazorZaawansowane scenariusze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c4b6-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="6c4b6-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6c4b6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a>Blazor Server<span data-ttu-id="6c4b6-105">Procedura obsługi obwodu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-105"> circuit handler</span></span>

Blazor Server<span data-ttu-id="6c4b6-106">umożliwia kodowi Definiowanie *procedury obsługi obwodu*, która umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-106"> allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="6c4b6-107">Procedura obsługi obwodu jest implementowana przez wyprowadzanie z `CircuitHandler` i rejestrowanie klasy w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="6c4b6-108">Poniższy przykład obsługi obwodu śledzi otwarte SignalR połączenia:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="6c4b6-109">Procedury obsługi obwodu są rejestrowane przy użyciu funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="6c4b6-110">Wystąpienia w zakresie są tworzone na wystąpienie obwodu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="6c4b6-111">Korzystając z `TrackingCircuitHandler` powyższego przykładu, tworzona jest usługa singleton, ponieważ stan wszystkich obwodów musi być śledzony:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="6c4b6-112">Jeśli metody obsługi niestandardowego obwodu zgłaszają nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="6c4b6-113">Aby tolerować wyjątki w kodzie programu obsługi lub metodach wywoływanych, zawiń kod w jednej lub kilku [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcjach z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="6c4b6-114">Gdy obwód kończy się, ponieważ użytkownik odłączył się i struktura czyści stan obwodu, struktura usuwa zakres DI obwodu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="6c4b6-115">Oddysponowanie zakresu polega na usunięciu wszelkich usług DI-Scope w zakresie, które implementują <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="6c4b6-116">Jeśli jakakolwiek usługa nie zgłasza nieobsłużonego wyjątku podczas usuwania, struktura rejestruje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="6c4b6-117">Ręczna logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="6c4b6-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="6c4b6-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>zapewnia metody manipulowania składnikami i elementami, w tym ręczne Kompilowanie składników w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="6c4b6-119">Korzystanie z programu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> do tworzenia składników jest zaawansowanym scenariuszem.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="6c4b6-120">Nieprawidłowo sformułowany składnik (na przykład niezamknięty tag znacznika) może spowodować niezdefiniowane zachowanie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="6c4b6-121">Rozważmy następujący `PetDetails` składnik, który można ręcznie utworzyć w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="6c4b6-122">W poniższym przykładzie pętla w `CreateComponent` metodzie generuje trzy `PetDetails` składniki.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="6c4b6-123">Podczas wywoływania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod tworzenia składników ( `OpenComponent` i `AddAttribute` ) numery sekwencji są numerami wierszy kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-123">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="6c4b6-124">BlazorAlgorytm różnic polega na numerach sekwencji odpowiadających odrębnym wierszom kodu, a nie odrębnym wywoływaniu wywołań.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="6c4b6-125">Podczas tworzenia składnika przy użyciu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod umieszczaj argumenty dla numerów sekwencji.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="6c4b6-126">**Użycie obliczenia lub licznika do wygenerowania numeru sekwencji może prowadzić do niskiej wydajności.**</span><span class="sxs-lookup"><span data-stu-id="6c4b6-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="6c4b6-127">Aby uzyskać więcej informacji, zobacz sekcję [numery sekwencji powiązane z numerami wierszy kodu i kolejnością niewykonania](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="6c4b6-128">`BuiltContent`składnika</span><span class="sxs-lookup"><span data-stu-id="6c4b6-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="6c4b6-129">Typy w programie <xref:Microsoft.AspNetCore.Components.RenderTree> umożliwiają przetwarzanie *wyników* operacji renderowania.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="6c4b6-130">Są to wewnętrzne szczegóły Blazor implementacji platformy.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="6c4b6-131">Te typy powinny być uznawane za *niestabilne* i mogą ulec zmianie w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="6c4b6-132">Numery sekwencji odnoszą się do numerów wierszy kodu, a nie kolejności wykonywania</span><span class="sxs-lookup"><span data-stu-id="6c4b6-132">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="6c4b6-133">Pliki składników ( `.razor` ) są zawsze kompilowane.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-133"> component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="6c4b6-134">Kompilacja jest potencjalną możliwością przekroczenia interpretacji kodu, ponieważ krok kompilacji może służyć do iniekcji informacji, które zwiększają wydajność aplikacji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="6c4b6-135">Najważniejszym przykładem tych ulepszeń są *numery sekwencji*.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="6c4b6-136">Numery sekwencji wskazują na środowisko uruchomieniowe, które pochodzą z różnych i uporządkowanych wierszy kodu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="6c4b6-137">Środowisko uruchomieniowe używa tych informacji do generowania wydajnych różnic drzewa w czasie liniowym, które są znacznie szybsze niż zwykle jest to możliwe dla algorytmu różnicowego drzewa ogólnego.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="6c4b6-138">Weź pod uwagę następujący Razor plik składnika ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="6c4b6-138">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="6c4b6-139">Poprzedni kod kompiluje się w taki sposób, aby wyglądał następująco:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="6c4b6-140">Gdy kod jest wykonywany po raz pierwszy, jeśli `someFlag` jest `true` , Konstruktor odbiera:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="6c4b6-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="6c4b6-141">Sequence</span></span> | <span data-ttu-id="6c4b6-142">Typ</span><span class="sxs-lookup"><span data-stu-id="6c4b6-142">Type</span></span>      | <span data-ttu-id="6c4b6-143">Dane</span><span class="sxs-lookup"><span data-stu-id="6c4b6-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6c4b6-144">0</span><span class="sxs-lookup"><span data-stu-id="6c4b6-144">0</span></span>        | <span data-ttu-id="6c4b6-145">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-145">Text node</span></span> | <span data-ttu-id="6c4b6-146">First</span><span class="sxs-lookup"><span data-stu-id="6c4b6-146">First</span></span>  |
| <span data-ttu-id="6c4b6-147">1</span><span class="sxs-lookup"><span data-stu-id="6c4b6-147">1</span></span>        | <span data-ttu-id="6c4b6-148">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-148">Text node</span></span> | <span data-ttu-id="6c4b6-149">Sekunda</span><span class="sxs-lookup"><span data-stu-id="6c4b6-149">Second</span></span> |

<span data-ttu-id="6c4b6-150">Wyobraź sobie `someFlag` , że zostanie ona `false` przerenderowana, a znaczniki są renderowane ponownie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="6c4b6-151">Tym razem Konstruktor odbiera:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-151">This time, the builder receives:</span></span>

| <span data-ttu-id="6c4b6-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="6c4b6-152">Sequence</span></span> | <span data-ttu-id="6c4b6-153">Typ</span><span class="sxs-lookup"><span data-stu-id="6c4b6-153">Type</span></span>       | <span data-ttu-id="6c4b6-154">Dane</span><span class="sxs-lookup"><span data-stu-id="6c4b6-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="6c4b6-155">1</span><span class="sxs-lookup"><span data-stu-id="6c4b6-155">1</span></span>        | <span data-ttu-id="6c4b6-156">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-156">Text node</span></span>  | <span data-ttu-id="6c4b6-157">Sekunda</span><span class="sxs-lookup"><span data-stu-id="6c4b6-157">Second</span></span> |

<span data-ttu-id="6c4b6-158">Gdy środowisko uruchomieniowe wykonuje porównanie, zobaczy, że element w sekwencji `0` został usunięty, więc generuje następujący *skrypt*uproszczonej edycji:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="6c4b6-159">Usuń pierwszy węzeł tekstu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="6c4b6-160">Problem z programowo generowanymi numerami sekwencji</span><span class="sxs-lookup"><span data-stu-id="6c4b6-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="6c4b6-161">Wyobraź sobie, że została zapisana następująca logika konstruktora drzewa renderowania:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="6c4b6-162">Teraz pierwsze dane wyjściowe to:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-162">Now, the first output is:</span></span>

| <span data-ttu-id="6c4b6-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="6c4b6-163">Sequence</span></span> | <span data-ttu-id="6c4b6-164">Typ</span><span class="sxs-lookup"><span data-stu-id="6c4b6-164">Type</span></span>      | <span data-ttu-id="6c4b6-165">Dane</span><span class="sxs-lookup"><span data-stu-id="6c4b6-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6c4b6-166">0</span><span class="sxs-lookup"><span data-stu-id="6c4b6-166">0</span></span>        | <span data-ttu-id="6c4b6-167">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-167">Text node</span></span> | <span data-ttu-id="6c4b6-168">First</span><span class="sxs-lookup"><span data-stu-id="6c4b6-168">First</span></span>  |
| <span data-ttu-id="6c4b6-169">1</span><span class="sxs-lookup"><span data-stu-id="6c4b6-169">1</span></span>        | <span data-ttu-id="6c4b6-170">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-170">Text node</span></span> | <span data-ttu-id="6c4b6-171">Sekunda</span><span class="sxs-lookup"><span data-stu-id="6c4b6-171">Second</span></span> |

<span data-ttu-id="6c4b6-172">Ten wynik jest identyczny z poprzednim przypadkiem, dlatego nie istnieją żadne negatywne problemy.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="6c4b6-173">`someFlag`znajduje się `false` na drugim renderingu, a dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="6c4b6-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="6c4b6-174">Sequence</span></span> | <span data-ttu-id="6c4b6-175">Typ</span><span class="sxs-lookup"><span data-stu-id="6c4b6-175">Type</span></span>      | <span data-ttu-id="6c4b6-176">Dane</span><span class="sxs-lookup"><span data-stu-id="6c4b6-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="6c4b6-177">0</span><span class="sxs-lookup"><span data-stu-id="6c4b6-177">0</span></span>        | <span data-ttu-id="6c4b6-178">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="6c4b6-178">Text node</span></span> | <span data-ttu-id="6c4b6-179">Sekunda</span><span class="sxs-lookup"><span data-stu-id="6c4b6-179">Second</span></span> |

<span data-ttu-id="6c4b6-180">Tym razem algorytm diff widzi, że pojawiły się *dwie* zmiany, a algorytm generuje następujący skrypt edycji:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="6c4b6-181">Zmień wartość pierwszego węzła tekstowego na `Second` .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="6c4b6-182">Usuń drugi węzeł tekstu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-182">Remove the second text node.</span></span>

<span data-ttu-id="6c4b6-183">Generowanie numerów sekwencji utraciło wszystkie przydatne informacje o tym, gdzie `if/else` znajdują się gałęzie i pętle w oryginalnym kodzie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="6c4b6-184">Wynikiem tego jest różnica **dwa razy** , tak długo, jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="6c4b6-185">Jest to prosty przykład.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-185">This is a trivial example.</span></span> <span data-ttu-id="6c4b6-186">W bardziej realistycznych przypadkach ze złożonymi i głęboko zagnieżdżonymi strukturami, w szczególności z pętlami, koszt wydajności jest zwykle wyższy.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="6c4b6-187">Zamiast natychmiastowego identyfikowania, które bloki lub gałęzie pętli zostały wstawione lub usunięte, algorytm diff musi odnosił się do drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="6c4b6-188">Zwykle polega to na konieczności kompilowania dłużej edytowanych skryptów, ponieważ algorytm różnicowy jest niewiadome o tym, jak stare i nowe struktury odnoszą się do siebie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="6c4b6-189">Wskazówki i wnioski</span><span class="sxs-lookup"><span data-stu-id="6c4b6-189">Guidance and conclusions</span></span>

* <span data-ttu-id="6c4b6-190">Wydajność aplikacji ma wpływ na to, że numery sekwencji są generowane dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="6c4b6-191">Struktura nie może automatycznie tworzyć własnych numerów sekwencji w czasie wykonywania, ponieważ niezbędne informacje nie istnieją, chyba że są przechwytywane w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="6c4b6-192">Nie zapisuj długich bloków logiki wykonywanej ręcznie <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="6c4b6-193">Preferuj `.razor` pliki i Zezwalaj kompilatorowi na rozpatruje numery sekwencji.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="6c4b6-194">Jeśli nie możesz uniknąć ręcznej <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logiki, Podziel długie bloki kodu na mniejsze fragmenty opakowane <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="6c4b6-195">Każdy region ma własne oddzielne miejsce numerów sekwencyjnych, więc można uruchomić ponownie od zera (lub dowolnego innego numeru) w każdym regionie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="6c4b6-196">Jeśli numery sekwencji są stałee, algorytm diff wymaga tylko zwiększenia wartości sekwencji.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="6c4b6-197">Początkowa wartość i przerwy są nieistotne.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="6c4b6-198">Jedną z wiarygodnych opcji jest użycie numeru wiersza kodu jako numeru sekwencyjnego lub rozpoczęcie od zera i zwiększenie według wartości lub setek (lub dowolnego preferowanego interwału).</span><span class="sxs-lookup"><span data-stu-id="6c4b6-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="6c4b6-199">używa numerów sekwencji, podczas gdy inne struktury interfejsu użytkownika rozróżniania drzewa nie są używane.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="6c4b6-200">Różnica jest znacznie szybsza, gdy są używane numery sekwencyjne i Blazor ma zalety krok kompilacji, który zajmuje się automatycznie numerami sekwencyjnymi dla deweloperów tworzących `.razor` pliki.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="6c4b6-201">Wykonywanie dużych transferów danych w Blazor Server aplikacjach</span><span class="sxs-lookup"><span data-stu-id="6c4b6-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="6c4b6-202">W niektórych scenariuszach należy przenieść duże ilości danych między językami JavaScript i Blazor .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="6c4b6-203">Zwykle duże transfery danych odbywają się w przypadku:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="6c4b6-204">Interfejsy API systemu plików przeglądarki służą do przekazywania lub pobierania pliku.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="6c4b6-205">Wymagana jest współdziałanie z biblioteką innej firmy.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="6c4b6-206">W programie Blazor Server ograniczenie jest stosowane w celu uniemożliwienia przekazywania pojedynczych dużych komunikatów, które mogą powodować problemy z wydajnością.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="6c4b6-207">Podczas tworzenia kodu, który przesyła dane między językami JavaScript, należy wziąć pod uwagę następujące wskazówki Blazor :</span><span class="sxs-lookup"><span data-stu-id="6c4b6-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="6c4b6-208">Wydziel dane na mniejsze fragmenty i Wyślij segmenty danych sekwencyjnie, dopóki wszystkie dane nie zostaną odebrane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="6c4b6-209">Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="6c4b6-210">Nie blokuj głównego wątku interfejsu użytkownika przez długie okresy podczas wysyłania lub otrzymywania danych.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="6c4b6-211">Zwolnij wszystkie używane pamięci, gdy proces zostanie ukończony lub anulowany.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="6c4b6-212">Wymuś następujące dodatkowe wymagania dotyczące zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="6c4b6-213">Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przesłany.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="6c4b6-214">Zadeklaruj minimalną szybkość przekazywania z klienta na serwerze.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="6c4b6-215">Po odebraniu danych przez serwer dane mogą być następujące:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="6c4b6-216">Tymczasowo przechowywane w buforze pamięci do momentu zebrania wszystkich segmentów.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="6c4b6-217">Wykorzystano natychmiast.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-217">Consumed immediately.</span></span> <span data-ttu-id="6c4b6-218">Na przykład dane mogą być przechowywane bezpośrednio w bazie danych lub zapisywane na dysku w miarę odbierania poszczególnych segmentów.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="6c4b6-219">Następująca Klasa obiektu przekazującego plików obsługuje program JS Interop z klientem programu.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="6c4b6-220">Klasa obiektu przekazującego używa programu JS Interop do:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="6c4b6-221">Przesondowaj klienta, aby wysłał segment danych.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="6c4b6-222">Przerywaj transakcję, jeśli trwa sondowanie.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="6c4b6-223">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-223">In the preceding example:</span></span>

* <span data-ttu-id="6c4b6-224">`maxBase64SegmentSize`Jest ustawiona na `8192` , który jest obliczany z `maxBase64SegmentSize = segmentSize * 4 / 3` .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="6c4b6-225">Interfejsy API zarządzania pamięcią programu .NET Core na niskim poziomie są używane do przechowywania segmentów pamięci na serwerze w systemie `uploadedSegments` .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="6c4b6-226">`ReceiveFile`Metoda jest używana do obsługi przekazywania za pomocą narzędzia js Interop:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="6c4b6-227">Rozmiar pliku jest określany w bajtach za pomocą narzędzia JS Interop z `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="6c4b6-228">Liczba segmentów do odebrania jest obliczana i przechowywana w `numberOfSegments` .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="6c4b6-229">Segmenty są żądane w `for` pętli za pomocą narzędzia js Interop z `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="6c4b6-230">Wszystkie segmenty, ale ostatnie muszą mieć 8 192 bajtów przed dekodowaniem.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="6c4b6-231">Klient jest zmuszony do wydajnego wysyłania danych.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="6c4b6-232">W przypadku każdego odebranego segmentu sprawdzenia są wykonywane przed dekodowaniem w <xref:System.Convert.TryFromBase64String%2A> .</span><span class="sxs-lookup"><span data-stu-id="6c4b6-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="6c4b6-233">Strumień z danymi jest zwracany jako nowy <xref:System.IO.Stream> ( `SegmentedStream` ) po zakończeniu przekazywania.</span><span class="sxs-lookup"><span data-stu-id="6c4b6-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="6c4b6-234">Klasa łańcucha segmentów uwidacznia listę segmentów jako niemożliwy do przeszukiwania <xref:System.IO.Stream> :</span><span class="sxs-lookup"><span data-stu-id="6c4b6-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
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

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

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

<span data-ttu-id="6c4b6-235">Poniższy kod implementuje funkcje języka JavaScript do odbierania danych:</span><span class="sxs-lookup"><span data-stu-id="6c4b6-235">The following code implements JavaScript functions to receive the data:</span></span>

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
