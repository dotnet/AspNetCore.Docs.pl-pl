---
title: Integrowanie Razor składników ASP.NET Core Razor ze stronami i aplikacjami MVC
author: guardrex
description: Dowiedz się więcej na temat scenariuszy powiązań danych dla składników Blazor i elementów dom w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: eb4378223c40594ac52f50b7b890785067515555
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771777"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrowanie składników ASP.NET Core Razor z aplikacjami Razor Pages i MVC

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

Składniki Razor można zintegrować z aplikacjami Razor Pages i MVC. Gdy strona lub widok jest renderowany, składniki mogą być wstępnie renderowane w tym samym czasie.

Po [przygotowaniu aplikacji](#prepare-the-app)należy użyć wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:

* Składniki &ndash; routingu dla składników, które są bezpośrednio trasowane z żądań użytkowników. Postępuj zgodnie z tymi wskazówkami, gdy osoby odwiedzające powinny mieć możliwość wykonania żądania HTTP w swojej [`@page`](xref:mvc/views/razor#page) przeglądarce dla składnika z dyrektywą.
  * [Używanie składników rutowanych w aplikacji Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Używanie składników rutowanych w aplikacji MVC](#use-routable-components-in-an-mvc-app)
* [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view) &ndash; składników, które nie są bezpośrednio trasowane z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadzi składniki na istniejących stronach i widokach za pomocą [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Istniejąca aplikacja Razor Pages lub MVC może zintegrować składniki Razor ze stronami i widokami:

1. W pliku układu aplikacji (*_Layout. cshtml*):

   * Dodaj następujący `<base>` tag do `<head>` elementu:

     ```html
     <base href="~/" />
     ```

     Wartość ( *Ścieżka podstawowa aplikacji*) w poprzednim przykładzie zakłada, że aplikacja znajduje się w ścieżce adresu URL katalogu głównego (`/`). `href` Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze wskazówkami w sekcji *Ścieżka podstawowa aplikacji* <xref:host-and-deploy/blazor/index#app-base-path> artykułu.

     Plik *_Layout. cshtml* znajduje się w folderze *Pages/shared* w aplikacji Razor Pages lub *widokach/folderze udostępnionym* w aplikacji MVC.

   * Dodaj `<script>` tag dla skryptu *blazor. Server. js* bezpośrednio przed tagiem zamykającym `</body>` :

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Struktura dodaje skrypt *blazor. Server. js* do aplikacji. Nie trzeba ręcznie dodawać skryptu do aplikacji.

1. Dodaj plik *_Imports. Razor* do folderu głównego projektu o następującej zawartości (Zmień ostatnią przestrzeń nazw `MyAppNamespace`, do przestrzeni nazw aplikacji):

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

1. W `Startup.ConfigureServices`programie Zarejestruj usługę serwera Blazor:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. W `Startup.Configure`programie Dodaj punkt końcowy centrum Blazor do `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integruj składniki na dowolną stronę lub widok. Aby uzyskać więcej informacji, zobacz [składniki renderowania ze strony lub widoku](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Używanie składników rutowanych w aplikacji Razor Pages

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio trasowane z żądań użytkowników.*

Aby obsługiwać Routing składników Razor w aplikacjach Razor Pages:

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

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, `App` czy składnik:

   * Jest wstępnie renderowany na stronie.
   * Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.

   | Tryb renderowania | Opis |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla aplikacji serwera Blazor. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla aplikacji serwera Blazor. Dane wyjściowe ze `App` składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML. |

   Aby uzyskać więcej informacji na temat pomocnika tagów składnika <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>, zobacz.

1. Dodaj trasę o niskim priorytecie dla strony *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure`:

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

Aby zapewnić obsługę routingu składników Razor w aplikacjach MVC:

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
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Określa, `App` czy składnik:

   * Jest wstępnie renderowany na stronie.
   * Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia aplikacji Blazor z poziomu agenta użytkownika.

   | Tryb renderowania | Opis |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik dla Blazor aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla aplikacji Blazor serwera. Dane wyjściowe ze `App` składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML. |

   Aby uzyskać więcej informacji na temat pomocnika tagów składnika <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>, zobacz.

1. Dodaj akcję do kontrolera macierzystego:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok *_Host. cshtml* do konfiguracji punktu końcowego w `Startup.Configure`:

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
* Nowy stan składnika jest tworzony podczas ustanawiania SignalR połączenia.

Na poniższej Razor stronie jest `Counter` renderowany składnik:

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
* Jeśli folder o nazwie *Components* nie jest używany do przechowywania składników, `Components` przejdź do folderu, w którym znajdują się składniki.

```cshtml
@using MyAppNamespace.Components
```

Plik *_ViewImports. cshtml* znajduje się w folderze *Pages* aplikacji Razor Pages lub folderze *widoki* aplikacji MVC.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.
