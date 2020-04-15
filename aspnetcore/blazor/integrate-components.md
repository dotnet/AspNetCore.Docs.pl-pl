---
title: Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC
author: guardrex
description: Dowiedz się więcej o scenariuszach wiązania Blazor danych dla składników i elementów DOM w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383967"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="79be3-103">Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC</span><span class="sxs-lookup"><span data-stu-id="79be3-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="79be3-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="79be3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="79be3-105">Komponenty brzytwy można zintegrować ze żyletką Pages i aplikacjami MVC.</span><span class="sxs-lookup"><span data-stu-id="79be3-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="79be3-106">Gdy strona lub widok jest renderowany, komponenty mogą być prerendered w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="79be3-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="79be3-107">Po [przygotowaniu aplikacji](#prepare-the-app)skorzystaj ze wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:</span><span class="sxs-lookup"><span data-stu-id="79be3-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="79be3-108">Składniki &ndash; routingu Dla składników, które są bezpośrednio rutowalne z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="79be3-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="79be3-109">Postępuj zgodnie z tymi wskazówkami, gdy użytkownicy powinni mieć [`@page`](xref:mvc/views/razor#page) możliwość złożenia żądania HTTP w przeglądarce dla składnika z dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="79be3-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="79be3-110">Używanie składników routable w aplikacji Razor Pages</span><span class="sxs-lookup"><span data-stu-id="79be3-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="79be3-111">Używanie składników routingu w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="79be3-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="79be3-112">[Renderowanie składników ze strony lub widoku](#render-components-from-a-page-or-view) &ndash; Dla składników, które nie są bezpośrednio rutowalne z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="79be3-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="79be3-113">Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadza składniki na istniejących stronach i widokach za pomocą [Pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="79be3-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="79be3-114">Przygotowywanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="79be3-114">Prepare the app</span></span>

<span data-ttu-id="79be3-115">Istniejące strony Razor lub aplikacja MVC mogą integrować składniki Razor ze stronami i widokami:</span><span class="sxs-lookup"><span data-stu-id="79be3-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="79be3-116">W pliku układu aplikacji (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="79be3-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="79be3-117">Dodaj następujący `<base>` znacznik `<head>` do elementu:</span><span class="sxs-lookup"><span data-stu-id="79be3-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="79be3-118">Wartość `href` *(ścieżka podstawowa aplikacji)* w poprzednim przykładzie zakłada, że aplikacja znajduje`/`się przy ścieżce głównego adresu URL ( ).</span><span class="sxs-lookup"><span data-stu-id="79be3-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="79be3-119">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze <xref:host-and-deploy/blazor/index#app-base-path> wskazówkami w sekcji *Ścieżka podstawowa aplikacji* w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="79be3-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="79be3-120">Plik *_Layout.cshtml* znajduje się w folderze *Strony/Udostępnione* w aplikacji Razor Pages lub w folderze *Widoki/Udostępnione* w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="79be3-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="79be3-121">Dodaj `<script>` tag skryptu *blazor.server.js* bezpośrednio przed `</body>` tagiem zamykającym:</span><span class="sxs-lookup"><span data-stu-id="79be3-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="79be3-122">Struktura dodaje *skrypt blazor.server.js* do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79be3-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="79be3-123">Nie ma potrzeby ręcznego dodawania skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79be3-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="79be3-124">Dodaj plik *_Imports.razor* do folderu głównego projektu z następującą zawartością (zmień `MyAppNamespace`obszar nazw, do obszaru nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="79be3-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="79be3-125">W `Startup.ConfigureServices`, zarejestruj usługę Serwera Blazor:</span><span class="sxs-lookup"><span data-stu-id="79be3-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="79be3-126">W `Startup.Configure`programie dodaj punkt końcowy `app.UseEndpoints`Blazor Hub do:</span><span class="sxs-lookup"><span data-stu-id="79be3-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="79be3-127">Zintegruj komponenty z dowolną stroną lub widokiem.</span><span class="sxs-lookup"><span data-stu-id="79be3-127">Integrate components into any page or view.</span></span> <span data-ttu-id="79be3-128">Aby uzyskać więcej informacji, zobacz [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view) sekcji.</span><span class="sxs-lookup"><span data-stu-id="79be3-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="79be3-129">Używanie składników routable w aplikacji Razor Pages</span><span class="sxs-lookup"><span data-stu-id="79be3-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="79be3-130">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="79be3-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="79be3-131">Aby obsługiwać rutowalne składniki Razor w aplikacjach Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="79be3-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="79be3-132">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji.](#prepare-the-app)</span><span class="sxs-lookup"><span data-stu-id="79be3-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="79be3-133">Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="79be3-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="79be3-134">Dodaj *plik _Host.cshtml* do folderu *Pages* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="79be3-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="79be3-135">Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="79be3-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="79be3-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, `App` czy składnik:</span><span class="sxs-lookup"><span data-stu-id="79be3-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="79be3-137">Jest prerendered do strony.</span><span class="sxs-lookup"><span data-stu-id="79be3-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="79be3-138">Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="79be3-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="79be3-139">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="79be3-139">Render Mode</span></span> | <span data-ttu-id="79be3-140">Opis</span><span class="sxs-lookup"><span data-stu-id="79be3-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="79be3-141">Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="79be3-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="79be3-142">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="79be3-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="79be3-143">Renderuje znacznik aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="79be3-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="79be3-144">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="79be3-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="79be3-145">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="79be3-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="79be3-146">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="79be3-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="79be3-147">Aby uzyskać więcej informacji na temat <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>pomocnika znacznika składnika, zobacz .</span><span class="sxs-lookup"><span data-stu-id="79be3-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="79be3-148">Dodaj trasę o niskim priorytecie dla strony *_Host.cshtml* do konfiguracji punktu końcowego w : `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="79be3-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="79be3-149">Dodaj składniki rutowalne do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79be3-149">Add routable components to the app.</span></span> <span data-ttu-id="79be3-150">Przykład:</span><span class="sxs-lookup"><span data-stu-id="79be3-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="79be3-151">Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="79be3-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="79be3-152">Używanie składników routingu w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="79be3-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="79be3-153">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="79be3-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="79be3-154">Aby obsługiwać rutowalne komponenty Razor w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="79be3-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="79be3-155">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji.](#prepare-the-app)</span><span class="sxs-lookup"><span data-stu-id="79be3-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="79be3-156">Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="79be3-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="79be3-157">Dodaj plik *_Host.cshtml* do folderu *Widoki/Strona główna* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="79be3-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="79be3-158">Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="79be3-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="79be3-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, `App` czy składnik:</span><span class="sxs-lookup"><span data-stu-id="79be3-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="79be3-160">Jest prerendered do strony.</span><span class="sxs-lookup"><span data-stu-id="79be3-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="79be3-161">Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="79be3-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="79be3-162">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="79be3-162">Render Mode</span></span> | <span data-ttu-id="79be3-163">Opis</span><span class="sxs-lookup"><span data-stu-id="79be3-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="79be3-164">Renderuje `App` składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="79be3-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="79be3-165">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79be3-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="79be3-166">Renderuje znacznik aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="79be3-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="79be3-167">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="79be3-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="79be3-168">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79be3-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="79be3-169">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="79be3-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="79be3-170">Aby uzyskać więcej informacji na temat <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>pomocnika znacznika składnika, zobacz .</span><span class="sxs-lookup"><span data-stu-id="79be3-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="79be3-171">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="79be3-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="79be3-172">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok `Startup.Configure` *_Host.cshtml* do konfiguracji punktu końcowego w :</span><span class="sxs-lookup"><span data-stu-id="79be3-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="79be3-173">Utwórz folder *Strony* i dodaj do aplikacji składniki rutowalne.</span><span class="sxs-lookup"><span data-stu-id="79be3-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="79be3-174">Przykład:</span><span class="sxs-lookup"><span data-stu-id="79be3-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="79be3-175">Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="79be3-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="79be3-176">Renderowanie składników ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="79be3-176">Render components from a page or view</span></span>

<span data-ttu-id="79be3-177">*Ta sekcja dotyczy dodawania składników do stron lub widoków, gdzie składniki nie są bezpośrednio rutowalne z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="79be3-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="79be3-178">Aby renderować składnik ze strony lub widoku, użyj [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="79be3-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="79be3-179">Aby uzyskać więcej informacji na temat sposobu renderowania składników, stanu składnika i Pomocnika `Component` znaczników, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="79be3-179">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a><span data-ttu-id="79be3-180">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="79be3-180">Component namespaces</span></span>

<span data-ttu-id="79be3-181">W przypadku przechowywania składników aplikacji przy użyciu folderu niestandardowego należy dodać obszar nazw reprezentujący folder do strony/widoku lub pliku *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="79be3-181">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="79be3-182">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="79be3-182">In the following example:</span></span>

* <span data-ttu-id="79be3-183">Zmień `MyAppNamespace` obszar nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79be3-183">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="79be3-184">Jeśli folder o nazwie *Składniki* nie jest używany `Components` do przechowywania składników, zmień folder, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="79be3-184">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="79be3-185">Plik *_ViewImports.cshtml* znajduje się w folderze *Strony* aplikacji Razor Pages lub w folderze *Widoki* aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="79be3-185">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="79be3-186">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="79be3-186">For more information, see <xref:blazor/components#import-components>.</span></span>
