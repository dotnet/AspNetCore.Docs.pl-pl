---
title: ASP.NET Core Blazor WebAssembly najlepszych rozwiązań dotyczących wydajności
author: pranavkm
description: Wskazówki dotyczące zwiększania wydajności Blazor WebAssembly aplikacji ASP.NET Core i unikania typowych problemów z wydajnością.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 64205dcf7aeecbe594b0164a7966b7f96bcca68b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013362"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="f9572-103">ASP.NET Core Blazor WebAssembly najlepszych rozwiązań dotyczących wydajności</span><span class="sxs-lookup"><span data-stu-id="f9572-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="f9572-104">Autor [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="f9572-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="f9572-105">Ten artykuł zawiera wskazówki dotyczące najlepszych rozwiązań dotyczących Blazor WebAssembly wydajności ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9572-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="f9572-106">Unikaj renderowania zbędnych składników</span><span class="sxs-lookup"><span data-stu-id="f9572-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="f9572-107">Blazoralgorytm różnicowania unika odrenderowania składnika, gdy algorytm postrzega, że składnik nie został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="f9572-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="f9572-108">Przesłoń dla szczegółowej <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> kontroli nad renderowaniem składników.</span><span class="sxs-lookup"><span data-stu-id="f9572-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="f9572-109">W przypadku tworzenia składnika tylko interfejsu użytkownika, który nigdy nie zmienia się po początkowym renderowaniu, skonfiguruj <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> do zwrócenia `false` :</span><span class="sxs-lookup"><span data-stu-id="f9572-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="f9572-110">Większość aplikacji nie wymaga precyzyjnej kontroli, ale <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> może służyć do selektywnego renderowania składnika odpowiadającego zdarzeniu interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f9572-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="f9572-111">Użycie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> może być również ważne w scenariuszach, w których renderowane jest dużą liczbę składników.</span><span class="sxs-lookup"><span data-stu-id="f9572-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="f9572-112">Weź pod uwagę siatkę, w której użycie <xref:Microsoft.AspNetCore.Components.EventCallback> w jednym składniku w jednej komórce z wywołań siatki <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> w siatce.</span><span class="sxs-lookup"><span data-stu-id="f9572-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="f9572-113">Wywołanie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> powoduje ponowne renderowanie każdego składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="f9572-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="f9572-114">Jeśli tylko niewielka liczba komórek wymaga odtworzenia, użyj, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Aby uniknąć pogorszenia wydajności niepotrzebnych renderowania.</span><span class="sxs-lookup"><span data-stu-id="f9572-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="f9572-115">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f9572-115">In the following example:</span></span>

* <span data-ttu-id="f9572-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>jest zastępowany i ustawiany na wartość <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pola, początkowo `false` podczas ładowania składnika.</span><span class="sxs-lookup"><span data-stu-id="f9572-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="f9572-117">Gdy przycisk jest zaznaczony, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> jest ustawiony na `true` , co wymusza ponowną renderowanie składnika przy użyciu zaktualizowanego `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="f9572-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="f9572-118">Natychmiast po <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ponownym renderowaniu ustawia wartość z powrotem na, aby zapobiec dalszemu ponownemu przywracaniu do <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` momentu, gdy przycisk zostanie wybrany.</span><span class="sxs-lookup"><span data-stu-id="f9572-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="f9572-119">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="f9572-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="f9572-120">Wirtualizacja fragmentów do ponownego użycia</span><span class="sxs-lookup"><span data-stu-id="f9572-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="f9572-121">Składniki oferują wygodną metodę tworzenia fragmentów kodu i znaczników do ponownego użycia.</span><span class="sxs-lookup"><span data-stu-id="f9572-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="f9572-122">Ogólnie rzecz biorąc zalecamy tworzenie pojedynczych składników, które najlepiej dopasowują się do wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f9572-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="f9572-123">Jednym z zawartoocią jest to, że każdy dodatkowy składnik podrzędny wnosi do łącznego czasu potrzebnego na renderowanie składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="f9572-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="f9572-124">W przypadku większości aplikacji dodatkowe obciążenie jest znikome.</span><span class="sxs-lookup"><span data-stu-id="f9572-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="f9572-125">W przypadku aplikacji, które tworzą dużą liczbę składników, należy rozważyć użycie strategii w celu zmniejszenia nakładu pracy, np. ograniczenia liczby renderowanych składników.</span><span class="sxs-lookup"><span data-stu-id="f9572-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="f9572-126">Na przykład siatka lub lista, która renderuje setki wierszy zawierających składniki, ma intensywną obsługę procesora.</span><span class="sxs-lookup"><span data-stu-id="f9572-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="f9572-127">Rozważ wirtualizację układu siatki lub listy tak, że tylko podzestaw składników jest renderowany w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="f9572-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="f9572-128">Aby zapoznać się z przykładem renderowania podzestawu składników, zobacz następujące składniki w [ `Virtualization` aplikacji przykładowej (repozytorium w witrynie GitHub/przykłady)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="f9572-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="f9572-129">`Virtualize`składnik ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): składnik zapisany w języku C#, który implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> zestaw wierszy danych pogody na podstawie przewijania przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f9572-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="f9572-130">`FetchData`składnik ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): używa `Virtualize` składnika do wyświetlania 25 wierszy danych pogody jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="f9572-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="f9572-131">Unikaj międzyoperacyjności JavaScript do organizowania danych</span><span class="sxs-lookup"><span data-stu-id="f9572-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="f9572-132">W programie Blazor WebAssembly wywołanie międzyoperacyjne języka JavaScript (js) musi przechodzić przez granicę webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="f9572-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="f9572-133">Serializacja i deserializacja zawartości w dwóch kontekstach powoduje utworzenie obciążenia dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f9572-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="f9572-134">Częste wywołania w programie JS Interop często mają negatywny wpływ na wydajność.</span><span class="sxs-lookup"><span data-stu-id="f9572-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="f9572-135">Aby zmniejszyć kierowanie danych między granicami, ustal, czy aplikacja może skonsolidować wiele małych ładunków do pojedynczego dużego ładunku, aby uniknąć dużej liczby przełączeń kontekstu między zestawem webassembly i JS.</span><span class="sxs-lookup"><span data-stu-id="f9572-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="f9572-136">Użyj System.Text.Jsna</span><span class="sxs-lookup"><span data-stu-id="f9572-136">Use System.Text.Json</span></span>

