---
title: ASP.NET podstawowe Blazor powiązanie danych
author: guardrex
description: Dowiedz się więcej o funkcjach Blazor wiązania danych dla składników i elementów DOM w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320950"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET podstawowe Blazor powiązanie danych

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

Składniki maszynki do golenia zapewniają funkcje [`@bind`](xref:mvc/views/razor#bind) wiązania danych za pośrednictwem atrybutu elementu HTML o nazwie z wartością wyrażenia pole, właściwość lub razor.

Poniższy przykład wiąże `CurrentValue` właściwość z wartością pola tekstowego:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Gdy pole tekstowe traci fokus, wartość właściwości jest aktualizowana.

Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości właściwości. Ponieważ składniki renderowania się po wykonaniu kodu obsługi zdarzeń, aktualizacje właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.

Korzystanie `@bind` z `CurrentValue` właściwości`<input @bind="CurrentValue" />`( ) jest zasadniczo równoważne z następującymi:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Gdy składnik jest renderowany, `value` element wejściowy `CurrentValue` pochodzi z właściwości. Gdy użytkownik wpisuje w polu tekstowym `onchange` i zmienia fokus elementu, zdarzenie jest uruchamiane i `CurrentValue` właściwość jest ustawiona na zmienioną wartość. W rzeczywistości generowanie kodu jest `@bind` bardziej złożone, ponieważ obsługuje przypadki, w których są wykonywane konwersje typu. Zasadniczo `@bind` kojarzy bieżącą wartość wyrażenia `value` z atrybutem i obsługuje zmiany przy użyciu zarejestrowanego programu obsługi.

Powiąż właściwość lub pole `@bind:event` z innymi `event` zdarzeniami, dołączając również atrybut z parametrem. Poniższy przykład wiąże `CurrentValue` właściwość `oninput` ze zdarzeniem:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

W `onchange`przeciwieństwie do , który uruchamia `oninput` się, gdy element traci fokus, uruchamia się, gdy zmienia się wartość pola tekstowego.

Użyj `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` ze składnią do wiązania `value`atrybutów elementów innych niż . W poniższym przykładzie styl akapitu `_paragraphStyle` jest aktualizowany po zmianie wartości:

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

Rozróżniana wielkość liter. Na przykład `@bind` jest prawidłowy i `@Bind` jest nieprawidłowy.

## <a name="unparsable-values"></a>Wartości niedajne

Gdy użytkownik udostępnia nieparadowalną wartość elementu połączenia danych, wartość nieparasable jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia powiązania.

Poniżej przedstawiono przykładowy scenariusz:

* Element `<input>` jest powiązany `int` z typem `123`o wartości początkowej:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Użytkownik aktualizuje wartość elementu `123.45` na stronie i zmienia fokus elementu.

W poprzednim scenariuszu wartość elementu jest przywracana `123`do . Gdy wartość `123.45` zostanie odrzucona na rzecz `123`oryginalnej wartości , użytkownik rozumie, że ich wartość nie została zaakceptowana.

Domyślnie powiązanie ma zastosowanie do `onchange` zdarzenia`@bind="{PROPERTY OR FIELD}"`elementu ( ). Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązania dla innego zdarzenia. W `oninput` przypadku`@bind:event="oninput"`zdarzenia ( ), odwrócenie występuje po każdym naciśnięciu klawisza, który wprowadza wartość niedościsobie. Podczas kierowania `oninput` zdarzenia z `int`typem powiązanym użytkownik nie może `.` wpisywać znaku. Znak `.` jest natychmiast usuwany, więc użytkownik otrzymuje natychmiastową informację, że dozwolone są tylko liczby pełne. Istnieją scenariusze, w których przywracanie wartości w zdarzeniu `oninput` nie jest idealne, na przykład gdy `<input>` użytkownik powinien mieć możliwość wyczyszczenia wartości niepardziellnej. Alternatywy obejmują:

* Nie używaj `oninput` zdarzenia. Użyj zdarzenia `onchange` domyślnego `@bind="{PROPERTY OR FIELD}"`(tylko określić ), gdzie nieprawidłowa wartość nie jest przywracana, dopóki element traci fokus.
* Powiązanie z typem nullable, takich jak `int?` lub `string`, i podać niestandardową logikę do obsługi nieprawidłowych wpisów.
* Użyj [składnika sprawdzania poprawności formularza,](xref:blazor/forms-validation)takiego jak `InputNumber` lub `InputDate`. Składniki sprawdzania poprawności formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi. Składniki sprawdzania poprawności formularza:
  * Zezwalaj użytkownikowi na podanie nieprawidłowych danych wejściowych `EditContext`i odbierania błędów sprawdzania poprawności na skojarzonym programie .
  * Wyświetlanie błędów sprawdzania poprawności w interfejsie użytkownika bez zakłócania wprowadzania przez użytkownika dodatkowych danych webform.

## <a name="format-strings"></a>Formatowanie ciągów

Powiązanie danych <xref:System.DateTime> działa z [`@bind:format`](xref:mvc/views/razor#bind)ciągami formatu przy użyciu programu . Inne wyrażenia formatu, takie jak formaty walut lub liczb, nie są obecnie dostępne.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

W poprzednim kodzie `<input>` typ pola elementu`type`( ) `text`domyślnie ma wartość . `@bind:format`jest obsługiwana w celu powiązania następujących typów .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Atrybut `@bind:format` określa format daty, który `value` ma `<input>` być stosowany do elementu. Format jest również używany do analizowania `onchange` wartości w przypadku wystąpienia zdarzenia.

Określanie formatu `date` dla typu pola nie Blazor jest zalecane, ponieważ ma wbudowaną obsługę formatowania dat. Pomimo zalecenia, należy używać `yyyy-MM-dd` tylko format daty do wiązania do poprawnego działania, jeśli format jest dostarczany z typem `date` pola:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Powiązanie nadrzędny-podrzędny z parametrami komponentu

Powiązanie rozpoznaje parametry `@bind-{PROPERTY}` składnika, gdzie można powiązać wartość właściwości ze składnika nadrzędnego w dół do składnika podrzędnego. Powiązanie z elementem podrzędnym do rodzica jest objęte [Child-to-parent powiązania z sieciowej](#child-to-parent-binding-with-chained-bind) sekcji powiązania.

Następujący składnik podrzędny`ChildComponent`( `Year` ) `YearChanged` ma parametr składnika i wywołanie zwrotne:

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`wyjaśniono w <xref:blazor/event-handling#eventcallback>.

Następujący składnik nadrzędny używa:

* `ChildComponent`i wiąże `ParentYear` parametr z elementem `Year` nadrzędnym z parametrem w komponencie podrzędnym.
* Zdarzenie `onclick` jest używane do `ChangeTheYear` wyzwalania metody. Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Ładowanie `ParentComponent` powoduje następujące znaczniki:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Jeśli wartość właściwości `ParentYear` zostanie zmieniona przez wybranie `ParentComponent`przycisku w , `Year` właściwość `ChildComponent` jest aktualizowana. Nowa wartość `Year` jest renderowana w interfejsie `ParentComponent` użytkownika, gdy jest rerendered:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Parametr `Year` jest wiązany, ponieważ `YearChanged` ma zdarzenie towarzyszące, które pasuje do typu parametru. `Year`

Zgodnie z `<ChildComponent @bind-Year="ParentYear" />` konwencją, jest zasadniczo równoważne piśmie:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Ogólnie rzecz biorąc właściwość może być powiązana `@bind-{PROPRETY}:event` z odpowiednim programem obsługi zdarzeń, dołączając atrybut. Na przykład właściwość `MyProp` może `MyEventHandler` być powiązana przy użyciu następujących dwóch atrybutów:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Powiązanie podrzędne z elementem nadrzędnym z powiązaniem łańcuchowym

Typowym scenariuszem jest łączenie parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika. Ten scenariusz jest nazywany *powiązanie łańcuchowe,* ponieważ wiele poziomów wiązania występują jednocześnie.

Powiązanie łańcuchowe nie może być `@bind` zaimplementowane ze składnią w elemencie strony. Program obsługi zdarzeń i wartość musi być określony oddzielnie. Składnik nadrzędny może jednak `@bind` używać składni z parametrem składnika.

Następujący `PasswordField` składnik (*PasswordField.brzytwa):*

* Ustawia `<input>` wartość elementu na `Password` właściwość.
* Udostępnia zmiany `Password` właściwości do składnika nadrzędnego z [EventCallback](xref:blazor/event-handling#eventcallback).
* Używa `onclick` zdarzenia jest używany do `ToggleShowPassword` wyzwalania metody. Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

Składnik `PasswordField` jest używany w innym składniku:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Aby wykonać błędy sprawdzania lub zalewkowania hasła w poprzednim przykładzie:

* Utwórz pole `Password` zapasowe dla (`_password` w poniższym przykładzie kodu).
* Wykonaj błędy kontroli lub zalewki w ustawiacza. `Password`

Poniższy przykład zapewnia natychmiastową informację zwrotną dla użytkownika, jeśli spacja jest używana w wartości hasła:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a>Przycisków

Aby uzyskać informacje na temat powiązania <xref:blazor/forms-validation#work-with-radio-buttons>z przyciskami radiowymi w formularzu, zobacz .
