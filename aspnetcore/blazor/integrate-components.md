---
title: Integrowanie składników ASP.NET Core Razor z aplikacjami Razor Pages i MVC
author: guardrex
description: Dowiedz się więcej na temat scenariuszy powiązań danych dla składników Blazor i elementów dom w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 4e2103b7e8b65478808093d7a31e8cfe29b04984
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558921"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="5b3ab-103">Integrowanie składników ASP.NET Core Razor z aplikacjami Razor Pages i MVC</span><span class="sxs-lookup"><span data-stu-id="5b3ab-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="5b3ab-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5b3ab-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5b3ab-105">Składniki Razor można zintegrować z aplikacjami Razor Pages i MVC.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="5b3ab-106">Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="5b3ab-107">Po [przygotowaniu aplikacji](#prepare-the-app)należy użyć wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="5b3ab-108">Składniki &ndash; routingu dla składników, które są bezpośrednio trasowane z żądań użytkowników.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="5b3ab-109">Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej [`@page`](xref:mvc/views/razor#page) przeglądarce dla składnika z dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="5b3ab-110">Używanie składników rutowanych w aplikacji Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5b3ab-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="5b3ab-111">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="5b3ab-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="5b3ab-112">[Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view) &ndash; składników, które nie są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="5b3ab-113">Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5b3ab-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="5b3ab-114">Przygotowywanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="5b3ab-114">Prepare the app</span></span>

<span data-ttu-id="5b3ab-115">Istniejąca aplikacja Razor Pages lub MVC może zintegrować składniki Razor ze stronami i widokami:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="5b3ab-116">W pliku układu aplikacji (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="5b3ab-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="5b3ab-117">Dodaj następujący `<base>` tag do `<head>` elementu:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="5b3ab-118">Wartość ( *Ścieżka podstawowa aplikacji*) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego (`/`). `href`</span><span class="sxs-lookup"><span data-stu-id="5b3ab-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="5b3ab-119">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:host-and-deploy/blazor/index#app-base-path> artykułu.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="5b3ab-120">Plik *_Layout. cshtml* znajduje się w folderze *Pages/shared* w aplikacji Razor Pages lub *widokach/folderze udostępnionym* w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="5b3ab-121">Dodaj `<script>` tag dla skryptu *blazor. Server. js* bezpośrednio przed tagiem zamykającym `</body>` :</span><span class="sxs-lookup"><span data-stu-id="5b3ab-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="5b3ab-122">Struktura dodaje skrypt *blazor. Server. js* do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="5b3ab-123">Nie trzeba ręcznie dodawać skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="5b3ab-124">Dodaj plik *_Imports. Razor* do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw `MyAppNamespace`, do przestrzeni nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="5b3ab-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="5b3ab-125">W `Startup.ConfigureServices`programie Zarejestruj usługę serwera Blazor:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="5b3ab-126">W `Startup.Configure`programie Dodaj punkt końcowy centrum Blazor do `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="5b3ab-127">Integruj składniki na dowolną stronę lub widok.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-127">Integrate components into any page or view.</span></span> <span data-ttu-id="5b3ab-128">Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="5b3ab-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="5b3ab-129">Używanie składników rutowanych w aplikacji Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5b3ab-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="5b3ab-130">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="5b3ab-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5b3ab-131">Aby obsługiwać Routing składników Razor w aplikacjach Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="5b3ab-132">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="5b3ab-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5b3ab-133">Dodaj plik *App. Razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="5b3ab-134">Dodaj plik *_Host. cshtml* do folderu *stron* o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5b3ab-135">Składniki używają udostępnionego pliku *_Layout. cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="5b3ab-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, `App` czy składnik:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5b3ab-137">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5b3ab-138">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5b3ab-139">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="5b3ab-139">Render Mode</span></span> | <span data-ttu-id="5b3ab-140">Opis</span><span class="sxs-lookup"><span data-stu-id="5b3ab-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5b3ab-141">Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla aplikacji serwera Blazor.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5b3ab-142">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5b3ab-143">Renderuje znacznik dla aplikacji serwera Blazor.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5b3ab-144">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5b3ab-145">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5b3ab-146">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5b3ab-147">Aby uzyskać więcej informacji na temat pomocnika tagów składnika <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5b3ab-148">Dodaj trasę o niskim priorytecie dla strony *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="5b3ab-149">Dodaj składniki routingu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-149">Add routable components to the app.</span></span> <span data-ttu-id="5b3ab-150">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5b3ab-151">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="5b3ab-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="5b3ab-152">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="5b3ab-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="5b3ab-153">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="5b3ab-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5b3ab-154">Aby zapewnić obsługę routingu składników Razor w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="5b3ab-155">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="5b3ab-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5b3ab-156">Dodaj plik *App. Razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="5b3ab-157">Dodaj plik *_Host. cshtml* do folderu *widoki/główne* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5b3ab-158">Składniki używają udostępnionego pliku *_Layout. cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="5b3ab-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, `App` czy składnik:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5b3ab-160">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5b3ab-161">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5b3ab-162">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="5b3ab-162">Render Mode</span></span> | <span data-ttu-id="5b3ab-163">Opis</span><span class="sxs-lookup"><span data-stu-id="5b3ab-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5b3ab-164">Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5b3ab-165">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5b3ab-166">Renderuje znacznik dla aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5b3ab-167">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5b3ab-168">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5b3ab-169">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5b3ab-170">Aby uzyskać więcej informacji na temat pomocnika tagów składnika <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5b3ab-171">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="5b3ab-172">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="5b3ab-173">Utwórz folder *strony* i Dodaj składniki do obsługi routingu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="5b3ab-174">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5b3ab-175">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="5b3ab-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="5b3ab-176">Renderuj składniki ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="5b3ab-176">Render components from a page or view</span></span>

<span data-ttu-id="5b3ab-177">*Ta sekcja dotyczy dodawania składników do stron lub widoków, w których składniki nie są bezpośrednio trasowane z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="5b3ab-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="5b3ab-178">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5b3ab-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="5b3ab-179">Renderuj składniki interaktywne ze stanem</span><span class="sxs-lookup"><span data-stu-id="5b3ab-179">Render stateful interactive components</span></span>

<span data-ttu-id="5b3ab-180">Można dodać składniki interaktywne ze stanem do strony lub widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="5b3ab-181">Gdy renderuje stronę lub widok:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-181">When the page or view renders:</span></span>

* <span data-ttu-id="5b3ab-182">Składnik jest wstępnie renderowany przy użyciu strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="5b3ab-183">Początkowy stan składnika używany na potrzeby renderowania wstępnego został utracony.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="5b3ab-184">Nowy stan składnika jest tworzony podczas ustanawiania SignalR połączenia.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="5b3ab-185">Następująca strona Razor renderuje `Counter` składnik:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="5b3ab-186">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="5b3ab-187">Renderuj nieinteraktywne składniki</span><span class="sxs-lookup"><span data-stu-id="5b3ab-187">Render noninteractive components</span></span>

<span data-ttu-id="5b3ab-188">Na poniższej stronie Razor `Counter` składnik jest statycznie renderowany z wartością początkową określoną przy użyciu formularza.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="5b3ab-189">Ze względu na to, że składnik jest renderowany statycznie, składnik nie jest interaktywny:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="5b3ab-190">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="5b3ab-191">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="5b3ab-191">Component namespaces</span></span>

<span data-ttu-id="5b3ab-192">W przypadku używania folderu niestandardowego do przechowywania składników aplikacji należy dodać przestrzeń nazw reprezentującą folder do strony/widoku lub pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5b3ab-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5b3ab-193">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5b3ab-193">In the following example:</span></span>

* <span data-ttu-id="5b3ab-194">Przejdź `MyAppNamespace` do przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="5b3ab-195">Jeśli folder o nazwie *Components* nie jest używany do przechowywania składników, `Components` przejdź do folderu, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="5b3ab-196">Plik *_ViewImports. cshtml* znajduje się w folderze *strony* aplikacji Razor Pages lub folderu *widoki* aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-196">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="5b3ab-197">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="5b3ab-197">For more information, see <xref:blazor/components#import-components>.</span></span>
