---
title: Zestawy ładowania z opóźnieniem w ASP.NET Core Blazor WebAssembly
author: guardrex
description: Odkryj, jak ładować zestawy w aplikacjach ASP.NET Core Blazor WebAssembly .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
no-loc:
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: eb4aaa2f3d412cdf650ed2daf7c12166991d92a1
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592907"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Zestawy ładowania z opóźnieniem w ASP.NET Core Blazor WebAssembly

Autorzy [Safia Abdalla](https://safia.rocks) i [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly wydajność uruchamiania aplikacji można ulepszyć, odwołując ładowanie niektórych zestawów aplikacji do momentu, gdy nie są wymagane, co jest nazywane *ładowaniem z opóźnieniem*. Na przykład zestawy, które są używane tylko do renderowania pojedynczego składnika, można skonfigurować tak, aby ładowały się tylko wtedy, gdy użytkownik nawiguje do tego składnika. Po załadowaniu zestawy są buforowane po stronie klienta i są dostępne dla wszystkich przyszłych nawigacji.

BlazorFunkcja ładowania z opóźnieniem umożliwia oznaczanie zestawów aplikacji na potrzeby ładowania z opóźnieniem, które ładuje zestawy podczas środowiska uruchomieniowego, gdy użytkownik przechodzi do określonej trasy. Funkcja składa się z zmian w pliku projektu i zmian w routerze aplikacji.

> [!NOTE]
> Ładowanie z opóźnieniem zestawu nie pozwala na korzystanie z Blazor Server aplikacji, ponieważ zestawy nie są pobierane do klienta w Blazor Server aplikacji.

## <a name="project-file"></a>Plik projektu

Oznacz zestawy do ładowania z opóźnieniem w pliku projektu aplikacji ( `.csproj` ) przy użyciu `BlazorWebAssemblyLazyLoad` elementu. Użyj nazwy zestawu bez `.dll` rozszerzenia. BlazorStruktura uniemożliwia ładowanie zestawów określonych przez tę grupę elementów podczas uruchamiania aplikacji. Poniższy przykład oznacza duże niestandardowe zestawy ( `GrantImaharaRobotControls.dll` ) dla ładowania z opóźnieniem. Jeśli zestaw, który jest oznaczony do ładowania z opóźnieniem ma zależności, muszą także być oznaczone do ładowania opóźnionego w pliku projektu.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>`Router` cm6long

Blazor`Router`składnik wyznacza, który zestaw Blazor wyszukuje składniki routingu. `Router`Składnik jest również odpowiedzialny za renderowanie składnika dla trasy, w której przechodzi użytkownik. `Router`Składnik obsługuje `OnNavigateAsync` funkcję, która może być używana w połączeniu z opóźnionym ładowaniem.

W `Router` składniku aplikacji ( `App.razor` ):

* Dodaj `OnNavigateAsync` wywołanie zwrotne. `OnNavigateAsync`Procedura obsługi jest wywoływana, gdy użytkownik:
  * Po raz pierwszy odwiedza trasę, przechodząc do niego bezpośrednio z przeglądarki.
  * Przechodzi do nowej trasy przy użyciu linku lub <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wywołania.
* Jeśli zestawy ładowane z opóźnieniem zawierają składniki routingu, należy dodać [listę](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (na przykład nazwa `lazyLoadedAssemblies` ) do składnika. Zestawy są przenoszone z powrotem do <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> kolekcji w przypadku, gdy zestawy zawierają składniki routingu. Struktura przeszukuje zestawy pod kątem tras i aktualizuje kolekcję tras w przypadku znalezienia nowych tras.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

Jeśli `OnNavigateAsync` wywołanie zwrotne zgłasza nieobsłużony wyjątek, zostanie wywołany [ Blazor interfejs użytkownika błędu](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .

### <a name="assembly-load-logic-in-onnavigateasync"></a>Logika ładowania zestawu w `OnNavigateAsync`

`OnNavigateAsync` zawiera `NavigationContext` parametr, który zawiera informacje o bieżącym asynchronicznym zdarzeniu nawigacji, w tym ścieżkę docelową ( `Path` ) i tokenem anulowania ( `CancellationToken` ):

* `Path`Właściwość jest ścieżką docelową użytkownika względem ścieżki podstawowej aplikacji, na przykład `/robot` .
* `CancellationToken`Może służyć do obserwowania anulowania zadania asynchronicznego. `OnNavigateAsync` automatycznie anuluje aktualnie uruchomione zadanie nawigacji, gdy użytkownik nawiguje do innej strony.

Wewnątrz `OnNavigateAsync` , należy wdrożyć logikę, aby określić zestawy do załadowania. Dostępne opcje:

* Kontrole warunkowe wewnątrz `OnNavigateAsync` metody.
* Tabela wyszukiwania, w której są mapowane trasy do nazw zestawów, które zostały dodane do składnika lub zaimplementowane w [`@code`](xref:mvc/views/razor#code) bloku.

`LazyAssemblyLoader` to usługa singleton udostępniona przez platformę do ładowania zestawów. Wsuń `LazyAssemblyLoader` do `Router` składnika:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader`Zapewnia `LoadAssembliesAsync` metodę, która:

* Używa programu JS Interop do pobierania zestawów za pośrednictwem połączenia sieciowego.
* Ładuje zestawy do wykonywania w środowisku uruchomieniowym w zestawie webassembly w przeglądarce.

Implementacja wdrożenia z opóźnieniem platformy obsługuje ładowanie z opóźnieniem z użyciem prerenderowania w hostowanym Blazor rozwiązaniu. Podczas renderowania prerenderingu założono, że wszystkie zestawy, w tym zaznaczone dla ładowania z opóźnieniem, zostały załadowane. Zarejestruj ręcznie `LazyAssemblyLoader` w metodzie projektu *serwera* `Startup.ConfigureServices` ( `Startup.cs` ):

```csharp
services.AddSingleton<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>Interakcja użytkownika z `<Navigating>` zawartością

Podczas ładowania zestawów, które mogą potrwać kilka sekund, `Router` składnik może wskazywać użytkownikowi, że występuje przejście strony:

* Dodaj [`@using`](xref:mvc/views/razor#using) dyrektywę dla <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> przestrzeni nazw.
* Dodaj `<Navigating>` do składnika tag zawierający znaczniki, które mają być wyświetlane podczas zdarzeń przejścia strony.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a>Obsługa anulowania w `OnNavigateAsync`

`NavigationContext`Obiekt przesłany do `OnNavigateAsync` wywołania zwrotnego zawiera `CancellationToken` zestaw, który jest ustawiony w momencie wystąpienia nowego zdarzenia nawigacji. `OnNavigateAsync`Wywołanie zwrotne musi zgłosić, gdy ten token anulowania jest ustawiony tak, aby uniknąć kontynuowania uruchamiania `OnNavigateAsync` wywołania zwrotnego w nieaktualnej nawigacji.

Jeśli użytkownik przejdzie do trasy a, a następnie natychmiast do trasy B, aplikacja nie powinna nadal uruchamiać `OnNavigateAsync` wywołania zwrotnego dla trasy a:

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> Nie zgłaszaj, czy token anulowania w programie `NavigationContext` został anulowany może spowodować niezamierzone zachowanie, takie jak renderowanie składnika z poprzedniej nawigacji.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync` zdarzenia i pliki zestawu o zmienionej nazwie

Moduł ładujący zasoby opiera się na nazwach zestawów, które są zdefiniowane w `blazor.boot.json` pliku. W przypadku [zmiany nazwy zestawów](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)nazwy zestawów używane w `OnNavigateAsync` metodach i nazwach zestawów w `blazor.boot.json` pliku nie są zsynchronizowane.

Aby to skorygować:

* Sprawdź, czy aplikacja jest uruchomiona w środowisku produkcyjnym podczas określania, które nazwy zestawów mają być używane.
* Zapisz nazwy zestawów o zmienionej nazwie w osobnym pliku i odczytaj z tego pliku, aby określić nazwę zestawu, który ma być używany w `LazyLoadAssemblyService` `OnNavigateAsync` metodach i.

### <a name="complete-example"></a>Pełny przykład

Poniższy kompletny `Router` składnik pokazuje ładowania zestawu, `GrantImaharaRobotControls.dll` gdy użytkownik nawiguje do `/robot` . Podczas przechodzenia strony do użytkownika zostanie wyświetlony komunikat z stylem.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

* W przypadku wystąpienia nieoczekiwanego renderowania (na przykład składnika z poprzedniej nawigacji jest renderowany) Upewnij się, że kod zgłasza, czy ustawiono token anulowania.
* Jeśli zestawy są nadal ładowane podczas uruchamiania aplikacji, sprawdź, czy zestaw jest oznaczony jako opóźniony załadowany w pliku projektu.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/webassembly-performance-best-practices>
