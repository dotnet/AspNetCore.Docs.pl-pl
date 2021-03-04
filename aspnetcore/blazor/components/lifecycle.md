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
ms.openlocfilehash: 6e9d2c3180fb9e4c3e5ccc0b6d8e17183f78d698
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109848"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="755a3-103">ASP.NET Core Blazor cykl życia</span><span class="sxs-lookup"><span data-stu-id="755a3-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="755a3-104">BlazorPlatforma obejmuje metody cyklu życia synchronicznego i asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="755a3-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="755a3-105">Zastąp metody cyklu życia, aby wykonać dodatkowe operacje na składnikach podczas inicjowania i renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="755a3-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="755a3-106">Poniższe diagramy ilustrują Blazor cykl życia.</span><span class="sxs-lookup"><span data-stu-id="755a3-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="755a3-107">Metody cyklu życia są definiowane przy użyciu przykładów w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="755a3-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="755a3-108">Zdarzenia cyklu życia składnika:</span><span class="sxs-lookup"><span data-stu-id="755a3-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="755a3-109">Jeśli składnik jest renderowany po raz pierwszy w żądaniu:</span><span class="sxs-lookup"><span data-stu-id="755a3-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="755a3-110">Utwórz wystąpienie składnika.</span><span class="sxs-lookup"><span data-stu-id="755a3-110">Create the component's instance.</span></span>
   * <span data-ttu-id="755a3-111">Wykonaj iniekcję właściwości.</span><span class="sxs-lookup"><span data-stu-id="755a3-111">Perform property injection.</span></span> <span data-ttu-id="755a3-112">Uruchom [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="755a3-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="755a3-113">Wywołanie [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="755a3-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="755a3-114">W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="755a3-115">Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="755a3-116">Wywołaj [`OnParametersSet{Async}`](#after-parameters-are-set) i Renderuj składnik.</span><span class="sxs-lookup"><span data-stu-id="755a3-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="755a3-117">Jeśli <xref:System.Threading.Tasks.Task> jest zwracany z `OnParametersSetAsync` , <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie składnik jest ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Zdarzenia cyklu życia składnika::: No-Loc (Razor)::: składnik w::: No-Loc (Blazor)::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="755a3-119">Przetwarzanie zdarzeń w Document Object Model (DOM):</span><span class="sxs-lookup"><span data-stu-id="755a3-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="755a3-120">Procedura obsługi zdarzeń jest uruchamiana.</span><span class="sxs-lookup"><span data-stu-id="755a3-120">The event handler is run.</span></span>
1. <span data-ttu-id="755a3-121">W przypadku <xref:System.Threading.Tasks.Task> zwrócenia elementu <xref:System.Threading.Tasks.Task> jest oczekiwany, a następnie jest renderowany składnik.</span><span class="sxs-lookup"><span data-stu-id="755a3-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="755a3-122">Jeśli element <xref:System.Threading.Tasks.Task> nie jest zwracany, składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Przetwarzanie zdarzeń Document Object Model (DOM)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="755a3-124">`Render`Cykl życia:</span><span class="sxs-lookup"><span data-stu-id="755a3-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="755a3-125">Unikaj dalszych operacji renderowania na składniku:</span><span class="sxs-lookup"><span data-stu-id="755a3-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="755a3-126">Po pierwszym wykonaniu.</span><span class="sxs-lookup"><span data-stu-id="755a3-126">After the first render.</span></span>
   * <span data-ttu-id="755a3-127">Gdy [`ShouldRender`](#suppress-ui-refreshing) jest `false` .</span><span class="sxs-lookup"><span data-stu-id="755a3-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="755a3-128">Kompiluj porównanie drzewa renderowania (różnica) i Renderuj składnik.</span><span class="sxs-lookup"><span data-stu-id="755a3-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="755a3-129">Oczekiwanie na zaktualizowanie modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="755a3-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="755a3-130">Wywołanie [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="755a3-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Cykl życia renderowania](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="755a3-132">Deweloperzy mogą wykonywać wywołania [`StateHasChanged`](#state-changes) w wyniku.</span><span class="sxs-lookup"><span data-stu-id="755a3-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="755a3-133">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="755a3-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="755a3-134">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="755a3-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="755a3-135">Przed ustawieniem parametrów</span><span class="sxs-lookup"><span data-stu-id="755a3-135">Before parameters are set</span></span>

<span data-ttu-id="755a3-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> Ustawia parametry dostarczone przez element nadrzędny składnika w drzewie renderowania lub z parametrów trasy.</span><span class="sxs-lookup"><span data-stu-id="755a3-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="755a3-137">Zastępując metodę, kod dewelopera może współdziałać bezpośrednio z <xref:Microsoft.AspNetCore.Components.ParameterView> parametrami.</span><span class="sxs-lookup"><span data-stu-id="755a3-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="755a3-138">W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> przypisuje `Param` wartość parametru do, `value` Jeśli analizowanie parametru trasy dla programu `Param` zakończyło się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="755a3-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="755a3-139">Gdy `value` nie jest `null` , wartość jest wyświetlana przez składnik.</span><span class="sxs-lookup"><span data-stu-id="755a3-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="755a3-140">Chociaż [dopasowanie parametrów trasy nie uwzględnia wielkości liter](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> w szablonie trasy są uwzględniane tylko nazwy parametrów z uwzględnieniem wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="755a3-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="755a3-141">Poniższy przykład jest wymagany do korzystania z `/{Param?}` nie, aby `/{param?}` można było pobrać wartość.</span><span class="sxs-lookup"><span data-stu-id="755a3-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="755a3-142">Jeśli `/{param?}` jest używana w tym scenariuszu, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> zwraca `false` i `message` nie jest ustawiona na ciąg.</span><span class="sxs-lookup"><span data-stu-id="755a3-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="755a3-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="755a3-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="755a3-144"><xref:Microsoft.AspNetCore.Components.ParameterView> zawiera zestaw wartości parametrów dla składnika, gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="755a3-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="755a3-145">Domyślna implementacja programu <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> ustawia wartość każdej właściwości z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [ `[CascadingParameter]` atrybutem](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) lub, który ma odpowiednią wartość w <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="755a3-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="755a3-146">Parametry, które nie mają odpowiedniej wartości w, <xref:Microsoft.AspNetCore.Components.ParameterView> pozostają bez zmian.</span><span class="sxs-lookup"><span data-stu-id="755a3-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="755a3-147">Jeśli [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nie zostanie wywołana, kod niestandardowy może interpretować wartość parametrów przychodzących w dowolny sposób.</span><span class="sxs-lookup"><span data-stu-id="755a3-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="755a3-148">Na przykład nie jest wymagane przypisanie parametrów przychodzących do właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="755a3-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="755a3-149">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="755a3-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="755a3-150">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="755a3-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="755a3-151">Metody inicjujące składniki</span><span class="sxs-lookup"><span data-stu-id="755a3-151">Component initialization methods</span></span>

<span data-ttu-id="755a3-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> są wywoływane, gdy składnik zostanie zainicjowany po odebraniu początkowych parametrów z jego składnika nadrzędnego w <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="755a3-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="755a3-153">Użyj <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , gdy składnik wykonuje operację asynchroniczną i powinien być odświeżany po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="755a3-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="755a3-154">W przypadku operacji synchronicznej Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="755a3-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="755a3-155">Aby wykonać operację asynchroniczną, Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> i Użyj [`await`](/dotnet/csharp/language-reference/operators/await) operatora dla operacji:</span><span class="sxs-lookup"><span data-stu-id="755a3-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="755a3-156">Blazor Serveraplikacje, które dwukrotnie wywołują [swoje wywołanie zawartości](xref:blazor/fundamentals/signalr#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="755a3-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="755a3-157">Gdy składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="755a3-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="755a3-158">Drugi raz, gdy przeglądarka nawiąże połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="755a3-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="755a3-159">Aby zapobiec <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> dwukrotnemu uruchomieniu kodu dewelopera, zapoznaj się z sekcją [ponowne łączenie po prerenderowania](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="755a3-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="755a3-160">Podczas gdy Blazor Server aplikacja jest wstępnie renderowana, niektóre akcje, takie jak wywoływanie kodu JavaScript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="755a3-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="755a3-161">Składniki mogą być konieczne w różny sposób, gdy są wstępnie renderowane.</span><span class="sxs-lookup"><span data-stu-id="755a3-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="755a3-162">Aby uzyskać więcej informacji, zobacz sekcję [wykrywanie, gdy aplikacja jest prerenderowana](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="755a3-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="755a3-163">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="755a3-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="755a3-164">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="755a3-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="755a3-165">Po ustawieniu parametrów</span><span class="sxs-lookup"><span data-stu-id="755a3-165">After parameters are set</span></span>

<span data-ttu-id="755a3-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> są wywoływane:</span><span class="sxs-lookup"><span data-stu-id="755a3-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="755a3-167">Po zainicjowaniu składnika w programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> lub <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="755a3-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="755a3-168">Po ponownym wyrenderowaniu i zaopatrzeniu składnika nadrzędnego:</span><span class="sxs-lookup"><span data-stu-id="755a3-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="755a3-169">Tylko znane niezmienne typy pierwotne, których co najmniej jeden parametr został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="755a3-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="755a3-170">Wszystkie parametry złożone z typem.</span><span class="sxs-lookup"><span data-stu-id="755a3-170">Any complex-typed parameters.</span></span> <span data-ttu-id="755a3-171">Struktura nie może wiedzieć, czy wartości parametru złożonego są mutacją wewnętrznie, dlatego traktuje zestaw parametrów jako zmieniony.</span><span class="sxs-lookup"><span data-stu-id="755a3-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="755a3-172">Asynchroniczna działanie, gdy stosowane są parametry i wartości właściwości, muszą wystąpić podczas <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="755a3-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="755a3-173">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="755a3-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="755a3-174">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="755a3-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="755a3-175">Po renderowania składników</span><span class="sxs-lookup"><span data-stu-id="755a3-175">After component render</span></span>

<span data-ttu-id="755a3-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> są wywoływane po zakończeniu renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="755a3-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="755a3-177">Odwołania do elementów i składników są wypełniane w tym momencie.</span><span class="sxs-lookup"><span data-stu-id="755a3-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="755a3-178">Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanej zawartości, takiej jak aktywacja bibliotek języka JavaScript innych firm, które działają na renderowanych elementach DOM.</span><span class="sxs-lookup"><span data-stu-id="755a3-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="755a3-179">`firstRender`Parametr dla <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="755a3-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="755a3-180">Jest ustawiany po `true` raz pierwszy, gdy wystąpienie składnika jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="755a3-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="755a3-181">Można go użyć, aby upewnić się, że zadania inicjowania są wykonywane tylko raz.</span><span class="sxs-lookup"><span data-stu-id="755a3-181">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="755a3-182">Asynchroniczne działanie natychmiast po wyrenderowaniu musi wystąpić w trakcie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="755a3-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="755a3-183">Nawet w przypadku powrotu <xref:System.Threading.Tasks.Task> z programu <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , struktura nie planuje dalszych cykli renderowania dla składnika po zakończeniu tego zadania.</span><span class="sxs-lookup"><span data-stu-id="755a3-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="755a3-184">Ma to na celu uniknięcie nieskończonej pętli renderowania.</span><span class="sxs-lookup"><span data-stu-id="755a3-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="755a3-185">Różnią się one od innych metod cyklu życia, które Zaplanuj kolejny cykl renderowania po zakończeniu zwracanego zadania.</span><span class="sxs-lookup"><span data-stu-id="755a3-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="755a3-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nie są wywoływane podczas procesu renderowania wstępnego na serwerze*.</span><span class="sxs-lookup"><span data-stu-id="755a3-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="755a3-187">Metody są wywoływane, gdy składnik jest renderowany interaktywnie po zakończeniu renderowania prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="755a3-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="755a3-188">Gdy aplikacja jest przedrenderowana:</span><span class="sxs-lookup"><span data-stu-id="755a3-188">When the app prerenders:</span></span>

1. <span data-ttu-id="755a3-189">Składnik jest wykonywany na serwerze w celu utworzenia statycznego znacznika HTML w odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="755a3-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="755a3-190">W tej fazie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nie są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="755a3-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="755a3-191">Kiedy `blazor.server.js` lub `blazor.webassembly.js` uruchamiasz w przeglądarce, składnik jest uruchamiany ponownie w trybie renderowania interaktywnego.</span><span class="sxs-lookup"><span data-stu-id="755a3-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="755a3-192">Po ponownym uruchomieniu składnika <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **są** wywoływane, ponieważ aplikacja nie znajduje się w fazie renderowania prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="755a3-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="755a3-193">W przypadku skonfigurowania dowolnych programów obsługi zdarzeń odłączanie ich do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="755a3-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="755a3-194">Aby uzyskać więcej informacji, zobacz sekcję [Usuwanie składnika `IDisposable` z](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="755a3-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="755a3-195">Pomiń odświeżanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="755a3-195">Suppress UI refreshing</span></span>

<span data-ttu-id="755a3-196">Przesłoń <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> , aby pominąć odświeżanie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="755a3-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="755a3-197">Jeśli implementacja zwraca `true` , interfejs użytkownika zostanie odświeżony:</span><span class="sxs-lookup"><span data-stu-id="755a3-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="755a3-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest wywoływana za każdym razem, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="755a3-199">Nawet jeśli <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest zastępowany, składnik jest zawsze początkowo renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="755a3-200">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="755a3-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="755a3-201">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="755a3-201">State changes</span></span>

<span data-ttu-id="755a3-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powiadamia składnik o zmianie stanu.</span><span class="sxs-lookup"><span data-stu-id="755a3-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="755a3-203">Jeśli ma to zastosowanie, wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje, że składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="755a3-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana automatycznie dla <xref:Microsoft.AspNetCore.Components.EventCallback> metod.</span><span class="sxs-lookup"><span data-stu-id="755a3-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="755a3-205">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="755a3-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="755a3-206">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="755a3-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="755a3-207">Obsługuj niekompletne akcje asynchroniczne podczas renderowania</span><span class="sxs-lookup"><span data-stu-id="755a3-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="755a3-208">Akcje asynchroniczne wykonane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika.</span><span class="sxs-lookup"><span data-stu-id="755a3-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="755a3-209">Obiekty mogą być `null` lub uzupełniane wypełniane danymi podczas wykonywania metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="755a3-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="755a3-210">Zapewnianie logiki renderowania w celu potwierdzenia, że obiekty są inicjowane.</span><span class="sxs-lookup"><span data-stu-id="755a3-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="755a3-211">Renderuj zastępcze elementy interfejsu użytkownika (na przykład komunikat ładowania), gdy obiekty są `null` .</span><span class="sxs-lookup"><span data-stu-id="755a3-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="755a3-212">W `FetchData` składniku Blazor szablonów program <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest zastępowany asynchronicznie odbieranych danych prognozy ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="755a3-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="755a3-213">Gdy `forecasts` tak jest `null` , zostanie wyświetlony komunikat ładowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="755a3-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="755a3-214">Po `Task` zakończeniu zwracany przez <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> program składnik zostanie przerenderowany ze zaktualizowanym stanem.</span><span class="sxs-lookup"><span data-stu-id="755a3-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="755a3-215">`Pages/FetchData.razor` w Blazor Server szablonie:</span><span class="sxs-lookup"><span data-stu-id="755a3-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="755a3-216">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="755a3-216">Handle errors</span></span>

<span data-ttu-id="755a3-217">Aby uzyskać informacje na temat obsługi błędów podczas wykonywania metody cyklu życia, zobacz <xref:blazor/fundamentals/handle-errors> .</span><span class="sxs-lookup"><span data-stu-id="755a3-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="755a3-218">Stanowe Ponowne nawiązywanie połączenia po przeprowadzeniu prerenderowania</span><span class="sxs-lookup"><span data-stu-id="755a3-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="755a3-219">W Blazor Server aplikacji, gdy <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> jest <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , składnik jest początkowo renderowany statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="755a3-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="755a3-220">Gdy przeglądarka nawiąże połączenie z serwerem, składnik jest renderowany *ponownie*, a składnik jest teraz interaktywny.</span><span class="sxs-lookup"><span data-stu-id="755a3-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="755a3-221">Jeśli [`OnInitialized{Async}`](#component-initialization-methods) istnieje metoda cyklu życia do inicjowania składnika, metoda jest wykonywana *dwukrotnie*:</span><span class="sxs-lookup"><span data-stu-id="755a3-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="755a3-222">Gdy składnik jest wstępnie renderowany statycznie.</span><span class="sxs-lookup"><span data-stu-id="755a3-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="755a3-223">Po nawiązaniu połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="755a3-223">After the server connection has been established.</span></span>

<span data-ttu-id="755a3-224">Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="755a3-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="755a3-225">Aby uniknąć podwójnego renderowania w Blazor Server aplikacji:</span><span class="sxs-lookup"><span data-stu-id="755a3-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="755a3-226">Przekaż identyfikator, który może służyć do buforowania stanu podczas wykonywania prerenderowania i pobierania stanu po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="755a3-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="755a3-227">Użyj identyfikatora podczas renderowania, aby zapisać stan składnika.</span><span class="sxs-lookup"><span data-stu-id="755a3-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="755a3-228">Użyj identyfikatora po włączeniu, aby pobrać buforowany stan.</span><span class="sxs-lookup"><span data-stu-id="755a3-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="755a3-229">Poniższy kod ilustruje aktualizację `WeatherForecastService` w aplikacji opartej na szablonie Blazor Server , która pozwala uniknąć podwójnego renderowania:</span><span class="sxs-lookup"><span data-stu-id="755a3-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="755a3-230">Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> , zobacz <xref:blazor/fundamentals/signalr#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="755a3-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="755a3-231">Wykryj, kiedy aplikacja jest przedrenderowana</span><span class="sxs-lookup"><span data-stu-id="755a3-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="755a3-232">Usuwanie składnika z `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="755a3-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="755a3-233">Jeśli składnik implementuje <xref:System.IDisposable> , struktura wywołuje [metodę usuwania](/dotnet/standard/garbage-collection/implementing-dispose) po usunięciu składnika z interfejsu użytkownika, w którym można wydać niezarządzane zasoby.</span><span class="sxs-lookup"><span data-stu-id="755a3-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="755a3-234">Usuwanie może odbywać się w dowolnym momencie, w tym podczas [inicjowania składnika](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="755a3-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="755a3-235">Następujący składnik implementuje <xref:System.IDisposable> z [`@implements`](xref:mvc/views/razor#implements) Razor dyrektywą:</span><span class="sxs-lookup"><span data-stu-id="755a3-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="755a3-236">Jeśli obiekt wymaga usunięcia, można użyć wyrażenia lambda do usuwania obiektu, gdy <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="755a3-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="755a3-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="755a3-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="755a3-238">Poprzedni przykład jest wyświetlany w <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> .</span><span class="sxs-lookup"><span data-stu-id="755a3-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="755a3-239">W przypadku asynchronicznych zadań usuwania Użyj `DisposeAsync` zamiast <xref:System.IDisposable.Dispose> :</span><span class="sxs-lookup"><span data-stu-id="755a3-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="755a3-240">Wywoływanie metody <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> `Dispose` nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="755a3-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="755a3-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> może być wywoływana w ramach rozrywania modułu renderowania, dlatego żądanie aktualizacji interfejsu użytkownika nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="755a3-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="755a3-242">Procedury obsługi zdarzeń anulowania subskrypcji z zdarzeń platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="755a3-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="755a3-243">Poniższe przykłady [ Blazor formularzy](xref:blazor/forms-validation) pokazują, jak anulować subskrypcję programu obsługi zdarzeń w `Dispose` metodzie:</span><span class="sxs-lookup"><span data-stu-id="755a3-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="755a3-244">Pole prywatne i podejście lambda</span><span class="sxs-lookup"><span data-stu-id="755a3-244">Private field and lambda approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* <span data-ttu-id="755a3-245">Podejście metody prywatnej</span><span class="sxs-lookup"><span data-stu-id="755a3-245">Private method approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

<span data-ttu-id="755a3-246">Gdy są używane [anonimowe funkcje](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), metody lub wyrażenia, nie trzeba implementować <xref:System.IDisposable> i anulować subskrybowania delegatów.</span><span class="sxs-lookup"><span data-stu-id="755a3-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="755a3-247">Niepowodzenie anulowania subskrypcji delegata jest jednak problemem, **gdy obiekt ujawniający zdarzenie wyrejestruje okres istnienia elementu delegowanego**.</span><span class="sxs-lookup"><span data-stu-id="755a3-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="755a3-248">W takim przypadku przyczyną przecieku pamięci jest fakt, że zarejestrowany delegat zachowuje oryginalny obiekt.</span><span class="sxs-lookup"><span data-stu-id="755a3-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="755a3-249">W związku z tym należy stosować następujące podejścia tylko wtedy, gdy wiadomo, że delegat zdarzenia szybko usuwa.</span><span class="sxs-lookup"><span data-stu-id="755a3-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="755a3-250">W razie wątpliwości dotyczących okresu istnienia obiektów, które wymagają usunięcia, zasubskrybuj metodę delegata i prawidłowo Usuń delegata, jak pokazano w poprzednich przykładach.</span><span class="sxs-lookup"><span data-stu-id="755a3-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="755a3-251">Anonimowe podejście metody lambda (jawne usuwanie nie jest wymagane)</span><span class="sxs-lookup"><span data-stu-id="755a3-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="755a3-252">Anonimowe podejście wyrażenia lambda (jawne usuwanie nie jest wymagane)</span><span class="sxs-lookup"><span data-stu-id="755a3-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="755a3-253">Pełny przykład powyższego kodu z anonimowymi wyrażeniami lambda pojawia się w <xref:blazor/forms-validation#validator-components> .</span><span class="sxs-lookup"><span data-stu-id="755a3-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="755a3-254">Aby uzyskać więcej informacji, zobacz [Oczyszczanie zasobów niezarządzanych](/dotnet/standard/garbage-collection/unmanaged) i tematy, które po nich wykonują, przy wdrażaniu `Dispose` `DisposeAsync` metod i.</span><span class="sxs-lookup"><span data-stu-id="755a3-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="755a3-255">Anulowanie pracy w tle</span><span class="sxs-lookup"><span data-stu-id="755a3-255">Cancelable background work</span></span>

<span data-ttu-id="755a3-256">Składniki często wykonują długotrwałą pracę w tle, taką jak tworzenie wywołań sieciowych ( <xref:System.Net.Http.HttpClient> ) i współdziałanie z bazami danych.</span><span class="sxs-lookup"><span data-stu-id="755a3-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="755a3-257">Pożądane jest zatrzymanie pracy w tle w celu zapełnienia zasobów systemowych w kilku sytuacjach.</span><span class="sxs-lookup"><span data-stu-id="755a3-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="755a3-258">Na przykład operacje asynchroniczne w tle nie są automatycznie przerywane, gdy użytkownik przechodzi poza składnik.</span><span class="sxs-lookup"><span data-stu-id="755a3-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="755a3-259">Inne powody, dla których elementy robocze w tle mogą wymagać anulowania, to:</span><span class="sxs-lookup"><span data-stu-id="755a3-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="755a3-260">Rozpoczęto wykonywanie zadania w tle z uszkodzonymi danymi wejściowymi lub przetwarzaniem parametrów.</span><span class="sxs-lookup"><span data-stu-id="755a3-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="755a3-261">Bieżący zestaw wykonywanych elementów roboczych w tle musi zostać zastąpiony nowym zestawem elementów roboczych.</span><span class="sxs-lookup"><span data-stu-id="755a3-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="755a3-262">Priorytet aktualnie wykonywanych zadań musi zostać zmieniony.</span><span class="sxs-lookup"><span data-stu-id="755a3-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="755a3-263">Aby można było ponownie wdrożyć je na serwerze, należy zamknąć aplikację.</span><span class="sxs-lookup"><span data-stu-id="755a3-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="755a3-264">Zasoby serwera są ograniczone, co wymaga ponownego zaplanowania elementów roboczych w tle.</span><span class="sxs-lookup"><span data-stu-id="755a3-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="755a3-265">Aby zaimplementować w składniku wzorzec pracy z możliwością anulowania w tle:</span><span class="sxs-lookup"><span data-stu-id="755a3-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="755a3-266">Użyj <xref:System.Threading.CancellationTokenSource> i <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="755a3-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="755a3-267">Po [usunięciu składnika](#component-disposal-with-idisposable) i w dowolnym momencie anulowanie jest wymagane przez ręczne unieważnienie tokenu, wywołanie [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) do sygnału, że działanie w tle powinno być anulowane.</span><span class="sxs-lookup"><span data-stu-id="755a3-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="755a3-268">Po wywołaniu asynchronicznym wywołanie <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na tokenie.</span><span class="sxs-lookup"><span data-stu-id="755a3-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="755a3-269">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="755a3-269">In the following example:</span></span>

* <span data-ttu-id="755a3-270">`await Task.Delay(5000, cts.Token);` reprezentuje długotrwałe działanie asynchroniczne w tle.</span><span class="sxs-lookup"><span data-stu-id="755a3-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="755a3-271">`BackgroundResourceMethod` reprezentuje długotrwałą metodę w tle, która nie powinna być uruchamiana, jeśli element `Resource` zostanie usunięty przed wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="755a3-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="755a3-272">Blazor Server zdarzenia ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="755a3-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="755a3-273">Zdarzenia cyklu życia składnika omówione w tym artykule działają niezależnie od [ Blazor Server programów obsługi zdarzeń ponownego połączenia](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="755a3-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="755a3-274">Gdy Blazor Server aplikacja utraci SignalR połączenie z klientem, wszystkie aktualizacje interfejsu użytkownika są przerywane.</span><span class="sxs-lookup"><span data-stu-id="755a3-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="755a3-275">Aktualizacje interfejsu użytkownika są wznawiane po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="755a3-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="755a3-276">Aby uzyskać więcej informacji na temat zdarzeń i konfiguracji obsługi obwodów, zobacz <xref:blazor/fundamentals/signalr> .</span><span class="sxs-lookup"><span data-stu-id="755a3-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
