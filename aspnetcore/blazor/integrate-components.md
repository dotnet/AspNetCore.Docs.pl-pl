---
title: Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC
author: guardrex
description: Dowiedz się więcej o scenariuszach wiązania Blazor danych dla składników i elementów DOM w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383967"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integracja komponentów ASP.NET Core Razor w aplikacjach Razor Pages i MVC

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

Komponenty brzytwy można zintegrować ze żyletką Pages i aplikacjami MVC. Gdy strona lub widok jest renderowany, komponenty mogą być prerendered w tym samym czasie.

Po [przygotowaniu aplikacji](#prepare-the-app)skorzystaj ze wskazówek w poniższych sekcjach, w zależności od wymagań aplikacji:

* Składniki &ndash; routingu Dla składników, które są bezpośrednio rutowalne z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy użytkownicy powinni mieć [`@page`](xref:mvc/views/razor#page) możliwość złożenia żądania HTTP w przeglądarce dla składnika z dyrektywą.
  * [Używanie składników routable w aplikacji Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Używanie składników routingu w aplikacji MVC](#use-routable-components-in-an-mvc-app)
* [Renderowanie składników ze strony lub widoku](#render-components-from-a-page-or-view) &ndash; Dla składników, które nie są bezpośrednio rutowalne z żądań użytkownika. Postępuj zgodnie z tymi wskazówkami, gdy aplikacja osadza składniki na istniejących stronach i widokach za pomocą [Pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Istniejące strony Razor lub aplikacja MVC mogą integrować składniki Razor ze stronami i widokami:

1. W pliku układu aplikacji (*_Layout.cshtml*):

   * Dodaj następujący `<base>` znacznik `<head>` do elementu:

     ```html
     <base href="~/" />
     ```

     Wartość `href` *(ścieżka podstawowa aplikacji)* w poprzednim przykładzie zakłada, że aplikacja znajduje`/`się przy ścieżce głównego adresu URL ( ). Jeśli aplikacja jest aplikacją podrzędną, postępuj zgodnie ze <xref:host-and-deploy/blazor/index#app-base-path> wskazówkami w sekcji *Ścieżka podstawowa aplikacji* w tym artykule.

     Plik *_Layout.cshtml* znajduje się w folderze *Strony/Udostępnione* w aplikacji Razor Pages lub w folderze *Widoki/Udostępnione* w aplikacji MVC.

   * Dodaj `<script>` tag skryptu *blazor.server.js* bezpośrednio przed `</body>` tagiem zamykającym:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Struktura dodaje *skrypt blazor.server.js* do aplikacji. Nie ma potrzeby ręcznego dodawania skryptu do aplikacji.

1. Dodaj plik *_Imports.razor* do folderu głównego projektu z następującą zawartością (zmień `MyAppNamespace`obszar nazw, do obszaru nazw aplikacji):

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

1. W `Startup.ConfigureServices`, zarejestruj usługę Serwera Blazor:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. W `Startup.Configure`programie dodaj punkt końcowy `app.UseEndpoints`Blazor Hub do:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Zintegruj komponenty z dowolną stroną lub widokiem. Aby uzyskać więcej informacji, zobacz [Renderuj składniki ze strony lub widoku](#render-components-from-a-page-or-view) sekcji.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Używanie składników routable w aplikacji Razor Pages

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*

Aby obsługiwać rutowalne składniki Razor w aplikacjach Razor Pages:

1. Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji.](#prepare-the-app)

1. Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:

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

1. Dodaj *plik _Host.cshtml* do folderu *Pages* z następującą zawartością:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, `App` czy składnik:

   * Jest prerendered do strony.
   * Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.

   | Tryb renderowania | Opis |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML i zawiera znacznik aplikacji Blazor Server. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik aplikacji Blazor Server. Dane wyjściowe ze `App` składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania aplikacji Blazor. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML. |

   Aby uzyskać więcej informacji na temat <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>pomocnika znacznika składnika, zobacz .

1. Dodaj trasę o niskim priorytecie dla strony *_Host.cshtml* do konfiguracji punktu końcowego w : `Startup.Configure`

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Dodaj składniki rutowalne do aplikacji. Przykład:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)

## <a name="use-routable-components-in-an-mvc-app"></a>Używanie składników routingu w aplikacji MVC

*Ta sekcja dotyczy dodawania składników, które są bezpośrednio rutowalne z żądań użytkownika.*

Aby obsługiwać rutowalne komponenty Razor w aplikacjach MVC:

1. Postępuj zgodnie ze wskazówkami w sekcji [Przygotowywanie aplikacji.](#prepare-the-app)

1. Dodaj plik *App.razor* do katalogu głównego projektu z następującą zawartością:

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

1. Dodaj plik *_Host.cshtml* do folderu *Widoki/Strona główna* z następującą zawartością:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Składniki używają udostępnionego *pliku _Layout.cshtml* dla ich układu.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, `App` czy składnik:

   * Jest prerendered do strony.
   * Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.

   | Tryb renderowania | Opis |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje `App` składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik aplikacji Blazor serwera. Dane wyjściowe ze `App` składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje `App` składnik do statycznego kodu HTML. |

   Aby uzyskać więcej informacji na temat <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>pomocnika znacznika składnika, zobacz .

1. Dodaj akcję do kontrolera macierzystego:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Dodaj trasę o niskim priorytecie dla akcji kontrolera, która zwraca widok `Startup.Configure` *_Host.cshtml* do konfiguracji punktu końcowego w :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Utwórz folder *Strony* i dodaj do aplikacji składniki rutowalne. Przykład:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Aby uzyskać więcej informacji na temat obszarów nazw, zobacz [sekcję Obszary nazw składnika.](#component-namespaces)

## <a name="render-components-from-a-page-or-view"></a>Renderowanie składników ze strony lub widoku

*Ta sekcja dotyczy dodawania składników do stron lub widoków, gdzie składniki nie są bezpośrednio rutowalne z żądań użytkowników.*

Aby renderować składnik ze strony lub widoku, użyj [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Aby uzyskać więcej informacji na temat sposobu renderowania składników, stanu składnika i Pomocnika `Component` znaczników, zobacz następujące artykuły:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a>Przestrzenie nazw składników

W przypadku przechowywania składników aplikacji przy użyciu folderu niestandardowego należy dodać obszar nazw reprezentujący folder do strony/widoku lub pliku *_ViewImports.cshtml.* W poniższym przykładzie:

* Zmień `MyAppNamespace` obszar nazw aplikacji.
* Jeśli folder o nazwie *Składniki* nie jest używany `Components` do przechowywania składników, zmień folder, w którym znajdują się składniki.

```cshtml
@using MyAppNamespace.Components
```

Plik *_ViewImports.cshtml* znajduje się w folderze *Strony* aplikacji Razor Pages lub w folderze *Widoki* aplikacji MVC.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components#import-components>.
