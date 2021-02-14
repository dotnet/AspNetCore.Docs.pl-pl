---
title: BlazorStruktura projektu ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat Blazor struktury projektu aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
ms.openlocfilehash: 94b5a3d8c0f5b94ecac32e6fc5f94efeb8337f37
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280351"
---
# <a name="aspnet-core-blazor-project-structure"></a>BlazorStruktura projektu ASP.NET Core

W tym artykule opisano pliki i foldery wchodzące w skład Blazor aplikacji wygenerowanej na podstawie Blazor szablonów projektu.

## Blazor WebAssembly

Blazor WebAssemblySzablon ( `blazorwasm` ) tworzy początkowe pliki i strukturę katalogów dla Blazor WebAssembly aplikacji. Aplikacja jest wypełniana kodem demonstracyjnym `FetchData` składnika, który ładuje dane ze statycznego zasobu, `weather.json` oraz interakcję użytkownika ze `Counter` składnikiem.

* `Pages` folder: zawiera składniki/strony z obsługą routingu ( `.razor` ), które tworzą Blazor aplikację. Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy. Szablon zawiera następujące składniki:
  * `Counter` składnik ( `Counter.razor` ): implementuje stronę licznika.
  * `FetchData` składnik ( `FetchData.razor` ): implementuje stronę pobieranie danych.
  * `Index` składnik ( `Index.razor` ): implementuje stronę główną.
  
* `Properties/launchSettings.json`: Zawiera [konfigurację środowiska deweloperskiego](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` folder: zawiera następujące składniki udostępnione i arkusze stylów:
  * `MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.
  * `MainLayout.razor.css`: Arkusz stylów dla układu głównego aplikacji.
  * `NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym. Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.
  * `NavMenu.razor.css`: Arkusz stylów dla menu nawigacji aplikacji.
  * `SurveyPrompt` składnik ( `SurveyPrompt.razor` ): Blazor składnik badania.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` folder: zawiera następujące składniki udostępnione:
  * `MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.
  * `NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym. Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.
  * `SurveyPrompt` składnik ( `SurveyPrompt.razor` ): Blazor składnik badania.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) aplikacji zawierający publiczne statyczne zasoby aplikacji, w tym `appsettings.json` pliki ustawień aplikacji środowiskowych dla [ustawień konfiguracji](xref:blazor/fundamentals/configuration). Strona `index.html` sieci Web jest stroną główną aplikacji zaimplementowaną jako strona HTML:
  * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
  * Strona określa, gdzie `App` jest renderowany składnik główny. Składnik jest renderowany w lokalizacji `div` elementu dom z `id` `app` ( `<div id="app">Loading...</div>` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) aplikacji zawierający publiczne statyczne zasoby aplikacji, w tym `appsettings.json` pliki ustawień aplikacji środowiskowych dla [ustawień konfiguracji](xref:blazor/fundamentals/configuration). Strona `index.html` sieci Web jest stroną główną aplikacji zaimplementowaną jako strona HTML:
  * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
  * Strona określa, gdzie `App` jest renderowany składnik główny. Składnik jest renderowany w lokalizacji `app` elementu Dom ( `<app>Loading...</app>` ).

::: moniker-end

> [!NOTE]
> Pliki JavaScript (JS) dodawane do `wwwroot/index.html` pliku powinny pojawić się przed tagiem zamykającym `</body>` . Kolejność ładowania niestandardowego kodu JS z plików JS jest ważna w niektórych scenariuszach. Na przykład upewnij się, że pliki JS z metodami międzyoperacyjnymi są uwzględniane przed Blazor plikami Framework js.

* `_Imports.razor`: Zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji ( `.razor` ), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.

