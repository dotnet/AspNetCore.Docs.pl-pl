---
title: 'ASP.NET Core Blazor cykl życia'
author: guardrex
description: 'Dowiedz się, jak korzystać z Razor metod cyklu życia składników w Blazor aplikacjach ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 08fc393160e0a7396963901e2add3b44fc7b02b9
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508030"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="282e3-103">ASP.NET Core Blazor cykl życia</span><span class="sxs-lookup"><span data-stu-id="282e3-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="282e3-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="282e3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="282e3-105">BlazorPlatforma obejmuje metody cyklu życia synchronicznego i asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="282e3-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="282e3-106">Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="282e3-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="282e3-107">Poniższe diagramy ilustrują Blazor cykl życia.</span><span class="sxs-lookup"><span data-stu-id="282e3-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="282e3-108">Metody cyklu życia są definiowane przy użyciu przykładów w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="282e3-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="282e3-109">Zdarzenia cyklu życia składnika:</span><span class="sxs-lookup"><span data-stu-id="282e3-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="282e3-110">Jeśli składnik jest renderowany po raz pierwszy w żądaniu:</span><span class="sxs-lookup"><span data-stu-id="282e3-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="282e3-111">Utwórz wystąpienie składnika.</span><span class="sxs-lookup"><span data-stu-id="282e3-111">Create the component's instance.</span></span>
   * <span data-ttu-id="282e3-112">Wykonaj iniekcję właściwości.</span><span class="sxs-lookup"><span data-stu-id="282e3-112">Perform property injection.</span></span> <span data-ttu-id="282e3-113">Uruchom [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="282e3-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="282e3-114">Wywołanie [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="282e3-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="282e3-115">W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie jest renderowany składnik.</span><span class="sxs-lookup"><span data-stu-id="282e3-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="282e3-116">Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, renderowanie składnika.</span><span class="sxs-lookup"><span data-stu-id="282e3-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="282e3-117">Wywołaj [`OnParametersSet{Async}`](#after-parameters-are-set) i Renderuj składnik.</span><span class="sxs-lookup"><span data-stu-id="282e3-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="282e3-118">Jeśli <xref:System.Threading.Tasks.Task> jest zwracany z `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie składnik jest ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="282e3-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Zdarzenia cyklu życia składnika::: No-Loc (Razor)::: składnik w::: No-Loc (Blazor)::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="282e3-120">Przetwarzanie zdarzeń w Document Object Model (DOM):</span><span class="sxs-lookup"><span data-stu-id="282e3-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="282e3-121">Procedura obsługi zdarzeń jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="282e3-121">The event handler is run.</span></span>
1. <span data-ttu-id="282e3-122">W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie jest renderowany składnik.</span><span class="sxs-lookup"><span data-stu-id="282e3-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="282e3-123">Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="282e3-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Przetwarzanie zdarzeń Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="282e3-125">`Render`Cykl życia:</span><span class="sxs-lookup"><span data-stu-id="282e3-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="282e3-126">Jeśli nie jest to pierwsze renderowanie składnika lub [`ShouldRender`](#suppress-ui-refreshing) jest oceniane jako `false` , nie wykonuj dalszych operacji na składniku.</span><span class="sxs-lookup"><span data-stu-id="282e3-126">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="282e3-127">Kompiluj porównanie drzewa renderowania (różnica) i Renderuj składnik.</span><span class="sxs-lookup"><span data-stu-id="282e3-127">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="282e3-128">Oczekiwanie na zaktualizowanie modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="282e3-128">Await the DOM to update.</span></span>
1. <span data-ttu-id="282e3-129">Wywołanie [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="282e3-129">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Cykl życia renderowania](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="282e3-131">Deweloperzy mogą wykonywać wywołania [`StateHasChanged`](#state-changes) w wyniku.</span><span class="sxs-lookup"><span data-stu-id="282e3-131">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="282e3-132">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="282e3-132">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="282e3-133">Przed ustawieniem parametrów</span><span class="sxs-lookup"><span data-stu-id="282e3-133">Before parameters are set</span></span>

<span data-ttu-id="282e3-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania:</span><span class="sxs-lookup"><span data-stu-id="282e3-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="282e3-135"><xref:Microsoft.AspNetCore.Components.ParameterView> zawiera zestaw wartości parametrów dla składnika, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="282e3-135"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="282e3-136">Domyślna implementacja programu <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ustawia wartość każdej właściwości z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atrybutem lub, który ma odpowiednią wartość w <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="282e3-136">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="282e3-137">Parametry, które nie mają odpowiedniej wartości w, <xref:Microsoft.AspNetCore.Components.ParameterView> pozostają bez zmian.</span><span class="sxs-lookup"><span data-stu-id="282e3-137">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="282e3-138">Jeśli [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób.</span><span class="sxs-lookup"><span data-stu-id="282e3-138">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="282e3-139">Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="282e3-139">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="282e3-140">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="282e3-140">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="282e3-141">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="282e3-141">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="282e3-142">Metody inicjujące składniki</span><span class="sxs-lookup"><span data-stu-id="282e3-142">Component initialization methods</span></span>

<span data-ttu-id="282e3-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego w <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="282e3-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="282e3-144">Użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="282e3-144">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="282e3-145">W przypadku operacji synchronicznej Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="282e3-145">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="282e3-146">Aby wykonać operację asynchroniczną, Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i Użyj [`await`](/dotnet/csharp/language-reference/operators/await) operatora dla operacji:</span><span class="sxs-lookup"><span data-stu-id="282e3-146">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="282e3-147">Blazor Serveraplikacje, które dwukrotnie wywołują [swoje wywołanie zawartości](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_** :</span><span class="sxs-lookup"><span data-stu-id="282e3-147">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_** :</span></span>

* <span data-ttu-id="282e3-148">Gdy składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="282e3-148">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="282e3-149">Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="282e3-149">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="282e3-150">Aby zapobiec <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dwukrotnemu uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="282e3-150">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="282e3-151">Podczas gdy Blazor Server aplikacja jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="282e3-151">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="282e3-152">Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.</span><span class="sxs-lookup"><span data-stu-id="282e3-152">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="282e3-153">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="282e3-153">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="282e3-154">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="282e3-154">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="282e3-155">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="282e3-155">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="282e3-156">Po ustawieniu parametrów</span><span class="sxs-lookup"><span data-stu-id="282e3-156">After parameters are set</span></span>

<span data-ttu-id="282e3-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:</span><span class="sxs-lookup"><span data-stu-id="282e3-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="282e3-158">Po zainicjowaniu składnika w programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="282e3-158">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="282e3-159">Po ponownym wyrenderowaniu i zaopatrzeniu składnika nadrzędnego:</span><span class="sxs-lookup"><span data-stu-id="282e3-159">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="282e3-160">Tylko znane niezmienne typy pierwotne, których co najmniej jeden parametr został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="282e3-160">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="282e3-161">Wszystkie parametry złożone z typem.</span><span class="sxs-lookup"><span data-stu-id="282e3-161">Any complex-typed parameters.</span></span> <span data-ttu-id="282e3-162">Struktura nie może wiedzieć, czy wartości parametru złożonego są mutacją wewnętrznie, dlatego traktuje zestaw parametrów jako zmieniony.</span><span class="sxs-lookup"><span data-stu-id="282e3-162">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="282e3-163">Asynchroniczna działanie, gdy stosowane są parametry i wartości właściwości, muszą wystąpić podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="282e3-163">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="282e3-164">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="282e3-164">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="282e3-165">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="282e3-165">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="282e3-166">Po renderowania składników</span><span class="sxs-lookup"><span data-stu-id="282e3-166">After component render</span></span>

<span data-ttu-id="282e3-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="282e3-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="282e3-168">Odwołania do elementów i składników są wypełniane w tym momencie.</span><span class="sxs-lookup"><span data-stu-id="282e3-168">Element and component references are populated at this point.</span></span> <span data-ttu-id="282e3-169">Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.</span><span class="sxs-lookup"><span data-stu-id="282e3-169">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="282e3-170">`firstRender`Parametr dla <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="282e3-170">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="282e3-171">Jest ustawiany po `true` raz pierwszy, gdy wystąpienie składnika jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="282e3-171">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="282e3-172">Można go użyć, aby upewnić się, że zadania inicjowania są wykonywane tylko raz.</span><span class="sxs-lookup"><span data-stu-id="282e3-172">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="282e3-173">Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić w trakcie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="282e3-173">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="282e3-174">Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z programu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania.</span><span class="sxs-lookup"><span data-stu-id="282e3-174">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="282e3-175">Ma to na celu uniknięcie nieskończonej pętli renderowania.</span><span class="sxs-lookup"><span data-stu-id="282e3-175">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="282e3-176">Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.</span><span class="sxs-lookup"><span data-stu-id="282e3-176">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="282e3-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nie są wywoływane podczas procesu renderowania wstępnego na serwerze*.</span><span class="sxs-lookup"><span data-stu-id="282e3-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="282e3-178">Metody są wywoływane, gdy składnik jest renderowany interaktywnie po zakończeniu renderowania prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="282e3-178">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="282e3-179">Gdy aplikacja jest przedrenderowana:</span><span class="sxs-lookup"><span data-stu-id="282e3-179">When the app prerenders:</span></span>

1. <span data-ttu-id="282e3-180">Składnik jest wykonywany na serwerze w celu utworzenia statycznego znacznika HTML w odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="282e3-180">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="282e3-181">W tej fazie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nie są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="282e3-181">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="282e3-182">Kiedy `blazor.server.js` lub `blazor.webassembly.js` uruchamiasz w przeglądarce, składnik jest uruchamiany ponownie w trybie renderowania interaktywnego.</span><span class="sxs-lookup"><span data-stu-id="282e3-182">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="282e3-183">Po ponownym uruchomieniu składnika <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **są** wywoływane, ponieważ aplikacja nie znajduje się w fazie renderowania prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="282e3-183">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="282e3-184">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="282e3-184">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="282e3-185">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="282e3-185">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="282e3-186">Pomiń odświeżanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="282e3-186">Suppress UI refreshing</span></span>

<span data-ttu-id="282e3-187">Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="282e3-187">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="282e3-188">Jeśli implementacja zwraca `true` , interfejs użytkownika zostanie odświeżony:</span><span class="sxs-lookup"><span data-stu-id="282e3-188">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="282e3-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest wywoływana za każdym razem, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="282e3-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="282e3-190">Nawet jeśli <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest zastępowany, składnik jest zawsze początkowo renderowany.</span><span class="sxs-lookup"><span data-stu-id="282e3-190">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="282e3-191">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="282e3-191">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="282e3-192">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="282e3-192">State changes</span></span>

<span data-ttu-id="282e3-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powiadamia składnik o zmianie stanu.</span><span class="sxs-lookup"><span data-stu-id="282e3-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="282e3-194">Jeśli ma to zastosowanie, wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje, że składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="282e3-194">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="282e3-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana automatycznie dla <xref:Microsoft.AspNetCore.Components.EventCallback> metod.</span><span class="sxs-lookup"><span data-stu-id="282e3-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="282e3-196">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="282e3-196">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="282e3-197">Obsługuj niekompletne akcje asynchroniczne podczas renderowania</span><span class="sxs-lookup"><span data-stu-id="282e3-197">Handle incomplete async actions at render</span></span>

<span data-ttu-id="282e3-198">Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika.</span><span class="sxs-lookup"><span data-stu-id="282e3-198">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="282e3-199">Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="282e3-199">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="282e3-200">Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane.</span><span class="sxs-lookup"><span data-stu-id="282e3-200">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="282e3-201">Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty są `null` .</span><span class="sxs-lookup"><span data-stu-id="282e3-201">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="282e3-202">W `FetchData` składniku Blazor szablonów program <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> został zastąpiony asynchronicznie odbierania danych prognozy ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="282e3-202">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="282e3-203">Gdy `forecasts` tak jest `null` , zostanie wyświetlony komunikat ładowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="282e3-203">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="282e3-204">Po `Task` zakończeniu zwracany przez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> program składnik zostanie przerenderowany ze zaktualizowanym stanem.</span><span class="sxs-lookup"><span data-stu-id="282e3-204">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="282e3-205">`Pages/FetchData.razor` w Blazor Server szablonie:</span><span class="sxs-lookup"><span data-stu-id="282e3-205">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="282e3-206">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="282e3-206">Handle errors</span></span>

<span data-ttu-id="282e3-207">Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu życia, zobacz <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="282e3-207">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="282e3-208">Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania</span><span class="sxs-lookup"><span data-stu-id="282e3-208">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="282e3-209">W Blazor Server aplikacji, gdy <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> jest <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="282e3-209">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="282e3-210">Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie* , a składnik jest teraz interaktywny.</span><span class="sxs-lookup"><span data-stu-id="282e3-210">Once the browser establishes a connection back to the server, the component is rendered *again* , and the component is now interactive.</span></span> <span data-ttu-id="282e3-211">Jeśli [`OnInitialized{Async}`](#component-initialization-methods) istnieje metoda cyklu życia do inicjowania składnika, metoda jest wykonywana *dwukrotnie* :</span><span class="sxs-lookup"><span data-stu-id="282e3-211">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice* :</span></span>

* <span data-ttu-id="282e3-212">Gdy składnik jest wstępnie renderowany statycznie.</span><span class="sxs-lookup"><span data-stu-id="282e3-212">When the component is prerendered statically.</span></span>
* <span data-ttu-id="282e3-213">Po nawiązaniu połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="282e3-213">After the server connection has been established.</span></span>

<span data-ttu-id="282e3-214">Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="282e3-214">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="282e3-215">Aby uniknąć podwójnego renderowania w Blazor Server aplikacji:</span><span class="sxs-lookup"><span data-stu-id="282e3-215">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="282e3-216">Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="282e3-216">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="282e3-217">Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.</span><span class="sxs-lookup"><span data-stu-id="282e3-217">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="282e3-218">Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.</span><span class="sxs-lookup"><span data-stu-id="282e3-218">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="282e3-219">Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji opartej na szablonie Blazor Server , która pozwala uniknąć podwójnego renderowania:</span><span class="sxs-lookup"><span data-stu-id="282e3-219">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="282e3-220">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , zobacz <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="282e3-220">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="282e3-221">Wykryj, kiedy aplikacja jest przedrenderowana</span><span class="sxs-lookup"><span data-stu-id="282e3-221">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="282e3-222">Usuwanie składnika z interfejsem IDisposable</span><span class="sxs-lookup"><span data-stu-id="282e3-222">Component disposal with IDisposable</span></span>

<span data-ttu-id="282e3-223">Jeśli składnik implementuje <xref:System.IDisposable> , [ `Dispose` Metoda](/dotnet/standard/garbage-collection/implementing-dispose) jest wywoływana, gdy składnik zostanie usunięty z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="282e3-223">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="282e3-224">Usuwanie może odbywać się w dowolnym momencie, w tym podczas [inicjowania składnika](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="282e3-224">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="282e3-225">Poniższy składnik używa `@implements IDisposable` i `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="282e3-225">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="282e3-226">Wywoływanie metody <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> `Dispose` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="282e3-226">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="282e3-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="282e3-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="282e3-228">Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="282e3-228">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="282e3-229">Poniższe przykłady [ Blazor formularzy](xref:blazor/forms-validation) pokazują, jak odpiąć procedurę obsługi zdarzeń w `Dispose` metodzie:</span><span class="sxs-lookup"><span data-stu-id="282e3-229">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="282e3-230">Pole prywatne i podejście lambda</span><span class="sxs-lookup"><span data-stu-id="282e3-230">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="282e3-231">Podejście metody prywatnej</span><span class="sxs-lookup"><span data-stu-id="282e3-231">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="282e3-232">Anulowanie pracy w tle</span><span class="sxs-lookup"><span data-stu-id="282e3-232">Cancelable background work</span></span>

<span data-ttu-id="282e3-233">Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych ( <xref:System.Net.Http.HttpClient> ) i współdziałanie z bazami danych.</span><span class="sxs-lookup"><span data-stu-id="282e3-233">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="282e3-234">Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach.</span><span class="sxs-lookup"><span data-stu-id="282e3-234">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="282e3-235">Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.</span><span class="sxs-lookup"><span data-stu-id="282e3-235">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="282e3-236">Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:</span><span class="sxs-lookup"><span data-stu-id="282e3-236">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="282e3-237">Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.</span><span class="sxs-lookup"><span data-stu-id="282e3-237">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="282e3-238">Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.</span><span class="sxs-lookup"><span data-stu-id="282e3-238">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="282e3-239">Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.</span><span class="sxs-lookup"><span data-stu-id="282e3-239">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="282e3-240">Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.</span><span class="sxs-lookup"><span data-stu-id="282e3-240">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="282e3-241">Zasoby serwera są ograniczone, co wymaga ponownego zaplanowania elementów roboczych w tle.</span><span class="sxs-lookup"><span data-stu-id="282e3-241">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="282e3-242">Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:</span><span class="sxs-lookup"><span data-stu-id="282e3-242">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="282e3-243">Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="282e3-243">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="282e3-244">Po [usunięciu składnika](#component-disposal-with-idisposable) i w dowolnym momencie anulowanie jest wymagane przez ręczne unieważnienie tokenu, wywołanie [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) do sygnału, że działanie w tle powinno być anulowane.</span><span class="sxs-lookup"><span data-stu-id="282e3-244">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="282e3-245">Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.</span><span class="sxs-lookup"><span data-stu-id="282e3-245">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="282e3-246">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="282e3-246">In the following example:</span></span>

* <span data-ttu-id="282e3-247">`await Task.Delay(5000, cts.Token);` reprezentuje długotrwałe działanie asynchroniczne w tle.</span><span class="sxs-lookup"><span data-stu-id="282e3-247">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="282e3-248">`BackgroundResourceMethod` reprezentuje długotrwałą metodę w tle, która nie powinna być uruchamiana, jeśli element `Resource` zostanie usunięty przed wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="282e3-248">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
