---
title: :::no-loc(Blazor):::Zaawansowane scenariusze ASP.NET Core
author: guardrex
description: 'Poznaj zaawansowane scenariusze w programie :::no-loc(Blazor)::: , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056559"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="4deac-103">:::no-loc(Blazor):::Zaawansowane scenariusze ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4deac-103">ASP.NET Core :::no-loc(Blazor)::: advanced scenarios</span></span>

<span data-ttu-id="4deac-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4deac-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="4deac-105">:::no-loc(Blazor Server)::: Procedura obsługi obwodu</span><span class="sxs-lookup"><span data-stu-id="4deac-105">:::no-loc(Blazor Server)::: circuit handler</span></span>

<span data-ttu-id="4deac-106">:::no-loc(Blazor Server)::: umożliwia kodowi Definiowanie *procedury obsługi obwodu* , która umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4deac-106">:::no-loc(Blazor Server)::: allows code to define a *circuit handler* , which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="4deac-107">Procedura obsługi obwodu jest implementowana przez wyprowadzanie z `CircuitHandler` i rejestrowanie klasy w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4deac-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="4deac-108">Poniższy przykład obsługi obwodu śledzi otwarte :::no-loc(SignalR)::: połączenia:</span><span class="sxs-lookup"><span data-stu-id="4deac-108">The following example of a circuit handler tracks open :::no-loc(SignalR)::: connections:</span></span>

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

