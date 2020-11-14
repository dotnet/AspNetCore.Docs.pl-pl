---
title: Pomocnik tagu składnika w ASP.NET Core
author: guardrex
ms.author: riande
description: Dowiedz się, jak używać pomocnika tagów składnika ASP.NET Core, aby renderować Razor składniki na stronach i widokach.
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595457"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Pomocnik tagu składnika w ASP.NET Core

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

## <a name="prerequisites"></a>Wymagania wstępne

::: moniker range=">= aspnetcore-5.0"

Postępuj zgodnie ze wskazówkami w sekcji *konfiguracji* dla następujących elementów:

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Postępuj zgodnie ze wskazówkami w sekcji *Konfiguracja* <xref:blazor/components/prerendering-and-integration?pivots=server> artykułu.

::: moniker-end

## <a name="component-tag-helper"></a>Pomocnik tagów składnika

Aby renderować składnik ze strony lub widoku, użyj [pomocnika tagów składnika](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag).

> [!NOTE]
> Integrowanie Razor składników na Razor stronach i aplikacjach MVC w *hostowanej Blazor WebAssembly aplikacji* jest obsługiwane w ASP.NET Core na platformie .NET 5,0 lub nowszej.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> Określa, czy składnik:

* Jest wstępnie renderowany na stronie.
* Jest renderowany jako statyczny kod HTML na stronie lub zawiera informacje niezbędne do uruchomienia Blazor aplikacji przez agenta użytkownika.

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly w poniższej tabeli przedstawiono tryby renderowania aplikacji.

| Tryb renderowania | Opis |
| ----------- | ----------- |
| `WebAssembly` | Renderuje znacznik dla Blazor WebAssembly aplikacji w celu uwzględnienia składnika interaktywnego, gdy jest ładowany w przeglądarce. Składnik nie jest wstępnie renderowany. Ta opcja ułatwia renderowanie różnych Blazor WebAssembly składników na różnych stronach. |
| `WebAssemblyPrerendered` | Wstępnie renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor WebAssembly aplikacji w celu późniejszego użycia w celu zapewnienia, że składnik będzie interaktywny po załadowaniu w przeglądarce. |

Blazor Server w poniższej tabeli przedstawiono tryby renderowania aplikacji.

| Tryb renderowania | Opis |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor Server aplikacji. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje składnik do statycznego kodu HTML. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server w poniższej tabeli przedstawiono tryby renderowania aplikacji.

| Tryb renderowania | Opis |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje składnik do statycznego kodu HTML i zawiera znacznik dla Blazor Server aplikacji. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik dla Blazor Server aplikacji. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany do uruchamiania Blazor aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje składnik do statycznego kodu HTML. |

::: moniker-end

Dodatkowe cechy obejmują:

* Wiele pomocników tagów składnika renderuje wiele Razor składników jest dozwolonych.
* Nie można dynamicznie renderować składników po rozpoczęciu aplikacji.
* Podczas gdy strony i widoki mogą korzystać ze składników, wartość nie jest równa "true". Składniki nie mogą korzystać z funkcji specjalnych, takich jak widoki częściowe i sekcje. Aby użyć logiki z widoku częściowego w składniku, należy rozłożyć logikę widoku częściowego na składnik.
* Renderowanie składników serwera ze statyczną stroną HTML nie jest obsługiwane.

Poniższy pomocnik tagów składnika renderuje `Counter` składnik na stronie lub widoku w Blazor Server aplikacji za pomocą `ServerPrerendered` :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

W poprzednim przykładzie przyjęto założenie, że `Counter` składnik znajduje się w folderze *strony* aplikacji. Symbol zastępczy `{APP ASSEMBLY}` to nazwa zestawu aplikacji (na przykład `@using BlazorSample.Pages` lub `@using BlazorSample.Client.Pages` w rozwiązaniu hostowanym Blazor ).

Pomocnik tagów składnika może również przekazywać parametry do składników. Rozważmy poniższy `ColorfulCheckbox` składnik, który ustawia kolor i rozmiar etykiety pola wyboru:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

`Size` `int` Parametry składnika () `Color` i `string` ( [component parameters](xref:blazor/components/index#component-parameters) ) mogą być ustawiane przez pomocnika tagów składnika:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

W poprzednim przykładzie przyjęto założenie, że `ColorfulCheckbox` składnik znajduje się w folderze *udostępnionym* aplikacji. Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample.Shared` ).

Następujący kod HTML jest renderowany na stronie lub w widoku:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Przekazywanie ciągu w cudzysłowie wymaga [ Razor wyrażenia jawnego](xref:mvc/views/razor#explicit-razor-expressions), jak pokazano `param-Color` w powyższym przykładzie. RazorZachowanie analizy dla `string` wartości typu nie ma zastosowania do atrybutu, `param-*` ponieważ atrybut jest `object` typem.

Wszystkie typy parametrów są obsługiwane, z wyjątkiem:

* Parametry ogólne.
* Parametry, których nie można serializować.
* Dziedziczenie w parametrach kolekcji.
* Parametry, których typ jest zdefiniowany poza Blazor WebAssembly aplikacją lub w ramach zestawu załadowanego przez opóźnieniem.

Typ parametru musi być możliwy do serializacji JSON, co oznacza, że typ musi mieć domyślny Konstruktor i właściwości settable. Na przykład można określić wartość dla `Size` i `Color` w poprzednim przykładzie, ponieważ typy `Size` i `Color` są typami pierwotnymi ( `int` i `string` ), które są obsługiwane przez serializator JSON.

W poniższym przykładzie obiekt klasy jest przenoszona do składnika:

*MyClass.cs* :

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**Klasa musi mieć publiczny Konstruktor bez parametrów.**

*Shared/webcomponent. Razor* :

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Strony/Moje strony. cshtml* :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

W poprzednim przykładzie przyjęto założenie, że `MyComponent` składnik znajduje się w folderze *udostępnionym* aplikacji. Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `@using BlazorSample` i `@using BlazorSample.Shared` ). `MyClass` znajduje się w przestrzeni nazw aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
