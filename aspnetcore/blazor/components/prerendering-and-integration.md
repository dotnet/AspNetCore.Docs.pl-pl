---
title: Wyprerender i Zintegruj Razor składniki ASP.NET Core
author: guardrex
description: Informacje o Razor scenariuszach integracji składników dla Blazor aplikacji, w tym do prerenderowania Razor składników na serwerze.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431706"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>Wyprerender i Zintegruj Razor składniki ASP.NET Core

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor składniki można zintegrować Razor ze stronami i aplikacjami MVC w hostowanym Blazor WebAssembly rozwiązaniu. Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.

## <a name="configuration"></a>Konfiguracja

Aby skonfigurować renderowanie w Blazor WebAssembly aplikacji:

1. Hostowanie Blazor WebAssembly aplikacji w aplikacji ASP.NET Core. Aplikację autonomiczną Blazor WebAssembly można dodać do rozwiązania ASP.NET Core lub można użyć hostowanej Blazor WebAssembly aplikacji utworzonej na podstawie Blazor szablonu hostowanego projektu.

1. Usuń domyślny plik statyczny `wwwroot/index.html` z Blazor WebAssembly projektu klienta.

1. Usuń następujący wiersz w `Program.Main` projekcie klienta:

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. Dodaj `Pages/_Host.cshtml` plik do projektu serwera. Plik można uzyskać `_Host.cshtml` z aplikacji utworzonej na podstawie Blazor Server szablonu za pomocą `dotnet new blazorserver -o BlazorServer` polecenia w powłoce poleceń. Po umieszczeniu `Pages/_Host.cshtml` pliku w aplikacji serwera hostowanego Blazor WebAssembly rozwiązania wprowadź następujące zmiany w pliku:

   * Ustaw przestrzeń nazw na folder aplikacji serwera `Pages` (na przykład `@namespace BlazorHosted.Server.Pages` ).
   * Ustaw [`@using`](xref:mvc/views/razor#using) dyrektywę dla projektu klienta (na przykład `@using BlazorHosted.Client` ).
   * Zaktualizuj linki arkusza stylów, aby wskazywały arkusz stylów aplikacji webassembly. W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * Zaktualizuj `render-mode` składnik [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) w celu wyrenderowania `App` składnika głównego:

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Zaktualizuj Blazor Źródło skryptu, aby używało skryptu po stronie klienta Blazor WebAssembly :

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. W `Startup.Configure` ( `Startup.cs` ) projektu serwera:

   * Zadzwoń do `UseDeveloperExceptionPage` programu App Builder w środowisku deweloperskim.
   * Zadzwoń do `UseBlazorFrameworkFiles` programu App Builder.
   * Zmień wartość Fallback ze `index.html` strony ( `endpoints.MapFallbackToFile("index.html");` ) na `_Host.cshtml` stronę.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Renderuj składniki na stronie lub widoku za pomocą pomocnika tagów składnika

Pomocnik tagu składnika obsługuje dwa tryby renderowania na potrzeby renderowania składnika z Blazor WebAssembly aplikacji na stronie lub widoku:

* `WebAssembly`: Renderuje znacznik dla Blazor WebAssembly aplikacji w celu uwzględnienia składnika interaktywnego, gdy jest ładowany w przeglądarce. Składnik nie jest wstępnie renderowany. Ta opcja ułatwia renderowanie różnych Blazor WebAssembly składników na różnych stronach.
* `WebAssemblyPrerendered`: Program wstępnie renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor WebAssembly aplikacji w celu późniejszego użycia, aby składnik był interaktywny po załadowaniu go do przeglądarki.

Na poniższym Razor przykładzie strony `Counter` składnik jest renderowany na stronie. Aby składnik był interaktywny, Blazor WebAssembly skrypt zostanie uwzględniony w [sekcji renderowania](xref:mvc/views/layout#sections)strony. Aby uniknąć używania pełnej przestrzeni nazw dla `Counter` składnika z pomocnikiem tagów składnika ( `{APP ASSEMBLY}.Pages.Counter` ), Dodaj [`@using`](xref:mvc/views/razor#using) dyrektywę dla `Pages` przestrzeni nazw aplikacji klienta. W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy składnik:

* Jest wstępnie renderowany na stronie.
* Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

Aby uzyskać więcej informacji na temat pomocnika tagów składnika, w tym przekazywania parametrów i <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguracji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

Poprzedni przykład wymaga, aby układ aplikacji serwera ( `_Layout.cshtml` ) zawierał [sekcję renderowania](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) dla skryptu wewnątrz tagu zamykającego `</body>` :

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Plik znajduje się `_Layout.cshtml` w `Pages/Shared` folderze w Razor aplikacji lub `Views/Shared` folderze stron w aplikacji MVC.

Jeśli aplikacja powinna również stylować składniki przy użyciu stylów w Blazor WebAssembly aplikacji, należy uwzględnić w pliku style aplikacji `_Layout.cshtml` . W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Renderuj składniki na stronie lub widoku za pomocą selektora CSS

Dodaj główne składniki do projektu *klienta* w `Program.Main` ( `Program.cs` ). W poniższym przykładzie `Counter` składnik jest zadeklarowany jako składnik główny z selektorem CSS, który wybiera element z `id` pasującymi `my-counter` . W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

Na poniższym Razor przykładzie strony `Counter` składnik jest renderowany na stronie. Aby składnik był interaktywny, Blazor WebAssembly skrypt zostanie uwzględniony w [sekcji renderowania](xref:mvc/views/layout#sections)strony:

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

Poprzedni przykład wymaga, aby układ aplikacji serwera ( `_Layout.cshtml` ) zawierał [sekcję renderowania](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) dla skryptu wewnątrz tagu zamykającego `</body>` :

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Plik znajduje się `_Layout.cshtml` w `Pages/Shared` folderze w Razor aplikacji lub `Views/Shared` folderze stron w aplikacji MVC.

Jeśli aplikacja powinna również stylować składniki przy użyciu stylów w Blazor WebAssembly aplikacji, należy uwzględnić w pliku style aplikacji `_Layout.cshtml` . W poniższym przykładzie przestrzeń nazw aplikacji klienckiej to `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Integrowanie Razor składników na Razor stronach i aplikacjach MVC w hostowanym Blazor WebAssembly rozwiązaniu jest obsługiwane w ASP.NET Core w programie .NET 5 lub nowszym. Wybierz platformę .NET 5 lub nowszą wersję tego artykułu.

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor składniki można zintegrować Razor ze stronami i aplikacjami MVC w Blazor Server aplikacji. Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.

Po [skonfigurowaniu aplikacji](#configuration)Użyj wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:

* Składniki routingu: dla składników, które są bezpośrednio trasowane z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej przeglądarce dla składnika z [`@page`](xref:mvc/views/razor#page) dyrektywą.
  * [Używanie składników rutowanych w Razor aplikacji stron](#use-routable-components-in-a-razor-pages-app)
  * [Używanie składników rutowanych w aplikacji MVC](#use-routable-components-in-an-mvc-app)
* [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view): dla składników, które nie są bezpośrednio trasowane z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="configuration"></a>Konfiguracja

Istniejące Razor strony lub aplikacja MVC mogą integrować Razor składniki na stronach i widokach:

1. W pliku układu aplikacji ( `_Layout.cshtml` ):

   * Dodaj następujący `<base>` tag do `<head>` elementu:

     ```html
     <base href="~/" />
     ```

     `href`Wartość ( *Ścieżka podstawowa aplikacji* ) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego ( `/` ). Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:blazor/host-and-deploy/index#app-base-path> artykułu.

     Plik znajduje się `_Layout.cshtml` w `Pages/Shared` folderze w Razor aplikacji lub `Views/Shared` folderze stron w aplikacji MVC.

   * Dodaj `<script>` tag dla `blazor.server.js` skryptu bezpośrednio przed `Scripts` sekcją renderowania:

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     Struktura dodaje `blazor.server.js` skrypt do aplikacji. Nie trzeba ręcznie dodawać skryptu do aplikacji.

1. Dodaj `_Imports.razor` plik do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw, `MyAppNamespace` do przestrzeni nazw aplikacji):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. W programie `Startup.ConfigureServices` zarejestruj Blazor Server usługę:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. W programie `Startup.Configure` Dodaj Blazor punkt końcowy centrum do `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integruj składniki na dowolną stronę lub widok. Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Używanie składników rutowanych w Razor aplikacji stron

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*

Aby obsługiwać Razor składniki routingu w Razor aplikacjach stron:

1. Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja](#configuration) .

1. Dodaj `App.razor` plik do katalogu głównego projektu z następującą zawartością:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Dodaj `_Host.cshtml` plik do `Pages` folderu o następującej zawartości:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Składniki używają udostępnionego `_Layout.cshtml` pliku do ich układu.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy `App` składnik:

   * Jest wstępnie renderowany na stronie.
   * Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

   Aby uzyskać więcej informacji na temat pomocnika tagów składnika, w tym przekazywania parametrów i <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguracji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Dodaj trasę o niskim priorytecie dla `_Host.cshtml` konfiguracji strony do punktu końcowego w programie `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Dodaj składniki routingu do aplikacji. Na przykład:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Używanie składników rutowanych w aplikacji MVC

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*

Aby obsługiwać Razor składniki routingu w aplikacjach MVC:

1. Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja](#configuration) .

1. Dodaj `App.razor` plik do katalogu głównego projektu o następującej zawartości:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Dodaj `_Host.cshtml` plik do `Views/Home` folderu o następującej zawartości:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Składniki używają udostępnionego `_Layout.cshtml` pliku do ich układu.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy `App` składnik:

   * Jest wstępnie renderowany na stronie.
   * Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

   Aby uzyskać więcej informacji na temat pomocnika tagów składnika, w tym przekazywania parametrów i <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguracji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Dodaj akcję do kontrolera macierzystego:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca `_Host.cshtml` Widok do konfiguracji punktu końcowego w `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Utwórz `Pages` folder i Dodaj do aplikacji składniki obsługujące Routing. Na przykład:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .

## <a name="render-components-from-a-page-or-view"></a>Renderuj składniki ze strony lub widoku

*Ta sekcja dotyczy dodawania składników do stron lub widoków, w których składniki nie są bezpośrednio trasowane z żądań użytkownika.*

Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Renderuj składniki interaktywne ze stanem

Składniki interaktywne można dodać do Razor strony lub widoku.

Gdy renderuje stronę lub widok:

* Składnik jest wstępnie renderowany przy użyciu strony lub widoku.
* Początkowy stan składnika używany na potrzeby renderowania wstępnego został utracony.
* Nowy stan składnika jest tworzony podczas SignalR ustanawiania połączenia.

Na poniższej Razor stronie jest renderowany `Counter` składnik:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Renderuj nieinteraktywne składniki

Na poniższej Razor stronie `Counter` składnik jest renderowany statycznie z wartością początkową określoną przy użyciu formularza. Ze względu na to, że składnik jest renderowany statycznie, składnik nie jest interaktywny:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Przestrzenie nazw składników

W przypadku używania folderu niestandardowego do przechowywania składników aplikacji należy dodać przestrzeń nazw reprezentującą folder do strony/widoku lub do `_ViewImports.cshtml` pliku. W poniższym przykładzie:

* Przejdź `MyAppNamespace` do przestrzeni nazw aplikacji.
* Jeśli folder o nazwie `Components` nie jest używany do przechowywania składników, przejdź `Components` do folderu, w którym znajdują się składniki.

```cshtml
@using MyAppNamespace.Components
```

Plik znajduje się `_ViewImports.cshtml` w `Pages` folderze Razor aplikacji Pages lub w `Views` folderze aplikacji MVC.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#namespaces>.

::: zone-end
