---
title: BlazorSzablony ASP.NET Core
author: guardrex
description: Dowiedz się więcej na temat Blazor szablonów aplikacji ASP.NET Core i Blazor struktury projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 65d6a3156419b57eae6c7e41a9778fa25fd88f4f
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758531"
---
# <a name="aspnet-core-no-locblazor-templates"></a>BlazorSzablony ASP.NET Core

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

BlazorStruktura zawiera szablony do tworzenia aplikacji dla każdego z Blazor modeli hostingu:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .

Opcje szablonu są dostępne przez przekazanie `--help` opcji do [`dotnet new`](/dotnet/core/tools/dotnet-new) interfejsu wiersza polecenia:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>BlazorStruktura projektu

Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu projektu:

* `Program.cs`: Punkt wejścia aplikacji, który konfiguruje:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )
  * Host webassembly ( Blazor WebAssembly ): kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor WebAssembly szablonu ( `blazorwasm` ).
    * `App`Składnik jest głównym składnikiem aplikacji. `App`Składnik jest określony jako `app` element Dom ( `<app>...</app>` ) do kolekcji głównych składników ( `builder.RootComponents.Add<App>("app")` ).
    * [Usługi](xref:blazor/fundamentals/dependency-injection) są dodawane i konfigurowane (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

* `Startup.cs`( Blazor Server ): Zawiera logikę uruchamiania aplikacji. `Startup`Klasa definiuje dwie metody:

  * `ConfigureServices`: Służy do konfigurowania usług [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. W obszarze Blazor Server aplikacje usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.
  * `Configure`: Konfiguruje potok obsługi żądania aplikacji:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką. Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)jest wywoływana w celu skonfigurowania strony głównej aplikacji ( `Pages/_Host.cshtml` ) i włączenia nawigacji.

* `wwwroot/index.html`( Blazor WebAssembly ): Strona główna aplikacji zaimplementowana jako strona HTML:
  * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
  * Strona określa, gdzie `App` jest renderowany składnik główny. Składnik jest renderowany w lokalizacji `app` elementu Dom ( `<app>...</app>` ).
  * `_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:
    * Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.
    * Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.

* `App.razor`: Główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika. <xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.

* `Pages`folder: zawiera składniki/strony z obsługą routingu ( `.razor` ), które tworzą Blazor aplikację i Razor stronę główną Blazor Server aplikacji. Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy. Szablon zawiera następujące elementy:
  * `_Host.cshtml`( Blazor Server ): Strona główna aplikacji zaimplementowana jako Razor Stronic
    * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
    * `_framework/blazor.server.js`Plik JavaScript jest ładowany, który konfiguruje połączenie w czasie rzeczywistym SignalR między przeglądarką a serwerem.
    * Strona hosta określa, gdzie `App` jest renderowany składnik główny ( `App.razor` ).
  * `Counter`( `Pages/Counter.razor` ): Implementuje stronę licznika.
  * `Error`( `Error.razor` Blazor Server tylko aplikacja): renderowane, gdy wystąpił nieobsługiwany wyjątek w aplikacji.
  * `FetchData`( `Pages/FetchData.razor` ): Implementuje stronę pobieranie danych.
  * `Index`( `Pages/Index.razor` ): Implementuje stronę główną.
  
* `Properties/launchSettings.json`: Zawiera [konfigurację środowiska deweloperskiego](xref:fundamentals/environments#development-and-launchsettingsjson).

* `Shared`folder: zawiera inne składniki interfejsu użytkownika ( `.razor` ) używane przez aplikację:
  * `MainLayout`( `MainLayout.razor` ): [Składnik układu](xref:blazor/layouts)aplikacji.
  * `NavMenu`( `NavMenu.razor` ): Implementuje nawigację po pasku bocznym. Zawiera [ `NavLink` składnik](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.

* `_Imports.razor`: Zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji ( `.razor` ), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.

* `Data`folder ( Blazor Server ): zawiera `WeatherForecast` klasę i implementację `WeatherForecastService` , która zapewnia przykładowe dane pogodowe do `FetchData` składnika aplikacji.

* `wwwroot`: Folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.

* `appsettings.json`: Zawiera [Ustawienia konfiguracji](xref:blazor/fundamentals/configuration) aplikacji. W Blazor WebAssembly aplikacji plik ustawień aplikacji znajduje się w `wwwroot` folderze. W Blazor Server aplikacji plik ustawień aplikacji znajduje się w katalogu głównym projektu.
