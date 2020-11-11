---
title: :::no-loc(Blazor):::Szablony ASP.NET Core
author: guardrex
description: 'Dowiedz się więcej na temat :::no-loc(Blazor)::: szablonów aplikacji ASP.NET Core i :::no-loc(Blazor)::: struktury projektu.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/templates
ms.openlocfilehash: fc2e81cf130732d515fb871227031493e297cf9f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507775"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="0138f-103">:::no-loc(Blazor):::Szablony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0138f-103">ASP.NET Core :::no-loc(Blazor)::: templates</span></span>

<span data-ttu-id="0138f-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0138f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0138f-105">:::no-loc(Blazor):::Struktura zawiera szablony do tworzenia aplikacji dla każdego z :::no-loc(Blazor)::: modeli hostingu:</span><span class="sxs-lookup"><span data-stu-id="0138f-105">The :::no-loc(Blazor)::: framework provides templates to develop apps for each of the :::no-loc(Blazor)::: hosting models:</span></span>

* <span data-ttu-id="0138f-106">:::no-loc(Blazor WebAssembly)::: (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="0138f-106">:::no-loc(Blazor WebAssembly)::: (`blazorwasm`)</span></span>
* <span data-ttu-id="0138f-107">:::no-loc(Blazor Server)::: (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="0138f-107">:::no-loc(Blazor Server)::: (`blazorserver`)</span></span>

<span data-ttu-id="0138f-108">Aby uzyskać więcej informacji na temat :::no-loc(Blazor)::: modeli hostingu, zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0138f-108">For more information on :::no-loc(Blazor):::'s hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="0138f-109">Opcje szablonu są dostępne przez przekazanie `--help` opcji do [`dotnet new`](/dotnet/core/tools/dotnet-new) interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="0138f-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="0138f-110">:::no-loc(Blazor)::: Struktura projektu</span><span class="sxs-lookup"><span data-stu-id="0138f-110">:::no-loc(Blazor)::: project structure</span></span>

<span data-ttu-id="0138f-111">Następujące pliki i foldery tworzą :::no-loc(Blazor)::: aplikację wygenerowaną na podstawie :::no-loc(Blazor)::: szablonu projektu:</span><span class="sxs-lookup"><span data-stu-id="0138f-111">The following files and folders make up a :::no-loc(Blazor)::: app generated from a :::no-loc(Blazor)::: project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="0138f-112">`Program.cs`: Punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="0138f-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="0138f-113">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( :::no-loc(Blazor Server)::: )</span><span class="sxs-lookup"><span data-stu-id="0138f-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (:::no-loc(Blazor Server):::)</span></span>
  * <span data-ttu-id="0138f-114">Host webassembly ( :::no-loc(Blazor WebAssembly)::: ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie :::no-loc(Blazor WebAssembly)::: szablonu ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-114">WebAssembly host (:::no-loc(Blazor WebAssembly):::): The code in this file is unique to apps created from the :::no-loc(Blazor WebAssembly)::: template (`blazorwasm`).</span></span>
    * <span data-ttu-id="0138f-115">`App`Składnik jest głównym składnikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="0138f-116">`App`Składnik jest określony jako `app` element Dom ( `<div id="app">Loading...</div>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("#app")` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="0138f-117">[Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="0138f-118">`Program.cs`: Punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="0138f-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="0138f-119">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( :::no-loc(Blazor Server)::: )</span><span class="sxs-lookup"><span data-stu-id="0138f-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (:::no-loc(Blazor Server):::)</span></span>
  * <span data-ttu-id="0138f-120">Host webassembly ( :::no-loc(Blazor WebAssembly)::: ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie :::no-loc(Blazor WebAssembly)::: szablonu ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-120">WebAssembly host (:::no-loc(Blazor WebAssembly):::): The code in this file is unique to apps created from the :::no-loc(Blazor WebAssembly)::: template (`blazorwasm`).</span></span>
    * <span data-ttu-id="0138f-121">`App`Składnik jest głównym składnikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="0138f-122">`App`Składnik jest określony jako `app` element Dom ( `<app>Loading...</app>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="0138f-123">[Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="0138f-124">`Startup.cs` ( :::no-loc(Blazor Server)::: ): Zawiera logikę uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-124">`Startup.cs` (:::no-loc(Blazor Server):::): Contains the app's startup logic.</span></span> <span data-ttu-id="0138f-125">`Startup`Klasa definiuje dwie metody:</span><span class="sxs-lookup"><span data-stu-id="0138f-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="0138f-126">`ConfigureServices`: Służy do konfigurowania usług [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="0138f-127">W obszarze :::no-loc(Blazor Server)::: aplikacje usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSide:::no-loc(Blazor):::%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.</span><span class="sxs-lookup"><span data-stu-id="0138f-127">In :::no-loc(Blazor Server)::: apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSide:::no-loc(Blazor):::%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="0138f-128">`Configure`: Konfiguruje potok obsługi żądania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0138f-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="0138f-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="0138f-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="0138f-130">Połączenie jest tworzone przy użyciu [:::no-loc(SignalR):::](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-130">The connection is created with [:::no-loc(SignalR):::](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="0138f-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji ( `Pages/_Host.cshtml` ) i włączenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="0138f-132">`wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ): Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="0138f-132">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="0138f-133">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0138f-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="0138f-134">Strona określa, gdzie `App` jest renderowany składnik główny.</span><span class="sxs-lookup"><span data-stu-id="0138f-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="0138f-135">Składnik jest renderowany w lokalizacji `app` elementu Dom ( `<app>...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="0138f-136">`_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:</span><span class="sxs-lookup"><span data-stu-id="0138f-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="0138f-137">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="0138f-138">Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="0138f-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="0138f-139">`App.razor`: Główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika.</span><span class="sxs-lookup"><span data-stu-id="0138f-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="0138f-140"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.</span><span class="sxs-lookup"><span data-stu-id="0138f-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="0138f-141">`Pages` folder: zawiera składniki/strony z obsługą routingu ( `.razor` ), które tworzą :::no-loc(Blazor)::: aplikację i :::no-loc(Razor)::: stronę główną :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the :::no-loc(Blazor)::: app and the root :::no-loc(Razor)::: page of a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="0138f-142">Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="0138f-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0138f-143">Szablon zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="0138f-143">The template includes the following:</span></span>
  * <span data-ttu-id="0138f-144">`_Host.cshtml` ( :::no-loc(Blazor Server)::: ): Strona główna aplikacji zaimplementowana jako :::no-loc(Razor)::: Stronic</span><span class="sxs-lookup"><span data-stu-id="0138f-144">`_Host.cshtml` (:::no-loc(Blazor Server):::): The root page of the app implemented as a :::no-loc(Razor)::: Page:</span></span>
    * <span data-ttu-id="0138f-145">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="0138f-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="0138f-146">`_framework/blazor.server.js`Plik JavaScript jest ładowany, który konfiguruje połączenie w czasie rzeczywistym :::no-loc(SignalR)::: między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="0138f-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time :::no-loc(SignalR)::: connection between the browser and the server.</span></span>
    * <span data-ttu-id="0138f-147">Strona hosta określa, gdzie `App` jest renderowany składnik główny ( `App.razor` ).</span><span class="sxs-lookup"><span data-stu-id="0138f-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="0138f-148">`Counter` ( `Pages/Counter.razor` ): Implementuje stronę licznika.</span><span class="sxs-lookup"><span data-stu-id="0138f-148">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="0138f-149">`Error` ( `Error.razor` :::no-loc(Blazor Server)::: tylko aplikacja): renderowane, gdy wystąpił nieobsługiwany wyjątek w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-149">`Error` (`Error.razor`, :::no-loc(Blazor Server)::: app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="0138f-150">`FetchData` ( `Pages/FetchData.razor` ): Implementuje stronę pobieranie danych.</span><span class="sxs-lookup"><span data-stu-id="0138f-150">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="0138f-151">`Index` ( `Pages/Index.razor` ): Implementuje stronę główną.</span><span class="sxs-lookup"><span data-stu-id="0138f-151">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="0138f-152">`Properties/launchSettings.json`: Zawiera [konfigurację środowiska deweloperskiego](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="0138f-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="0138f-153">`Shared` folder: zawiera inne składniki interfejsu użytkownika ( `.razor` ) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="0138f-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="0138f-154">`MainLayout` ( `MainLayout.razor` ): [Składnik układu](xref:blazor/layouts)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-154">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="0138f-155">`NavMenu` ( `NavMenu.razor` ): Implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="0138f-155">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="0138f-156">Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych :::no-loc(Razor)::: składników.</span><span class="sxs-lookup"><span data-stu-id="0138f-156">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other :::no-loc(Razor)::: components.</span></span> <span data-ttu-id="0138f-157"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="0138f-157">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="0138f-158">`_Imports.razor`: Zawiera wspólne :::no-loc(Razor)::: dyrektywy do uwzględnienia w składnikach aplikacji ( `.razor` ), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="0138f-158">`_Imports.razor`: Includes common :::no-loc(Razor)::: directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="0138f-159">`Data` folder ( :::no-loc(Blazor Server)::: ): zawiera `WeatherForecast` klasę i implementację `WeatherForecastService` , która zapewnia przykładowe dane pogodowe do `FetchData` składnika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-159">`Data` folder (:::no-loc(Blazor Server):::): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="0138f-160">`wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-160">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="0138f-161">`:::no-loc(appsettings.json):::`: Zawiera [Ustawienia konfiguracji](xref:blazor/fundamentals/configuration) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0138f-161">`:::no-loc(appsettings.json):::`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="0138f-162">W :::no-loc(Blazor WebAssembly)::: aplikacji plik ustawień aplikacji znajduje się w `wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="0138f-162">In a :::no-loc(Blazor WebAssembly)::: app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="0138f-163">W :::no-loc(Blazor Server)::: aplikacji plik ustawień aplikacji znajduje się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="0138f-163">In a :::no-loc(Blazor Server)::: app, the app settings file is located at the project root.</span></span>
