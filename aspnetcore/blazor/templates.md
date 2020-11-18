---
title: BlazorSzablony ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat Blazor szablonów aplikacji ASP.NET Core i Blazor struktury projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703725"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="55532-103">BlazorSzablony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55532-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="55532-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="55532-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="55532-105">BlazorStruktura zawiera szablony do tworzenia aplikacji dla każdego z Blazor modeli hostingu:</span><span class="sxs-lookup"><span data-stu-id="55532-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="55532-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="55532-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="55532-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="55532-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="55532-108">Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="55532-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="55532-109">Opcje szablonu są dostępne przez przekazanie `--help` opcji do [`dotnet new`](/dotnet/core/tools/dotnet-new) interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="55532-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="55532-110">Blazor Struktura projektu</span><span class="sxs-lookup"><span data-stu-id="55532-110">Blazor project structure</span></span>

<span data-ttu-id="55532-111">Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu projektu:</span><span class="sxs-lookup"><span data-stu-id="55532-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="55532-112">`Program.cs`: Punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="55532-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="55532-113">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="55532-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="55532-114">Host webassembly ( Blazor WebAssembly ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor WebAssembly szablonu ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="55532-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="55532-115">`App`Składnik jest głównym składnikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="55532-116">`App`Składnik jest określony jako `app` element Dom ( `<div id="app">Loading...</div>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("#app")` ).</span><span class="sxs-lookup"><span data-stu-id="55532-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="55532-117">[Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="55532-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="55532-118">`Program.cs`: Punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="55532-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="55532-119">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="55532-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="55532-120">Host webassembly ( Blazor WebAssembly ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor WebAssembly szablonu ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="55532-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="55532-121">`App`Składnik jest głównym składnikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="55532-122">`App`Składnik jest określony jako `app` element Dom ( `<app>Loading...</app>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="55532-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="55532-123">[Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="55532-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="55532-124">`Startup.cs` ( Blazor Server ): Zawiera logikę uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="55532-125">`Startup`Klasa definiuje dwie metody:</span><span class="sxs-lookup"><span data-stu-id="55532-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="55532-126">`ConfigureServices`: Służy do konfigurowania usług [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="55532-127">W obszarze Blazor Server aplikacje usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.</span><span class="sxs-lookup"><span data-stu-id="55532-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="55532-128">`Configure`: Konfiguruje potok obsługi żądania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="55532-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="55532-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="55532-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="55532-130">Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="55532-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji ( `Pages/_Host.cshtml` ) i włączenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="55532-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="55532-132">`wwwroot/index.html` ( Blazor WebAssembly ): Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="55532-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="55532-133">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="55532-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="55532-134">Strona określa, gdzie `App` jest renderowany składnik główny.</span><span class="sxs-lookup"><span data-stu-id="55532-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="55532-135">Składnik jest renderowany w lokalizacji `app` elementu Dom ( `<app>...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="55532-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="55532-136">`_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:</span><span class="sxs-lookup"><span data-stu-id="55532-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="55532-137">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="55532-138">Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="55532-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="55532-139">`App.razor`: Główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika.</span><span class="sxs-lookup"><span data-stu-id="55532-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="55532-140"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.</span><span class="sxs-lookup"><span data-stu-id="55532-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="55532-141">`Pages` folder: zawiera składniki/strony z obsługą routingu ( `.razor` ), które tworzą Blazor aplikację i Razor stronę główną Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="55532-142">Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="55532-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="55532-143">Szablon zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="55532-143">The template includes the following:</span></span>
  * <span data-ttu-id="55532-144">`_Host.cshtml` ( Blazor Server ): Strona główna aplikacji zaimplementowana jako Razor Stronic</span><span class="sxs-lookup"><span data-stu-id="55532-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="55532-145">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="55532-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="55532-146">`_framework/blazor.server.js`Plik JavaScript jest ładowany, który konfiguruje połączenie w czasie rzeczywistym SignalR między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="55532-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="55532-147">Strona hosta określa, gdzie `App` jest renderowany składnik główny ( `App.razor` ).</span><span class="sxs-lookup"><span data-stu-id="55532-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="55532-148">`Counter` składnik ( `Pages/Counter.razor` ): implementuje stronę licznika.</span><span class="sxs-lookup"><span data-stu-id="55532-148">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="55532-149">`Error` składnik ( `Error.razor` Blazor Server tylko aplikacja): renderowane, gdy wystąpił nieobsługiwany wyjątek w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-149">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="55532-150">`FetchData` składnik ( `Pages/FetchData.razor` ): implementuje stronę pobieranie danych.</span><span class="sxs-lookup"><span data-stu-id="55532-150">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="55532-151">`Index` składnik ( `Pages/Index.razor` ): implementuje stronę główną.</span><span class="sxs-lookup"><span data-stu-id="55532-151">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="55532-152">`Properties/launchSettings.json`: Zawiera [konfigurację środowiska deweloperskiego](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="55532-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="55532-153">`Shared` folder: zawiera inne składniki interfejsu użytkownika ( `.razor` ) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="55532-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="55532-154">`MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-154">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="55532-155">`MainLayout.razor.css`: Arkusz stylów dla układu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-155">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="55532-156">`NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="55532-156">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="55532-157">Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników.</span><span class="sxs-lookup"><span data-stu-id="55532-157">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="55532-158"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="55532-158">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="55532-159">`NavMenu.razor.css`: Arkusz stylów dla menu nawigacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-159">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="55532-160">`Shared` folder: zawiera inne składniki interfejsu użytkownika ( `.razor` ) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="55532-160">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="55532-161">`MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-161">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="55532-162">`NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="55532-162">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="55532-163">Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników.</span><span class="sxs-lookup"><span data-stu-id="55532-163">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="55532-164"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="55532-164">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="55532-165">`_Imports.razor`: Zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji ( `.razor` ), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="55532-165">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="55532-166">`Data` folder ( Blazor Server ): zawiera `WeatherForecast` klasę i implementację `WeatherForecastService` , która zapewnia przykładowe dane pogodowe do `FetchData` składnika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-166">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="55532-167">`wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-167">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="55532-168">`appsettings.json`: Zawiera [Ustawienia konfiguracji](xref:blazor/fundamentals/configuration) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="55532-168">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="55532-169">W Blazor WebAssembly aplikacji plik ustawień aplikacji znajduje się w `wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="55532-169">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="55532-170">W Blazor Server aplikacji plik ustawień aplikacji znajduje się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="55532-170">In a Blazor Server app, the app settings file is located at the project root.</span></span>
