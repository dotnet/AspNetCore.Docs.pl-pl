---
title: Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC
author: guardrex
description: Dowiedz się więcej o scenariuszach wiązania Blazor danych dla składników i elementów DOM w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218937"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="48bd2-103">Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC</span><span class="sxs-lookup"><span data-stu-id="48bd2-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="48bd2-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="48bd2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="48bd2-105">Komponenty brzytwy można zintegrować ze żyletką Pages i aplikacjami MVC.</span><span class="sxs-lookup"><span data-stu-id="48bd2-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="48bd2-106">Gdy strona lub widok jest renderowany, komponenty mogą być prerendered w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="48bd2-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="48bd2-107">Przygotowanie aplikacji do używania składników na stronach i w widokach</span><span class="sxs-lookup"><span data-stu-id="48bd2-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="48bd2-108">Istniejące strony Razor lub aplikacja MVC mogą integrować składniki Razor ze stronami i widokami:</span><span class="sxs-lookup"><span data-stu-id="48bd2-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="48bd2-109">W pliku układu aplikacji (*_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="48bd2-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="48bd2-110">Dodaj następujący `<base>` znacznik `<head>` do elementu:</span><span class="sxs-lookup"><span data-stu-id="48bd2-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="48bd2-111">Wartość `href` *(ścieżka podstawowa aplikacji)* w poprzednim przykładzie zakłada, że aplikacja znajduje`/`się przy ścieżce głównego adresu URL ( ).</span><span class="sxs-lookup"><span data-stu-id="48bd2-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="48bd2-112">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze <xref:host-and-deploy/blazor/index#app-base-path> wskazówkami w sekcji *Ścieżka podstawowa aplikacji* w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="48bd2-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="48bd2-113">Plik *_Layout.cshtml* znajduje się w folderze *Strony/Udostępnione* w aplikacji Razor Pages lub w folderze *Widoki/Udostępnione* w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="48bd2-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="48bd2-114">Dodaj `<script>` tag skryptu *blazor.server.js* bezpośrednio przed `</body>` tagiem zamykającym:</span><span class="sxs-lookup"><span data-stu-id="48bd2-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="48bd2-115">Struktura dodaje *skrypt blazor.server.js* do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48bd2-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="48bd2-116">Nie ma potrzeby ręcznego dodawania skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48bd2-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="48bd2-117">Dodaj plik *_Imports.razor* do folderu głównego projektu z następującą zawartością (zmień `MyAppNamespace`obszar nazw, do obszaru nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="48bd2-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="48bd2-118">W `Startup.ConfigureServices`, Blazor zarejestruj usługę Serwer:</span><span class="sxs-lookup"><span data-stu-id="48bd2-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="48bd2-119">W `Startup.Configure`programie Blazor dodaj punkt `app.UseEndpoints`końcowy centrum do:</span><span class="sxs-lookup"><span data-stu-id="48bd2-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="48bd2-120">Zintegruj komponenty z dowolną stroną lub widokiem.</span><span class="sxs-lookup"><span data-stu-id="48bd2-120">Integrate components into any page or view.</span></span> <span data-ttu-id="48bd2-121">Aby uzyskać więcej informacji, zobacz [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view) sekcji.</span><span class="sxs-lookup"><span data-stu-id="48bd2-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="48bd2-122">Używanie składników routable w aplikacji Razor Pages</span><span class="sxs-lookup"><span data-stu-id="48bd2-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="48bd2-123">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="48bd2-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="48bd2-124">Aby obsługiwać rutowalne składniki Razor w aplikacjach Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="48bd2-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="48bd2-125">Postępuj zgodnie ze wskazówkami w [sekcji Przygotowywanie aplikacji do używania składników w](#prepare-the-app-to-use-components-in-pages-and-views) sekcji strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="48bd2-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="48bd2-126">Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="48bd2-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="48bd2-127">Dodaj *plik _Host.cshtml* do folderu *Pages* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="48bd2-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="48bd2-128">Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="48bd2-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="48bd2-129">Dodaj trasę o niskim priorytecie dla strony *_Host.cshtml* do konfiguracji punktu końcowego w : `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="48bd2-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="48bd2-130">Dodaj składniki rutowalne do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48bd2-130">Add routable components to the app.</span></span> <span data-ttu-id="48bd2-131">Przykład:</span><span class="sxs-lookup"><span data-stu-id="48bd2-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="48bd2-132">Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="48bd2-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="48bd2-133">Używanie składników routingu w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="48bd2-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="48bd2-134">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="48bd2-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="48bd2-135">Aby obsługiwać rutowalne komponenty Razor w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="48bd2-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="48bd2-136">Postępuj zgodnie ze wskazówkami w [sekcji Przygotowywanie aplikacji do używania składników w](#prepare-the-app-to-use-components-in-pages-and-views) sekcji strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="48bd2-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="48bd2-137">Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="48bd2-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="48bd2-138">Dodaj plik *_Host.cshtml* do folderu *Widoki/Strona główna* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="48bd2-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="48bd2-139">Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="48bd2-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="48bd2-140">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="48bd2-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="48bd2-141">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok `Startup.Configure` *_Host.cshtml* do konfiguracji punktu końcowego w :</span><span class="sxs-lookup"><span data-stu-id="48bd2-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="48bd2-142">Utwórz folder *Strony* i dodaj do aplikacji składniki rutowalne.</span><span class="sxs-lookup"><span data-stu-id="48bd2-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="48bd2-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="48bd2-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="48bd2-144">Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="48bd2-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="48bd2-145">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="48bd2-145">Component namespaces</span></span>

<span data-ttu-id="48bd2-146">W przypadku przechowywania składników aplikacji przy użyciu folderu niestandardowego należy dodać obszar nazw reprezentujący folder do strony/widoku lub pliku *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="48bd2-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="48bd2-147">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="48bd2-147">In the following example:</span></span>

* <span data-ttu-id="48bd2-148">Zmień `MyAppNamespace` obszar nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48bd2-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="48bd2-149">Jeśli folder o nazwie *Składniki* nie jest używany `Components` do przechowywania składników, zmień folder, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="48bd2-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="48bd2-150">Plik *_ViewImports.cshtml* znajduje się w folderze *Strony* aplikacji Razor Pages lub w folderze *Widoki* aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="48bd2-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="48bd2-151">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="48bd2-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="48bd2-152">Renderowanie składników ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="48bd2-152">Render components from a page or view</span></span>

<span data-ttu-id="48bd2-153">*Ta sekcja dotyczy dodawania składników do stron lub widoków, gdzie składniki nie są bezpośrednio rutowalne z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="48bd2-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="48bd2-154">Aby renderować składnik ze strony lub widoku, użyj [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="48bd2-154">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="48bd2-155">Aby uzyskać więcej informacji na temat sposobu renderowania składników, stanu składnika i Pomocnika `Component` znaczników, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="48bd2-155">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
