---
title: ASP.NET Core Blazor cykl życia
author: guardrex
description: Dowiedz się, jak korzystać z Razor metod cyklu życia składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 6b9653356659700ae8396a01b38c04d59a86625f
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059893"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="48f82-103">ASP.NET Core Blazor cykl życia</span><span class="sxs-lookup"><span data-stu-id="48f82-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="48f82-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="48f82-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="48f82-105">BlazorPlatforma obejmuje metody cyklu życia synchronicznego i asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="48f82-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="48f82-106">Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="48f82-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="48f82-107">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="48f82-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="48f82-108">Przed ustawieniem parametrów</span><span class="sxs-lookup"><span data-stu-id="48f82-108">Before parameters are set</span></span>

<span data-ttu-id="48f82-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania:</span><span class="sxs-lookup"><span data-stu-id="48f82-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="48f82-110"><xref:Microsoft.AspNetCore.Components.ParameterView>zawiera cały zestaw wartości parametrów każdorazowo <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> wywoływana.</span><span class="sxs-lookup"><span data-stu-id="48f82-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="48f82-111">Domyślna implementacja programu <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ustawia wartość każdej właściwości z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atrybutem lub, który ma odpowiednią wartość w <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="48f82-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="48f82-112">Parametry, które nie mają odpowiedniej wartości w, <xref:Microsoft.AspNetCore.Components.ParameterView> pozostają bez zmian.</span><span class="sxs-lookup"><span data-stu-id="48f82-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="48f82-113">Jeśli [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób.</span><span class="sxs-lookup"><span data-stu-id="48f82-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="48f82-114">Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="48f82-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="48f82-115">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="48f82-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="48f82-116">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="48f82-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="48f82-117">Metody inicjujące składniki</span><span class="sxs-lookup"><span data-stu-id="48f82-117">Component initialization methods</span></span>

<span data-ttu-id="48f82-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego w <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="48f82-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="48f82-119">Użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="48f82-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="48f82-120">W przypadku operacji synchronicznej Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="48f82-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="48f82-121">Aby wykonać operację asynchroniczną, Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i Użyj [`await`](/dotnet/csharp/language-reference/operators/await) operatora dla operacji:</span><span class="sxs-lookup"><span data-stu-id="48f82-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server<span data-ttu-id="48f82-122">aplikacje, które dwukrotnie wywołują [swoje wywołanie zawartości](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span><span class="sxs-lookup"><span data-stu-id="48f82-122"> apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="48f82-123">Gdy składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="48f82-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="48f82-124">Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="48f82-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="48f82-125">Aby zapobiec <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dwukrotnemu uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="48f82-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="48f82-126">Podczas gdy Blazor Server aplikacja jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="48f82-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="48f82-127">Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.</span><span class="sxs-lookup"><span data-stu-id="48f82-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="48f82-128">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="48f82-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="48f82-129">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="48f82-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="48f82-130">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="48f82-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="48f82-131">Po ustawieniu parametrów</span><span class="sxs-lookup"><span data-stu-id="48f82-131">After parameters are set</span></span>

<span data-ttu-id="48f82-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:</span><span class="sxs-lookup"><span data-stu-id="48f82-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="48f82-133">Po zainicjowaniu składnika w programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> .</span><span class="sxs-lookup"><span data-stu-id="48f82-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>.</span></span>
* <span data-ttu-id="48f82-134">Po ponownym wyrenderowaniu i zaopatrzeniu składnika nadrzędnego:</span><span class="sxs-lookup"><span data-stu-id="48f82-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="48f82-135">Tylko znane niezmienne typy pierwotne, których co najmniej jeden parametr został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="48f82-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="48f82-136">Wszystkie parametry złożone z typem.</span><span class="sxs-lookup"><span data-stu-id="48f82-136">Any complex-typed parameters.</span></span> <span data-ttu-id="48f82-137">Struktura nie może wiedzieć, czy wartości parametru złożonego są mutacją wewnętrznie, dlatego traktuje zestaw parametrów jako zmieniony.</span><span class="sxs-lookup"><span data-stu-id="48f82-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="48f82-138">Asynchroniczna działanie, gdy stosowane są parametry i wartości właściwości, muszą wystąpić podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="48f82-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="48f82-139">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="48f82-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="48f82-140">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="48f82-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="48f82-141">Po renderowania składników</span><span class="sxs-lookup"><span data-stu-id="48f82-141">After component render</span></span>

