---
title: ASP.NET Core Blazor cykl życia
author: guardrex
description: Dowiedz się, jak korzystać z Razor metod cyklu życia składników w Blazor aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
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
ms.openlocfilehash: e3abfd0535bc10867c9b5f980bb5439cc918dfab
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712327"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="28ceb-103">ASP.NET Core Blazor cykl życia</span><span class="sxs-lookup"><span data-stu-id="28ceb-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="28ceb-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="28ceb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="28ceb-105">BlazorPlatforma obejmuje metody cyklu życia synchronicznego i asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="28ceb-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="28ceb-106">Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="28ceb-107">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="28ceb-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="28ceb-108">Przed ustawieniem parametrów</span><span class="sxs-lookup"><span data-stu-id="28ceb-108">Before parameters are set</span></span>

<span data-ttu-id="28ceb-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania:</span><span class="sxs-lookup"><span data-stu-id="28ceb-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="28ceb-110"><xref:Microsoft.AspNetCore.Components.ParameterView> zawiera zestaw wartości parametrów dla składnika, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="28ceb-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="28ceb-111">Domyślna implementacja programu <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ustawia wartość każdej właściwości z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atrybutem lub, który ma odpowiednią wartość w <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="28ceb-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="28ceb-112">Parametry, które nie mają odpowiedniej wartości w, <xref:Microsoft.AspNetCore.Components.ParameterView> pozostają bez zmian.</span><span class="sxs-lookup"><span data-stu-id="28ceb-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="28ceb-113">Jeśli [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób.</span><span class="sxs-lookup"><span data-stu-id="28ceb-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="28ceb-114">Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="28ceb-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="28ceb-115">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="28ceb-116">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="28ceb-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="28ceb-117">Metody inicjujące składniki</span><span class="sxs-lookup"><span data-stu-id="28ceb-117">Component initialization methods</span></span>

<span data-ttu-id="28ceb-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego w <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="28ceb-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="28ceb-119">Użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="28ceb-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="28ceb-120">W przypadku operacji synchronicznej Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="28ceb-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="28ceb-121">Aby wykonać operację asynchroniczną, Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i Użyj [`await`](/dotnet/csharp/language-reference/operators/await) operatora dla operacji:</span><span class="sxs-lookup"><span data-stu-id="28ceb-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="28ceb-122">Blazor Serveraplikacje, które dwukrotnie wywołują [swoje wywołanie zawartości](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span><span class="sxs-lookup"><span data-stu-id="28ceb-122">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="28ceb-123">Gdy składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="28ceb-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="28ceb-124">Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="28ceb-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="28ceb-125">Aby zapobiec <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dwukrotnemu uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="28ceb-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="28ceb-126">Podczas gdy Blazor Server aplikacja jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="28ceb-127">Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="28ceb-128">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="28ceb-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="28ceb-129">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="28ceb-130">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="28ceb-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="28ceb-131">Po ustawieniu parametrów</span><span class="sxs-lookup"><span data-stu-id="28ceb-131">After parameters are set</span></span>

<span data-ttu-id="28ceb-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:</span><span class="sxs-lookup"><span data-stu-id="28ceb-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="28ceb-133">Po zainicjowaniu składnika w programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="28ceb-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="28ceb-134">Po ponownym wyrenderowaniu i zaopatrzeniu składnika nadrzędnego:</span><span class="sxs-lookup"><span data-stu-id="28ceb-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="28ceb-135">Tylko znane niezmienne typy pierwotne, których co najmniej jeden parametr został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="28ceb-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="28ceb-136">Wszystkie parametry złożone z typem.</span><span class="sxs-lookup"><span data-stu-id="28ceb-136">Any complex-typed parameters.</span></span> <span data-ttu-id="28ceb-137">Struktura nie może wiedzieć, czy wartości parametru złożonego są mutacją wewnętrznie, dlatego traktuje zestaw parametrów jako zmieniony.</span><span class="sxs-lookup"><span data-stu-id="28ceb-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="28ceb-138">Asynchroniczna działanie, gdy stosowane są parametry i wartości właściwości, muszą wystąpić podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="28ceb-139">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="28ceb-140">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="28ceb-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="28ceb-141">Po renderowania składników</span><span class="sxs-lookup"><span data-stu-id="28ceb-141">After component render</span></span>

