---
title: BlazorStruktura projektu ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat Blazor struktury projektu aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97513550"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="9976c-103">BlazorStruktura projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9976c-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="9976c-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9976c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9976c-105">Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu projektu:</span><span class="sxs-lookup"><span data-stu-id="9976c-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9976c-106">`Program.cs`: Punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="9976c-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="9976c-107">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="9976c-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="9976c-108">Host webassembly ( Blazor WebAssembly ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor WebAssembly szablonu ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="9976c-109">`App`Składnik jest głównym składnikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="9976c-110">`App`Składnik jest określony jako `div` element dom z `id` `app` ( `<div id="app">Loading...</div>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("#app")` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="9976c-111">[Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9976c-112">`Program.cs`: Punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="9976c-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="9976c-113">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="9976c-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="9976c-114">Host webassembly ( Blazor WebAssembly ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor WebAssembly szablonu ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="9976c-115">`App`Składnik jest głównym składnikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="9976c-116">`App`Składnik jest określony jako `app` element Dom ( `<app>Loading...</app>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="9976c-117">[Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="9976c-118">`Startup.cs` ( Blazor Server ): Zawiera logikę uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="9976c-119">`Startup`Klasa definiuje dwie metody:</span><span class="sxs-lookup"><span data-stu-id="9976c-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="9976c-120">`ConfigureServices`: Służy do konfigurowania usług [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="9976c-121">W obszarze Blazor Server aplikacje usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.</span><span class="sxs-lookup"><span data-stu-id="9976c-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="9976c-122">`Configure`: Konfiguruje potok obsługi żądania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9976c-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="9976c-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="9976c-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="9976c-124">Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="9976c-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji ( `Pages/_Host.cshtml` ) i włączenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9976c-126">`wwwroot/index.html` ( Blazor WebAssembly ): Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="9976c-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="9976c-127">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9976c-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="9976c-128">Strona określa, gdzie `App` jest renderowany składnik główny.</span><span class="sxs-lookup"><span data-stu-id="9976c-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="9976c-129">Składnik jest renderowany w lokalizacji `div` elementu dom z `id` `app` ( `<div id="app">Loading...</div>` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="9976c-130">`_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:</span><span class="sxs-lookup"><span data-stu-id="9976c-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="9976c-131">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="9976c-132">Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="9976c-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9976c-133">`wwwroot/index.html` ( Blazor WebAssembly ): Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="9976c-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="9976c-134">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9976c-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="9976c-135">Strona określa, gdzie `App` jest renderowany składnik główny.</span><span class="sxs-lookup"><span data-stu-id="9976c-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="9976c-136">Składnik jest renderowany w lokalizacji `app` elementu Dom ( `<app>Loading...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="9976c-137">`_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:</span><span class="sxs-lookup"><span data-stu-id="9976c-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="9976c-138">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="9976c-139">Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="9976c-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="9976c-140">`App.razor`: Główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika.</span><span class="sxs-lookup"><span data-stu-id="9976c-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="9976c-141"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.</span><span class="sxs-lookup"><span data-stu-id="9976c-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="9976c-142">`Pages` folder: zawiera składniki/strony z obsługą routingu ( `.razor` ), które tworzą Blazor aplikację i Razor stronę główną Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="9976c-143">Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="9976c-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="9976c-144">Szablon zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="9976c-144">The template includes the following:</span></span>
  * <span data-ttu-id="9976c-145">`_Host.cshtml` ( Blazor Server ): Strona główna aplikacji zaimplementowana jako Razor Stronic</span><span class="sxs-lookup"><span data-stu-id="9976c-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="9976c-146">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9976c-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="9976c-147">`_framework/blazor.server.js`Plik JavaScript jest ładowany, który konfiguruje połączenie w czasie rzeczywistym SignalR między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="9976c-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="9976c-148">Strona hosta określa, gdzie `App` jest renderowany składnik główny ( `App.razor` ).</span><span class="sxs-lookup"><span data-stu-id="9976c-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="9976c-149">`Counter` składnik ( `Pages/Counter.razor` ): implementuje stronę licznika.</span><span class="sxs-lookup"><span data-stu-id="9976c-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="9976c-150">`Error` składnik ( `Error.razor` Blazor Server tylko aplikacja): renderowane, gdy wystąpił nieobsługiwany wyjątek w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="9976c-151">`FetchData` składnik ( `Pages/FetchData.razor` ): implementuje stronę pobieranie danych.</span><span class="sxs-lookup"><span data-stu-id="9976c-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="9976c-152">`Index` składnik ( `Pages/Index.razor` ): implementuje stronę główną.</span><span class="sxs-lookup"><span data-stu-id="9976c-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="9976c-153">`Properties/launchSettings.json`: Zawiera [konfigurację środowiska deweloperskiego](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="9976c-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9976c-154">`Shared` folder: zawiera inne składniki interfejsu użytkownika ( `.razor` ) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="9976c-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="9976c-155">`MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="9976c-156">`MainLayout.razor.css`: Arkusz stylów dla układu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="9976c-157">`NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="9976c-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="9976c-158">Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników.</span><span class="sxs-lookup"><span data-stu-id="9976c-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="9976c-159"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="9976c-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="9976c-160">`NavMenu.razor.css`: Arkusz stylów dla menu nawigacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9976c-161">`Shared` folder: zawiera inne składniki interfejsu użytkownika ( `.razor` ) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="9976c-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="9976c-162">`MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="9976c-163">`NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="9976c-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="9976c-164">Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników.</span><span class="sxs-lookup"><span data-stu-id="9976c-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="9976c-165"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="9976c-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="9976c-166">`_Imports.razor`: Zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji ( `.razor` ), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="9976c-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="9976c-167">`Data` folder ( Blazor Server ): zawiera `WeatherForecast` klasę i implementację `WeatherForecastService` , która zapewnia przykładowe dane pogodowe do `FetchData` składnika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="9976c-168">`wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="9976c-169">`appsettings.json`: Zawiera [Ustawienia konfiguracji](xref:blazor/fundamentals/configuration) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9976c-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="9976c-170">W Blazor WebAssembly aplikacji plik ustawień aplikacji znajduje się w `wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="9976c-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="9976c-171">W Blazor Server aplikacji plik ustawień aplikacji znajduje się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="9976c-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