<span data-ttu-id="48f82-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="48f82-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="48f82-143">Odwołania do elementów i składników są wypełniane w tym momencie.</span><span class="sxs-lookup"><span data-stu-id="48f82-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="48f82-144">Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.</span><span class="sxs-lookup"><span data-stu-id="48f82-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="48f82-145">`firstRender`Parametr dla <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="48f82-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="48f82-146">Jest ustawiany po `true` raz pierwszy, gdy wystąpienie składnika jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="48f82-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="48f82-147">Można go użyć, aby upewnić się, że zadania inicjowania są wykonywane tylko raz.</span><span class="sxs-lookup"><span data-stu-id="48f82-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="48f82-148">Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić w trakcie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="48f82-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="48f82-149">Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z programu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania.</span><span class="sxs-lookup"><span data-stu-id="48f82-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="48f82-150">Ma to na celu uniknięcie nieskończonej pętli renderowania.</span><span class="sxs-lookup"><span data-stu-id="48f82-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="48f82-151">Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.</span><span class="sxs-lookup"><span data-stu-id="48f82-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="48f82-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nie są wywoływane podczas renderowania na serwerze.*</span><span class="sxs-lookup"><span data-stu-id="48f82-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="48f82-153">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="48f82-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="48f82-154">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="48f82-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="48f82-155">Pomiń odświeżanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="48f82-155">Suppress UI refreshing</span></span>

<span data-ttu-id="48f82-156">Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="48f82-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="48f82-157">Jeśli implementacja zwraca `true` , interfejs użytkownika zostanie odświeżony:</span><span class="sxs-lookup"><span data-stu-id="48f82-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="48f82-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>jest wywoływana za każdym razem, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="48f82-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="48f82-159">Nawet jeśli <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest zastępowany, składnik jest zawsze początkowo renderowany.</span><span class="sxs-lookup"><span data-stu-id="48f82-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="48f82-160">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="48f82-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="48f82-161">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="48f82-161">State changes</span></span>

<span data-ttu-id="48f82-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>powiadamia składnik o zmianie stanu.</span><span class="sxs-lookup"><span data-stu-id="48f82-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="48f82-163">Jeśli ma to zastosowanie, wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje, że składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="48f82-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="48f82-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest wywoływana automatycznie dla <xref:Microsoft.AspNetCore.Components.EventCallback> metod.</span><span class="sxs-lookup"><span data-stu-id="48f82-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="48f82-165">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="48f82-165">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="48f82-166">Obsługuj niekompletne akcje asynchroniczne podczas renderowania</span><span class="sxs-lookup"><span data-stu-id="48f82-166">Handle incomplete async actions at render</span></span>

<span data-ttu-id="48f82-167">Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika.</span><span class="sxs-lookup"><span data-stu-id="48f82-167">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="48f82-168">Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="48f82-168">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="48f82-169">Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane.</span><span class="sxs-lookup"><span data-stu-id="48f82-169">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="48f82-170">Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty są `null` .</span><span class="sxs-lookup"><span data-stu-id="48f82-170">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="48f82-171">W `FetchData` składniku Blazor szablonów program <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> został zastąpiony asynchronicznie odbierania danych prognozy ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="48f82-171">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="48f82-172">Gdy `forecasts` tak jest `null` , zostanie wyświetlony komunikat ładowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="48f82-172">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="48f82-173">Po `Task` zakończeniu zwracany przez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> program składnik zostanie przerenderowany ze zaktualizowanym stanem.</span><span class="sxs-lookup"><span data-stu-id="48f82-173">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="48f82-174">`Pages/FetchData.razor`w Blazor Server szablonie:</span><span class="sxs-lookup"><span data-stu-id="48f82-174">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="48f82-175">Usuwanie składnika z interfejsem IDisposable</span><span class="sxs-lookup"><span data-stu-id="48f82-175">Component disposal with IDisposable</span></span>

