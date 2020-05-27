---
title: "ASP.NET Core Blazor templates" Author: Description: "informacje o Blazor szablonach aplikacji ASP.NET Core i Blazor strukturze projektu".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="aspnet-core-blazor-templates"></a>BlazorSzablony ASP.NET Core

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

BlazorStruktura zawiera szablony do tworzenia aplikacji dla każdego z Blazor modeli hostingu:

* BlazorWebassembly ( `blazorwasm` )
* BlazorSerwer ( `blazorserver` )

Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .

Instrukcje krok po kroku dotyczące tworzenia Blazor aplikacji na podstawie szablonu znajdują się w temacie <xref:blazor/get-started> .

Opcje szablonu są dostępne przez przekazanie `--help` opcji do nowego interfejsu wiersza polecenia [dotnet](/dotnet/core/tools/dotnet-new) :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>BlazorStruktura projektu

Następujące pliki i foldery tworzą Blazor aplikację wygenerowaną na podstawie Blazor szablonu:

* *Program.cs* &ndash; punkt wejścia aplikacji, który konfiguruje:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor serwer)
  * Host webassembly ( Blazor webassembly) &ndash; kod w tym pliku jest unikatowy dla aplikacji utworzonych na podstawie Blazor szablonu webassembly ( `blazorwasm` ).
    * `App`Składnik, który jest głównym składnikiem aplikacji, jest określony jako `app` element dom dla `Add` metody.
    * Usługi można skonfigurować przy użyciu `ConfigureServices` metody w konstruktorze hosta (na przykład `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * Konfigurację można dostarczyć za pośrednictwem konstruktora hosta ( `builder.Configuration` ).

* *Startup.cs* ( Blazor serwer) &ndash; zawiera logikę uruchamiania aplikacji. `Startup`Klasa definiuje dwie metody:

  * `ConfigureServices`&ndash;Konfiguruje usługi dla [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. W Blazor przypadku aplikacji serwerowych usługi są dodawane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> i `WeatherForecastService` dodawane do kontenera usługi do użycia przez przykładowy `FetchData` składnik.
  * `Configure`&ndash;Konfiguruje potok obsługi żądania aplikacji:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>jest wywoływana w celu skonfigurowania punktu końcowego dla połączenia w czasie rzeczywistym z przeglądarką. Połączenie jest tworzone przy użyciu [SignalR](xref:signalr/introduction) , który jest strukturą do dodawania funkcji sieci Web w czasie rzeczywistym do aplikacji.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) jest wywoływana w celu skonfigurowania strony głównej aplikacji (*strony/_Host. cshtml*) i włączenia nawigacji.

* *wwwroot/index.html* ( Blazor webassembly) &ndash; Strona główna aplikacji zaimplementowana jako strona HTML:
  * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
  * Strona określa, gdzie `App` jest renderowany składnik główny. `App`Składnik (*App. Razor*) jest określony jako `app` element dom dla `AddComponent` metody w `Startup.Configure` .
  * `_framework/blazor.webassembly.js`Plik JavaScript jest ładowany, co:
    * Pobiera środowisko uruchomieniowe platformy .NET, aplikację i zależności aplikacji.
    * Inicjuje środowisko uruchomieniowe, aby uruchomić aplikację.

* *App. Razor* &ndash; główny składnik aplikacji, który konfiguruje Routing po stronie klienta za pomocą <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika. <xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik przechwytuje nawigację przeglądarki i renderuje stronę pasującą do żądanego adresu.

* *Pages* Folder strony &ndash; zawiera składniki/strony z obsługą routingu (*. Razor*), które tworzą Blazor aplikację i Razor stronę główną Blazor aplikacji serwera. Trasy dla każdej strony są określone za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy. Szablon zawiera następujące elementy:
  * *_Host. cshtml* ( Blazor serwer) &ndash; Strona główna aplikacji zaimplementowana jako Razor Strona:
    * Po wstępnym zażądaniu dowolnej strony aplikacji jest ona renderowana i zwracana w odpowiedzi.
    * `_framework/blazor.server.js`Plik JavaScript jest ładowany, który konfiguruje połączenie w czasie rzeczywistym SignalR między przeglądarką a serwerem.
    * Strona hosta określa, gdzie `App` jest renderowany składnik główny (*App. Razor*).
  * `Counter`(*Counter. Razor*) &ndash; implementuje stronę licznika.
  * `Error`(*Error. Razor*, Blazor Tylko aplikacja serwera) &ndash; renderowana, gdy w aplikacji wystąpi nieobsługiwany wyjątek.
  * `FetchData`(*FetchData. Razor*) &ndash; implementuje stronę pobieranie danych.
  * `Index`(*Index. Razor*) &ndash; zawiera implementację strony głównej.

* *Shared* Folder udostępniony &ndash; zawiera inne składniki interfejsu użytkownika (*. Razor*) używane przez aplikację:
  * `MainLayout`(*MainLayout. Razor*) &ndash; składnik układu aplikacji.
  * `NavMenu`(*NavMenu. Razor*) &ndash; implementuje nawigację po pasku bocznym. Zawiera [składnik NavLink](xref:blazor/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), który renderuje linki nawigacji do innych Razor składników. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik automatycznie wskazuje wybrany stan podczas ładowania składnika, co pomaga użytkownikowi zrozumieć, który składnik jest aktualnie wyświetlany.

* *_Imports. Razor* &ndash; Zawiera wspólne Razor dyrektywy do uwzględnienia w składnikach aplikacji (*. Razor*), takich jak [`@using`](xref:mvc/views/razor#using) dyrektywy dotyczące przestrzeni nazw.

* Folder *dane* ( Blazor serwer) &ndash; zawiera `WeatherForecast` klasy i implementacji `WeatherForecastService` , które zawierają przykładowe dane pogodowe do `FetchData` składnika aplikacji.

* *wwwroot* &ndash; Folder [główny sieci Web](xref:fundamentals/index#web-root) dla aplikacji zawierającej publiczne zasoby statyczne aplikacji.

* ustawienia konfiguracji pliku *appSettings. JSON* ( Blazor serwera) &ndash; dla aplikacji.
