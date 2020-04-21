---
title: szablony ASP.NET Blazor Core
author: guardrex
description: Dowiedz się więcej Blazor o szablonach Blazor aplikacji ASP.NET Core i strukturze projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661725"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="f10cc-103">szablony ASP.NET Blazor Core</span><span class="sxs-lookup"><span data-stu-id="f10cc-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="f10cc-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f10cc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f10cc-105">Struktura Blazor zawiera szablony do tworzenia aplikacji Blazor dla każdego z modeli hostingu:</span><span class="sxs-lookup"><span data-stu-id="f10cc-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="f10cc-106">WebAssembly`blazorwasm`( )</span><span class="sxs-lookup"><span data-stu-id="f10cc-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="f10cc-107">Serwer`blazorserver`( )</span><span class="sxs-lookup"><span data-stu-id="f10cc-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="f10cc-108">Aby uzyskać Blazorwięcej informacji na <xref:blazor/hosting-models>temat modeli hostingu, zobacz .</span><span class="sxs-lookup"><span data-stu-id="f10cc-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="f10cc-109">Aby uzyskać instrukcje krok po kroku Blazor dotyczące tworzenia aplikacji <xref:blazor/get-started>z szablonu, zobacz .</span><span class="sxs-lookup"><span data-stu-id="f10cc-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="f10cc-110">Opcje szablonu są `--help` dostępne, przekazując tę opcję do nowego polecenia [dotnet:](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="f10cc-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="f10cc-111">struktura projektu</span><span class="sxs-lookup"><span data-stu-id="f10cc-111"> project structure</span></span>