<span data-ttu-id="48f82-176">Jeśli składnik implementuje <xref:System.IDisposable> , [ `Dispose` Metoda](/dotnet/standard/garbage-collection/implementing-dispose) jest wywoływana, gdy składnik zostanie usunięty z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="48f82-176">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="48f82-177">Poniższy składnik używa `@implements IDisposable` i `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="48f82-177">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="48f82-178">Wywoływanie metody <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> `Dispose` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="48f82-178">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="48f82-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="48f82-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="48f82-180">Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="48f82-180">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="48f82-181">Poniższe przykłady [ Blazor formularzy](xref:blazor/forms-validation) pokazują, jak odpiąć procedurę obsługi zdarzeń w `Dispose` metodzie:</span><span class="sxs-lookup"><span data-stu-id="48f82-181">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="48f82-182">Pole prywatne i podejście lambda</span><span class="sxs-lookup"><span data-stu-id="48f82-182">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="48f82-183">Podejście metody prywatnej</span><span class="sxs-lookup"><span data-stu-id="48f82-183">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="48f82-184">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="48f82-184">Handle errors</span></span>

<span data-ttu-id="48f82-185">Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu życia, zobacz <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="48f82-185">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="48f82-186">Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania</span><span class="sxs-lookup"><span data-stu-id="48f82-186">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="48f82-187">W Blazor Server aplikacji, gdy <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> jest <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="48f82-187">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="48f82-188">Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie*, a składnik jest teraz interaktywny.</span><span class="sxs-lookup"><span data-stu-id="48f82-188">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="48f82-189">Jeśli [`OnInitialized{Async}`](#component-initialization-methods) istnieje metoda cyklu życia do inicjowania składnika, metoda jest wykonywana *dwukrotnie*:</span><span class="sxs-lookup"><span data-stu-id="48f82-189">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="48f82-190">Gdy składnik jest wstępnie renderowany statycznie.</span><span class="sxs-lookup"><span data-stu-id="48f82-190">When the component is prerendered statically.</span></span>
* <span data-ttu-id="48f82-191">Po nawiązaniu połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="48f82-191">After the server connection has been established.</span></span>

<span data-ttu-id="48f82-192">Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="48f82-192">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="48f82-193">Aby uniknąć podwójnego renderowania w Blazor Server aplikacji:</span><span class="sxs-lookup"><span data-stu-id="48f82-193">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="48f82-194">Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48f82-194">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="48f82-195">Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.</span><span class="sxs-lookup"><span data-stu-id="48f82-195">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="48f82-196">Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.</span><span class="sxs-lookup"><span data-stu-id="48f82-196">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="48f82-197">Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji opartej na szablonie Blazor Server , która pozwala uniknąć podwójnego renderowania:</span><span class="sxs-lookup"><span data-stu-id="48f82-197">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="48f82-198">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , zobacz <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="48f82-198">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="48f82-199">Wykryj, kiedy aplikacja jest przedrenderowana</span><span class="sxs-lookup"><span data-stu-id="48f82-199">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="48f82-200">Anulowanie pracy w tle</span><span class="sxs-lookup"><span data-stu-id="48f82-200">Cancelable background work</span></span>

<span data-ttu-id="48f82-201">Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych ( <xref:System.Net.Http.HttpClient> ) i współdziałanie z bazami danych.</span><span class="sxs-lookup"><span data-stu-id="48f82-201">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="48f82-202">Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach.</span><span class="sxs-lookup"><span data-stu-id="48f82-202">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="48f82-203">Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.</span><span class="sxs-lookup"><span data-stu-id="48f82-203">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="48f82-204">Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:</span><span class="sxs-lookup"><span data-stu-id="48f82-204">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="48f82-205">Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.</span><span class="sxs-lookup"><span data-stu-id="48f82-205">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="48f82-206">Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.</span><span class="sxs-lookup"><span data-stu-id="48f82-206">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="48f82-207">Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.</span><span class="sxs-lookup"><span data-stu-id="48f82-207">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="48f82-208">Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.</span><span class="sxs-lookup"><span data-stu-id="48f82-208">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="48f82-209">Zasoby serwera są ograniczone, co wymaga ponownego zaplanowania elementów roboczych w tle.</span><span class="sxs-lookup"><span data-stu-id="48f82-209">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="48f82-210">Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:</span><span class="sxs-lookup"><span data-stu-id="48f82-210">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="48f82-211">Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="48f82-211">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="48f82-212">Po [usunięciu składnika](#component-disposal-with-idisposable) i w dowolnym momencie anulowanie jest wymagane przez ręczne unieważnienie tokenu, wywołanie [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) do sygnału, że działanie w tle powinno być anulowane.</span><span class="sxs-lookup"><span data-stu-id="48f82-212">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="48f82-213">Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.</span><span class="sxs-lookup"><span data-stu-id="48f82-213">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="48f82-214">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="48f82-214">In the following example:</span></span>

* <span data-ttu-id="48f82-215">`await Task.Delay(5000, cts.Token);`reprezentuje długotrwałe działanie asynchroniczne w tle.</span><span class="sxs-lookup"><span data-stu-id="48f82-215">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="48f82-216">`BackgroundResourceMethod`reprezentuje długotrwałą metodę w tle, która nie powinna być uruchamiana, jeśli element `Resource` zostanie usunięty przed wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="48f82-216">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
