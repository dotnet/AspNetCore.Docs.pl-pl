---
<span data-ttu-id="f6c92-101">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-103">'Blazor'</span></span>
- <span data-ttu-id="f6c92-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-104">'Identity'</span></span>
- <span data-ttu-id="f6c92-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-106">'Razor'</span></span>
- <span data-ttu-id="f6c92-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="f6c92-108">Integrowanie Razor składników ASP.NET Core ze Razor stronami i aplikacjami MVC</span><span class="sxs-lookup"><span data-stu-id="f6c92-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="f6c92-109">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f6c92-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="f6c92-110">składniki można zintegrować ze Razor stronami i aplikacjami MVC.</span><span class="sxs-lookup"><span data-stu-id="f6c92-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="f6c92-111">Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="f6c92-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="f6c92-112">Po [przygotowaniu aplikacji](#prepare-the-app)należy użyć wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f6c92-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="f6c92-113">Składniki routingu: dla składników, które są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f6c92-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="f6c92-114">Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej przeglądarce dla składnika z [`@page`](xref:mvc/views/razor#page) dyrektywą.</span><span class="sxs-lookup"><span data-stu-id="f6c92-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="f6c92-115">[Używanie składników rutowanych w Razor aplikacji stron](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="f6c92-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="f6c92-116">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="f6c92-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="f6c92-117">[Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view): dla składników, które nie są bezpośrednio trasowane z żądań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f6c92-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="f6c92-118">Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f6c92-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="f6c92-119">Przygotowywanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f6c92-119">Prepare the app</span></span>

<span data-ttu-id="f6c92-120">Istniejące Razor strony lub aplikacja MVC mogą integrować Razor składniki na stronach i widokach:</span><span class="sxs-lookup"><span data-stu-id="f6c92-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="f6c92-121">W pliku układu aplikacji (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f6c92-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="f6c92-122">Dodaj następujący `<base>` tag do `<head>` elementu:</span><span class="sxs-lookup"><span data-stu-id="f6c92-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="f6c92-123">`href`Wartość ( *Ścieżka podstawowa aplikacji*) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="f6c92-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="f6c92-124">Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:host-and-deploy/blazor/index#app-base-path> artykułu.</span><span class="sxs-lookup"><span data-stu-id="f6c92-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="f6c92-125">Plik *_Layout. cshtml* znajduje się w folderze *Pages/Shared* w Razor aplikacji strony lub *widokach/folderze udostępnionym* w aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="f6c92-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="f6c92-126">Dodaj `<script>` tag dla skryptu *blazor. Server. js* bezpośrednio przed tagiem zamykającym `</body>` :</span><span class="sxs-lookup"><span data-stu-id="f6c92-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="f6c92-127">Struktura dodaje skrypt *blazor. Server. js* do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="f6c92-128">Nie trzeba ręcznie dodawać skryptu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="f6c92-129">Dodaj plik *_Imports. Razor* do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw, `MyAppNamespace` do przestrzeni nazw aplikacji):</span><span class="sxs-lookup"><span data-stu-id="f6c92-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="f6c92-130">W programie `Startup.ConfigureServices` zarejestruj Blazor usługę serwera:</span><span class="sxs-lookup"><span data-stu-id="f6c92-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="f6c92-131">W programie `Startup.Configure` Dodaj Blazor punkt końcowy centrum do `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="f6c92-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="f6c92-132">Integruj składniki na dowolną stronę lub widok.</span><span class="sxs-lookup"><span data-stu-id="f6c92-132">Integrate components into any page or view.</span></span> <span data-ttu-id="f6c92-133">Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="f6c92-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="f6c92-134">Używanie składników rutowanych w Razor aplikacji stron</span><span class="sxs-lookup"><span data-stu-id="f6c92-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="f6c92-135">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="f6c92-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f6c92-136">Aby obsługiwać Razor składniki routingu w Razor aplikacjach stron:</span><span class="sxs-lookup"><span data-stu-id="f6c92-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="f6c92-137">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="f6c92-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="f6c92-138">Dodaj plik *App. Razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="f6c92-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="f6c92-139">Dodaj plik *_Host. cshtml* do folderu *stron* o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="f6c92-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f6c92-140">Składniki używają udostępnionego pliku *_Layout. cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="f6c92-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="f6c92-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy `App` składnik:</span><span class="sxs-lookup"><span data-stu-id="f6c92-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f6c92-142">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="f6c92-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f6c92-143">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f6c92-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="f6c92-144">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="f6c92-144">Render Mode</span></span> | <span data-ttu-id="f6c92-145">Opis</span><span class="sxs-lookup"><span data-stu-id="f6c92-145">Description</span></span> |
   | ---
<span data-ttu-id="f6c92-146">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-147">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-148">'Blazor'</span></span>
- <span data-ttu-id="f6c92-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-149">'Identity'</span></span>
- <span data-ttu-id="f6c92-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-151">'Razor'</span></span>
- <span data-ttu-id="f6c92-152">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-153">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-154">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-155">'Blazor'</span></span>
- <span data-ttu-id="f6c92-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-156">'Identity'</span></span>
- <span data-ttu-id="f6c92-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-158">'Razor'</span></span>
- <span data-ttu-id="f6c92-159">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-160">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-161">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-162">'Blazor'</span></span>
- <span data-ttu-id="f6c92-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-163">'Identity'</span></span>
- <span data-ttu-id="f6c92-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-165">'Razor'</span></span>
- <span data-ttu-id="f6c92-166">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-166">'SignalR' uid:</span></span> 

<span data-ttu-id="f6c92-167">------ | ---title: "Integruj Razor składniki ASP.NET Core Razor ze stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-168">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-169">'Blazor'</span></span>
- <span data-ttu-id="f6c92-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-170">'Identity'</span></span>
- <span data-ttu-id="f6c92-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-172">'Razor'</span></span>
- <span data-ttu-id="f6c92-173">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-174">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-175">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-176">'Blazor'</span></span>
- <span data-ttu-id="f6c92-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-177">'Identity'</span></span>
- <span data-ttu-id="f6c92-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-179">'Razor'</span></span>
- <span data-ttu-id="f6c92-180">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-181">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-182">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-183">'Blazor'</span></span>
- <span data-ttu-id="f6c92-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-184">'Identity'</span></span>
- <span data-ttu-id="f6c92-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-186">'Razor'</span></span>
- <span data-ttu-id="f6c92-187">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-187">'SignalR' uid:</span></span> 

<span data-ttu-id="f6c92-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="f6c92-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f6c92-189">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f6c92-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="f6c92-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f6c92-191">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="f6c92-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="f6c92-192">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f6c92-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="f6c92-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="f6c92-194">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="f6c92-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f6c92-195">Dodaj trasę o niskim priorytecie dla strony *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f6c92-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="f6c92-196">Dodaj składniki routingu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-196">Add routable components to the app.</span></span> <span data-ttu-id="f6c92-197">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f6c92-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="f6c92-198">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="f6c92-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="f6c92-199">Używanie składników rutowanych w aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="f6c92-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="f6c92-200">*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*</span><span class="sxs-lookup"><span data-stu-id="f6c92-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f6c92-201">Aby obsługiwać Razor składniki routingu w aplikacjach MVC:</span><span class="sxs-lookup"><span data-stu-id="f6c92-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="f6c92-202">Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="f6c92-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="f6c92-203">Dodaj plik *App. Razor* do katalogu głównego projektu z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="f6c92-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="f6c92-204">Dodaj plik *_Host. cshtml* do folderu *widoki/główne* z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="f6c92-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f6c92-205">Składniki używają udostępnionego pliku *_Layout. cshtml* dla ich układu.</span><span class="sxs-lookup"><span data-stu-id="f6c92-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="f6c92-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy `App` składnik:</span><span class="sxs-lookup"><span data-stu-id="f6c92-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f6c92-207">Jest wstępnie renderowany na stronie.</span><span class="sxs-lookup"><span data-stu-id="f6c92-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f6c92-208">Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f6c92-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="f6c92-209">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="f6c92-209">Render Mode</span></span> | <span data-ttu-id="f6c92-210">Opis</span><span class="sxs-lookup"><span data-stu-id="f6c92-210">Description</span></span> |
   | ---
<span data-ttu-id="f6c92-211">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-213">'Blazor'</span></span>
- <span data-ttu-id="f6c92-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-214">'Identity'</span></span>
- <span data-ttu-id="f6c92-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-216">'Razor'</span></span>
- <span data-ttu-id="f6c92-217">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-218">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-220">'Blazor'</span></span>
- <span data-ttu-id="f6c92-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-221">'Identity'</span></span>
- <span data-ttu-id="f6c92-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-223">'Razor'</span></span>
- <span data-ttu-id="f6c92-224">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-225">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-227">'Blazor'</span></span>
- <span data-ttu-id="f6c92-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-228">'Identity'</span></span>
- <span data-ttu-id="f6c92-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-230">'Razor'</span></span>
- <span data-ttu-id="f6c92-231">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-231">'SignalR' uid:</span></span> 

<span data-ttu-id="f6c92-232">------ | ---title: "Integruj Razor składniki ASP.NET Core Razor ze stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-234">'Blazor'</span></span>
- <span data-ttu-id="f6c92-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-235">'Identity'</span></span>
- <span data-ttu-id="f6c92-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-237">'Razor'</span></span>
- <span data-ttu-id="f6c92-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-239">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-241">'Blazor'</span></span>
- <span data-ttu-id="f6c92-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-242">'Identity'</span></span>
- <span data-ttu-id="f6c92-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-244">'Razor'</span></span>
- <span data-ttu-id="f6c92-245">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f6c92-246">title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".</span><span class="sxs-lookup"><span data-stu-id="f6c92-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="f6c92-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f6c92-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f6c92-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-248">'Blazor'</span></span>
- <span data-ttu-id="f6c92-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f6c92-249">'Identity'</span></span>
- <span data-ttu-id="f6c92-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f6c92-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="f6c92-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f6c92-251">'Razor'</span></span>
- <span data-ttu-id="f6c92-252">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="f6c92-252">'SignalR' uid:</span></span> 

<span data-ttu-id="f6c92-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="f6c92-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f6c92-254">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f6c92-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="f6c92-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f6c92-256">Dane wyjściowe ze `App` składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="f6c92-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="f6c92-257">Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="f6c92-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="f6c92-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="f6c92-259">Aby uzyskać więcej informacji na temat pomocnika tagów składnika, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="f6c92-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f6c92-260">Dodaj akcję do kontrolera macierzystego:</span><span class="sxs-lookup"><span data-stu-id="f6c92-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="f6c92-261">Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f6c92-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="f6c92-262">Utwórz folder *strony* i Dodaj składniki do obsługi routingu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="f6c92-263">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f6c92-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="f6c92-264">Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="f6c92-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="f6c92-265">Renderuj składniki ze strony lub widoku</span><span class="sxs-lookup"><span data-stu-id="f6c92-265">Render components from a page or view</span></span>

<span data-ttu-id="f6c92-266">*Ta sekcja dotyczy dodawania składników do stron lub widoków, w których składniki nie są bezpośrednio trasowane z żądań użytkownika.*</span><span class="sxs-lookup"><span data-stu-id="f6c92-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="f6c92-267">Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f6c92-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="f6c92-268">Renderuj składniki interaktywne ze stanem</span><span class="sxs-lookup"><span data-stu-id="f6c92-268">Render stateful interactive components</span></span>

<span data-ttu-id="f6c92-269">Składniki interaktywne można dodać do Razor strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="f6c92-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="f6c92-270">Gdy renderuje stronę lub widok:</span><span class="sxs-lookup"><span data-stu-id="f6c92-270">When the page or view renders:</span></span>

* <span data-ttu-id="f6c92-271">Składnik jest wstępnie renderowany przy użyciu strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="f6c92-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="f6c92-272">Początkowy stan składnika używany na potrzeby renderowania wstępnego został utracony.</span><span class="sxs-lookup"><span data-stu-id="f6c92-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="f6c92-273">Nowy stan składnika jest tworzony podczas SignalR ustanawiania połączenia.</span><span class="sxs-lookup"><span data-stu-id="f6c92-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="f6c92-274">Na poniższej Razor stronie jest renderowany `Counter` składnik:</span><span class="sxs-lookup"><span data-stu-id="f6c92-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="f6c92-275">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="f6c92-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="f6c92-276">Renderuj nieinteraktywne składniki</span><span class="sxs-lookup"><span data-stu-id="f6c92-276">Render noninteractive components</span></span>

<span data-ttu-id="f6c92-277">Na poniższej Razor stronie `Counter` składnik jest renderowany statycznie z wartością początkową określoną przy użyciu formularza.</span><span class="sxs-lookup"><span data-stu-id="f6c92-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="f6c92-278">Ze względu na to, że składnik jest renderowany statycznie, składnik nie jest interaktywny:</span><span class="sxs-lookup"><span data-stu-id="f6c92-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="f6c92-279">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="f6c92-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="f6c92-280">Przestrzenie nazw składników</span><span class="sxs-lookup"><span data-stu-id="f6c92-280">Component namespaces</span></span>

<span data-ttu-id="f6c92-281">W przypadku używania folderu niestandardowego do przechowywania składników aplikacji należy dodać przestrzeń nazw reprezentującą folder do strony/widoku lub pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f6c92-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f6c92-282">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f6c92-282">In the following example:</span></span>

* <span data-ttu-id="f6c92-283">Przejdź `MyAppNamespace` do przestrzeni nazw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f6c92-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="f6c92-284">Jeśli folder o nazwie *Components* nie jest używany do przechowywania składników, przejdź `Components` do folderu, w którym znajdują się składniki.</span><span class="sxs-lookup"><span data-stu-id="f6c92-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="f6c92-285">Plik *_ViewImports. cshtml* znajduje się w folderze *Pages* Razor aplikacji Pages lub folderze *widoki* aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="f6c92-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="f6c92-286">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="f6c92-286">For more information, see <xref:blazor/components#import-components>.</span></span>
