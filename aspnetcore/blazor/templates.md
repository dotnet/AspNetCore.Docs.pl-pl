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
# <a name="aspnet-core-blazor-templates"></a>Szablony Blazor ASP.NET Core

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Struktura zawiera szablony do tworzenia aplikacji dla każdego z modeli Blazor hostingu:

* BlazorWebassembly (`blazorwasm`)
* BlazorSerwer (`blazorserver`)

Aby uzyskać więcej informacji Blazorna temat modeli hostingu, <xref:blazor/hosting-models>Zobacz.

Instrukcje krok po kroku dotyczące tworzenia Blazor aplikacji na podstawie szablonu znajdują się w temacie. <xref:blazor/get-started>

Opcje szablonu są dostępne przez przekazanie `--help` opcji do nowego interfejsu wiersza polecenia [dotnet](/dotnet/core/tools/dotnet-new) :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>BlazorStruktura projektu

Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu:

* *Program.cs* &ndash; punkt wejścia aplikacji, który konfiguruje:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor serwer)
  * Host webassembly (Blazor webassembly) &ndash; kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor szablonu webassembly (`blazorwasm`).
    * `App` Składnik, który jest głównym składnikiem aplikacji, jest określony jako element `app` dom dla `Add` metody.
    * Usługi można skonfigurować przy użyciu `ConfigureServices` metody w konstruktorze hosta (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Konfigurację można dostarczyć za pośrednictwem konstruktora hosta (`builder.Configuration`).

* *Startup.cs* (Blazor serwer) &ndash; zawiera logikę uruchamiania aplikacji. `Startup` Klasa definiuje dwie metody:

  * `ConfigureServices`&ndash; Konfiguruje usługi dla [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. W Blazor przypadku aplikacji serwerowych usługi są dodawane przez <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>wywołanie i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.
  * `Configure`&ndash; Konfiguruje potok obsługi żądania aplikacji:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką. Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction), który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji (*strony/_Host. cshtml*) i włączenia nawigacji.

* *wwwroot/index.html* (Blazor webassembly) &ndash; Strona główna aplikacji zaimplementowana jako strona HTML:
  * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
  * Strona określa, gdzie jest renderowany `App` składnik główny. `App` Składnik (*App. Razor*) jest określony jako element `app` dom dla `AddComponent` metody w. `Startup.Configure`
  * Plik `_framework/blazor.webassembly.js` JavaScript jest ładowany, co:
    * Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.
    * Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.

* *App. Razor* &ndash; główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika. `Router` Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.

* *Pages* Folder &ndash; strony zawiera składniki/strony z obsługą routingu (*. Razor*), które Blazor tworzą aplikację i stronę Razor główną aplikacji Blazor serwera. Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy. Szablon zawiera następujące elementy:
  * *_Host. cshtml* (Blazor serwer) &ndash; Strona główna aplikacji zaimplementowana jako Razor Strona:
    * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
    * Plik `_framework/blazor.server.js` JavaScript jest ładowany, który konfiguruje SignalR połączenie w czasie rzeczywistym między przeglądarką a serwerem.
    * Strona hosta określa, gdzie jest renderowany `App` składnik główny (*App. Razor*).
  * `Counter`(*Counter. Razor*) &ndash; implementuje stronę licznika.
  * `Error`(*Błąd. Razor*, Blazor tylko aplikacja serwera) &ndash; Renderowane, gdy w aplikacji wystąpi nieobsługiwany wyjątek.
  * `FetchData`(*FetchData. Razor*) &ndash; implementuje stronę pobieranie danych.
  * `Index`(*Index. Razor*) &ndash; zawiera implementację strony głównej.

* *Shared* Folder &ndash; udostępniony zawiera inne składniki interfejsu użytkownika (*. Razor*) używane przez aplikację:
  * `MainLayout`(*MainLayout. Razor*) &ndash; składnik układu aplikacji.
  * `NavMenu`(*NavMenu. Razor*) &ndash; implementuje nawigację po pasku bocznym. Zawiera [składnik NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), który renderuje linki nawigacji do innych Razor składników. `NavLink` Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.

* *_Imports. Razor* &ndash; zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji (*. Razor*), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.

* Folder *dane* (Blazor serwer) &ndash; zawiera `WeatherForecast` klasy i implementacji `WeatherForecastService` , które zawierają przykładowe dane pogodowe do `FetchData` składnika aplikacji.

* *wwwroot* &ndash; folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne statyczne zasoby aplikacji.

* ustawienia konfiguracji pliku *appSettings. JSON* (Blazor serwera) &ndash; dla aplikacji.
