---
title: ASP.NET Core Blazor cykl życia
author: guardrex
description: Dowiedz się, jak korzystać z Razor metod cyklu życia składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 7152f45cd799128b668ec5002fb20b4f30e69585
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710662"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="80d5a-103">ASP.NET Core Blazor cykl życia</span><span class="sxs-lookup"><span data-stu-id="80d5a-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="80d5a-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="80d5a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="80d5a-105">BlazorPlatforma obejmuje metody cyklu życia synchronicznego i asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="80d5a-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="80d5a-106">Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="80d5a-107">Poniższe diagramy ilustrują Blazor cykl życia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="80d5a-108">Metody cyklu życia są definiowane przy użyciu przykładów w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="80d5a-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="80d5a-109">Zdarzenia cyklu życia składnika:</span><span class="sxs-lookup"><span data-stu-id="80d5a-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="80d5a-110">Jeśli składnik jest renderowany po raz pierwszy w żądaniu:</span><span class="sxs-lookup"><span data-stu-id="80d5a-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="80d5a-111">Utwórz wystąpienie składnika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-111">Create the component's instance.</span></span>
   * <span data-ttu-id="80d5a-112">Wykonaj iniekcję właściwości.</span><span class="sxs-lookup"><span data-stu-id="80d5a-112">Perform property injection.</span></span> <span data-ttu-id="80d5a-113">Uruchom [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="80d5a-114">Wywołanie [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="80d5a-115">W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="80d5a-116">Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="80d5a-117">Wywołaj [`OnParametersSet{Async}`](#after-parameters-are-set) i Renderuj składnik.</span><span class="sxs-lookup"><span data-stu-id="80d5a-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="80d5a-118">Jeśli <xref:System.Threading.Tasks.Task> jest zwracany z `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie składnik jest ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Zdarzenia cyklu życia składnika::: No-Loc (Razor)::: składnik w::: No-Loc (Blazor)::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="80d5a-120">Przetwarzanie zdarzeń w Document Object Model (DOM):</span><span class="sxs-lookup"><span data-stu-id="80d5a-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="80d5a-121">Procedura obsługi zdarzeń jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="80d5a-121">The event handler is run.</span></span>
1. <span data-ttu-id="80d5a-122">W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie jest renderowany składnik.</span><span class="sxs-lookup"><span data-stu-id="80d5a-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="80d5a-123">Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Przetwarzanie zdarzeń Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="80d5a-125">`Render`Cykl życia:</span><span class="sxs-lookup"><span data-stu-id="80d5a-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="80d5a-126">Unikaj dalszych operacji renderowania na składniku:</span><span class="sxs-lookup"><span data-stu-id="80d5a-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="80d5a-127">Po pierwszym wykonaniu.</span><span class="sxs-lookup"><span data-stu-id="80d5a-127">After the first render.</span></span>
   * <span data-ttu-id="80d5a-128">Gdy [`ShouldRender`](#suppress-ui-refreshing) jest `false` .</span><span class="sxs-lookup"><span data-stu-id="80d5a-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="80d5a-129">Kompiluj porównanie drzewa renderowania (różnica) i Renderuj składnik.</span><span class="sxs-lookup"><span data-stu-id="80d5a-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="80d5a-130">Oczekiwanie na zaktualizowanie modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="80d5a-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="80d5a-131">Wywołanie [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Cykl życia renderowania](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="80d5a-133">Deweloperzy mogą wykonywać wywołania [`StateHasChanged`](#state-changes) w wyniku.</span><span class="sxs-lookup"><span data-stu-id="80d5a-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="80d5a-134">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="80d5a-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="80d5a-135">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="80d5a-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="80d5a-136">Przed ustawieniem parametrów</span><span class="sxs-lookup"><span data-stu-id="80d5a-136">Before parameters are set</span></span>

<span data-ttu-id="80d5a-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania:</span><span class="sxs-lookup"><span data-stu-id="80d5a-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="80d5a-138"><xref:Microsoft.AspNetCore.Components.ParameterView> zawiera zestaw wartości parametrów dla składnika, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="80d5a-138"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="80d5a-139">Domyślna implementacja programu <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ustawia wartość każdej właściwości z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atrybutem lub, który ma odpowiednią wartość w <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-139">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="80d5a-140">Parametry, które nie mają odpowiedniej wartości w, <xref:Microsoft.AspNetCore.Components.ParameterView> pozostają bez zmian.</span><span class="sxs-lookup"><span data-stu-id="80d5a-140">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="80d5a-141">Jeśli [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób.</span><span class="sxs-lookup"><span data-stu-id="80d5a-141">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="80d5a-142">Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="80d5a-142">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="80d5a-143">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-143">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="80d5a-144">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-144">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="80d5a-145">Metody inicjujące składniki</span><span class="sxs-lookup"><span data-stu-id="80d5a-145">Component initialization methods</span></span>

<span data-ttu-id="80d5a-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego w <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="80d5a-147">Użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="80d5a-147">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="80d5a-148">W przypadku operacji synchronicznej Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="80d5a-148">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="80d5a-149">Aby wykonać operację asynchroniczną, Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i Użyj [`await`](/dotnet/csharp/language-reference/operators/await) operatora dla operacji:</span><span class="sxs-lookup"><span data-stu-id="80d5a-149">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="80d5a-150">Blazor Serveraplikacje, które dwukrotnie wywołują [swoje wywołanie zawartości](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="80d5a-150">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="80d5a-151">Gdy składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="80d5a-151">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="80d5a-152">Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="80d5a-152">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="80d5a-153">Aby zapobiec <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dwukrotnemu uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-153">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="80d5a-154">Podczas gdy Blazor Server aplikacja jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-154">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="80d5a-155">Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-155">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="80d5a-156">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-156">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="80d5a-157">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-157">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="80d5a-158">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-158">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="80d5a-159">Po ustawieniu parametrów</span><span class="sxs-lookup"><span data-stu-id="80d5a-159">After parameters are set</span></span>

<span data-ttu-id="80d5a-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:</span><span class="sxs-lookup"><span data-stu-id="80d5a-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="80d5a-161">Po zainicjowaniu składnika w programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-161">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="80d5a-162">Po ponownym wyrenderowaniu i zaopatrzeniu składnika nadrzędnego:</span><span class="sxs-lookup"><span data-stu-id="80d5a-162">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="80d5a-163">Tylko znane niezmienne typy pierwotne, których co najmniej jeden parametr został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="80d5a-163">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="80d5a-164">Wszystkie parametry złożone z typem.</span><span class="sxs-lookup"><span data-stu-id="80d5a-164">Any complex-typed parameters.</span></span> <span data-ttu-id="80d5a-165">Struktura nie może wiedzieć, czy wartości parametru złożonego są mutacją wewnętrznie, dlatego traktuje zestaw parametrów jako zmieniony.</span><span class="sxs-lookup"><span data-stu-id="80d5a-165">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="80d5a-166">Asynchroniczna działanie, gdy stosowane są parametry i wartości właściwości, muszą wystąpić podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-166">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="80d5a-167">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-167">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="80d5a-168">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-168">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="80d5a-169">Po renderowania składników</span><span class="sxs-lookup"><span data-stu-id="80d5a-169">After component render</span></span>

<span data-ttu-id="80d5a-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="80d5a-171">Odwołania do elementów i składników są wypełniane w tym momencie.</span><span class="sxs-lookup"><span data-stu-id="80d5a-171">Element and component references are populated at this point.</span></span> <span data-ttu-id="80d5a-172">Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.</span><span class="sxs-lookup"><span data-stu-id="80d5a-172">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="80d5a-173">`firstRender`Parametr dla <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="80d5a-173">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="80d5a-174">Jest ustawiany po `true` raz pierwszy, gdy wystąpienie składnika jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-174">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="80d5a-175">Można go użyć, aby upewnić się, że zadania inicjowania są wykonywane tylko raz.</span><span class="sxs-lookup"><span data-stu-id="80d5a-175">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="80d5a-176">Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić w trakcie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-176">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="80d5a-177">Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z programu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania.</span><span class="sxs-lookup"><span data-stu-id="80d5a-177">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="80d5a-178">Ma to na celu uniknięcie nieskończonej pętli renderowania.</span><span class="sxs-lookup"><span data-stu-id="80d5a-178">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="80d5a-179">Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.</span><span class="sxs-lookup"><span data-stu-id="80d5a-179">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="80d5a-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nie są wywoływane podczas procesu renderowania wstępnego na serwerze*.</span><span class="sxs-lookup"><span data-stu-id="80d5a-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="80d5a-181">Metody są wywoływane, gdy składnik jest renderowany interaktywnie po zakończeniu renderowania prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="80d5a-181">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="80d5a-182">Gdy aplikacja jest przedrenderowana:</span><span class="sxs-lookup"><span data-stu-id="80d5a-182">When the app prerenders:</span></span>

1. <span data-ttu-id="80d5a-183">Składnik jest wykonywany na serwerze w celu utworzenia statycznego znacznika HTML w odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="80d5a-183">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="80d5a-184">W tej fazie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nie są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-184">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="80d5a-185">Kiedy `blazor.server.js` lub `blazor.webassembly.js` uruchamiasz w przeglądarce, składnik jest uruchamiany ponownie w trybie renderowania interaktywnego.</span><span class="sxs-lookup"><span data-stu-id="80d5a-185">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="80d5a-186">Po ponownym uruchomieniu składnika <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **są** wywoływane, ponieważ aplikacja nie znajduje się w fazie renderowania prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="80d5a-186">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="80d5a-187">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-187">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="80d5a-188">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="80d5a-188">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="80d5a-189">Pomiń odświeżanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="80d5a-189">Suppress UI refreshing</span></span>

<span data-ttu-id="80d5a-190">Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-190">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="80d5a-191">Jeśli implementacja zwraca `true` , interfejs użytkownika zostanie odświeżony:</span><span class="sxs-lookup"><span data-stu-id="80d5a-191">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="80d5a-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest wywoływana za każdym razem, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="80d5a-193">Nawet jeśli <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest zastępowany, składnik jest zawsze początkowo renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-193">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="80d5a-194">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="80d5a-194">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="80d5a-195">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="80d5a-195">State changes</span></span>

<span data-ttu-id="80d5a-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powiadamia składnik o zmianie stanu.</span><span class="sxs-lookup"><span data-stu-id="80d5a-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="80d5a-197">Jeśli ma to zastosowanie, wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje, że składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-197">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="80d5a-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana automatycznie dla <xref:Microsoft.AspNetCore.Components.EventCallback> metod.</span><span class="sxs-lookup"><span data-stu-id="80d5a-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="80d5a-199">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="80d5a-199">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="80d5a-200">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="80d5a-200">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="80d5a-201">Obsługuj niekompletne akcje asynchroniczne podczas renderowania</span><span class="sxs-lookup"><span data-stu-id="80d5a-201">Handle incomplete async actions at render</span></span>

<span data-ttu-id="80d5a-202">Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-202">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="80d5a-203">Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-203">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="80d5a-204">Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-204">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="80d5a-205">Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty są `null` .</span><span class="sxs-lookup"><span data-stu-id="80d5a-205">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="80d5a-206">W `FetchData` składniku Blazor szablonów program <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest zastępowany asynchronicznie odbieranych danych prognozy ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="80d5a-206">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="80d5a-207">Gdy `forecasts` tak jest `null` , zostanie wyświetlony komunikat ładowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-207">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="80d5a-208">Po `Task` zakończeniu zwracany przez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> program składnik zostanie przerenderowany ze zaktualizowanym stanem.</span><span class="sxs-lookup"><span data-stu-id="80d5a-208">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="80d5a-209">`Pages/FetchData.razor` w Blazor Server szablonie:</span><span class="sxs-lookup"><span data-stu-id="80d5a-209">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="80d5a-210">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="80d5a-210">Handle errors</span></span>

<span data-ttu-id="80d5a-211">Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu życia, zobacz <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-211">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="80d5a-212">Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania</span><span class="sxs-lookup"><span data-stu-id="80d5a-212">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="80d5a-213">W Blazor Server aplikacji, gdy <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> jest <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="80d5a-213">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="80d5a-214">Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie*, a składnik jest teraz interaktywny.</span><span class="sxs-lookup"><span data-stu-id="80d5a-214">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="80d5a-215">Jeśli [`OnInitialized{Async}`](#component-initialization-methods) istnieje metoda cyklu życia do inicjowania składnika, metoda jest wykonywana *dwukrotnie*:</span><span class="sxs-lookup"><span data-stu-id="80d5a-215">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="80d5a-216">Gdy składnik jest wstępnie renderowany statycznie.</span><span class="sxs-lookup"><span data-stu-id="80d5a-216">When the component is prerendered statically.</span></span>
* <span data-ttu-id="80d5a-217">Po nawiązaniu połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="80d5a-217">After the server connection has been established.</span></span>

<span data-ttu-id="80d5a-218">Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="80d5a-218">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="80d5a-219">Aby uniknąć podwójnego renderowania w Blazor Server aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80d5a-219">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="80d5a-220">Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80d5a-220">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="80d5a-221">Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.</span><span class="sxs-lookup"><span data-stu-id="80d5a-221">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="80d5a-222">Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.</span><span class="sxs-lookup"><span data-stu-id="80d5a-222">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="80d5a-223">Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji opartej na szablonie Blazor Server , która pozwala uniknąć podwójnego renderowania:</span><span class="sxs-lookup"><span data-stu-id="80d5a-223">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="80d5a-224">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , zobacz <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-224">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="80d5a-225">Wykryj, kiedy aplikacja jest przedrenderowana</span><span class="sxs-lookup"><span data-stu-id="80d5a-225">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="80d5a-226">Usuwanie składnika z `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="80d5a-226">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="80d5a-227">Jeśli składnik implementuje <xref:System.IDisposable> , struktura wywołuje [metodę usuwania](/dotnet/standard/garbage-collection/implementing-dispose) po usunięciu składnika z interfejsu użytkownika, w którym można wydać niezarządzane zasoby.</span><span class="sxs-lookup"><span data-stu-id="80d5a-227">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="80d5a-228">Usuwanie może odbywać się w dowolnym momencie, w tym podczas [inicjowania składnika](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="80d5a-228">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="80d5a-229">Następujący składnik implementuje <xref:System.IDisposable> z [`@implements`](xref:mvc/views/razor#implements) Razor dyrektywą:</span><span class="sxs-lookup"><span data-stu-id="80d5a-229">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="80d5a-230">W przypadku asynchronicznych zadań usuwania Użyj `DisposeAsync` zamiast tego `Dispose` w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="80d5a-230">For asynchronous disposal tasks, use `DisposeAsync` instead of `Dispose` in the preceding example:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="80d5a-231">Wywoływanie metody <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> `Dispose` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-231">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="80d5a-232"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-232"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="80d5a-233">Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="80d5a-233">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="80d5a-234">Poniższe przykłady [ Blazor formularzy](xref:blazor/forms-validation) pokazują, jak odpiąć procedurę obsługi zdarzeń w `Dispose` metodzie:</span><span class="sxs-lookup"><span data-stu-id="80d5a-234">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="80d5a-235">Pole prywatne i podejście lambda</span><span class="sxs-lookup"><span data-stu-id="80d5a-235">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="80d5a-236">Podejście metody prywatnej</span><span class="sxs-lookup"><span data-stu-id="80d5a-236">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]
  
<span data-ttu-id="80d5a-237">Aby uzyskać więcej informacji, zobacz [Oczyszczanie zasobów niezarządzanych](/dotnet/standard/garbage-collection/unmanaged) i tematy, które po nich wykonują, przy wdrażaniu `Dispose` `DisposeAsync` metod i.</span><span class="sxs-lookup"><span data-stu-id="80d5a-237">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="80d5a-238">Anulowanie pracy w tle</span><span class="sxs-lookup"><span data-stu-id="80d5a-238">Cancelable background work</span></span>

<span data-ttu-id="80d5a-239">Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych ( <xref:System.Net.Http.HttpClient> ) i współdziałanie z bazami danych.</span><span class="sxs-lookup"><span data-stu-id="80d5a-239">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="80d5a-240">Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach.</span><span class="sxs-lookup"><span data-stu-id="80d5a-240">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="80d5a-241">Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.</span><span class="sxs-lookup"><span data-stu-id="80d5a-241">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="80d5a-242">Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:</span><span class="sxs-lookup"><span data-stu-id="80d5a-242">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="80d5a-243">Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.</span><span class="sxs-lookup"><span data-stu-id="80d5a-243">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="80d5a-244">Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.</span><span class="sxs-lookup"><span data-stu-id="80d5a-244">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="80d5a-245">Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.</span><span class="sxs-lookup"><span data-stu-id="80d5a-245">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="80d5a-246">Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.</span><span class="sxs-lookup"><span data-stu-id="80d5a-246">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="80d5a-247">Zasoby serwera są ograniczone, co wymaga ponownego zaplanowania elementów roboczych w tle.</span><span class="sxs-lookup"><span data-stu-id="80d5a-247">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="80d5a-248">Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:</span><span class="sxs-lookup"><span data-stu-id="80d5a-248">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="80d5a-249">Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-249">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="80d5a-250">Po [usunięciu składnika](#component-disposal-with-idisposable) i w dowolnym momencie anulowanie jest wymagane przez ręczne unieważnienie tokenu, wywołanie [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) do sygnału, że działanie w tle powinno być anulowane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-250">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="80d5a-251">Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.</span><span class="sxs-lookup"><span data-stu-id="80d5a-251">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="80d5a-252">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="80d5a-252">In the following example:</span></span>

* <span data-ttu-id="80d5a-253">`await Task.Delay(5000, cts.Token);` reprezentuje długotrwałe działanie asynchroniczne w tle.</span><span class="sxs-lookup"><span data-stu-id="80d5a-253">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="80d5a-254">`BackgroundResourceMethod` reprezentuje długotrwałą metodę w tle, która nie powinna być uruchamiana, jeśli element `Resource` zostanie usunięty przed wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="80d5a-254">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="80d5a-255">Blazor Server zdarzenia ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="80d5a-255">Blazor Server reconnection events</span></span>

<span data-ttu-id="80d5a-256">Zdarzenia cyklu życia składnika omówione w tym artykule działają niezależnie od [ Blazor Server programów obsługi zdarzeń ponownego połączenia](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="80d5a-256">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="80d5a-257">Gdy Blazor Server aplikacja utraci SignalR połączenie z klientem, wszystkie aktualizacje interfejsu użytkownika są przerywane.</span><span class="sxs-lookup"><span data-stu-id="80d5a-257">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="80d5a-258">Aktualizacje interfejsu użytkownika są wznawiane po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="80d5a-258">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="80d5a-259">Aby uzyskać więcej informacji na temat zdarzeń i konfiguracji obsługi obwodów, zobacz <xref:blazor/fundamentals/additional-scenarios> .</span><span class="sxs-lookup"><span data-stu-id="80d5a-259">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
