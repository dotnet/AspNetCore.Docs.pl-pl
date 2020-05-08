---
title: Szablony Blazor ASP.NET Core
author: guardrex
description: Dowiedz się Blazor więcej na temat Blazor szablonów aplikacji ASP.NET Core i struktury projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967431"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="a7381-103">Szablony Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7381-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="a7381-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a7381-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a7381-105">Blazor Struktura zawiera szablony do tworzenia aplikacji dla każdego z modeli Blazor hostingu:</span><span class="sxs-lookup"><span data-stu-id="a7381-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="a7381-106">Webassembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="a7381-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="a7381-107">Serwer (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="a7381-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="a7381-108">Aby uzyskać więcej informacji Blazorna temat modeli hostingu, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="a7381-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="a7381-109">Instrukcje krok po kroku dotyczące tworzenia Blazor aplikacji na podstawie szablonu znajdują się w temacie. <xref:blazor/get-started></span><span class="sxs-lookup"><span data-stu-id="a7381-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="a7381-110">Opcje szablonu są dostępne przez przekazanie `--help` opcji do nowego interfejsu wiersza polecenia [dotnet](/dotnet/core/tools/dotnet-new) :</span><span class="sxs-lookup"><span data-stu-id="a7381-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="a7381-111">Struktura projektu</span><span class="sxs-lookup"><span data-stu-id="a7381-111"> project structure</span></span>

<span data-ttu-id="a7381-112">Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu:</span><span class="sxs-lookup"><span data-stu-id="a7381-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="a7381-113">*Program.cs* &ndash; punkt wejścia aplikacji, który konfiguruje:</span><span class="sxs-lookup"><span data-stu-id="a7381-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="a7381-114">[Host](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor serwer)</span><span class="sxs-lookup"><span data-stu-id="a7381-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="a7381-115">Host webassembly (Blazor webassembly) &ndash; kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor szablonu webassembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="a7381-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="a7381-116">`App` Składnik, który jest głównym składnikiem aplikacji, jest określony jako element `app` dom dla `Add` metody.</span><span class="sxs-lookup"><span data-stu-id="a7381-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="a7381-117">Usługi można skonfigurować przy użyciu `ConfigureServices` metody w konstruktorze hosta (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span><span class="sxs-lookup"><span data-stu-id="a7381-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="a7381-118">Konfigurację można dostarczyć za pośrednictwem konstruktora hosta (`builder.Configuration`).</span><span class="sxs-lookup"><span data-stu-id="a7381-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="a7381-119">*Startup.cs* (Blazor serwer) &ndash; zawiera logikę uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="a7381-120">`Startup` Klasa definiuje dwie metody:</span><span class="sxs-lookup"><span data-stu-id="a7381-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="a7381-121">`ConfigureServices`&ndash; Konfiguruje usługi dla [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="a7381-122">W Blazor przypadku aplikacji serwerowych usługi są dodawane przez <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>wywołanie i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.</span><span class="sxs-lookup"><span data-stu-id="a7381-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="a7381-123">`Configure`&ndash; Konfiguruje potok obsługi żądania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a7381-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="a7381-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="a7381-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="a7381-125">Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction), który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="a7381-126">[MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji (*strony/_Host. cshtml*) i włączenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="a7381-127">*wwwroot/index.html* (Blazor webassembly) &ndash; Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="a7381-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a7381-128">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7381-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a7381-129">Strona określa, gdzie jest renderowany `App` składnik główny.</span><span class="sxs-lookup"><span data-stu-id="a7381-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a7381-130">`App` Składnik (*App. Razor*) jest określony jako element `app` dom dla `AddComponent` metody w. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a7381-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="a7381-131">Plik `_framework/blazor.webassembly.js` JavaScript jest ładowany, co:</span><span class="sxs-lookup"><span data-stu-id="a7381-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="a7381-132">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="a7381-133">Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="a7381-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="a7381-134">*App. Razor* &ndash; główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika.</span><span class="sxs-lookup"><span data-stu-id="a7381-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a7381-135">`Router` Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.</span><span class="sxs-lookup"><span data-stu-id="a7381-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="a7381-136">*Pages* Folder &ndash; strony zawiera składniki/strony z obsługą routingu (*. Razor*), które Blazor tworzą aplikację i stronę Razor główną aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="a7381-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="a7381-137">Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="a7381-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a7381-138">Szablon zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="a7381-138">The template includes the following:</span></span>
  * <span data-ttu-id="a7381-139">*_Host. cshtml* (Blazor serwer) &ndash; Strona główna aplikacji zaimplementowana jako Razor Strona:</span><span class="sxs-lookup"><span data-stu-id="a7381-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="a7381-140">Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a7381-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="a7381-141">Plik `_framework/blazor.server.js` JavaScript jest ładowany, który konfiguruje SignalR połączenie w czasie rzeczywistym między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="a7381-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="a7381-142">Strona hosta określa, gdzie jest renderowany `App` składnik główny (*App. Razor*).</span><span class="sxs-lookup"><span data-stu-id="a7381-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="a7381-143">`Counter`(*Counter. Razor*) &ndash; implementuje stronę licznika.</span><span class="sxs-lookup"><span data-stu-id="a7381-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="a7381-144">`Error`(*Błąd. Razor*, Blazor tylko aplikacja serwera) &ndash; Renderowane, gdy w aplikacji wystąpi nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a7381-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="a7381-145">`FetchData`(*FetchData. Razor*) &ndash; implementuje stronę pobieranie danych.</span><span class="sxs-lookup"><span data-stu-id="a7381-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a7381-146">`Index`(*Index. Razor*) &ndash; zawiera implementację strony głównej.</span><span class="sxs-lookup"><span data-stu-id="a7381-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="a7381-147">*Shared* Folder &ndash; udostępniony zawiera inne składniki interfejsu użytkownika (*. Razor*) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="a7381-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="a7381-148">`MainLayout`(*MainLayout. Razor*) &ndash; składnik układu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="a7381-149">`NavMenu`(*NavMenu. Razor*) &ndash; implementuje nawigację po pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="a7381-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="a7381-150">Zawiera [składnik NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), który renderuje linki nawigacji do innych Razor składników.</span><span class="sxs-lookup"><span data-stu-id="a7381-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a7381-151">`NavLink` Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="a7381-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="a7381-152">*_Imports. Razor* &ndash; zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji (*. Razor*), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="a7381-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a7381-153">Folder *dane* (Blazor serwer) &ndash; zawiera `WeatherForecast` klasy i implementacji `WeatherForecastService` , które zawierają przykładowe dane pogodowe do `FetchData` składnika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="a7381-154">*wwwroot* &ndash; folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne statyczne zasoby aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="a7381-155">ustawienia konfiguracji pliku *appSettings. JSON* (Blazor serwera) &ndash; dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a7381-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