<span data-ttu-id="f9572-137">Blazorimplementacja elementu webinterop w języku JS polega na tym <xref:System.Text.Json> , że jest to biblioteka serializacji JSON o wysokiej wydajności z alokacją małej ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="f9572-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="f9572-138">Użycie nie powoduje, że <xref:System.Text.Json> rozmiar ładunku aplikacji jest większy niż dodanie co najmniej jednej alternatywnej biblioteki JSON.</span><span class="sxs-lookup"><span data-stu-id="f9572-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="f9572-139">Aby uzyskać wskazówki dotyczące migracji, zobacz [Jak przeprowadzić migrację z `Newtonsoft.Json` do programu `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="f9572-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="f9572-140">Używaj synchronicznych i nieskierowanych interfejsów API międzyoperacyjności w razie potrzeby</span><span class="sxs-lookup"><span data-stu-id="f9572-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="f9572-141">Blazor WebAssemblyoferuje dwie dodatkowe wersje programu dla <xref:Microsoft.JSInterop.IJSRuntime> jednej wersji dostępne dla Blazor Server aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f9572-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="f9572-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime>umożliwia Asynchroniczne wywoływanie wywołań programu JS Interop, które ma mniej obciążenia niż wersje asynchroniczne:</span><span class="sxs-lookup"><span data-stu-id="f9572-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="f9572-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>zezwala na nieskierowanie wywołań międzyoperacyjnych JS:</span><span class="sxs-lookup"><span data-stu-id="f9572-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="f9572-144">Podczas używania <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> ma najmniejsze obciążenie związane z interfejsem js międzyoperacyjności, interfejsy API języka JavaScript wymagane do współdziałania z tymi interfejsy API są obecnie nieudokumentowane i mogą ulegać zmianom w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="f9572-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="f9572-145">Zmniejsz rozmiar aplikacji</span><span class="sxs-lookup"><span data-stu-id="f9572-145">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="f9572-146">Tworzenie łączy języka pośredniego (IL)</span><span class="sxs-lookup"><span data-stu-id="f9572-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="f9572-147">[Łączenie Blazor WebAssembly aplikacji](xref:blazor/host-and-deploy/configure-linker) zmniejsza rozmiar aplikacji przez przycinanie nieużywanego kodu w plikach binarnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f9572-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="f9572-148">Domyślnie konsolidator jest włączony tylko w przypadku kompilowania w `Release` konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f9572-148">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="f9572-149">Aby z tego skorzystać, Opublikuj aplikację do wdrożenia przy użyciu [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z opcją [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) ustawioną na `Release` :</span><span class="sxs-lookup"><span data-stu-id="f9572-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="f9572-150">Zestawy ładowania z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="f9572-150">Lazy load assemblies</span></span>

<span data-ttu-id="f9572-151">Ładuj zestawy w czasie wykonywania, gdy zestawy są wymagane przez trasę.</span><span class="sxs-lookup"><span data-stu-id="f9572-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="f9572-152">Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="f9572-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="f9572-153">Kompresja</span><span class="sxs-lookup"><span data-stu-id="f9572-153">Compression</span></span>

<span data-ttu-id="f9572-154">Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="f9572-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="f9572-155">Blazoropiera się na serwerze w celu przeprowadzenia negotation zawartości i obkompresji plików skompresowanych statycznie.</span><span class="sxs-lookup"><span data-stu-id="f9572-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="f9572-156">Po wdrożeniu aplikacji Sprawdź, czy aplikacja obsługuje skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="f9572-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="f9572-157">Zbadaj kartę Sieć w Narzędzia deweloperskie przeglądarki i sprawdź, czy pliki są obsługiwane przez program `Content-Encoding: br` lub `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="f9572-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="f9572-158">Jeśli host nie obsługuje skompresowanych plików, postępuj zgodnie z instrukcjami w temacie <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="f9572-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="f9572-159">Wyłącz nieużywane funkcje</span><span class="sxs-lookup"><span data-stu-id="f9572-159">Disable unused features</span></span>

<span data-ttu-id="f9572-160">Blazor WebAssemblyśrodowisko uruchomieniowe obejmuje następujące funkcje platformy .NET, które można wyłączyć, jeśli aplikacja nie wymaga ich dla mniejszego rozmiaru ładunku:</span><span class="sxs-lookup"><span data-stu-id="f9572-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="f9572-161">Plik danych jest uwzględniany w celu poprawnego wprowadzania informacji o strefie czasowej.</span><span class="sxs-lookup"><span data-stu-id="f9572-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="f9572-162">Jeśli aplikacja nie wymaga tej funkcji, rozważ wyłączenie jej przez ustawienie właściwości programu `BlazorEnableTimeZoneSupport` MSBuild w pliku projektu aplikacji na `false` :</span><span class="sxs-lookup"><span data-stu-id="f9572-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="f9572-163">Informacje o sortowaniu są uwzględniane w celu <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> poprawnego działania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="f9572-163">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="f9572-164">Jeśli masz pewność, że aplikacja nie wymaga danych sortowania, rozważ wyłączenie jej przez ustawienie `BlazorWebAssemblyPreserveCollationData` Właściwości programu MSBuild w pliku projektu aplikacji na `false` :</span><span class="sxs-lookup"><span data-stu-id="f9572-164">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
