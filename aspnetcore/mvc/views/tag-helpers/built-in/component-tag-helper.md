---
title: Pomocnik znaczników składników w ASP.NET rdzeniu
author: guardrex
ms.author: riande
description: Dowiedz się, jak używać pomocnika ASP.NET Core Component Tag Helper do renderowania składników Razor na stronach i widokach.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440964"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Pomocnik znaczników składników w ASP.NET rdzeniu

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

Aby renderować składnik ze strony lub widoku, użyj [pomocnika znacznika składnika](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie ze wskazówkami w *przygotowaniu aplikacji do używania składników w* sekcji strony i widoki <xref:blazor/integrate-components#prepare-the-app> artykułu.

## <a name="component-tag-helper"></a>Pomocnik znaczników składników

Pomocnik znacznika składnika renderuje składnik na `Counter` stronie lub w widoku:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

W poprzednim przykładzie przyjęto założenie, że `Counter` składnik znajduje się w folderze *Strony* aplikacji.

Pomocnik znaczników składnika może również przekazywać parametry do komponentów. Należy wziąć `ColorfulCheckbox` pod uwagę następujący składnik, który ustawia kolor i rozmiar etykiety pola wyboru:

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

Parametry `Size` `int` [komponentu](xref:blazor/components#component-parameters) `string`( ) i `Color` ( ) mogą być ustawiane przez pomocnika znacznika komponentu:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

W poprzednim przykładzie przyjęto założenie, że `ColorfulCheckbox` składnik znajduje się w folderze *udostępnionym* aplikacji.

Na stronie lub w widoku renderowany jest następujący kod HTML:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Przekazywanie cytowanego ciągu wymaga [jawnego wyrażenia Razor](xref:mvc/views/razor#explicit-razor-expressions), jak pokazano `param-Color` w poprzednim przykładzie. Razor analizowanie zachowanie dla `string` wartości typu nie ma `param-*` zastosowania do atrybutu, `object` ponieważ atrybut jest typem.

Typ parametru musi być serializowalny JSON, co zazwyczaj oznacza, że typ musi mieć domyślny konstruktor i właściwości settable. Na przykład można określić `Size` wartość `Color` dla i w poprzednim `Size` przykładzie, ponieważ typy i `Color` są typami pierwotnymi (`int` i `string`), które są obsługiwane przez serializatorA JSON.

W poniższym przykładzie obiekt klasy jest przekazywany do składnika:

*MyClass.cs:*

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

**Klasa musi mieć publiczny konstruktor bez parametrów.**

*Udostępnione/MyComponent.brzytwa*:

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

*Strony/MyPage.cshtml*:

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

W poprzednim przykładzie przyjęto założenie, że `MyComponent` składnik znajduje się w folderze *udostępnionym* aplikacji. `MyClass`znajduje się w obszarze nazw`{APP ASSEMBLY}`aplikacji ( ).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>określa, czy składnik:

* Jest prerendered do strony.
* Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.

| Tryb renderowania | Opis |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderuje składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera. Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderuje znacznik aplikacji Blazor serwera. Dane wyjściowe ze składnika nie są uwzględniane. Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderuje składnik do statycznego kodu HTML. |

Podczas gdy strony i widoki mogą używać składników, odwrotność nie jest prawdziwa. Składniki nie mogą używać funkcji specyficznych dla widoku i strony, takich jak widoki częściowe i sekcje. Aby użyć logiki z widoku częściowego w komponencie, należy uwzględnić logikę widoku częściowego w składniku.

Renderowanie składników serwera ze statycznej strony HTML nie jest obsługiwane.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