* `App.razor`: Główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika. <xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: Punkt wejścia aplikacji, który konfiguruje hosta webassembly:
  
  * `App`Składnik jest głównym składnikiem aplikacji. `App`Składnik jest określony jako `div` element dom z `id` `app` ( `<div id="app">Loading...</div>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("#app")` ).
  * [Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: Punkt wejścia aplikacji, który konfiguruje hosta webassembly:

  * `App`Składnik jest głównym składnikiem aplikacji. `App`Składnik jest określony jako `app` element Dom ( `<app>Loading...</app>` w `wwwroot/index.html` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("app")` ).
  * [Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

## Blazor Server

Blazor ServerSzablon ( `blazorserver` ) tworzy początkowe pliki i strukturę katalogów dla Blazor Server aplikacji. Aplikacja jest wypełniana kodem demonstracyjnym `FetchData` składnika, który ładuje dane z zarejestrowanej usługi, `WeatherForecastService` i interakcję użytkownika ze `Counter` składnikiem.

* `Data` folder: zawiera `WeatherForecast` klasę i implementację `WeatherForecastService` , która zapewnia przykładowe dane pogodowe do `FetchData` składnika aplikacji.

* `Pages` folder: zawiera składniki/strony z obsługą routingu ( `.razor` ), które tworzą Blazor aplikację i Razor stronę główną Blazor Server aplikacji. Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy. Szablon zawiera następujące elementy:
  * `_Host.cshtml`: Strona główna aplikacji zaimplementowana jako Razor Strona:
    * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
    * Strona hosta określa, gdzie `App` jest renderowany składnik główny ( `App.razor` ).
  * `Counter` składnik ( `Counter.razor` ): implementuje stronę licznika.
  * `Error` składnik ( `Error.razor` ): renderowane, gdy wystąpił nieobsługiwany wyjątek w aplikacji.
  * `FetchData` składnik ( `FetchData.razor` ): implementuje stronę pobieranie danych.
  * `Index` składnik ( `Index.razor` ): implementuje stronę główną.

> [!NOTE]
> Pliki JavaScript (JS) dodawane do `Pages/_Host.cshtml` pliku powinny pojawić się przed tagiem zamykającym `</body>` . Kolejność ładowania niestandardowego kodu JS z plików JS jest ważna w niektórych scenariuszach. Na przykład upewnij się, że pliki JS z metodami międzyoperacyjnymi są uwzględniane przed Blazor plikami Framework js.

* `Properties/launchSettings.json`: Zawiera [konfigurację środowiska deweloperskiego](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` folder: zawiera następujące składniki udostępnione i arkusze stylów:
  * `MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.
  * `MainLayout.razor.css`: Arkusz stylów dla układu głównego aplikacji.
  * `NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym. Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.
  * `NavMenu.razor.css`: Arkusz stylów dla menu nawigacji aplikacji.
  * `SurveyPrompt` składnik ( `SurveyPrompt.razor` ): Blazor składnik badania.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` folder: zawiera następujące składniki udostępnione:
  * `MainLayout` składnik ( `MainLayout.razor` ): [składnik układu](xref:blazor/layouts)aplikacji.
  * `NavMenu` składnik ( `NavMenu.razor` ): implementuje nawigację po pasku bocznym. Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.
  * `SurveyPrompt` składnik ( `SurveyPrompt.razor` ): Blazor składnik badania.
  
::: moniker-end

* `wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.

* `_Imports.razor`: Zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji ( `.razor` ), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.

* `App.razor`: Główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika. <xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.

* `appsettings.json` Pliki ustawień aplikacji dla środowiska: Podaj [Ustawienia konfiguracji](xref:blazor/fundamentals/configuration) dla aplikacji.

* `Program.cs`: Punkt wejścia aplikacji, który konfiguruje [hosta](xref:fundamentals/host/generic-host)ASP.NET Core.

* `Startup.cs`: Zawiera logikę uruchamiania aplikacji. `Startup`Klasa definiuje dwie metody:

  * `ConfigureServices`: Służy do konfigurowania usług [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. Usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.
  * `Configure`: Konfiguruje potok obsługi żądania aplikacji:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką. Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji ( `Pages/_Host.cshtml` ) i włączenia nawigacji.
