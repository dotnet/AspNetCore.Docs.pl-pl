---
title: ASP.NET Core Blazor powiązania danych
author: guardrex
description: Dowiedz się więcej o funkcjach powiązań danych dla składników i elementów modelu DOM w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3f823ca9cf96b7ff439ade59f946db222b7f7e60
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606694"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor powiązania danych

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Steve Sanderson](https://github.com/SteveSandersonMS)

Razor składniki zapewniają funkcje powiązań danych za pośrednictwem atrybutu elementu HTML o nazwie [`@bind`](xref:mvc/views/razor#bind) z wartością pola, właściwości lub Razor wyrażenia.

Poniższy przykład wiąże `<input>` element z `currentValue` polem i `<input>` elementem `CurrentValue` Właściwości:

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

Gdy jeden z elementów utraci fokus, jego powiązane pole lub właściwość jest aktualizowana.

Pole tekstowe jest aktualizowane w interfejsie użytkownika tylko wtedy, gdy składnik jest renderowany, a nie w odpowiedzi na zmianę wartości pola lub właściwości. Ponieważ składniki renderują się po wykonaniu kodu procedury obsługi zdarzeń, aktualizacje pól i właściwości są *zwykle* odzwierciedlane w interfejsie użytkownika natychmiast po wyzwoleniu programu obsługi zdarzeń.

Używanie [`@bind`](xref:mvc/views/razor#bind) z `CurrentValue` właściwością ( `<input @bind="CurrentValue" />` ) jest zasadniczo równoważne z następującymi:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Gdy składnik jest renderowany, `value` element wejściowy pochodzi z `CurrentValue` właściwości. Gdy użytkownik wpisze w polu tekstowym i zmieni fokus elementu, `onchange` zdarzenie jest wywoływane i `CurrentValue` Właściwość jest ustawiana na wartość zmieniona. W rzeczywistości generowanie kodu jest bardziej złożone niż to, ponieważ [`@bind`](xref:mvc/views/razor#bind) obsługuje przypadki, w których są wykonywane konwersje typów. W zasadzie [`@bind`](xref:mvc/views/razor#bind) kojarzy bieżącą wartość wyrażenia z `value` atrybutem i obsługuje zmiany przy użyciu zarejestrowanej procedury obsługi.

Powiąż właściwość lub pole w innych zdarzeniach, dołączając także `@bind:event` atrybut z `event` parametrem. Poniższy przykład wiąże `CurrentValue` Właściwość `oninput` zdarzenia:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

W przeciwieństwie do `onchange` , które jest wyzwalane, gdy element utraci fokus, `oninput` jest uruchamiany po zmianie wartości pola tekstowego.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

W powiązaniu atrybutu rozróżniana jest wielkość liter:

* `@bind` jest prawidłowy.
* `@Bind` i `@BIND` są nieprawidłowe.

## <a name="unparsable-values"></a>Wartości niemożliwy do przeanalizowania

Gdy użytkownik dostarczy wartość niemożliwy do przeanalizowania do elementu powiązanego z danymi, wartość niemożliwy do przeanalizowania jest automatycznie przywracana do poprzedniej wartości po wyzwoleniu zdarzenia bind.

Poniżej przedstawiono przykładowy scenariusz:

* `<input>`Element jest powiązany z `int` typem z początkową wartością `123` :

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* Użytkownik aktualizuje wartość elementu na `123.45` liście na stronie i zmienia fokus elementu.

W poprzednim scenariuszu wartość elementu jest przywracana `123` . Gdy wartość `123.45` zostanie odrzucona na korzyść oryginalnej wartości `123` , użytkownik rozumie, że ich wartość nie została zaakceptowana.

Domyślnie powiązanie dotyczy `onchange` zdarzenia elementu ( `@bind="{PROPERTY OR FIELD}"` ). Służy `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` do wyzwalania powiązań na innym zdarzeniu. W przypadku `oninput` zdarzenia ( `@bind:event="oninput"` ) jego wersja następuje po naciśnięciu klawisza, które wprowadza niemożliwy do przeanalizowania wartość. Podczas określania wartości docelowej dla `oninput` zdarzenia z `int` typem związanym użytkownik nie jest w trakcie wpisywania `.` znaku. `.`Znak zostanie natychmiast usunięty, więc użytkownik otrzymuje natychmiastową opinię, że dozwolone są tylko liczby całkowite. Istnieją scenariusze, w których przywrócenie wartości `oninput` zdarzenia nie jest idealne, na przykład wtedy, gdy użytkownik powinien mieć możliwość wyczyszczenia wartości, która nie może być przewidziana `<input>` . Alternatywy obejmują:

* Nie używaj `oninput` zdarzenia. Użyj zdarzenia domyślnego `onchange` (tylko określenie `@bind="{PROPERTY OR FIELD}"` ), gdzie nie jest przywracana nieprawidłowa wartość, dopóki element nie utraci fokusu.
* Powiąż z typem dopuszczającym wartość null, na przykład `int?` lub `string` i podaj logikę niestandardową do obsługi nieprawidłowych wpisów.
* Użyj [składnika walidacji formularza](xref:blazor/forms-validation), takiego jak <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> lub <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Składniki walidacji formularza mają wbudowaną obsługę zarządzania nieprawidłowymi danymi wejściowymi. Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation>. Składniki walidacji formularza:
  * Zezwalaj użytkownikowi na dostarczenie nieprawidłowych danych wejściowych i odbieranie błędów walidacji w skojarzonym <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Wyświetlaj błędy walidacji w interfejsie użytkownika bez zakłócania wprowadzania dodatkowych danych przez użytkownika.

## <a name="format-strings"></a>Ciągi formatujące

Powiązanie danych działa z <xref:System.DateTime> ciągami formatu przy użyciu `@bind:format` . W tej chwili nie są dostępne inne wyrażenia formatu, takie jak formaty walutowe lub liczbowe.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

W poprzednim kodzie `<input>` Typ pola () elementu jest `type` wartością domyślną `text` . `@bind:format` jest obsługiwana w celu powiązania następujących typów .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Ten `@bind:format` atrybut określa format daty, który ma zostać zastosowany do `value` `<input>` elementu. Format jest również używany do analizowania wartości w przypadku `onchange` wystąpienia zdarzenia.

Określanie formatu dla `date` typu pola nie jest zalecane, ponieważ Blazor ma wbudowaną obsługę formatowania dat. Pomimo zalecenia, należy używać tylko `yyyy-MM-dd` formatu daty dla powiązania, aby prawidłowo działać, jeśli format jest dostarczany z `date` typem pola:

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Powiązanie element nadrzędny-to-Child z parametrami składnika

Parametry składnika umożliwiają powiązanie właściwości i pól składnika nadrzędnego z `@bind-{PROPERTY OR FIELD}` składnią.

Następujący `Child` składnik ( `Shared/Child.razor` ) ma `Year` parametr składnika i `YearChanged` wywołanie zwrotne:

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

Wywołanie zwrotne ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) musi być nazwane jako nazwa parametru składnika, po którym następuje `Changed` sufiks "" `{PARAMETER NAME}Changed` . W poprzednim przykładzie wywołanie zwrotne ma nazwę `YearChanged` . Aby uzyskać więcej informacji na temat <xref:Microsoft.AspNetCore.Components.EventCallback%601> , zobacz <xref:blazor/components/event-handling#eventcallback> .

W poniższym `Parent` składniku ( `Parent.razor` ) `year` pole jest powiązane z `Year` parametrem składnika podrzędnego:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

`Year`Parametr jest możliwy do powiązania, ponieważ ma zdarzenie towarzyszące `YearChanged` pasujące do typu `Year` parametru.

Zgodnie z Konwencją Właściwość można powiązać z odpowiadającą jej obsługą zdarzeń, dołączając `@bind-{PROPERTY}:event` atrybut przypisany do procedury obsługi. `<Child @bind-Year="year" />` jest odpowiednikiem zapisu:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Powiązanie elementu podrzędnego z elementem nadrzędnym z powiązaniem łańcuchowym

Typowy scenariusz polega na łańcuchu parametru powiązanego z danymi do elementu strony w danych wyjściowych składnika. Ten scenariusz jest nazywany *powiązaniem łańcuchowym* , ponieważ wiele poziomów powiązań występuje jednocześnie.

Nie można zaimplementować powiązania łańcuchowego ze [`@bind`](xref:mvc/views/razor#bind) składnią w składniku podrzędnym. Program obsługi zdarzeń i wartość muszą być określone osobno. Składnik nadrzędny, jednak może używać [`@bind`](xref:mvc/views/razor#bind) składni z parametrem składnika podrzędnego.

Następujący `PasswordField` składnik ( `PasswordField.razor` ):

* Ustawia `<input>` wartość elementu na `password` pole.
* Uwidacznia zmiany właściwości w `Password` składniku nadrzędnym [`EventCallback`](xref:blazor/components/event-handling#eventcallback) , który przekazuje w bieżącej wartości pola elementu podrzędnego `password` jako argument.
* Używa `onclick` zdarzenia do wyzwolenia `ToggleShowPassword` metody. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling>.

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField`Składnik jest używany w innym składniku:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Wykonaj sprawdzenia lub błędy pułapki w metodzie, która wywołuje delegata powiązania. Poniższy przykład przedstawia natychmiastową opinię dla użytkownika, jeśli w wartości hasła jest używana spacja:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="bind-across-more-than-two-components"></a>Powiązywanie w więcej niż dwóch składnikach

Można powiązać dowolną liczbę składników zagnieżdżonych, ale należy przestrzegać jednokierunkowego przepływu danych:

* Powiadomienia o zmianach *przepływają w górę hierarchii*.
* Nowe wartości parametrów *przepływają w dół hierarchii*.

Typowym i Zalecanym podejściem jest przechowywanie wyłącznie danych źródłowych w składniku nadrzędnym, aby uniknąć wszelkich pomyłek dotyczących stanu, który należy zaktualizować.

Poniższe składniki przedstawiają powyższe koncepcje:

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

W przypadku alternatywnego podejścia przystosowanego do udostępniania danych w pamięci między składnikami, które nie są niekoniecznie zagnieżdżone, zobacz <xref:blazor/state-management#in-memory-state-container-service> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Powiązywanie z przyciskami radiowymi w formularzu](xref:blazor/forms-validation#radio-buttons)
* [Powiązywanie `<select>` opcji elementu z wartościami obiektów C# `null` w formularzu](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