<span data-ttu-id="28ceb-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="28ceb-143">Odwołania do elementów i składników są wypełniane w tym momencie.</span><span class="sxs-lookup"><span data-stu-id="28ceb-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="28ceb-144">Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.</span><span class="sxs-lookup"><span data-stu-id="28ceb-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="28ceb-145">`firstRender`Parametr dla <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="28ceb-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="28ceb-146">Jest ustawiany po `true` raz pierwszy, gdy wystąpienie składnika jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="28ceb-147">Można go użyć, aby upewnić się, że zadania inicjowania są wykonywane tylko raz.</span><span class="sxs-lookup"><span data-stu-id="28ceb-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="28ceb-148">Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić w trakcie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="28ceb-149">Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z programu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania.</span><span class="sxs-lookup"><span data-stu-id="28ceb-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="28ceb-150">Ma to na celu uniknięcie nieskończonej pętli renderowania.</span><span class="sxs-lookup"><span data-stu-id="28ceb-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="28ceb-151">Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.</span><span class="sxs-lookup"><span data-stu-id="28ceb-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="28ceb-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nie są wywoływane podczas renderowania na serwerze.*</span><span class="sxs-lookup"><span data-stu-id="28ceb-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="28ceb-153">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="28ceb-154">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="28ceb-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="28ceb-155">Pomiń odświeżanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="28ceb-155">Suppress UI refreshing</span></span>

<span data-ttu-id="28ceb-156">Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="28ceb-157">Jeśli implementacja zwraca `true` , interfejs użytkownika zostanie odświeżony:</span><span class="sxs-lookup"><span data-stu-id="28ceb-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="28ceb-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest wywoływana za każdym razem, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="28ceb-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="28ceb-159">Nawet jeśli <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest zastępowany, składnik jest zawsze początkowo renderowany.</span><span class="sxs-lookup"><span data-stu-id="28ceb-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="28ceb-160">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="28ceb-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="28ceb-161">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="28ceb-161">State changes</span></span>

<span data-ttu-id="28ceb-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powiadamia składnik o zmianie stanu.</span><span class="sxs-lookup"><span data-stu-id="28ceb-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="28ceb-163">Jeśli ma to zastosowanie, wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje, że składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="28ceb-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="28ceb-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana automatycznie dla <xref:Microsoft.AspNetCore.Components.EventCallback> metod.</span><span class="sxs-lookup"><span data-stu-id="28ceb-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="28ceb-165">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="28ceb-165">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="28ceb-166">Obsługuj niekompletne akcje asynchroniczne podczas renderowania</span><span class="sxs-lookup"><span data-stu-id="28ceb-166">Handle incomplete async actions at render</span></span>

<span data-ttu-id="28ceb-167">Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-167">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="28ceb-168">Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="28ceb-168">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="28ceb-169">Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-169">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="28ceb-170">Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty są `null` .</span><span class="sxs-lookup"><span data-stu-id="28ceb-170">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="28ceb-171">W `FetchData` składniku Blazor szablonów program <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> został zastąpiony asynchronicznie odbierania danych prognozy ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="28ceb-171">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="28ceb-172">Gdy `forecasts` tak jest `null` , zostanie wyświetlony komunikat ładowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-172">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="28ceb-173">Po `Task` zakończeniu zwracany przez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> program składnik zostanie przerenderowany ze zaktualizowanym stanem.</span><span class="sxs-lookup"><span data-stu-id="28ceb-173">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="28ceb-174">`Pages/FetchData.razor` w Blazor Server szablonie:</span><span class="sxs-lookup"><span data-stu-id="28ceb-174">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="28ceb-175">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="28ceb-175">Handle errors</span></span>

