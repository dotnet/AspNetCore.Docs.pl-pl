---
title: 'Zestawy ładowania z opóźnieniem w ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Odkryj, jak ładować zestawy w aplikacjach ASP.NET Core :::no-loc(Blazor WebAssembly)::: .'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6a1feffb5341d432d6d1949a9e26b9537b85ba03
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054791"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="a6fd8-103">Zestawy ładowania z opóźnieniem w ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a6fd8-103">Lazy load assemblies in ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="a6fd8-104">Autorzy [Safia Abdalla](https://safia.rocks) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a6fd8-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a6fd8-105">:::no-loc(Blazor WebAssembly)::: wydajność uruchamiania aplikacji można ulepszyć, odwołując ładowanie niektórych zestawów aplikacji do momentu, gdy nie są wymagane, co jest nazywane *ładowaniem z opóźnieniem* .</span><span class="sxs-lookup"><span data-stu-id="a6fd8-105">:::no-loc(Blazor WebAssembly)::: app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading* .</span></span> <span data-ttu-id="a6fd8-106">Na przykład zestawy, które są używane tylko do renderowania pojedynczego składnika, można skonfigurować tak, aby ładowały się tylko wtedy, gdy użytkownik nawiguje do tego składnika.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="a6fd8-107">Po załadowaniu zestawy są buforowane po stronie klienta i są dostępne dla wszystkich przyszłych nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="a6fd8-108">:::no-loc(Blazor):::Funkcja ładowania z opóźnieniem umożliwia oznaczanie zestawów aplikacji na potrzeby ładowania z opóźnieniem, które ładuje zestawy podczas środowiska uruchomieniowego, gdy użytkownik przechodzi do określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-108">:::no-loc(Blazor):::'s lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="a6fd8-109">Funkcja składa się z zmian w pliku projektu i zmian w routerze aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="a6fd8-110">Ładowanie z opóźnieniem zestawu nie pozwala na korzystanie z :::no-loc(Blazor Server)::: aplikacji, ponieważ zestawy nie są pobierane do klienta w :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-110">Assembly lazy loading doesn't benefit :::no-loc(Blazor Server)::: apps because assemblies aren't downloaded to the client in a :::no-loc(Blazor Server)::: app.</span></span>

## <a name="project-file"></a><span data-ttu-id="a6fd8-111">Plik projektu</span><span class="sxs-lookup"><span data-stu-id="a6fd8-111">Project file</span></span>

<span data-ttu-id="a6fd8-112">Oznacz zestawy do ładowania z opóźnieniem w pliku projektu aplikacji ( `.csproj` ) przy użyciu `:::no-loc(Blazor):::WebAssemblyLazyLoad` elementu.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `:::no-loc(Blazor):::WebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="a6fd8-113">Użyj nazwy zestawu z `.dll` rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-113">Use the assembly name with the `.dll` extension.</span></span> <span data-ttu-id="a6fd8-114">:::no-loc(Blazor):::Struktura uniemożliwia ładowanie zestawów określonych przez tę grupę elementów podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-114">The :::no-loc(Blazor)::: framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="a6fd8-115">Poniższy przykład oznacza duże niestandardowe zestawy ( `GrantImaharaRobotControls.dll` ) dla ładowania z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="a6fd8-116">Jeśli zestaw, który jest oznaczony do ładowania z opóźnieniem ma zależności, muszą także być oznaczone do ładowania opóźnionego w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <:::no-loc(Blazor):::WebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="a6fd8-117">`Router` cm6long</span><span class="sxs-lookup"><span data-stu-id="a6fd8-117">`Router` component</span></span>

<span data-ttu-id="a6fd8-118">:::no-loc(Blazor):::`Router`składnik wyznacza, który zestaw :::no-loc(Blazor)::: wyszukuje składniki routingu.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-118">:::no-loc(Blazor):::'s `Router` component designates which assemblies :::no-loc(Blazor)::: searches for routable components.</span></span> <span data-ttu-id="a6fd8-119">`Router`Składnik jest również odpowiedzialny za renderowanie składnika dla trasy, w której przechodzi użytkownik.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-119">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="a6fd8-120">`Router`Składnik obsługuje `OnNavigateAsync` funkcję, która może być używana w połączeniu z opóźnionym ładowaniem.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-120">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="a6fd8-121">W `Router` składniku aplikacji ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a6fd8-121">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="a6fd8-122">Dodaj `OnNavigateAsync` wywołanie zwrotne.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-122">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="a6fd8-123">`OnNavigateAsync`Procedura obsługi jest wywoływana, gdy użytkownik:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-123">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="a6fd8-124">Po raz pierwszy odwiedza trasę, przechodząc do niego bezpośrednio z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-124">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="a6fd8-125">Przechodzi do nowej trasy przy użyciu linku lub <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wywołania.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-125">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="a6fd8-126">Jeśli zestawy ładowane z opóźnieniem zawierają składniki routingu, należy dodać [listę](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (na przykład nazwa `lazyLoadedAssemblies` ) do składnika.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-126">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="a6fd8-127">Zestawy są przenoszone z powrotem do <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> kolekcji w przypadku, gdy zestawy zawierają składniki routingu.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-127">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="a6fd8-128">Struktura przeszukuje zestawy pod kątem tras i aktualizuje kolekcję tras w przypadku znalezienia nowych tras.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-128">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="a6fd8-129">Jeśli `OnNavigateAsync` wywołanie zwrotne zgłasza nieobsłużony wyjątek, zostanie wywołany [ :::no-loc(Blazor)::: interfejs użytkownika błędu](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .</span><span class="sxs-lookup"><span data-stu-id="a6fd8-129">If the `OnNavigateAsync` callback throws an unhandled exception, the [:::no-loc(Blazor)::: error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="a6fd8-130">Logika ładowania zestawu w `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="a6fd8-130">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="a6fd8-131">`OnNavigateAsync` zawiera `NavigationContext` parametr, który zawiera informacje o bieżącym asynchronicznym zdarzeniu nawigacji, w tym ścieżkę docelową ( `Path` ) i tokenem anulowania ( `CancellationToken` ):</span><span class="sxs-lookup"><span data-stu-id="a6fd8-131">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="a6fd8-132">`Path`Właściwość jest ścieżką docelową użytkownika względem ścieżki podstawowej aplikacji, na przykład `/robot` .</span><span class="sxs-lookup"><span data-stu-id="a6fd8-132">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="a6fd8-133">`CancellationToken`Może służyć do obserwowania anulowania zadania asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-133">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="a6fd8-134">`OnNavigateAsync` automatycznie anuluje aktualnie uruchomione zadanie nawigacji, gdy użytkownik nawiguje do innej strony.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-134">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="a6fd8-135">Wewnątrz `OnNavigateAsync` , należy wdrożyć logikę, aby określić zestawy do załadowania.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-135">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="a6fd8-136">Dostępne opcje:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-136">Options include:</span></span>

* <span data-ttu-id="a6fd8-137">Kontrole warunkowe wewnątrz `OnNavigateAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-137">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="a6fd8-138">Tabela wyszukiwania, w której są mapowane trasy do nazw zestawów, które zostały dodane do składnika lub zaimplementowane w [`@code`](xref:mvc/views/razor#code) bloku.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-138">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="a6fd8-139">`LazyAssemblyLoader` to usługa singleton udostępniona przez platformę do ładowania zestawów.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-139">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="a6fd8-140">Wsuń `LazyAssemblyLoader` do `Router` składnika:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-140">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="a6fd8-141">`LazyAssemblyLoader`Zapewnia `LoadAssembliesAsync` metodę, która:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-141">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="a6fd8-142">Używa programu JS Interop do pobierania zestawów za pośrednictwem połączenia sieciowego.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-142">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="a6fd8-143">Ładuje zestawy do wykonywania w środowisku uruchomieniowym w zestawie webassembly w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-143">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="a6fd8-144">Implementacja wdrożenia z opóźnieniem platformy obsługuje ładowanie z opóźnieniem z użyciem prerenderowania w hostowanym :::no-loc(Blazor)::: rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-144">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted :::no-loc(Blazor)::: solution.</span></span> <span data-ttu-id="a6fd8-145">Podczas renderowania prerenderingu założono, że wszystkie zestawy, w tym zaznaczone dla ładowania z opóźnieniem, zostały załadowane.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-145">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="a6fd8-146">Zarejestruj ręcznie `LazyAssemblyLoader` w metodzie projektu *serwera* `Startup.ConfigureServices` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="a6fd8-146">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="a6fd8-147">Interakcja użytkownika z `<Navigating>` zawartością</span><span class="sxs-lookup"><span data-stu-id="a6fd8-147">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="a6fd8-148">Podczas ładowania zestawów, które mogą potrwać kilka sekund, `Router` składnik może wskazywać użytkownikowi, że występuje przejście strony:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-148">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="a6fd8-149">Dodaj [`@using`](xref:mvc/views/razor#using) dyrektywę dla <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-149">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="a6fd8-150">Dodaj `<Navigating>` do składnika tag zawierający znaczniki, które mają być wyświetlane podczas zdarzeń przejścia strony.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-150">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="a6fd8-151">Obsługa anulowania w `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="a6fd8-151">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="a6fd8-152">`NavigationContext`Obiekt przesłany do `OnNavigateAsync` wywołania zwrotnego zawiera `CancellationToken` zestaw, który jest ustawiony w momencie wystąpienia nowego zdarzenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-152">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="a6fd8-153">`OnNavigateAsync`Wywołanie zwrotne musi zgłosić, gdy ten token anulowania jest ustawiony tak, aby uniknąć kontynuowania uruchamiania `OnNavigateAsync` wywołania zwrotnego w nieaktualnej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-153">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="a6fd8-154">Jeśli użytkownik przejdzie do trasy a, a następnie natychmiast do trasy B, aplikacja nie powinna nadal uruchamiać `OnNavigateAsync` wywołania zwrotnego dla trasy a:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-154">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="a6fd8-155">Nie zgłaszaj, czy token anulowania w programie `NavigationContext` został anulowany może spowodować niezamierzone zachowanie, takie jak renderowanie składnika z poprzedniej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-155">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="a6fd8-156">`OnNavigateAsync` zdarzenia i pliki zestawu o zmienionej nazwie</span><span class="sxs-lookup"><span data-stu-id="a6fd8-156">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="a6fd8-157">Moduł ładujący zasoby opiera się na nazwach zestawów, które są zdefiniowane w `blazor.boot.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-157">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="a6fd8-158">W przypadku [zmiany nazwy zestawów](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)nazwy zestawów używane w `OnNavigateAsync` metodach i nazwach zestawów w `blazor.boot.json` pliku nie są zsynchronizowane.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-158">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="a6fd8-159">Aby to skorygować:</span><span class="sxs-lookup"><span data-stu-id="a6fd8-159">To rectify this:</span></span>

* <span data-ttu-id="a6fd8-160">Sprawdź, czy aplikacja jest uruchomiona w środowisku produkcyjnym podczas określania, które nazwy zestawów mają być używane.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-160">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="a6fd8-161">Zapisz nazwy zestawów o zmienionej nazwie w osobnym pliku i odczytaj z tego pliku, aby określić nazwę zestawu, który ma być używany w `LazyLoadAssemblyService` `OnNavigateAsync` metodach i.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-161">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="a6fd8-162">Pełny przykład</span><span class="sxs-lookup"><span data-stu-id="a6fd8-162">Complete example</span></span>

<span data-ttu-id="a6fd8-163">Poniższy kompletny `Router` składnik pokazuje ładowania zestawu, `GrantImaharaRobotControls.dll` gdy użytkownik nawiguje do `/robot` .</span><span class="sxs-lookup"><span data-stu-id="a6fd8-163">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="a6fd8-164">Podczas przechodzenia strony do użytkownika zostanie wyświetlony komunikat z stylem.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-164">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="a6fd8-165">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="a6fd8-165">Troubleshoot</span></span>

* <span data-ttu-id="a6fd8-166">W przypadku wystąpienia nieoczekiwanego renderowania (na przykład składnika z poprzedniej nawigacji jest renderowany) Upewnij się, że kod zgłasza, czy ustawiono token anulowania.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-166">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="a6fd8-167">Jeśli zestawy są nadal ładowane podczas uruchamiania aplikacji, sprawdź, czy zestaw jest oznaczony jako opóźniony załadowany w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="a6fd8-167">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6fd8-168">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a6fd8-168">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
