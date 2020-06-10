---
title: BlazorSzablony ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat Blazor szablonów aplikacji ASP.NET Core i Blazor struktury projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: f582e8201a3393b848cf3f2c21ce3a7df5554100
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105535"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="47241-103">BlazorSzablony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47241-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="47241-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47241-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="47241-105">BlazorStruktura zawiera szablony do tworzenia aplikacji dla każdego z Blazor modeli hostingu:</span><span class="sxs-lookup"><span data-stu-id="47241-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="47241-106">Webassembly ( `blazorwasm` )</span><span class="sxs-lookup"><span data-stu-id="47241-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="47241-107">Serwer ( `blazorserver` )</span><span class="sxs-lookup"><span data-stu-id="47241-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="47241-108">Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="47241-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="47241-109">Instrukcje krok po kroku dotyczące tworzenia Blazor aplikacji na podstawie szablonu znajdują się w temacie <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="47241-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="47241-110">Opcje szablonu są dostępne przez przekazanie `--help` opcji do nowego interfejsu wiersza polecenia [dotnet](/dotnet/core/tools/dotnet-new) :</span><span class="sxs-lookup"><span data-stu-id="47241-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="47241-111">Struktura projektu</span><span class="sxs-lookup"><span data-stu-id="47241-111"> project structure</span></span>

<span data-ttu-id="47241-112">Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu:</span><span class="sxs-lookup"><span data-stu-id="47241-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="47241-113">*Program.cs*: punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="47241-113">*Program.cs*: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="47241-114">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor serwer)</span><span class="sxs-lookup"><span data-stu-id="47241-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="47241-115">Host webassembly ( Blazor webassembly): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor szablonu webassembly ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="47241-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="47241-116">`App`Składnik, który jest głównym składnikiem aplikacji, jest określony jako `app` element dom dla `Add` metody.</span><span class="sxs-lookup"><span data-stu-id="47241-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="47241-117">Usługi można skonfigurować przy użyciu `ConfigureServices` metody w konstruktorze hosta (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).</span><span class="sxs-lookup"><span data-stu-id="47241-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="47241-118">Konfigurację można dostarczyć za pośrednictwem konstruktora hosta ( `builder.Configuration` ).</span><span class="sxs-lookup"><span data-stu-id="47241-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="47241-119">*Startup.cs* ( Blazor serwer): zawiera logikę uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-119">*Startup.cs* (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="47241-120">`Startup`Klasa definiuje dwie metody:</span><span class="sxs-lookup"><span data-stu-id="47241-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="47241-121">`ConfigureServices`: Służy do konfigurowania usług [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="47241-122">W Blazor przypadku aplikacji serwerowych usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.</span><span class="sxs-lookup"><span data-stu-id="47241-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="47241-123">`Configure`: Konfiguruje potok obsługi żądania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="47241-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="47241-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="47241-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="47241-125">Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="47241-126">[MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji (*strony/_Host. cshtml*) i włączenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="47241-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="47241-127">*wwwroot/index.html* ( Blazor webassembly): Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="47241-127">*wwwroot/index.html* (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="47241-128">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="47241-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="47241-129">Strona określa, gdzie `App` jest renderowany składnik główny.</span><span class="sxs-lookup"><span data-stu-id="47241-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="47241-130">`App`Składnik (*App. Razor*) jest określony jako `app` element dom dla `AddComponent` metody w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="47241-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="47241-131">`_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:</span><span class="sxs-lookup"><span data-stu-id="47241-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="47241-132">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="47241-133">Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="47241-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="47241-134">*App. Razor*: główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika.</span><span class="sxs-lookup"><span data-stu-id="47241-134">*App.razor*: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="47241-135"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.</span><span class="sxs-lookup"><span data-stu-id="47241-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="47241-136">Folder *stron* : zawiera składniki/strony z obsługą routingu (*. Razor*), które tworzą Blazor aplikację i Razor stronę główną Blazor aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="47241-136">*Pages* folder: Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="47241-137">Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="47241-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="47241-138">Szablon zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="47241-138">The template includes the following:</span></span>
  * <span data-ttu-id="47241-139">*_Host. cshtml* ( Blazor serwer): Strona główna aplikacji zaimplementowana jako Razor Strona:</span><span class="sxs-lookup"><span data-stu-id="47241-139">*_Host.cshtml* (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="47241-140">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="47241-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="47241-141">`_framework/blazor.server.js`Plik JavaScript jest ładowany, który konfiguruje połączenie w czasie rzeczywistym SignalR między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="47241-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="47241-142">Strona hosta określa, gdzie `App` jest renderowany składnik główny (*App. Razor*).</span><span class="sxs-lookup"><span data-stu-id="47241-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="47241-143">`Counter`(*Counter. Razor*): implementuje stronę licznika.</span><span class="sxs-lookup"><span data-stu-id="47241-143">`Counter` (*Counter.razor*): Implements the Counter page.</span></span>
  * <span data-ttu-id="47241-144">`Error`(*Error. Razor*, Blazor Tylko aplikacja serwera): renderowane, gdy w aplikacji wystąpi nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="47241-144">`Error` (*Error.razor*, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="47241-145">`FetchData`(*FetchData. Razor*): implementuje stronę pobieranie danych.</span><span class="sxs-lookup"><span data-stu-id="47241-145">`FetchData` (*FetchData.razor*): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="47241-146">`Index`(*Index. Razor*): implementuje stronę główną.</span><span class="sxs-lookup"><span data-stu-id="47241-146">`Index` (*Index.razor*): Implements the Home page.</span></span>

* <span data-ttu-id="47241-147">Folder *udostępniony* : zawiera inne składniki interfejsu użytkownika (*. Razor*) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="47241-147">*Shared* folder: Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="47241-148">`MainLayout`(*MainLayout. Razor*): składnik układu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-148">`MainLayout` (*MainLayout.razor*): The app's layout component.</span></span>
  * <span data-ttu-id="47241-149">`NavMenu`(*NavMenu. Razor*): implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="47241-149">`NavMenu` (*NavMenu.razor*): Implements sidebar navigation.</span></span> <span data-ttu-id="47241-150">Zawiera [składnik NavLink](xref:blazor/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników.</span><span class="sxs-lookup"><span data-stu-id="47241-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="47241-151"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="47241-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="47241-152">*_Imports. Razor*: zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji (*. Razor*), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="47241-152">*_Imports.razor*: Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="47241-153">Folder *danych* ( Blazor serwer): zawiera `WeatherForecast` klasę i implementację `WeatherForecastService` , która zapewnia przykładowe dane pogodowe do `FetchData` składnika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-153">*Data* folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="47241-154">*wwwroot*: folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne statyczne zasoby aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-154">*wwwroot*: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="47241-155">*appSettings. JSON* ( Blazor serwer): ustawienia konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="47241-155">*appsettings.json* (Blazor Server): Configuration settings for the app.</span></span>
