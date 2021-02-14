---
title: Zestawy ładowania z opóźnieniem w ASP.NET Core Blazor WebAssembly
author: guardrex
description: Odkryj, jak ładować zestawy w aplikacjach ASP.NET Core Blazor WebAssembly .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: e8589a1e288c39b487673fafc04c59fa07916335
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280668"
---
# <a name="lazy-load-assemblies-in-aspnet-core-blazor-webassembly"></a><span data-ttu-id="91130-103">Zestawy ładowania z opóźnieniem w ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="91130-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="91130-104">Blazor WebAssembly wydajność uruchamiania aplikacji można ulepszyć, odwołując ładowanie niektórych zestawów aplikacji do momentu, gdy nie są wymagane, co jest nazywane *ładowaniem z opóźnieniem*.</span><span class="sxs-lookup"><span data-stu-id="91130-104">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="91130-105">Na przykład zestawy, które są używane tylko do renderowania pojedynczego składnika, można skonfigurować tak, aby ładowały się tylko wtedy, gdy użytkownik nawiguje do tego składnika.</span><span class="sxs-lookup"><span data-stu-id="91130-105">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="91130-106">Po załadowaniu zestawy są buforowane po stronie klienta i są dostępne dla wszystkich przyszłych nawigacji.</span><span class="sxs-lookup"><span data-stu-id="91130-106">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="91130-107">BlazorFunkcja ładowania z opóźnieniem umożliwia oznaczanie zestawów aplikacji na potrzeby ładowania z opóźnieniem, które ładuje zestawy podczas środowiska uruchomieniowego, gdy użytkownik przechodzi do określonej trasy.</span><span class="sxs-lookup"><span data-stu-id="91130-107">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="91130-108">Funkcja składa się z zmian w pliku projektu i zmian w routerze aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91130-108">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="91130-109">Ładowanie z opóźnieniem zestawu nie pozwala na korzystanie z Blazor Server aplikacji, ponieważ zestawy nie są pobierane do klienta w Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91130-109">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="91130-110">Plik projektu</span><span class="sxs-lookup"><span data-stu-id="91130-110">Project file</span></span>

<span data-ttu-id="91130-111">Oznacz zestawy do ładowania z opóźnieniem w pliku projektu aplikacji ( `.csproj` ) przy użyciu `BlazorWebAssemblyLazyLoad` elementu.</span><span class="sxs-lookup"><span data-stu-id="91130-111">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="91130-112">Użyj nazwy zestawu z `.dll` rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="91130-112">Use the assembly name with the `.dll` extension.</span></span> <span data-ttu-id="91130-113">BlazorStruktura uniemożliwia ładowanie zestawów określonych przez tę grupę elementów podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="91130-113">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="91130-114">Poniższy przykład oznacza duże niestandardowe zestawy ( `GrantImaharaRobotControls.dll` ) dla ładowania z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="91130-114">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="91130-115">Jeśli zestaw, który jest oznaczony do ładowania z opóźnieniem ma zależności, muszą także być oznaczone do ładowania opóźnionego w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="91130-115">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="91130-116">`Router` cm6long</span><span class="sxs-lookup"><span data-stu-id="91130-116">`Router` component</span></span>