<span data-ttu-id="f10cc-112">Następujące pliki i foldery Blazor tworzą aplikację Blazor wygenerowaną na podstawie szablonu:</span><span class="sxs-lookup"><span data-stu-id="f10cc-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="f10cc-113">Program.cs Punkt wejścia aplikacji, który konfiguruje: *Program.cs* &ndash;</span><span class="sxs-lookup"><span data-stu-id="f10cc-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="f10cc-114">ASP.NET host [Core](xref:fundamentals/host/generic-host) Blazor (Serwer)</span><span class="sxs-lookup"><span data-stu-id="f10cc-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="f10cc-115">WebAssembly hostBlazor ( WebAssembly) &ndash; Kod w tym pliku Blazor jest unikatowy dla`blazorwasm`aplikacji utworzonych z szablonu WebAssembly ( ).</span><span class="sxs-lookup"><span data-stu-id="f10cc-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="f10cc-116">Składnik, `App` który jest głównym składnikiem aplikacji, jest `app` określony jako `Add` element DOM do metody.</span><span class="sxs-lookup"><span data-stu-id="f10cc-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="f10cc-117">Usługi można skonfigurować `ConfigureServices` za pomocą metody konstruktora `builder.Services.AddSingleton<IMyDependency, MyDependency>();`hosta (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="f10cc-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="f10cc-118">Konfiguracja może być dostarczana`builder.Configuration`za pośrednictwem konstruktora hosta ( ).</span><span class="sxs-lookup"><span data-stu-id="f10cc-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="f10cc-119">*Startup.cs* (Blazor Serwer) &ndash; Zawiera logikę uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="f10cc-120">Klasa `Startup` definiuje dwie metody:</span><span class="sxs-lookup"><span data-stu-id="f10cc-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="f10cc-121">`ConfigureServices`&ndash; Konfiguruje usługi [iniekcji zależności aplikacji (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="f10cc-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="f10cc-122">W Blazor aplikacjach serwera usługi <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>są dodawane przez wywołanie , a `WeatherForecastService` `FetchData` jest dodawany do kontenera usługi do użycia przez przykładowy składnik.</span><span class="sxs-lookup"><span data-stu-id="f10cc-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="f10cc-123">`Configure`&ndash; Konfiguruje potok obsługi żądań aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f10cc-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="f10cc-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="f10cc-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="f10cc-125">Połączenie jest tworzone [SignalR](xref:signalr/introduction)za pomocą programu , który jest platformą do dodawania funkcji sieci web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="f10cc-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji *(Pages/_Host.cshtml)* i włączenia nawigacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="f10cc-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; Strona główna aplikacji zaimplementowana jako strona HTML:</span><span class="sxs-lookup"><span data-stu-id="f10cc-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="f10cc-128">Gdy początkowo żądana jest dowolna strona aplikacji, ta strona jest renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f10cc-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="f10cc-129">Strona określa, gdzie `App` jest renderowany składnik główny.</span><span class="sxs-lookup"><span data-stu-id="f10cc-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="f10cc-130">Składnik `App` (*App.brzytwa*) `app` jest określony `AddComponent` jako `Startup.Configure`element DOM do metody w .</span><span class="sxs-lookup"><span data-stu-id="f10cc-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="f10cc-131">Załadowany `_framework/blazor.webassembly.js` jest plik JavaScript, który:</span><span class="sxs-lookup"><span data-stu-id="f10cc-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="f10cc-132">Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="f10cc-133">Inicjuje środowisko wykonawcze, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="f10cc-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="f10cc-134">*App.razor* &ndash; Główny składnik aplikacji, która konfiguruje routing <xref:Microsoft.AspNetCore.Components.Routing.Router> po stronie klienta przy użyciu składnika.</span><span class="sxs-lookup"><span data-stu-id="f10cc-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="f10cc-135">Składnik `Router` przechwytuje nawigację w przeglądarce i renderuje stronę, która pasuje do żądanego adresu.</span><span class="sxs-lookup"><span data-stu-id="f10cc-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="f10cc-136">*Folder* &ndash; Strony Zawiera rutowalne składniki/strony (*.brzytwa*), które Blazor tworzą Blazor aplikację i główną stronę Razor aplikacji Serwera.</span><span class="sxs-lookup"><span data-stu-id="f10cc-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="f10cc-137">Trasa dla każdej strony jest [`@page`](xref:mvc/views/razor#page) określona przy użyciu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="f10cc-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="f10cc-138">Szablon zawiera następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="f10cc-138">The template includes the following:</span></span>
  * <span data-ttu-id="f10cc-139">*_Host.cshtml* (Blazor Serwer) &ndash; Strona główna aplikacji zaimplementowana jako strona Razor:</span><span class="sxs-lookup"><span data-stu-id="f10cc-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="f10cc-140">Gdy początkowo żądana jest dowolna strona aplikacji, ta strona jest renderowana i zwracana w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f10cc-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="f10cc-141">Załadowany `_framework/blazor.server.js` jest plik JavaScript, który konfiguruje połączenie w czasie SignalR rzeczywistym między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="f10cc-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="f10cc-142">Strona Host określa, gdzie `App` jest renderowany składnik główny (*App.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="f10cc-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="f10cc-143">`Counter`*(Counter.brzytwa)* &ndash; Implementuje stronę Licznik.</span><span class="sxs-lookup"><span data-stu-id="f10cc-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="f10cc-144">`Error`*(Error.brzytwa*, Blazor Tylko aplikacja serwera) &ndash; Renderowane, gdy wystąpi nieobsługiowany wyjątek w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="f10cc-145">`FetchData`*(FetchData.brzytwa)* &ndash; Implementuje stronę pobierania danych.</span><span class="sxs-lookup"><span data-stu-id="f10cc-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="f10cc-146">`Index`(*Index.brzytwa*) &ndash; Implementuje stronę główną.</span><span class="sxs-lookup"><span data-stu-id="f10cc-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="f10cc-147">*Folder* &ndash; udostępniony Zawiera inne składniki interfejsu użytkownika (*.brzytwa*) używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="f10cc-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="f10cc-148">`MainLayout`(*MainLayout.brzytwa*) &ndash; Składnik układu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="f10cc-149">`NavMenu`*(NavMenu.brzytwa)* &ndash; Implementuje nawigację paska bocznego.</span><span class="sxs-lookup"><span data-stu-id="f10cc-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="f10cc-150">Zawiera [komponent NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), który renderuje łącza nawigacyjne do innych komponentów Razor.</span><span class="sxs-lookup"><span data-stu-id="f10cc-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="f10cc-151">Komponent `NavLink` automatycznie wskazuje wybrany stan podczas ładowania jego składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="f10cc-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="f10cc-152">*_Imports.brzytwa* &ndash; zawiera typowe dyrektywy Razor do uwzględnienia w składnikach aplikacji ( [`@using`](xref:mvc/views/razor#using) *.brzytwa*), takich jak dyrektywy dla obszarów nazw.</span><span class="sxs-lookup"><span data-stu-id="f10cc-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="f10cc-153">*Folder* danychBlazor (Serwer) &ndash; Zawiera `WeatherForecast` klasę `WeatherForecastService` i implementację, które zapewniają `FetchData` przykładowe dane pogodowe do składnika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="f10cc-154">*wwwroot* &ndash; Folder [Web Root](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="f10cc-155">*appsettings.json* Blazor ( &ndash; Serwer) Ustawienia konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f10cc-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
