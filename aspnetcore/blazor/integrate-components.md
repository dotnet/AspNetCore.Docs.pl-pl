---
title: Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC
author: guardrex
description: Dowiedz się więcej o scenariuszach wiązania Blazor danych dla składników i elementów DOM w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 6efa84c550a4605bde5e1f2bca4f2d1aa4a2667b
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123361"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="32778-103">Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC</span><span class="sxs-lookup"><span data-stu-id="32778-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="32778-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="32778-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="32778-105">Komponenty brzytwy można zintegrować ze żyletką Pages i aplikacjami MVC.</span><span class="sxs-lookup"><span data-stu-id="32778-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="32778-106">Gdy strona lub widok jest renderowany, komponenty mogą być prerendered w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="32778-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="32778-107">Przygotowanie aplikacji do używania składników na stronach i w widokach</span><span class="sxs-lookup"><span data-stu-id="32778-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="32778-108">Istniejące strony Razor lub aplikacja MVC mogą integrować składniki Razor ze stronami i widokami:</span><span class="sxs-lookup"><span data-stu-id="32778-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="32778-109">W pliku układu aplikacji (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="32778-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="32778-110">Dodaj następujący `<base>` znacznik `<head>` do elementu:</span><span class="sxs-lookup"><span data-stu-id="32778-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="32778-111">Wartość `href` *(ścieżka podstawowa aplikacji)* w poprzednim przykładzie zakłada, że aplikacja znajduje`/`się przy ścieżce głównego adresu URL ( ).</span><span class="sxs-lookup"><span data-stu-id="32778-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="32778-112">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze <xref:host-and-deploy/blazor/index#app-base-path> wskazówkami w sekcji *Ścieżka podstawowa aplikacji* w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="32778-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="32778-113">Plik *_Layout.cshtml* znajduje się w folderze *Strony/Udostępnione* w aplikacji Razor Pages lub w folderze *Widoki/Udostępnione* w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="32778-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="32778-114">Dodaj `<script>` tag skryptu *blazor.server.js* bezpośrednio przed `</body>` tagiem zamykającym:</span><span class="sxs-lookup"><span data-stu-id="32778-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="32778-115">Struktura dodaje *skrypt blazor.server.js* do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32778-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="32778-116">Nie ma potrzeby ręcznego dodawania skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32778-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="32778-117">Dodaj plik *_Imports.razor* do folderu głównego projektu z następującą zawartością (zmień `MyAppNamespace`obszar nazw, do obszaru nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="32778-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="32778-118">W `Startup.ConfigureServices`, zarejestruj usługę Serwera Blazor:</span><span class="sxs-lookup"><span data-stu-id="32778-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="32778-119">W `Startup.Configure`programie dodaj punkt końcowy `app.UseEndpoints`Blazor Hub do:</span><span class="sxs-lookup"><span data-stu-id="32778-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="32778-120">Zintegruj komponenty z dowolną stroną lub widokiem.</span><span class="sxs-lookup"><span data-stu-id="32778-120">Integrate components into any page or view.</span></span> <span data-ttu-id="32778-121">Aby uzyskać więcej informacji, zobacz [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view) sekcji.</span><span class="sxs-lookup"><span data-stu-id="32778-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="32778-122">Używanie składników routable w aplikacji Razor Pages</span><span class="sxs-lookup"><span data-stu-id="32778-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="32778-123">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="32778-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="32778-124">Aby obsługiwać rutowalne składniki Razor w aplikacjach Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="32778-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="32778-125">Postępuj zgodnie ze wskazówkami w [sekcji Przygotowywanie aplikacji do używania składników w](#prepare-the-app-to-use-components-in-pages-and-views) sekcji strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="32778-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="32778-126">Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="32778-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="32778-127">Dodaj *plik _Host.cshtml* do folderu *Pages* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="32778-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="32778-128">Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="32778-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="32778-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, `App` czy składnik:</span><span class="sxs-lookup"><span data-stu-id="32778-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="32778-130">Jest prerendered do strony.</span><span class="sxs-lookup"><span data-stu-id="32778-130">Is prerendered into the page.</span></span>
   * <span data-ttu-id="32778-131">Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="32778-131">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="32778-132">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="32778-132">Render Mode</span></span> | <span data-ttu-id="32778-133">Opis</span><span class="sxs-lookup"><span data-stu-id="32778-133">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="32778-134">Renderuje `App` składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="32778-134">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="32778-135">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32778-135">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="32778-136">Renderuje znacznik aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="32778-136">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="32778-137">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="32778-137">Output from the `App` component isn't included.</span></span> <span data-ttu-id="32778-138">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32778-138">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="32778-139">Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="32778-139">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="32778-140">Aby uzyskać więcej informacji na temat <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>pomocnika znacznika składnika, zobacz .</span><span class="sxs-lookup"><span data-stu-id="32778-140">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="32778-141">Dodaj trasę o niskim priorytecie dla strony *_Host.cshtml* do konfiguracji punktu końcowego w : `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="32778-141">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="32778-142">Dodaj składniki rutowalne do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32778-142">Add routable components to the app.</span></span> <span data-ttu-id="32778-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="32778-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="32778-144">Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="32778-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="32778-145">Używanie składników routingu w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="32778-145">Use routable components in an MVC app</span></span>

<span data-ttu-id="32778-146">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="32778-146">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="32778-147">Aby obsługiwać rutowalne komponenty Razor w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="32778-147">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="32778-148">Postępuj zgodnie ze wskazówkami w [sekcji Przygotowywanie aplikacji do używania składników w](#prepare-the-app-to-use-components-in-pages-and-views) sekcji strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="32778-148">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="32778-149">Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="32778-149">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="32778-150">Dodaj plik *_Host.cshtml* do folderu *Widoki/Strona główna* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="32778-150">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="32778-151">Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="32778-151">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="32778-152">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="32778-152">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="32778-153">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok `Startup.Configure` *_Host.cshtml* do konfiguracji punktu końcowego w :</span><span class="sxs-lookup"><span data-stu-id="32778-153">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="32778-154">Utwórz folder *Strony* i dodaj do aplikacji składniki rutowalne.</span><span class="sxs-lookup"><span data-stu-id="32778-154">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="32778-155">Przykład:</span><span class="sxs-lookup"><span data-stu-id="32778-155">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="32778-156">Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="32778-156">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="32778-157">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="32778-157">Component namespaces</span></span>

<span data-ttu-id="32778-158">W przypadku przechowywania składników aplikacji przy użyciu folderu niestandardowego należy dodać obszar nazw reprezentujący folder do strony/widoku lub pliku *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="32778-158">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="32778-159">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="32778-159">In the following example:</span></span>

* <span data-ttu-id="32778-160">Zmień `MyAppNamespace` obszar nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32778-160">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="32778-161">Jeśli folder o nazwie *Składniki* nie jest używany `Components` do przechowywania składników, zmień folder, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="32778-161">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="32778-162">Plik *_ViewImports.cshtml* znajduje się w folderze *Strony* aplikacji Razor Pages lub w folderze *Widoki* aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="32778-162">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="32778-163">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="32778-163">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="32778-164">Renderowanie składników ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="32778-164">Render components from a page or view</span></span>

<span data-ttu-id="32778-165">*Ta sekcja dotyczy dodawania składników do stron lub widoków, gdzie składniki nie są bezpośrednio rutowalne z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="32778-165">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="32778-166">Aby renderować składnik ze strony lub widoku, użyj [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="32778-166">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="32778-167">Aby uzyskać więcej informacji na temat sposobu renderowania składników, stanu składnika i Pomocnika `Component` znaczników, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="32778-167">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