<span data-ttu-id="4deac-109">Procedury obsługi obwodu są rejestrowane przy użyciu funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="4deac-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="4deac-110">Wystąpienia w zakresie są tworzone na wystąpienie obwodu.</span><span class="sxs-lookup"><span data-stu-id="4deac-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="4deac-111">Korzystając z `TrackingCircuitHandler` powyższego przykładu, tworzona jest usługa singleton, ponieważ stan wszystkich obwodów musi być śledzony:</span><span class="sxs-lookup"><span data-stu-id="4deac-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="4deac-112">Jeśli metody obsługi niestandardowego obwodu zgłaszają nieobsługiwany wyjątek, wyjątek jest krytyczny dla :::no-loc(Blazor Server)::: obwodu.</span><span class="sxs-lookup"><span data-stu-id="4deac-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="4deac-113">Aby tolerować wyjątki w kodzie programu obsługi lub metodach wywoływanych, zawiń kod w jednej lub kilku [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcjach z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="4deac-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="4deac-114">Gdy obwód kończy się, ponieważ użytkownik odłączył się i struktura czyści stan obwodu, struktura usuwa zakres DI obwodu.</span><span class="sxs-lookup"><span data-stu-id="4deac-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="4deac-115">Oddysponowanie zakresu polega na usunięciu wszelkich usług DI-Scope w zakresie, które implementują <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="4deac-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="4deac-116">Jeśli jakakolwiek usługa nie zgłasza nieobsłużonego wyjątku podczas usuwania, struktura rejestruje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="4deac-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="4deac-117">Ręczna logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="4deac-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="4deac-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> zapewnia metody manipulowania składnikami i elementami, w tym ręczne Kompilowanie składników w kodzie C#.</span><span class="sxs-lookup"><span data-stu-id="4deac-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="4deac-119">Korzystanie z programu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> do tworzenia składników jest zaawansowanym scenariuszem.</span><span class="sxs-lookup"><span data-stu-id="4deac-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="4deac-120">Nieprawidłowo sformułowany składnik (na przykład niezamknięty tag znacznika) może spowodować niezdefiniowane zachowanie.</span><span class="sxs-lookup"><span data-stu-id="4deac-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="4deac-121">Rozważmy następujący `PetDetails` składnik, który można ręcznie utworzyć w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="4deac-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="4deac-122">W poniższym przykładzie pętla w `CreateComponent` metodzie generuje trzy `PetDetails` składniki.</span><span class="sxs-lookup"><span data-stu-id="4deac-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="4deac-123">W <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metodach z numerem sekwencyjnym numery sekwencji są numerami wierszy kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="4deac-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="4deac-124">:::no-loc(Blazor):::Algorytm różnic polega na numerach sekwencji odpowiadających odrębnym wierszom kodu, a nie odrębnym wywoływaniu wywołań.</span><span class="sxs-lookup"><span data-stu-id="4deac-124">The :::no-loc(Blazor)::: difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="4deac-125">Podczas tworzenia składnika przy użyciu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod umieszczaj argumenty dla numerów sekwencji.</span><span class="sxs-lookup"><span data-stu-id="4deac-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="4deac-126">**Użycie obliczenia lub licznika do wygenerowania numeru sekwencji może prowadzić do niskiej wydajności.**</span><span class="sxs-lookup"><span data-stu-id="4deac-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="4deac-127">Aby uzyskać więcej informacji, zobacz sekcję [numery sekwencji powiązane z numerami wierszy kodu i kolejnością niewykonania](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="4deac-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="4deac-128">`BuiltContent` składnika</span><span class="sxs-lookup"><span data-stu-id="4deac-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="4deac-129">Typy w programie <xref:Microsoft.AspNetCore.Components.RenderTree> umożliwiają przetwarzanie *wyników* operacji renderowania.</span><span class="sxs-lookup"><span data-stu-id="4deac-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="4deac-130">Są to wewnętrzne szczegóły :::no-loc(Blazor)::: implementacji platformy.</span><span class="sxs-lookup"><span data-stu-id="4deac-130">These are internal details of the :::no-loc(Blazor)::: framework implementation.</span></span> <span data-ttu-id="4deac-131">Te typy powinny być uznawane za *niestabilne* i mogą ulec zmianie w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="4deac-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="4deac-132">Numery sekwencji odnoszą się do numerów wierszy kodu, a nie kolejności wykonywania</span><span class="sxs-lookup"><span data-stu-id="4deac-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="4deac-133">:::no-loc(Razor)::: Pliki składników ( `.razor` ) są zawsze kompilowane.</span><span class="sxs-lookup"><span data-stu-id="4deac-133">:::no-loc(Razor)::: component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="4deac-134">Kompilacja jest potencjalną możliwością przekroczenia interpretacji kodu, ponieważ krok kompilacji może służyć do iniekcji informacji, które zwiększają wydajność aplikacji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4deac-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="4deac-135">Najważniejszym przykładem tych ulepszeń są *numery sekwencji* .</span><span class="sxs-lookup"><span data-stu-id="4deac-135">A key example of these improvements involves *sequence numbers* .</span></span> <span data-ttu-id="4deac-136">Numery sekwencji wskazują na środowisko uruchomieniowe, które pochodzą z różnych i uporządkowanych wierszy kodu.</span><span class="sxs-lookup"><span data-stu-id="4deac-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="4deac-137">Środowisko uruchomieniowe używa tych informacji do generowania wydajnych różnic drzewa w czasie liniowym, które są znacznie szybsze niż zwykle jest to możliwe dla algorytmu różnicowego drzewa ogólnego.</span><span class="sxs-lookup"><span data-stu-id="4deac-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="4deac-138">Weź pod uwagę następujący :::no-loc(Razor)::: plik składnika ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="4deac-138">Consider the following :::no-loc(Razor)::: component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="4deac-139">Poprzedni kod kompiluje się w taki sposób, aby wyglądał następująco:</span><span class="sxs-lookup"><span data-stu-id="4deac-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="4deac-140">Gdy kod jest wykonywany po raz pierwszy, jeśli `someFlag` jest `true` , Konstruktor odbiera:</span><span class="sxs-lookup"><span data-stu-id="4deac-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="4deac-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="4deac-141">Sequence</span></span> | <span data-ttu-id="4deac-142">Typ</span><span class="sxs-lookup"><span data-stu-id="4deac-142">Type</span></span>      | <span data-ttu-id="4deac-143">Dane</span><span class="sxs-lookup"><span data-stu-id="4deac-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="4deac-144">0</span><span class="sxs-lookup"><span data-stu-id="4deac-144">0</span></span>        | <span data-ttu-id="4deac-145">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="4deac-145">Text node</span></span> | <span data-ttu-id="4deac-146">Pierwsze</span><span class="sxs-lookup"><span data-stu-id="4deac-146">First</span></span>  |
| <span data-ttu-id="4deac-147">1</span><span class="sxs-lookup"><span data-stu-id="4deac-147">1</span></span>        | <span data-ttu-id="4deac-148">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="4deac-148">Text node</span></span> | <span data-ttu-id="4deac-149">Second</span><span class="sxs-lookup"><span data-stu-id="4deac-149">Second</span></span> |

<span data-ttu-id="4deac-150">Wyobraź sobie `someFlag` , że zostanie ona `false` przerenderowana, a znaczniki są renderowane ponownie.</span><span class="sxs-lookup"><span data-stu-id="4deac-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="4deac-151">Tym razem Konstruktor odbiera:</span><span class="sxs-lookup"><span data-stu-id="4deac-151">This time, the builder receives:</span></span>

| <span data-ttu-id="4deac-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="4deac-152">Sequence</span></span> | <span data-ttu-id="4deac-153">Typ</span><span class="sxs-lookup"><span data-stu-id="4deac-153">Type</span></span>       | <span data-ttu-id="4deac-154">Dane</span><span class="sxs-lookup"><span data-stu-id="4deac-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="4deac-155">1</span><span class="sxs-lookup"><span data-stu-id="4deac-155">1</span></span>        | <span data-ttu-id="4deac-156">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="4deac-156">Text node</span></span>  | <span data-ttu-id="4deac-157">Second</span><span class="sxs-lookup"><span data-stu-id="4deac-157">Second</span></span> |

<span data-ttu-id="4deac-158">Gdy środowisko uruchomieniowe wykonuje porównanie, zobaczy, że element w sekwencji `0` został usunięty, więc generuje następujący *skrypt* uproszczonej edycji:</span><span class="sxs-lookup"><span data-stu-id="4deac-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* :</span></span>

* <span data-ttu-id="4deac-159">Usuń pierwszy węzeł tekstu.</span><span class="sxs-lookup"><span data-stu-id="4deac-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="4deac-160">Problem z programowo generowanymi numerami sekwencji</span><span class="sxs-lookup"><span data-stu-id="4deac-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="4deac-161">Wyobraź sobie, że została zapisana następująca logika konstruktora drzewa renderowania:</span><span class="sxs-lookup"><span data-stu-id="4deac-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="4deac-162">Teraz pierwsze dane wyjściowe to:</span><span class="sxs-lookup"><span data-stu-id="4deac-162">Now, the first output is:</span></span>

| <span data-ttu-id="4deac-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="4deac-163">Sequence</span></span> | <span data-ttu-id="4deac-164">Typ</span><span class="sxs-lookup"><span data-stu-id="4deac-164">Type</span></span>      | <span data-ttu-id="4deac-165">Dane</span><span class="sxs-lookup"><span data-stu-id="4deac-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="4deac-166">0</span><span class="sxs-lookup"><span data-stu-id="4deac-166">0</span></span>        | <span data-ttu-id="4deac-167">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="4deac-167">Text node</span></span> | <span data-ttu-id="4deac-168">Pierwsze</span><span class="sxs-lookup"><span data-stu-id="4deac-168">First</span></span>  |
| <span data-ttu-id="4deac-169">1</span><span class="sxs-lookup"><span data-stu-id="4deac-169">1</span></span>        | <span data-ttu-id="4deac-170">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="4deac-170">Text node</span></span> | <span data-ttu-id="4deac-171">Second</span><span class="sxs-lookup"><span data-stu-id="4deac-171">Second</span></span> |

<span data-ttu-id="4deac-172">Ten wynik jest identyczny z poprzednim przypadkiem, dlatego nie istnieją żadne negatywne problemy.</span><span class="sxs-lookup"><span data-stu-id="4deac-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="4deac-173">`someFlag` znajduje się `false` na drugim renderingu, a dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="4deac-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="4deac-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="4deac-174">Sequence</span></span> | <span data-ttu-id="4deac-175">Typ</span><span class="sxs-lookup"><span data-stu-id="4deac-175">Type</span></span>      | <span data-ttu-id="4deac-176">Dane</span><span class="sxs-lookup"><span data-stu-id="4deac-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="4deac-177">0</span><span class="sxs-lookup"><span data-stu-id="4deac-177">0</span></span>        | <span data-ttu-id="4deac-178">Węzeł tekstu</span><span class="sxs-lookup"><span data-stu-id="4deac-178">Text node</span></span> | <span data-ttu-id="4deac-179">Second</span><span class="sxs-lookup"><span data-stu-id="4deac-179">Second</span></span> |

<span data-ttu-id="4deac-180">Tym razem algorytm diff widzi, że pojawiły się *dwie* zmiany, a algorytm generuje następujący skrypt edycji:</span><span class="sxs-lookup"><span data-stu-id="4deac-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="4deac-181">Zmień wartość pierwszego węzła tekstowego na `Second` .</span><span class="sxs-lookup"><span data-stu-id="4deac-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="4deac-182">Usuń drugi węzeł tekstu.</span><span class="sxs-lookup"><span data-stu-id="4deac-182">Remove the second text node.</span></span>

<span data-ttu-id="4deac-183">Generowanie numerów sekwencji utraciło wszystkie przydatne informacje o tym, gdzie `if/else` znajdują się gałęzie i pętle w oryginalnym kodzie.</span><span class="sxs-lookup"><span data-stu-id="4deac-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="4deac-184">Wynikiem tego jest różnica **dwa razy** , tak długo, jak wcześniej.</span><span class="sxs-lookup"><span data-stu-id="4deac-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="4deac-185">Jest to prosty przykład.</span><span class="sxs-lookup"><span data-stu-id="4deac-185">This is a trivial example.</span></span> <span data-ttu-id="4deac-186">W bardziej realistycznych przypadkach ze złożonymi i głęboko zagnieżdżonymi strukturami, w szczególności z pętlami, koszt wydajności jest zwykle wyższy.</span><span class="sxs-lookup"><span data-stu-id="4deac-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="4deac-187">Zamiast natychmiastowego identyfikowania, które bloki lub gałęzie pętli zostały wstawione lub usunięte, algorytm diff musi odnosił się do drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="4deac-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="4deac-188">Zwykle polega to na konieczności kompilowania dłużej edytowanych skryptów, ponieważ algorytm różnicowy jest niewiadome o tym, jak stare i nowe struktury odnoszą się do siebie.</span><span class="sxs-lookup"><span data-stu-id="4deac-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="4deac-189">Wskazówki i wnioski</span><span class="sxs-lookup"><span data-stu-id="4deac-189">Guidance and conclusions</span></span>

* <span data-ttu-id="4deac-190">Wydajność aplikacji ma wpływ na to, że numery sekwencji są generowane dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="4deac-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="4deac-191">Struktura nie może automatycznie tworzyć własnych numerów sekwencji w czasie wykonywania, ponieważ niezbędne informacje nie istnieją, chyba że są przechwytywane w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="4deac-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="4deac-192">Nie zapisuj długich bloków logiki wykonywanej ręcznie <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4deac-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="4deac-193">Preferuj `.razor` pliki i Zezwalaj kompilatorowi na rozpatruje numery sekwencji.</span><span class="sxs-lookup"><span data-stu-id="4deac-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="4deac-194">Jeśli nie możesz uniknąć ręcznej <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logiki, Podziel długie bloki kodu na mniejsze fragmenty opakowane <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> .</span><span class="sxs-lookup"><span data-stu-id="4deac-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="4deac-195">Każdy region ma własne oddzielne miejsce numerów sekwencyjnych, więc można uruchomić ponownie od zera (lub dowolnego innego numeru) w każdym regionie.</span><span class="sxs-lookup"><span data-stu-id="4deac-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="4deac-196">Jeśli numery sekwencji są stałee, algorytm diff wymaga tylko zwiększenia wartości sekwencji.</span><span class="sxs-lookup"><span data-stu-id="4deac-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="4deac-197">Początkowa wartość i przerwy są nieistotne.</span><span class="sxs-lookup"><span data-stu-id="4deac-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="4deac-198">Jedną z wiarygodnych opcji jest użycie numeru wiersza kodu jako numeru sekwencyjnego lub rozpoczęcie od zera i zwiększenie według wartości lub setek (lub dowolnego preferowanego interwału).</span><span class="sxs-lookup"><span data-stu-id="4deac-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="4deac-199">:::no-loc(Blazor)::: używa numerów sekwencji, podczas gdy inne struktury interfejsu użytkownika rozróżniania drzewa nie są używane.</span><span class="sxs-lookup"><span data-stu-id="4deac-199">:::no-loc(Blazor)::: uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="4deac-200">Różnica jest znacznie szybsza, gdy są używane numery sekwencyjne i :::no-loc(Blazor)::: ma zalety krok kompilacji, który zajmuje się automatycznie numerami sekwencyjnymi dla deweloperów tworzących `.razor` pliki.</span><span class="sxs-lookup"><span data-stu-id="4deac-200">Diffing is far faster when sequence numbers are used, and :::no-loc(Blazor)::: has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