<span data-ttu-id="28ceb-176">Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu życia, zobacz <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="28ceb-176">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="28ceb-177">Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania</span><span class="sxs-lookup"><span data-stu-id="28ceb-177">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="28ceb-178">W Blazor Server aplikacji, gdy <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> jest <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="28ceb-178">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="28ceb-179">Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie*, a składnik jest teraz interaktywny.</span><span class="sxs-lookup"><span data-stu-id="28ceb-179">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="28ceb-180">Jeśli [`OnInitialized{Async}`](#component-initialization-methods) istnieje metoda cyklu życia do inicjowania składnika, metoda jest wykonywana *dwukrotnie*:</span><span class="sxs-lookup"><span data-stu-id="28ceb-180">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="28ceb-181">Gdy składnik jest wstępnie renderowany statycznie.</span><span class="sxs-lookup"><span data-stu-id="28ceb-181">When the component is prerendered statically.</span></span>
* <span data-ttu-id="28ceb-182">Po nawiązaniu połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="28ceb-182">After the server connection has been established.</span></span>

<span data-ttu-id="28ceb-183">Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="28ceb-183">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="28ceb-184">Aby uniknąć podwójnego renderowania w Blazor Server aplikacji:</span><span class="sxs-lookup"><span data-stu-id="28ceb-184">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="28ceb-185">Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28ceb-185">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="28ceb-186">Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-186">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="28ceb-187">Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.</span><span class="sxs-lookup"><span data-stu-id="28ceb-187">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="28ceb-188">Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji opartej na szablonie Blazor Server , która pozwala uniknąć podwójnego renderowania:</span><span class="sxs-lookup"><span data-stu-id="28ceb-188">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="28ceb-189">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , zobacz <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="28ceb-189">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="28ceb-190">Wykryj, kiedy aplikacja jest przedrenderowana</span><span class="sxs-lookup"><span data-stu-id="28ceb-190">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="28ceb-191">Usuwanie składnika z interfejsem IDisposable</span><span class="sxs-lookup"><span data-stu-id="28ceb-191">Component disposal with IDisposable</span></span>

<span data-ttu-id="28ceb-192">Jeśli składnik implementuje <xref:System.IDisposable> , [ `Dispose` Metoda](/dotnet/standard/garbage-collection/implementing-dispose) jest wywoływana, gdy składnik zostanie usunięty z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28ceb-192">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="28ceb-193">Usuwanie może odbywać się w dowolnym momencie, w tym podczas [inicjowania składnika](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="28ceb-193">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="28ceb-194">Poniższy składnik używa `@implements IDisposable` i `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="28ceb-194">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="28ceb-195">Wywoływanie metody <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> `Dispose` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-195">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="28ceb-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="28ceb-197">Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="28ceb-197">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="28ceb-198">Poniższe przykłady [ Blazor formularzy](xref:blazor/forms-validation) pokazują, jak odpiąć procedurę obsługi zdarzeń w `Dispose` metodzie:</span><span class="sxs-lookup"><span data-stu-id="28ceb-198">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="28ceb-199">Pole prywatne i podejście lambda</span><span class="sxs-lookup"><span data-stu-id="28ceb-199">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="28ceb-200">Podejście metody prywatnej</span><span class="sxs-lookup"><span data-stu-id="28ceb-200">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="28ceb-201">Anulowanie pracy w tle</span><span class="sxs-lookup"><span data-stu-id="28ceb-201">Cancelable background work</span></span>

<span data-ttu-id="28ceb-202">Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych ( <xref:System.Net.Http.HttpClient> ) i współdziałanie z bazami danych.</span><span class="sxs-lookup"><span data-stu-id="28ceb-202">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="28ceb-203">Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach.</span><span class="sxs-lookup"><span data-stu-id="28ceb-203">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="28ceb-204">Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.</span><span class="sxs-lookup"><span data-stu-id="28ceb-204">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="28ceb-205">Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:</span><span class="sxs-lookup"><span data-stu-id="28ceb-205">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="28ceb-206">Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.</span><span class="sxs-lookup"><span data-stu-id="28ceb-206">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="28ceb-207">Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.</span><span class="sxs-lookup"><span data-stu-id="28ceb-207">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="28ceb-208">Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.</span><span class="sxs-lookup"><span data-stu-id="28ceb-208">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="28ceb-209">Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.</span><span class="sxs-lookup"><span data-stu-id="28ceb-209">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="28ceb-210">Zasoby serwera są ograniczone, co wymaga ponownego zaplanowania elementów roboczych w tle.</span><span class="sxs-lookup"><span data-stu-id="28ceb-210">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="28ceb-211">Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:</span><span class="sxs-lookup"><span data-stu-id="28ceb-211">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="28ceb-212">Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="28ceb-212">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="28ceb-213">Po [usunięciu składnika](#component-disposal-with-idisposable) i w dowolnym momencie anulowanie jest wymagane przez ręczne unieważnienie tokenu, wywołanie [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) do sygnału, że działanie w tle powinno być anulowane.</span><span class="sxs-lookup"><span data-stu-id="28ceb-213">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="28ceb-214">Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.</span><span class="sxs-lookup"><span data-stu-id="28ceb-214">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="28ceb-215">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="28ceb-215">In the following example:</span></span>

* <span data-ttu-id="28ceb-216">`await Task.Delay(5000, cts.Token);` reprezentuje długotrwałe działanie asynchroniczne w tle.</span><span class="sxs-lookup"><span data-stu-id="28ceb-216">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="28ceb-217">`BackgroundResourceMethod` reprezentuje długotrwałą metodę w tle, która nie powinna być uruchamiana, jeśli element `Resource` zostanie usunięty przed wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="28ceb-217">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
