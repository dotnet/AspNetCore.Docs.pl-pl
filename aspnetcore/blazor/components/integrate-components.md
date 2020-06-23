---
title: Integrowanie Razor składników ASP.NET Core ze Razor stronami i aplikacjami MVC
author: guardrex
description: Dowiedz się więcej na temat scenariuszy powiązań danych dla składników i elementów DOM w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 1c71067528fb34ab141bb1ee846716834204ee40
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242462"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="4fc16-103">Integrowanie Razor składników ASP.NET Core ze Razor stronami i aplikacjami MVC</span><span class="sxs-lookup"><span data-stu-id="4fc16-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="4fc16-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4fc16-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="4fc16-105">składniki można zintegrować ze Razor stronami i aplikacjami MVC.</span><span class="sxs-lookup"><span data-stu-id="4fc16-105"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="4fc16-106">Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="4fc16-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="4fc16-107">Po [przygotowaniu aplikacji](#prepare-the-app)należy użyć wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4fc16-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="4fc16-108">Składniki routingu: dla składników, które są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4fc16-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="4fc16-109">Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej przeglądarce dla składnika z [`@page`](xref:mvc/views/razor#page) dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="4fc16-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="4fc16-110">[Używanie składników rutowanych w Razor aplikacji stron](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="4fc16-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="4fc16-111">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="4fc16-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="4fc16-112">[Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view): dla składników, które nie są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4fc16-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="4fc16-113">Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4fc16-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="4fc16-114">Przygotowywanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="4fc16-114">Prepare the app</span></span>

<span data-ttu-id="4fc16-115">Istniejące Razor strony lub aplikacja MVC mogą integrować Razor składniki na stronach i widokach:</span><span class="sxs-lookup"><span data-stu-id="4fc16-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="4fc16-116">W pliku układu aplikacji ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="4fc16-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="4fc16-117">Dodaj następujący `<base>` tag do `<head>` elementu:</span><span class="sxs-lookup"><span data-stu-id="4fc16-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="4fc16-118">`href`Wartość ( *Ścieżka podstawowa aplikacji*) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="4fc16-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="4fc16-119">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:blazor/host-and-deploy/index#app-base-path> artykułu.</span><span class="sxs-lookup"><span data-stu-id="4fc16-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="4fc16-120">Plik znajduje się `_Layout.cshtml` w folderze *strony/udostępnione* w Razor aplikacji strony lub *widokach/folderze udostępnionym* w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="4fc16-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="4fc16-121">Dodaj `<script>` tag dla skryptu *blazor.server.js* bezpośrednio przed tagiem zamykającym `</body>` :</span><span class="sxs-lookup"><span data-stu-id="4fc16-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="4fc16-122">Struktura dodaje do aplikacji skrypt *blazor.server.js* .</span><span class="sxs-lookup"><span data-stu-id="4fc16-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="4fc16-123">Nie trzeba ręcznie dodawać skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="4fc16-124">Dodaj `_Imports.razor` plik do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw, `MyAppNamespace` do przestrzeni nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="4fc16-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="4fc16-125">W programie `Startup.ConfigureServices` zarejestruj Blazor usługę serwera:</span><span class="sxs-lookup"><span data-stu-id="4fc16-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="4fc16-126">W programie `Startup.Configure` Dodaj Blazor punkt końcowy centrum do `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="4fc16-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="4fc16-127">Integruj składniki na dowolną stronę lub widok.</span><span class="sxs-lookup"><span data-stu-id="4fc16-127">Integrate components into any page or view.</span></span> <span data-ttu-id="4fc16-128">Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="4fc16-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="4fc16-129">Używanie składników rutowanych w Razor aplikacji stron</span><span class="sxs-lookup"><span data-stu-id="4fc16-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="4fc16-130">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="4fc16-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="4fc16-131">Aby obsługiwać Razor składniki routingu w Razor aplikacjach stron:</span><span class="sxs-lookup"><span data-stu-id="4fc16-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="4fc16-132">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="4fc16-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="4fc16-133">Dodaj `App.razor` plik do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="4fc16-133">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="4fc16-134">Dodaj `_Host.cshtml` plik do `Pages` folderu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="4fc16-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="4fc16-135">Składniki używają udostępnionego `_Layout.cshtml` pliku do ich układu.</span><span class="sxs-lookup"><span data-stu-id="4fc16-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="4fc16-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy `App` składnik:</span><span class="sxs-lookup"><span data-stu-id="4fc16-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="4fc16-137">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="4fc16-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="4fc16-138">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4fc16-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="4fc16-139">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="4fc16-139">Render Mode</span></span> | <span data-ttu-id="4fc16-140">Opis</span><span class="sxs-lookup"><span data-stu-id="4fc16-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4fc16-141">Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="4fc16-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4fc16-142">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4fc16-143">Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="4fc16-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4fc16-144">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="4fc16-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="4fc16-145">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4fc16-146">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="4fc16-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="4fc16-147">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="4fc16-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="4fc16-148">Dodaj trasę o niskim priorytecie dla `_Host.cshtml` konfiguracji strony do punktu końcowego w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4fc16-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="4fc16-149">Dodaj składniki routingu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-149">Add routable components to the app.</span></span> <span data-ttu-id="4fc16-150">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4fc16-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="4fc16-151">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="4fc16-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="4fc16-152">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="4fc16-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="4fc16-153">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="4fc16-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="4fc16-154">Aby obsługiwać Razor składniki routingu w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="4fc16-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="4fc16-155">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="4fc16-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="4fc16-156">Dodaj `App.razor` plik do katalogu głównego projektu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="4fc16-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="4fc16-157">Dodaj `_Host.cshtml` plik do `Views/Home` folderu o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="4fc16-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="4fc16-158">Składniki używają udostępnionego `_Layout.cshtml` pliku do ich układu.</span><span class="sxs-lookup"><span data-stu-id="4fc16-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="4fc16-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy `App` składnik:</span><span class="sxs-lookup"><span data-stu-id="4fc16-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="4fc16-160">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="4fc16-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="4fc16-161">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4fc16-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="4fc16-162">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="4fc16-162">Render Mode</span></span> | <span data-ttu-id="4fc16-163">Opis</span><span class="sxs-lookup"><span data-stu-id="4fc16-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4fc16-164">Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="4fc16-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4fc16-165">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4fc16-166">Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="4fc16-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4fc16-167">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="4fc16-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="4fc16-168">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4fc16-169">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="4fc16-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="4fc16-170">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="4fc16-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="4fc16-171">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="4fc16-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="4fc16-172">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca `_Host.cshtml` Widok do konfiguracji punktu końcowego w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4fc16-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="4fc16-173">Utwórz `Pages` folder i Dodaj do aplikacji składniki obsługujące Routing.</span><span class="sxs-lookup"><span data-stu-id="4fc16-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="4fc16-174">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4fc16-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="4fc16-175">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="4fc16-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="4fc16-176">Renderuj składniki ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="4fc16-176">Render components from a page or view</span></span>

<span data-ttu-id="4fc16-177">*Ta sekcja dotyczy dodawania składników do stron lub widoków, w których składniki nie są bezpośrednio trasowane z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="4fc16-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="4fc16-178">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4fc16-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="4fc16-179">Renderuj składniki interaktywne ze stanem</span><span class="sxs-lookup"><span data-stu-id="4fc16-179">Render stateful interactive components</span></span>

<span data-ttu-id="4fc16-180">Składniki interaktywne można dodać do Razor strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="4fc16-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="4fc16-181">Gdy renderuje stronę lub widok:</span><span class="sxs-lookup"><span data-stu-id="4fc16-181">When the page or view renders:</span></span>

* <span data-ttu-id="4fc16-182">Składnik jest wstępnie renderowany przy użyciu strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="4fc16-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="4fc16-183">Początkowy stan składnika używany na potrzeby renderowania wstępnego został utracony.</span><span class="sxs-lookup"><span data-stu-id="4fc16-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="4fc16-184">Nowy stan składnika jest tworzony podczas SignalR ustanawiania połączenia.</span><span class="sxs-lookup"><span data-stu-id="4fc16-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="4fc16-185">Na poniższej Razor stronie jest renderowany `Counter` składnik:</span><span class="sxs-lookup"><span data-stu-id="4fc16-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="4fc16-186">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4fc16-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="4fc16-187">Renderuj nieinteraktywne składniki</span><span class="sxs-lookup"><span data-stu-id="4fc16-187">Render noninteractive components</span></span>

<span data-ttu-id="4fc16-188">Na poniższej Razor stronie `Counter` składnik jest renderowany statycznie z wartością początkową określoną przy użyciu formularza.</span><span class="sxs-lookup"><span data-stu-id="4fc16-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="4fc16-189">Ze względu na to, że składnik jest renderowany statycznie, składnik nie jest interaktywny:</span><span class="sxs-lookup"><span data-stu-id="4fc16-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="4fc16-190">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4fc16-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="4fc16-191">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="4fc16-191">Component namespaces</span></span>

<span data-ttu-id="4fc16-192">W przypadku używania folderu niestandardowego do przechowywania składników aplikacji należy dodać przestrzeń nazw reprezentującą folder do strony/widoku lub do `_ViewImports.cshtml` pliku.</span><span class="sxs-lookup"><span data-stu-id="4fc16-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="4fc16-193">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="4fc16-193">In the following example:</span></span>

* <span data-ttu-id="4fc16-194">Przejdź `MyAppNamespace` do przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4fc16-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="4fc16-195">Jeśli folder o nazwie *Components* nie jest używany do przechowywania składników, przejdź `Components` do folderu, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="4fc16-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="4fc16-196">Plik znajduje się `_ViewImports.cshtml` w `Pages` folderze Razor aplikacji Pages lub w `Views` folderze aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="4fc16-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="4fc16-197">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="4fc16-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
