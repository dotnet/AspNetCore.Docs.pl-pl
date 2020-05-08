---
title: ASP.NET Core Blazor powiązania danych
author: guardrex
description: Dowiedz się więcej o funkcjach powiązań danych dla składników Blazor i elementów modelu dom w aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: b4951c5eb712b15db3a7c1ccd57ae01c530a23ef
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967171"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor powiązania danych

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

Razorskładniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML [`@bind`](xref:mvc/views/razor#bind) o nazwie z wartością pola, właściwości Razor lub wyrażenia.

Poniższy przykład wiąże `CurrentValue` właściwość z wartością pola tekstowego:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Gdy pole tekstowe utraci fokus, wartość właściwości jest aktualizowana.

Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości właściwości. Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.

Używanie `@bind` z `CurrentValue` właściwością (`<input @bind="CurrentValue" />`) jest zasadniczo równoważne z następującymi:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości. Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` właściwość jest ustawiana na wartość zmieniona. W rzeczywistości generowanie kodu jest bardziej skomplikowane, ponieważ `@bind` obsługuje przypadki, w których są wykonywane konwersje typów. W zasadzie `@bind` kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.

Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem. Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

W przeciwieństwie do `onchange`, które jest wyzwalane, gdy `oninput` element utraci fokus, jest uruchamiany po zmianie wartości pola tekstowego.

Użyj `@bind-{ATTRIBUTE}` składni `@bind-{ATTRIBUTE}:event` with, aby powiązać atrybuty elementu inne `value`niż. W poniższym przykładzie styl akapitu zostanie zaktualizowany po zmianie `paragraphStyle` wartości:

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

W powiązaniu atrybutu rozróżniana jest wielkość liter. Na przykład `@bind` prawidłowe i `@Bind` jest nieprawidłowe.

## <a name="unparsable-values"></a>Wartości niemożliwy do przeanalizowania

Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.

Poniżej przedstawiono przykładowy scenariusz:

* `<input>` Element jest powiązany z `int` typem z początkową wartością `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.

W poprzednim scenariuszu wartość elementu jest przywracana `123`. Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123`, użytkownik rozumie, że ich wartość nie została zaakceptowana.

Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu (`@bind="{PROPERTY OR FIELD}"`). Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu. W przypadku `oninput` zdarzenia (`@bind:event="oninput"`) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość. Podczas określania wartości `oninput` docelowej dla zdarzenia `int`z typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku. `.` Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite. Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia `<input>` wartości, która nie może być przewidziana. Alternatywy obejmują:

* Nie używaj `oninput` zdarzenia. Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"`), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.
* Powiąż z typem dopuszczającym wartość `int?` null `string`, taką jak lub, i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.
* Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak `InputNumber` lub `InputDate`. Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi. Składniki walidacji formularza:
  * Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym `EditContext`.
  * Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.

## <a name="format-strings"></a>Ciągi formatujące

Powiązanie danych działa z <xref:System.DateTime> ciągami formatu [`@bind:format`](xref:mvc/views/razor#bind)przy użyciu. W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

W poprzednim kodzie typ pola ( `<input>` `type`) elementu jest wartością domyślną `text`. `@bind:format`jest obsługiwana w celu powiązania następujących typów .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany `value` do `<input>` elementu. Format jest również używany do analizowania wartości w przypadku wystąpienia `onchange` zdarzenia.

Określanie formatu dla typu `date` pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat. Pomimo zalecenia, należy używać tylko formatu `yyyy-MM-dd` daty do poprawnego działania, jeśli format jest dostarczany z typem `date` pola:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Powiązanie element nadrzędny-to-Child z parametrami składnika

Powiązanie rozpoznaje parametry składnika, gdzie `@bind-{PROPERTY}` można powiązać wartość właściwości z składnika nadrzędnego w dół ze składnikiem podrzędnym. Powiązanie z elementu podrzędnego z elementem nadrzędnym jest omówione w [powiązaniu podrzędnie-to-Parent z częściowym powiązaniem powiązania](#child-to-parent-binding-with-chained-bind) .

Następujący składnik podrzędny (`ChildComponent`) ma parametr `Year` składnika i `YearChanged` wywołanie zwrotne:

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

`EventCallback<T>`wyjaśniono w <xref:blazor/event-handling#eventcallback>temacie.

Poniższy składnik nadrzędny używa:

* `ChildComponent`i wiąże `ParentYear` parametr z elementu nadrzędnego z `Year` parametrem w składniku podrzędnym.
* To `onclick` zdarzenie służy do wyzwalania `ChangeTheYear` metody. Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.

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

Załadowanie `ParentComponent` powoduje utworzenie następującej adjustacji:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Jeśli wartość `ParentYear` właściwości zostanie zmieniona przez wybranie przycisku w `ParentComponent`, `Year` Właściwość `ChildComponent` jest aktualizowana. Nowa wartość `Year` jest renderowana w interfejsie użytkownika podczas jego `ParentComponent` renderowania:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.

Zgodnie z Konwencją, `<ChildComponent @bind-Year="ParentYear" />` jest zasadniczo równoważne zapisowi:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Ogólnie rzecz biorąc, właściwość może być powiązana z odpowiadającą jej obsługą zdarzeń `@bind-{PROPRETY}:event` przez uwzględnienie atrybutu. Na przykład właściwość `MyProp` może być powiązana z `MyEventHandler` użyciem następujących dwóch atrybutów:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Powiązanie elementu podrzędnego z elementem nadrzędnym z powiązaniem łańcuchowym

Typowy scenariusz polega na łańcuchu parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika. Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.

Nie można zaimplementować powiązania łańcuchowego przy użyciu `@bind` składni w elemencie strony. Program obsługi zdarzeń i wartość muszą być określone osobno. Składnik nadrzędny, jednak może używać `@bind` składni z parametrem składnika.

Następujący `PasswordField` składnik (*PasswordField. Razor*):

* Ustawia wartość `<input>` elementu na `Password` właściwość.
* Uwidacznia zmiany `Password` właściwości w składniku nadrzędnym z [EventCallback](xref:blazor/event-handling#eventcallback).
* Używa `onclick` zdarzenia, aby wyzwolić `ToggleShowPassword` metodę. Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

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
        showPassword = !showPassword;
    }
}
```

`PasswordField` Składnik jest używany w innym składniku:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Aby przeprowadzić sprawdzenia lub błędy pułapki dla hasła w poprzednim przykładzie:

* Utwórz pole zapasowe dla `Password` (`password` w poniższym przykładowym kodzie).
* Wykonaj testy lub błędy pułapek w metodzie `Password` ustawiającej.

Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
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
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a>Przyciski radiowe

Aby uzyskać informacje na temat tworzenia powiązań z przyciskami radiowymi <xref:blazor/forms-validation#work-with-radio-buttons>w formularzu, zobacz.
