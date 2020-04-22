---
title: ASP.NET Podstawowy Blazor cykl życia
author: guardrex
description: Dowiedz się, jak korzystać z metod cyklu Blazor życia komponentu Razor w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791466"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="7d814-103">ASP.NET Podstawowy Blazor cykl życia</span><span class="sxs-lookup"><span data-stu-id="7d814-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="7d814-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7d814-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7d814-105">Struktura Blazor obejmuje synchroniczne i asynchroniczne metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="7d814-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="7d814-106">Zastępowanie metod cyklu życia w celu wykonywania dodatkowych operacji na komponentach podczas inicjowania i renderowania komponentów.</span><span class="sxs-lookup"><span data-stu-id="7d814-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="7d814-107">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="7d814-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="7d814-108">Metody inicjowania komponentów</span><span class="sxs-lookup"><span data-stu-id="7d814-108">Component initialization methods</span></span>

<span data-ttu-id="7d814-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> są wywoływane, gdy składnik jest inicjowany po otrzymaniu jego parametrów początkowych ze składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="7d814-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="7d814-110">Użyj, `OnInitializedAsync` gdy składnik wykonuje operację asynchronizacyjną i należy odświeżyć po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="7d814-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="7d814-111">W przypadku operacji synchroniczowej `OnInitialized`zastępuj:</span><span class="sxs-lookup"><span data-stu-id="7d814-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="7d814-112">Aby wykonać operację asynchronizacyjną, należy zastąpić `OnInitializedAsync` i użyć `await` słowa kluczowego w operacji:</span><span class="sxs-lookup"><span data-stu-id="7d814-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="7d814-113">Aplikacje serwera, które [prerender ich zawartość](xref:blazor/hosting-model-configuration#render-mode) wywołać `OnInitializedAsync` dwa **_razy:_**</span><span class="sxs-lookup"><span data-stu-id="7d814-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="7d814-114">Raz, gdy składnik jest początkowo renderowane statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="7d814-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="7d814-115">Po raz drugi, gdy przeglądarka nawiązuje połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="7d814-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="7d814-116">Aby zapobiec dwa `OnInitializedAsync` razy uruchomiony kod dewelopera, zobacz [stateful ponownego połączenia po prerendering](#stateful-reconnection-after-prerendering) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7d814-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="7d814-117">Podczas Blazor gdy aplikacja Server jest prerendering, niektóre akcje, takie jak wywołanie javascript, nie są możliwe, ponieważ połączenie z przeglądarką nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="7d814-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="7d814-118">Składniki mogą wymagać renderowania inaczej podczas prerendered.</span><span class="sxs-lookup"><span data-stu-id="7d814-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="7d814-119">Aby uzyskać więcej informacji, zobacz [Wykrywanie, gdy aplikacja jest prerendering](#detect-when-the-app-is-prerendering) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7d814-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="7d814-120">Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji.</span><span class="sxs-lookup"><span data-stu-id="7d814-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7d814-121">Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7d814-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="7d814-122">Przed ustawieniem parametrów</span><span class="sxs-lookup"><span data-stu-id="7d814-122">Before parameters are set</span></span>

<span data-ttu-id="7d814-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>ustawia parametry dostarczone przez element nadrzędny komponentu w drzewie renderowania:</span><span class="sxs-lookup"><span data-stu-id="7d814-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="7d814-124"><xref:Microsoft.AspNetCore.Components.ParameterView>zawiera cały zestaw wartości parametrów za każdym razem, gdy `SetParametersAsync` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="7d814-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="7d814-125">Domyślna `SetParametersAsync` implementacja ustawia wartość każdej `[Parameter]` właściwości `[CascadingParameter]` z atrybutem lub atrybutem, który ma odpowiednią wartość w pliku `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="7d814-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="7d814-126">Parametry, które nie mają odpowiedniej `ParameterView` wartości w pozostają niezmienione.</span><span class="sxs-lookup"><span data-stu-id="7d814-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="7d814-127">Jeśli `base.SetParametersAync` nie jest wywoływana, kod niestandardowy można interpretować wartość parametrów przychodzących w dowolny sposób wymagane.</span><span class="sxs-lookup"><span data-stu-id="7d814-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="7d814-128">Na przykład nie ma wymogu, aby przypisać parametry przychodzące do właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="7d814-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="7d814-129">Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji.</span><span class="sxs-lookup"><span data-stu-id="7d814-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7d814-130">Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7d814-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="7d814-131">Po ustawieniu parametrów</span><span class="sxs-lookup"><span data-stu-id="7d814-131">After parameters are set</span></span>

<span data-ttu-id="7d814-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> są nazywane:</span><span class="sxs-lookup"><span data-stu-id="7d814-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="7d814-133">Gdy komponent jest inicjowany i otrzymał swój pierwszy zestaw parametrów ze swojego komponentu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="7d814-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="7d814-134">Gdy składnik nadrzędny ponownie renderuje i dostarcza:</span><span class="sxs-lookup"><span data-stu-id="7d814-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="7d814-135">Tylko znane pierwotne typy, których co najmniej jeden parametr uległ zmianie.</span><span class="sxs-lookup"><span data-stu-id="7d814-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="7d814-136">Wszelkie parametry typu complex.</span><span class="sxs-lookup"><span data-stu-id="7d814-136">Any complex-typed parameters.</span></span> <span data-ttu-id="7d814-137">Struktura nie może wiedzieć, czy wartości parametru typizowanego typu complex zostały zmutowane wewnętrznie, więc traktuje ustawiony parametr jako zmieniony.</span><span class="sxs-lookup"><span data-stu-id="7d814-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="7d814-138">Praca asynchronizacjowa podczas stosowania parametrów `OnParametersSetAsync` i wartości właściwości musi wystąpić podczas zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="7d814-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="7d814-139">Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji.</span><span class="sxs-lookup"><span data-stu-id="7d814-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7d814-140">Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7d814-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="7d814-141">Po renderowaniu komponentu</span><span class="sxs-lookup"><span data-stu-id="7d814-141">After component render</span></span>

<span data-ttu-id="7d814-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>i <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> są wywoływane po zakończeniu renderowania komponentu.</span><span class="sxs-lookup"><span data-stu-id="7d814-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="7d814-143">Odwołania do elementów i składników są wypełniane w tym momencie.</span><span class="sxs-lookup"><span data-stu-id="7d814-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="7d814-144">Ten etap służy do wykonywania dodatkowych kroków inicjowania przy użyciu renderowanych treści, takich jak aktywowanie bibliotek JavaScript innych firm działających na renderowanych elementach DOM.</span><span class="sxs-lookup"><span data-stu-id="7d814-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="7d814-145">Parametr `firstRender` dla `OnAfterRenderAsync` `OnAfterRender`i:</span><span class="sxs-lookup"><span data-stu-id="7d814-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="7d814-146">Jest `true` ustawiona na pierwszy raz, że wystąpienie składnika jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="7d814-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="7d814-147">Może służyć do zapewnienia, że praca inicjowania jest wykonywana tylko raz.</span><span class="sxs-lookup"><span data-stu-id="7d814-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="7d814-148">Praca asynchronicznego natychmiast po renderowaniu `OnAfterRenderAsync` musi wystąpić podczas zdarzenia cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="7d814-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="7d814-149">Nawet jeśli <xref:System.Threading.Tasks.Task> zwrócisz `OnAfterRenderAsync`z programu, struktura nie planuje dalszego cyklu renderowania dla składnika po zakończeniu tego zadania.</span><span class="sxs-lookup"><span data-stu-id="7d814-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="7d814-150">Ma to na celu uniknięcie nieskończonej pętli renderowania.</span><span class="sxs-lookup"><span data-stu-id="7d814-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="7d814-151">Różni się od innych metod cyklu życia, które planują kolejny cykl renderowania po zakończeniu zwracanego zadania.</span><span class="sxs-lookup"><span data-stu-id="7d814-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="7d814-152">`OnAfterRender`i `OnAfterRenderAsync` *nie są wywoływane podczas prerendering na serwerze.*</span><span class="sxs-lookup"><span data-stu-id="7d814-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="7d814-153">Jeśli są skonfigurowane programy obsługi zdarzeń, odłącz je w dyspozycji.</span><span class="sxs-lookup"><span data-stu-id="7d814-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7d814-154">Aby uzyskać więcej informacji, zobacz [usuwanie składnika z IDisposable](#component-disposal-with-idisposable) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7d814-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="7d814-155">Pomijanie odświeżania interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="7d814-155">Suppress UI refreshing</span></span>

<span data-ttu-id="7d814-156">Zastądeń, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> aby pominąć odświeżanie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7d814-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="7d814-157">Jeśli implementacja `true`powróci, interfejs użytkownika jest odświeżany:</span><span class="sxs-lookup"><span data-stu-id="7d814-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="7d814-158">`ShouldRender`jest wywoływana za każdym razem, gdy składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="7d814-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="7d814-159">Nawet `ShouldRender` jeśli jest zastępowany, składnik jest zawsze początkowo renderowane.</span><span class="sxs-lookup"><span data-stu-id="7d814-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="7d814-160">Zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="7d814-160">State changes</span></span>

<span data-ttu-id="7d814-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>powiadamia składnik, że jego stan uległ zmianie.</span><span class="sxs-lookup"><span data-stu-id="7d814-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="7d814-162">W stosownych `StateHasChanged` przypadkach wywołanie powoduje, że składnik ma być rerendered.</span><span class="sxs-lookup"><span data-stu-id="7d814-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="7d814-163">Obsługa niekompletnych akcji asynchronizowych podczas renderowania</span><span class="sxs-lookup"><span data-stu-id="7d814-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="7d814-164">Akcje asynchroniczne wykonywane w zdarzeniach cyklu życia mogły nie zostać ukończone przed renderowaniem składnika.</span><span class="sxs-lookup"><span data-stu-id="7d814-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="7d814-165">Obiekty mogą `null` być lub niecałkowicie wypełnione danymi podczas wykonywania metody cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="7d814-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="7d814-166">Podaj logikę renderowania, aby potwierdzić, że obiekty są inicjowane.</span><span class="sxs-lookup"><span data-stu-id="7d814-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="7d814-167">Renderuj elementy zastępczego interfejsu użytkownika (na przykład `null`wiadomość ładującej) podczas gdy obiekty są .</span><span class="sxs-lookup"><span data-stu-id="7d814-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="7d814-168">W `FetchData` składniku Blazor szablonów `OnInitializedAsync` jest zastępowane do asychronously otrzymywać`forecasts`dane prognozy ( ).</span><span class="sxs-lookup"><span data-stu-id="7d814-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="7d814-169">Gdy `forecasts` `null`jest , wiadomość ładowania jest wyświetlany do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7d814-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="7d814-170">Po `Task` zakończeniu `OnInitializedAsync` zwracania składnik jest rerenderowany ze zaktualizowanym stanem.</span><span class="sxs-lookup"><span data-stu-id="7d814-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="7d814-171">*Pages/FetchData.brzytwa* w szablonie Blazor serwera:</span><span class="sxs-lookup"><span data-stu-id="7d814-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="7d814-172">Utylizacja komponentów z IDisposable</span><span class="sxs-lookup"><span data-stu-id="7d814-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="7d814-173">Jeśli składnik implementuje, <xref:System.IDisposable> [Dispose metoda](/dotnet/standard/garbage-collection/implementing-dispose) jest wywoływana, gdy składnik jest usuwany z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7d814-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="7d814-174">Następujące zastosowania składnika `@implements IDisposable` `Dispose` i metody:</span><span class="sxs-lookup"><span data-stu-id="7d814-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="7d814-175">`Dispose` Wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7d814-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="7d814-176">`StateHasChanged`może być wywoływana jako część rozbiórki modułu renderowania, więc żądanie aktualizacji interfejsu użytkownika w tym momencie nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7d814-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="7d814-177">Anulowanie obsługi zdarzeń z .NET.</span><span class="sxs-lookup"><span data-stu-id="7d814-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="7d814-178">Poniższe przykłady [ Blazor formularza](xref:blazor/forms-validation) pokazują, jak odłączyć program obsługi zdarzeń w metodzie: `Dispose`</span><span class="sxs-lookup"><span data-stu-id="7d814-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="7d814-179">Prywatne pole i podejście lambda</span><span class="sxs-lookup"><span data-stu-id="7d814-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="7d814-180">Podejście metody prywatnej</span><span class="sxs-lookup"><span data-stu-id="7d814-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="7d814-181">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="7d814-181">Handle errors</span></span>

<span data-ttu-id="7d814-182">Aby uzyskać informacje na temat obsługi błędów <xref:blazor/handle-errors#lifecycle-methods>podczas wykonywania metody cyklu życia, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7d814-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="7d814-183">Stateful ponownego połączenia po prerendering</span><span class="sxs-lookup"><span data-stu-id="7d814-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="7d814-184">W Blazor aplikacji Server, gdy `RenderMode` jest `ServerPrerendered`, składnik jest początkowo renderowane statycznie jako część strony.</span><span class="sxs-lookup"><span data-stu-id="7d814-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="7d814-185">Gdy przeglądarka nawiązuje połączenie z serwerem, składnik jest renderowany *ponownie,* a składnik jest teraz interaktywny.</span><span class="sxs-lookup"><span data-stu-id="7d814-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="7d814-186">Jeśli istnieje metoda cyklu życia [OnInitialized{Async}](#component-initialization-methods) do inicjowania składnika, metoda jest wykonywana *dwa razy:*</span><span class="sxs-lookup"><span data-stu-id="7d814-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="7d814-187">Gdy składnik jest prerendered statycznie.</span><span class="sxs-lookup"><span data-stu-id="7d814-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="7d814-188">Po nawiązaniu połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="7d814-188">After the server connection has been established.</span></span>

<span data-ttu-id="7d814-189">Może to spowodować zauważalną zmianę danych wyświetlanych w interfejsie użytkownika, gdy składnik jest ostatecznie renderowany.</span><span class="sxs-lookup"><span data-stu-id="7d814-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="7d814-190">Aby uniknąć scenariusza podwójnego renderowania Blazor w aplikacji server:</span><span class="sxs-lookup"><span data-stu-id="7d814-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="7d814-191">Przekaż identyfikator, który może służyć do buforowania stanu podczas prerendering i pobrać stan po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d814-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="7d814-192">Użyj identyfikatora podczas prerendering, aby zapisać stan składnika.</span><span class="sxs-lookup"><span data-stu-id="7d814-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="7d814-193">Użyj identyfikatora po prerendering pobrać stan buforowane.</span><span class="sxs-lookup"><span data-stu-id="7d814-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="7d814-194">Poniższy kod pokazuje `WeatherForecastService` zaktualizowane w Blazor aplikacji serwera opartej na szablonach, która pozwala uniknąć podwójnego renderowania:</span><span class="sxs-lookup"><span data-stu-id="7d814-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="7d814-195">Aby uzyskać więcej `RenderMode`informacji <xref:blazor/hosting-model-configuration#render-mode>na temat , zobacz .</span><span class="sxs-lookup"><span data-stu-id="7d814-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="7d814-196">Wykrywanie, kiedy aplikacja jest prerendering</span><span class="sxs-lookup"><span data-stu-id="7d814-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