<span data-ttu-id="91130-117">Blazor`Router`składnik wyznacza, który zestaw Blazor wyszukuje składniki routingu.</span><span class="sxs-lookup"><span data-stu-id="91130-117">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="91130-118">`Router`Składnik jest również odpowiedzialny za renderowanie składnika dla trasy, w której przechodzi użytkownik.</span><span class="sxs-lookup"><span data-stu-id="91130-118">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="91130-119">`Router`Składnik obsługuje `OnNavigateAsync` funkcję, która może być używana w połączeniu z opóźnionym ładowaniem.</span><span class="sxs-lookup"><span data-stu-id="91130-119">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="91130-120">W `Router` składniku aplikacji ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="91130-120">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="91130-121">Dodaj `OnNavigateAsync` wywołanie zwrotne.</span><span class="sxs-lookup"><span data-stu-id="91130-121">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="91130-122">`OnNavigateAsync`Procedura obsługi jest wywoływana, gdy użytkownik:</span><span class="sxs-lookup"><span data-stu-id="91130-122">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="91130-123">Po raz pierwszy odwiedza trasę, przechodząc do niego bezpośrednio z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="91130-123">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="91130-124">Przechodzi do nowej trasy przy użyciu linku lub <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wywołania.</span><span class="sxs-lookup"><span data-stu-id="91130-124">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="91130-125">Jeśli zestawy ładowane z opóźnieniem zawierają składniki routingu, należy dodać [listę](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (na przykład nazwa `lazyLoadedAssemblies` ) do składnika.</span><span class="sxs-lookup"><span data-stu-id="91130-125">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="91130-126">Zestawy są przenoszone z powrotem do <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> kolekcji w przypadku, gdy zestawy zawierają składniki routingu.</span><span class="sxs-lookup"><span data-stu-id="91130-126">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="91130-127">Struktura przeszukuje zestawy pod kątem tras i aktualizuje kolekcję tras w przypadku znalezienia nowych tras.</span><span class="sxs-lookup"><span data-stu-id="91130-127">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="91130-128">Jeśli `OnNavigateAsync` wywołanie zwrotne zgłasza nieobsłużony wyjątek, zostanie wywołany [ Blazor interfejs użytkownika błędu](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .</span><span class="sxs-lookup"><span data-stu-id="91130-128">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="91130-129">Logika ładowania zestawu w `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="91130-129">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="91130-130">`OnNavigateAsync` zawiera `NavigationContext` parametr, który zawiera informacje o bieżącym asynchronicznym zdarzeniu nawigacji, w tym ścieżkę docelową ( `Path` ) i tokenem anulowania ( `CancellationToken` ):</span><span class="sxs-lookup"><span data-stu-id="91130-130">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="91130-131">`Path`Właściwość jest ścieżką docelową użytkownika względem ścieżki podstawowej aplikacji, na przykład `/robot` .</span><span class="sxs-lookup"><span data-stu-id="91130-131">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="91130-132">`CancellationToken`Może służyć do obserwowania anulowania zadania asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="91130-132">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="91130-133">`OnNavigateAsync` automatycznie anuluje aktualnie uruchomione zadanie nawigacji, gdy użytkownik nawiguje do innej strony.</span><span class="sxs-lookup"><span data-stu-id="91130-133">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="91130-134">Wewnątrz `OnNavigateAsync` , należy wdrożyć logikę, aby określić zestawy do załadowania.</span><span class="sxs-lookup"><span data-stu-id="91130-134">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="91130-135">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="91130-135">Options include:</span></span>

* <span data-ttu-id="91130-136">Kontrole warunkowe wewnątrz `OnNavigateAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="91130-136">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="91130-137">Tabela wyszukiwania, w której są mapowane trasy do nazw zestawów, które zostały dodane do składnika lub zaimplementowane w [`@code`](xref:mvc/views/razor#code) bloku.</span><span class="sxs-lookup"><span data-stu-id="91130-137">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="91130-138">`LazyAssemblyLoader` to usługa singleton udostępniona przez platformę do ładowania zestawów.</span><span class="sxs-lookup"><span data-stu-id="91130-138">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="91130-139">Wsuń `LazyAssemblyLoader` do `Router` składnika:</span><span class="sxs-lookup"><span data-stu-id="91130-139">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="91130-140">`LazyAssemblyLoader`Zapewnia `LoadAssembliesAsync` metodę, która:</span><span class="sxs-lookup"><span data-stu-id="91130-140">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="91130-141">Używa programu JS Interop do pobierania zestawów za pośrednictwem połączenia sieciowego.</span><span class="sxs-lookup"><span data-stu-id="91130-141">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="91130-142">Ładuje zestawy do wykonywania w środowisku uruchomieniowym w zestawie webassembly w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="91130-142">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="91130-143">Implementacja wdrożenia z opóźnieniem platformy obsługuje ładowanie z opóźnieniem z użyciem prerenderowania w hostowanym Blazor rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="91130-143">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted Blazor solution.</span></span> <span data-ttu-id="91130-144">Podczas renderowania prerenderingu założono, że wszystkie zestawy, w tym zaznaczone dla ładowania z opóźnieniem, zostały załadowane.</span><span class="sxs-lookup"><span data-stu-id="91130-144">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="91130-145">Zarejestruj ręcznie `LazyAssemblyLoader` w metodzie projektu *serwera* `Startup.ConfigureServices` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="91130-145">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="91130-146">Interakcja użytkownika z `<Navigating>` zawartością</span><span class="sxs-lookup"><span data-stu-id="91130-146">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="91130-147">Podczas ładowania zestawów, które mogą potrwać kilka sekund, `Router` składnik może wskazywać użytkownikowi, że występuje przejście strony:</span><span class="sxs-lookup"><span data-stu-id="91130-147">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="91130-148">Dodaj [`@using`](xref:mvc/views/razor#using) dyrektywę dla <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="91130-148">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="91130-149">Dodaj `<Navigating>` do składnika tag zawierający znaczniki, które mają być wyświetlane podczas zdarzeń przejścia strony.</span><span class="sxs-lookup"><span data-stu-id="91130-149">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="91130-150">Obsługa anulowania w `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="91130-150">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="91130-151">`NavigationContext`Obiekt przesłany do `OnNavigateAsync` wywołania zwrotnego zawiera `CancellationToken` zestaw, który jest ustawiony w momencie wystąpienia nowego zdarzenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="91130-151">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="91130-152">`OnNavigateAsync`Wywołanie zwrotne musi zgłosić, gdy ten token anulowania jest ustawiony tak, aby uniknąć kontynuowania uruchamiania `OnNavigateAsync` wywołania zwrotnego w nieaktualnej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="91130-152">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="91130-153">Jeśli użytkownik przejdzie do trasy a, a następnie natychmiast do trasy B, aplikacja nie powinna nadal uruchamiać `OnNavigateAsync` wywołania zwrotnego dla trasy a:</span><span class="sxs-lookup"><span data-stu-id="91130-153">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> <span data-ttu-id="91130-154">Nie zgłaszaj, czy token anulowania w programie `NavigationContext` został anulowany może spowodować niezamierzone zachowanie, takie jak renderowanie składnika z poprzedniej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="91130-154">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="91130-155">`OnNavigateAsync` zdarzenia i pliki zestawu o zmienionej nazwie</span><span class="sxs-lookup"><span data-stu-id="91130-155">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="91130-156">Moduł ładujący zasoby opiera się na nazwach zestawów, które są zdefiniowane w `blazor.boot.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="91130-156">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="91130-157">W przypadku [zmiany nazwy zestawów](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)nazwy zestawów używane w `OnNavigateAsync` metodach i nazwach zestawów w `blazor.boot.json` pliku nie są zsynchronizowane.</span><span class="sxs-lookup"><span data-stu-id="91130-157">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="91130-158">Aby to skorygować:</span><span class="sxs-lookup"><span data-stu-id="91130-158">To rectify this:</span></span>

* <span data-ttu-id="91130-159">Sprawdź, czy aplikacja jest uruchomiona w środowisku produkcyjnym podczas określania, które nazwy zestawów mają być używane.</span><span class="sxs-lookup"><span data-stu-id="91130-159">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="91130-160">Zapisz nazwy zestawów o zmienionej nazwie w osobnym pliku i odczytaj z tego pliku, aby określić nazwę zestawu, który ma być używany w `LazyLoadAssemblyService` `OnNavigateAsync` metodach i.</span><span class="sxs-lookup"><span data-stu-id="91130-160">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="91130-161">Pełny przykład</span><span class="sxs-lookup"><span data-stu-id="91130-161">Complete example</span></span>

<span data-ttu-id="91130-162">Poniższy kompletny `Router` składnik pokazuje ładowania zestawu, `GrantImaharaRobotControls.dll` gdy użytkownik nawiguje do `/robot` .</span><span class="sxs-lookup"><span data-stu-id="91130-162">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="91130-163">Podczas przechodzenia strony do użytkownika zostanie wyświetlony komunikat z stylem.</span><span class="sxs-lookup"><span data-stu-id="91130-163">During page transitions, a styled message is displayed to the user.</span></span>

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

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a><span data-ttu-id="91130-164">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="91130-164">Troubleshoot</span></span>

* <span data-ttu-id="91130-165">W przypadku wystąpienia nieoczekiwanego renderowania (na przykład składnika z poprzedniej nawigacji jest renderowany) Upewnij się, że kod zgłasza, czy ustawiono token anulowania.</span><span class="sxs-lookup"><span data-stu-id="91130-165">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="91130-166">Jeśli zestawy są nadal ładowane podczas uruchamiania aplikacji, sprawdź, czy zestaw jest oznaczony jako opóźniony załadowany w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="91130-166">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91130-167">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="91130-167">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
