---
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrowanie Razor składników ASP.NET Core ze Razor stronami i aplikacjami MVC

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

Razorskładniki można zintegrować ze Razor stronami i aplikacjami MVC. Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.

Po [przygotowaniu aplikacji](#prepare-the-app)należy użyć wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:

* Składniki routingu: dla składników, które są bezpośrednio trasowane z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej przeglądarce dla składnika z [`@page`](xref:mvc/views/razor#page) dyrektywą.
  * [Używanie składników rutowanych w Razor aplikacji stron](#use-routable-components-in-a-razor-pages-app)
  * [Używanie składników rutowanych w aplikacji MVC](#use-routable-components-in-an-mvc-app)
* [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view): dla składników, które nie są bezpośrednio trasowane z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Istniejące Razor strony lub aplikacja MVC mogą integrować Razor składniki na stronach i widokach:

1. W pliku układu aplikacji (*_Layout. cshtml*):

   * Dodaj następujący `<base>` tag do `<head>` elementu:

     ```html
     <base href="~/" />
     ```

     `href`Wartość ( *Ścieżka podstawowa aplikacji*) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego ( `/` ). Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:host-and-deploy/blazor/index#app-base-path> artykułu.

     Plik *_Layout. cshtml* znajduje się w folderze *Pages/Shared* w Razor aplikacji strony lub *widokach/folderze udostępnionym* w aplikacji MVC.

   * Dodaj `<script>` tag dla skryptu *blazor. Server. js* bezpośrednio przed tagiem zamykającym `</body>` :

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Struktura dodaje skrypt *blazor. Server. js* do aplikacji. Nie trzeba ręcznie dodawać skryptu do aplikacji.

1. Dodaj plik *_Imports. Razor* do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw, `MyAppNamespace` do przestrzeni nazw aplikacji):

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

1. W programie `Startup.ConfigureServices` zarejestruj Blazor usługę serwera:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. W programie `Startup.Configure` Dodaj Blazor punkt końcowy centrum do `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integruj składniki na dowolną stronę lub widok. Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Używanie składników rutowanych w Razor aplikacji stron

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*

Aby obsługiwać Razor składniki routingu w Razor aplikacjach stron:

1. Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .

1. Dodaj plik *App. Razor* do katalogu głównego projektu z następującą zawartością:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Dodaj plik *_Host. cshtml* do folderu *stron* o następującej zawartości:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Składniki używają udostępnionego pliku *_Layout. cshtml* dla ich układu.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy `App` składnik:

   * Jest wstępnie renderowany na stronie.
   * Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

   | Tryb renderowania | Opis |
   | ---
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | ---title: "Integruj Razor składniki ASP.NET Core Razor ze stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor aplikacji serwera. Dane wyjściowe ze `App` składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML. |

   Aby uzyskać więcej informacji na temat pomocnika tagów składnika, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Dodaj trasę o niskim priorytecie dla strony *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Dodaj składniki routingu do aplikacji. Przykład:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Aby uzyskać więcej informacji na temat przestrzeni nazw, zobacz sekcję [przestrzenie nazw składników](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Używanie składników rutowanych w aplikacji MVC

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*

Aby obsługiwać Razor składniki routingu w aplikacjach MVC:

1. Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji](#prepare-the-app) .

1. Dodaj plik *App. Razor* do katalogu głównego projektu z następującą zawartością:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Dodaj plik *_Host. cshtml* do folderu *widoki/główne* z następującą zawartością:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Składniki używają udostępnionego pliku *_Layout. cshtml* dla ich układu.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, czy `App` składnik:

   * Jest wstępnie renderowany na stronie.
   * Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

   | Tryb renderowania | Opis |
   | ---
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | ---title: "Integruj Razor składniki ASP.NET Core Razor ze stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "Integruj Razor składniki ASP.NET Core ze Razor stronami i aplikacjami MVC" Author: Description: "informacje o scenariuszach powiązań danych dla składników i elementów dom w Blazor aplikacjach".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor aplikacji serwera. Dane wyjściowe ze `App` składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML. |

   Aby uzyskać więcej informacji na temat pomocnika tagów składnika, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Dodaj akcję do kontrolera macierzystego:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Utwórz folder *strony* i Dodaj składniki do obsługi routingu do aplikacji. Przykład:

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

W przypadku używania folderu niestandardowego do przechowywania składników aplikacji należy dodać przestrzeń nazw reprezentującą folder do strony/widoku lub pliku *_ViewImports. cshtml* . W poniższym przykładzie:

* Przejdź `MyAppNamespace` do przestrzeni nazw aplikacji.
* Jeśli folder o nazwie *Components* nie jest używany do przechowywania składników, przejdź `Components` do folderu, w którym znajdują się składniki.

```cshtml
@using MyAppNamespace.Components
```

Plik *_ViewImports. cshtml* znajduje się w folderze *Pages* Razor aplikacji Pages lub folderze *widoki* aplikacji MVC.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.
