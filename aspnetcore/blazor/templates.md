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
# <a name="aspnet-core-opno-locblazor-templates"></a>szablony ASP.NET Blazor Core

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Struktura Blazor zawiera szablony do tworzenia aplikacji Blazor dla każdego z modeli hostingu:

* BlazorWebAssembly`blazorwasm`( )
* BlazorSerwer`blazorserver`( )

Aby uzyskać Blazorwięcej informacji na <xref:blazor/hosting-models>temat modeli hostingu, zobacz .

Aby uzyskać instrukcje krok po kroku Blazor dotyczące tworzenia aplikacji <xref:blazor/get-started>z szablonu, zobacz .

Opcje szablonu są `--help` dostępne, przekazując tę opcję do nowego polecenia [dotnet:](/dotnet/core/tools/dotnet-new)

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazorstruktura projektu

Następujące pliki i foldery Blazor tworzą aplikację Blazor wygenerowaną na podstawie szablonu:

* Program.cs Punkt wejścia aplikacji, który konfiguruje: *Program.cs* &ndash;

  * ASP.NET host [Core](xref:fundamentals/host/generic-host) Blazor (Serwer)
  * WebAssembly hostBlazor ( WebAssembly) &ndash; Kod w tym pliku Blazor jest unikatowy dla`blazorwasm`aplikacji utworzonych z szablonu WebAssembly ( ).
    * Składnik, `App` który jest głównym składnikiem aplikacji, jest `app` określony jako `Add` element DOM do metody.
    * Usługi można skonfigurować `ConfigureServices` za pomocą metody konstruktora `builder.Services.AddSingleton<IMyDependency, MyDependency>();`hosta (na przykład ).
    * Konfiguracja może być dostarczana`builder.Configuration`za pośrednictwem konstruktora hosta ( ).

* *Startup.cs* (Blazor Serwer) &ndash; Zawiera logikę uruchamiania aplikacji. Klasa `Startup` definiuje dwie metody:

  * `ConfigureServices`&ndash; Konfiguruje usługi [iniekcji zależności aplikacji (DI).](xref:fundamentals/dependency-injection) W Blazor aplikacjach serwera usługi <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>są dodawane przez wywołanie , a `WeatherForecastService` `FetchData` jest dodawany do kontenera usługi do użycia przez przykładowy składnik.
  * `Configure`&ndash; Konfiguruje potok obsługi żądań aplikacji:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką. Połączenie jest tworzone [SignalR](xref:signalr/introduction)za pomocą programu , który jest platformą do dodawania funkcji sieci web w czasie rzeczywistym do aplikacji.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji *(Pages/_Host.cshtml)* i włączenia nawigacji.

* *wwwroot/index.html* (Blazor WebAssembly) &ndash; Strona główna aplikacji zaimplementowana jako strona HTML:
  * Gdy początkowo żądana jest dowolna strona aplikacji, ta strona jest renderowana i zwracana w odpowiedzi.
  * Strona określa, gdzie `App` jest renderowany składnik główny. Składnik `App` (*App.brzytwa*) `app` jest określony `AddComponent` jako `Startup.Configure`element DOM do metody w .
  * Załadowany `_framework/blazor.webassembly.js` jest plik JavaScript, który:
    * Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.
    * Inicjuje środowisko wykonawcze, aby uruchomić aplikację.

* *App.razor* &ndash; Główny składnik aplikacji, która konfiguruje routing <xref:Microsoft.AspNetCore.Components.Routing.Router> po stronie klienta przy użyciu składnika. Składnik `Router` przechwytuje nawigację w przeglądarce i renderuje stronę, która pasuje do żądanego adresu.

* *Folder* &ndash; Strony Zawiera rutowalne składniki/strony (*.brzytwa*), które Blazor tworzą Blazor aplikację i główną stronę Razor aplikacji Serwera. Trasa dla każdej strony jest [`@page`](xref:mvc/views/razor#page) określona przy użyciu dyrektywy. Szablon zawiera następujące elementy:
  * *_Host.cshtml* (Blazor Serwer) &ndash; Strona główna aplikacji zaimplementowana jako strona Razor:
    * Gdy początkowo żądana jest dowolna strona aplikacji, ta strona jest renderowana i zwracana w odpowiedzi.
    * Załadowany `_framework/blazor.server.js` jest plik JavaScript, który konfiguruje połączenie w czasie SignalR rzeczywistym między przeglądarką a serwerem.
    * Strona Host określa, gdzie `App` jest renderowany składnik główny (*App.brzytwa).*
  * `Counter`*(Counter.brzytwa)* &ndash; Implementuje stronę Licznik.
  * `Error`*(Error.brzytwa*, Blazor Tylko aplikacja serwera) &ndash; Renderowane, gdy wystąpi nieobsługiowany wyjątek w aplikacji.
  * `FetchData`*(FetchData.brzytwa)* &ndash; Implementuje stronę pobierania danych.
  * `Index`(*Index.brzytwa*) &ndash; Implementuje stronę główną.

* *Folder* &ndash; udostępniony Zawiera inne składniki interfejsu użytkownika (*.brzytwa*) używane przez aplikację:
  * `MainLayout`(*MainLayout.brzytwa*) &ndash; Składnik układu aplikacji.
  * `NavMenu`*(NavMenu.brzytwa)* &ndash; Implementuje nawigację paska bocznego. Zawiera [komponent NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), który renderuje łącza nawigacyjne do innych komponentów Razor. Komponent `NavLink` automatycznie wskazuje wybrany stan podczas ładowania jego składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.

* *_Imports.brzytwa* &ndash; zawiera typowe dyrektywy Razor do uwzględnienia w składnikach aplikacji ( [`@using`](xref:mvc/views/razor#using) *.brzytwa*), takich jak dyrektywy dla obszarów nazw.

* *Folder* danychBlazor (Serwer) &ndash; Zawiera `WeatherForecast` klasę `WeatherForecastService` i implementację, które zapewniają `FetchData` przykładowe dane pogodowe do składnika aplikacji.

* *wwwroot* &ndash; Folder [Web Root](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.

* *appsettings.json* Blazor ( &ndash; Serwer) Ustawienia konfiguracji aplikacji.
