---
title: Wyprerender i Zintegruj Razor składniki ASP.NET Core
author: guardrex
description: Informacje o Razor scenariuszach integracji składników dla Blazor aplikacji, w tym do prerenderowania Razor składników na serwerze.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506984"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="8eff4-103">Wyprerender i Zintegruj Razor składniki ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8eff4-103">Prerender and integrate ASP.NET Core Razor components</span></span>

<span data-ttu-id="8eff4-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8eff4-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8eff4-105">Razor składniki można zintegrować Razor ze stronami i aplikacjami MVC w hostowanym Blazor WebAssembly rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="8eff4-105">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="8eff4-106">Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="8eff4-107">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="8eff4-107">Configuration</span></span>

<span data-ttu-id="8eff4-108">Aby skonfigurować renderowanie w Blazor WebAssembly aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8eff4-108">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="8eff4-109">Hostowanie Blazor WebAssembly aplikacji w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8eff4-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="8eff4-110">Aplikację autonomiczną Blazor WebAssembly można dodać do rozwiązania ASP.NET Core lub można użyć hostowanej Blazor WebAssembly aplikacji utworzonej na podstawie Blazor szablonu hostowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="8eff4-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="8eff4-111">Usuń domyślny plik statyczny `wwwroot/index.html` z Blazor WebAssembly projektu klienta.</span><span class="sxs-lookup"><span data-stu-id="8eff4-111">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="8eff4-112">Usuń następujący wiersz w `Program.Main` projekcie klienta:</span><span class="sxs-lookup"><span data-stu-id="8eff4-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="8eff4-113">Dodaj `Pages/_Host.cshtml` plik do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="8eff4-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="8eff4-114">Plik można uzyskać `_Host.cshtml` z aplikacji utworzonej na podstawie Blazor Server szablonu za pomocą `dotnet new blazorserver -o BlazorServer` polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="8eff4-114">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="8eff4-115">Po umieszczeniu `Pages/_Host.cshtml` pliku w aplikacji serwera hostowanego Blazor WebAssembly rozwiązania wprowadź następujące zmiany w pliku:</span><span class="sxs-lookup"><span data-stu-id="8eff4-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="8eff4-116">Ustaw przestrzeń nazw na folder aplikacji serwera `Pages` (na przykład `@namespace BlazorHosted.Server.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="8eff4-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="8eff4-117">Ustaw [`@using`](xref:mvc/views/razor#using) dyrektywę dla projektu klienta (na przykład `@using BlazorHosted.Client` ).</span><span class="sxs-lookup"><span data-stu-id="8eff4-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="8eff4-118">Zaktualizuj linki arkusza stylów, aby wskazywały arkusz stylów aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="8eff4-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="8eff4-119">W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-119">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="8eff4-120">Zaktualizuj `render-mode` składnik [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) w celu wyrenderowania `App` składnika głównego:</span><span class="sxs-lookup"><span data-stu-id="8eff4-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="8eff4-121">Zaktualizuj Blazor Źródło skryptu, aby używało skryptu po stronie klienta Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="8eff4-121">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="8eff4-122">W `Startup.Configure` ( `Startup.cs` ) projektu serwera:</span><span class="sxs-lookup"><span data-stu-id="8eff4-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="8eff4-123">Zadzwoń do `UseDeveloperExceptionPage` programu App Builder w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8eff4-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="8eff4-124">Zadzwoń do `UseBlazorFrameworkFiles` programu App Builder.</span><span class="sxs-lookup"><span data-stu-id="8eff4-124">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="8eff4-125">Zmień wartość Fallback ze `index.html` strony ( `endpoints.MapFallbackToFile("index.html");` ) na `_Host.cshtml` stronę.</span><span class="sxs-lookup"><span data-stu-id="8eff4-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="8eff4-126">Renderuj składniki na stronie lub widoku za pomocą pomocnika tagów składnika</span><span class="sxs-lookup"><span data-stu-id="8eff4-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="8eff4-127">Pomocnik tagu składnika obsługuje dwa tryby renderowania na potrzeby renderowania składnika z Blazor WebAssembly aplikacji na stronie lub widoku:</span><span class="sxs-lookup"><span data-stu-id="8eff4-127">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="8eff4-128">`WebAssembly`: Renderuje znacznik dla Blazor WebAssembly aplikacji w celu uwzględnienia składnika interaktywnego, gdy jest ładowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="8eff4-128">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="8eff4-129">Składnik nie jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="8eff4-129">The component isn't prerendered.</span></span> <span data-ttu-id="8eff4-130">Ta opcja ułatwia renderowanie różnych Blazor WebAssembly składników na różnych stronach.</span><span class="sxs-lookup"><span data-stu-id="8eff4-130">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="8eff4-131">`WebAssemblyPrerendered`: Program wstępnie renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor WebAssembly aplikacji w celu późniejszego użycia, aby składnik był interaktywny po załadowaniu go do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="8eff4-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="8eff4-132">Na poniższym Razor przykładzie strony `Counter` składnik jest renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-132">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="8eff4-133">Aby składnik był interaktywny, Blazor WebAssembly skrypt zostanie uwzględniony w [sekcji renderowania](xref:mvc/views/layout#sections)strony.</span><span class="sxs-lookup"><span data-stu-id="8eff4-133">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="8eff4-134">Aby uniknąć używania pełnej przestrzeni nazw dla `Counter` składnika z pomocnikiem tagów składnika ( `{APP ASSEMBLY}.Pages.Counter` ), Dodaj [`@using`](xref:mvc/views/razor#using) dyrektywę dla `Pages` przestrzeni nazw aplikacji klienta.</span><span class="sxs-lookup"><span data-stu-id="8eff4-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="8eff4-135">W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-135">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="8eff4-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="8eff4-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="8eff4-137">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="8eff4-138">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8eff4-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="8eff4-139">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, w tym przekazywania parametrów i <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguracji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="8eff4-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="8eff4-140">Poprzedni przykład wymaga, aby układ aplikacji serwera ( `_Layout.cshtml` ) zawierał [sekcję renderowania](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) dla skryptu wewnątrz tagu zamykającego `</body>` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="8eff4-141">Plik znajduje się `_Layout.cshtml` w `Pages/Shared` folderze w Razor aplikacji lub `Views/Shared` folderze stron w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="8eff4-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="8eff4-142">Jeśli aplikacja powinna również stylować składniki przy użyciu stylów w Blazor WebAssembly aplikacji, należy uwzględnić w pliku style aplikacji `_Layout.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="8eff4-142">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="8eff4-143">W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-143">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="8eff4-144">Renderuj składniki na stronie lub widoku za pomocą selektora CSS</span><span class="sxs-lookup"><span data-stu-id="8eff4-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="8eff4-145">Dodaj główne składniki do projektu *klienta* w `Program.Main` ( `Program.cs` ).</span><span class="sxs-lookup"><span data-stu-id="8eff4-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="8eff4-146">W poniższym przykładzie `Counter` składnik jest zadeklarowany jako składnik główny z selektorem CSS, który wybiera element z `id` pasującymi `my-counter` .</span><span class="sxs-lookup"><span data-stu-id="8eff4-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="8eff4-147">W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-147">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="8eff4-148">Na poniższym Razor przykładzie strony `Counter` składnik jest renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-148">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="8eff4-149">Aby składnik był interaktywny, Blazor WebAssembly skrypt zostanie uwzględniony w [sekcji renderowania](xref:mvc/views/layout#sections)strony:</span><span class="sxs-lookup"><span data-stu-id="8eff4-149">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="8eff4-150">Poprzedni przykład wymaga, aby układ aplikacji serwera ( `_Layout.cshtml` ) zawierał [sekcję renderowania](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) dla skryptu wewnątrz tagu zamykającego `</body>` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="8eff4-151">Plik znajduje się `_Layout.cshtml` w `Pages/Shared` folderze w Razor aplikacji lub `Views/Shared` folderze stron w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="8eff4-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="8eff4-152">Jeśli aplikacja powinna również stylować składniki przy użyciu stylów w Blazor WebAssembly aplikacji, należy uwzględnić w pliku style aplikacji `_Layout.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="8eff4-152">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="8eff4-153">W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-153">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8eff4-154">Integrowanie Razor składników na Razor stronach i aplikacjach MVC w hostowanym Blazor WebAssembly rozwiązaniu jest obsługiwane w ASP.NET Core w programie .NET 5 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="8eff4-154">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="8eff4-155">Wybierz platformę .NET 5 lub nowszą wersję tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="8eff4-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="8eff4-156">Razor składniki można zintegrować Razor ze stronami i aplikacjami MVC w Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8eff4-156">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="8eff4-157">Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="8eff4-158">Po [skonfigurowaniu aplikacji](#configuration)Użyj wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8eff4-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="8eff4-159">Składniki routingu: dla składników, które są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8eff4-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="8eff4-160">Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej przeglądarce dla składnika z [`@page`](xref:mvc/views/razor#page) dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="8eff4-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="8eff4-161">Używanie składników rutowanych w Razor aplikacji stron</span><span class="sxs-lookup"><span data-stu-id="8eff4-161">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="8eff4-162">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="8eff4-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="8eff4-163">[Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view): dla składników, które nie są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8eff4-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="8eff4-164">Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="8eff4-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="8eff4-165">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="8eff4-165">Configuration</span></span>

<span data-ttu-id="8eff4-166">Istniejące Razor strony lub aplikacja MVC mogą integrować Razor składniki na stronach i widokach:</span><span class="sxs-lookup"><span data-stu-id="8eff4-166">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="8eff4-167">W pliku układu aplikacji ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="8eff4-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="8eff4-168">Dodaj następujący `<base>` tag do `<head>` elementu:</span><span class="sxs-lookup"><span data-stu-id="8eff4-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="8eff4-169">`href`Wartość ( *Ścieżka podstawowa aplikacji*) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="8eff4-169">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="8eff4-170">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:blazor/host-and-deploy/index#app-base-path> artykułu.</span><span class="sxs-lookup"><span data-stu-id="8eff4-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="8eff4-171">Plik znajduje się `_Layout.cshtml` w `Pages/Shared` folderze w Razor aplikacji lub `Views/Shared` folderze stron w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="8eff4-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="8eff4-172">Dodaj `<script>` tag dla `blazor.server.js` skryptu bezpośrednio przed `Scripts` sekcją renderowania:</span><span class="sxs-lookup"><span data-stu-id="8eff4-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="8eff4-173">Struktura dodaje `blazor.server.js` skrypt do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8eff4-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="8eff4-174">Nie trzeba ręcznie dodawać skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8eff4-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="8eff4-175">Dodaj `_Imports.razor` plik do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw, `MyAppNamespace` do przestrzeni nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="8eff4-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="8eff4-176">W programie `Startup.ConfigureServices` zarejestruj Blazor Server usługę:</span><span class="sxs-lookup"><span data-stu-id="8eff4-176">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="8eff4-177">W programie `Startup.Configure` Dodaj Blazor punkt końcowy centrum do `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-177">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="8eff4-178">Integruj składniki na dowolną stronę lub widok.</span><span class="sxs-lookup"><span data-stu-id="8eff4-178">Integrate components into any page or view.</span></span> <span data-ttu-id="8eff4-179">Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="8eff4-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="8eff4-180">Używanie składników rutowanych w Razor aplikacji stron</span><span class="sxs-lookup"><span data-stu-id="8eff4-180">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="8eff4-181">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="8eff4-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="8eff4-182">Aby obsługiwać Razor składniki routingu w Razor aplikacjach stron:</span><span class="sxs-lookup"><span data-stu-id="8eff4-182">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="8eff4-183">Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="8eff4-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="8eff4-184">Dodaj `App.razor` plik do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="8eff4-184">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="8eff4-185">Dodaj `_Host.cshtml` plik do `Pages` folderu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="8eff4-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="8eff4-186">Składniki używają udostępnionego `_Layout.cshtml` pliku do ich układu.</span><span class="sxs-lookup"><span data-stu-id="8eff4-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="8eff4-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy `App` składnik:</span><span class="sxs-lookup"><span data-stu-id="8eff4-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="8eff4-188">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="8eff4-189">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8eff4-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="8eff4-190">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, w tym przekazywania parametrów i <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguracji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="8eff4-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="8eff4-191">Dodaj trasę o niskim priorytecie dla `_Host.cshtml` konfiguracji strony do punktu końcowego w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="8eff4-192">Dodaj składniki routingu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8eff4-192">Add routable components to the app.</span></span> <span data-ttu-id="8eff4-193">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="8eff4-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="8eff4-194">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="8eff4-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="8eff4-195">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="8eff4-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="8eff4-196">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="8eff4-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="8eff4-197">Aby obsługiwać Razor składniki routingu w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="8eff4-197">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="8eff4-198">Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="8eff4-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="8eff4-199">Dodaj `App.razor` plik do katalogu głównego projektu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="8eff4-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="8eff4-200">Dodaj `_Host.cshtml` plik do `Views/Home` folderu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="8eff4-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="8eff4-201">Składniki używają udostępnionego `_Layout.cshtml` pliku do ich układu.</span><span class="sxs-lookup"><span data-stu-id="8eff4-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="8eff4-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy `App` składnik:</span><span class="sxs-lookup"><span data-stu-id="8eff4-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="8eff4-203">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="8eff4-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="8eff4-204">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8eff4-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="8eff4-205">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, w tym przekazywania parametrów i <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguracji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="8eff4-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="8eff4-206">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="8eff4-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="8eff4-207">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca `_Host.cshtml` Widok do konfiguracji punktu końcowego w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8eff4-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="8eff4-208">Utwórz `Pages` folder i Dodaj do aplikacji składniki obsługujące Routing.</span><span class="sxs-lookup"><span data-stu-id="8eff4-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="8eff4-209">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="8eff4-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="8eff4-210">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="8eff4-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="8eff4-211">Renderuj składniki ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="8eff4-211">Render components from a page or view</span></span>

<span data-ttu-id="8eff4-212">*Ta sekcja dotyczy dodawania składników do stron lub widoków, w których składniki nie są bezpośrednio trasowane z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="8eff4-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="8eff4-213">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="8eff4-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="8eff4-214">Renderuj składniki interaktywne ze stanem</span><span class="sxs-lookup"><span data-stu-id="8eff4-214">Render stateful interactive components</span></span>

<span data-ttu-id="8eff4-215">Składniki interaktywne można dodać do Razor strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="8eff4-215">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="8eff4-216">Gdy renderuje stronę lub widok:</span><span class="sxs-lookup"><span data-stu-id="8eff4-216">When the page or view renders:</span></span>

* <span data-ttu-id="8eff4-217">Składnik jest wstępnie renderowany przy użyciu strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="8eff4-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="8eff4-218">Początkowy stan składnika używany na potrzeby renderowania wstępnego został utracony.</span><span class="sxs-lookup"><span data-stu-id="8eff4-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="8eff4-219">Nowy stan składnika jest tworzony podczas SignalR ustanawiania połączenia.</span><span class="sxs-lookup"><span data-stu-id="8eff4-219">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="8eff4-220">Na poniższej Razor stronie jest renderowany `Counter` składnik:</span><span class="sxs-lookup"><span data-stu-id="8eff4-220">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="8eff4-221">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="8eff4-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="8eff4-222">Renderuj nieinteraktywne składniki</span><span class="sxs-lookup"><span data-stu-id="8eff4-222">Render noninteractive components</span></span>

<span data-ttu-id="8eff4-223">Na poniższej Razor stronie `Counter` składnik jest renderowany statycznie z wartością początkową określoną przy użyciu formularza.</span><span class="sxs-lookup"><span data-stu-id="8eff4-223">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="8eff4-224">Ze względu na to, że składnik jest renderowany statycznie, składnik nie jest interaktywny:</span><span class="sxs-lookup"><span data-stu-id="8eff4-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="8eff4-225">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="8eff4-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="8eff4-226">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="8eff4-226">Component namespaces</span></span>

<span data-ttu-id="8eff4-227">W przypadku używania folderu niestandardowego do przechowywania składników aplikacji należy dodać przestrzeń nazw reprezentującą folder do strony/widoku lub do `_ViewImports.cshtml` pliku.</span><span class="sxs-lookup"><span data-stu-id="8eff4-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="8eff4-228">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8eff4-228">In the following example:</span></span>

* <span data-ttu-id="8eff4-229">Przejdź `MyAppNamespace` do przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8eff4-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="8eff4-230">Jeśli folder o nazwie `Components` nie jest używany do przechowywania składników, przejdź `Components` do folderu, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="8eff4-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="8eff4-231">Plik znajduje się `_ViewImports.cshtml` w `Pages` folderze Razor aplikacji Pages lub w `Views` folderze aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="8eff4-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="8eff4-232">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="8eff4-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
