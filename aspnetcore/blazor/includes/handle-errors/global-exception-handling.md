---
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
ms.openlocfilehash: 1aa36c8d91dbd92485e85f223f2391303bebac42
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109715"
---
Blazor jest strukturą po stronie klienta jednostronicowej aplikacji (SPA). Przeglądarka pełni rolę hosta aplikacji i w ten sposób działa jako potok przetwarzania poszczególnych Razor składników w oparciu o żądania URI dotyczące nawigacji i zasobów statycznych. W przeciwieństwie do ASP.NET Core aplikacji uruchamianych na serwerze z potokiem przetwarzania oprogramowania pośredniczącego, nie istnieje potok oprogramowania pośredniczącego, który przetwarza żądania dotyczące Razor składników, których można użyć do obsługi błędów globalnych. Jednak aplikacja może używać składnika przetwarzania błędów jako wartości kaskadowej do przetwarzania błędów w scentralizowany sposób.

Poniższy `Error` składnik przekazuje sam siebie jako [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) składnik do elementów podrzędnych. Poniższy przykład powoduje jedynie rejestrowanie błędu, ale metody składnika mogą przetwarzać błędy w dowolny sposób wymagany przez aplikację, w tym za pomocą wielu metod przetwarzania błędów. Zaletą korzystania z składnika przez użycie [wstrzykniętej usługi](xref:blazor/fundamentals/dependency-injection) lub niestandardowej implementacji rejestratora jest to, że składnik kaskadowy może renderować zawartość i stosować style CSS w przypadku wystąpienia błędu.

`Shared/Error.razor`:

```razor
@using Microsoft.Extensions.Logging
@inject ILogger<Error> Logger

<CascadingValue Value=this>
    @ChildContent
</CascadingValue>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public void ProcessError(Exception ex)
    {
        Logger.LogError("Error:ProcessError - Type: {Type} Message: {Message}", 
            ex.GetType(), ex.Message);
    }
}
```

W `App` składniku zawiń składnik na składnik `Router` `Error` . Pozwala to `Error` składnikowi na kaskadowe umieszczanie w dowolnym składniku aplikacji, w której `Error` składnik został odebrany jako [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) .

`App.razor`:

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

Aby przetworzyć błędy w składniku:

* Wyznacz `Error` składnik jako [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) w [`@code`](xref:mvc/views/razor#code) bloku:

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* Wywołaj metodę przetwarzania błędu w dowolnym `catch` bloku z odpowiednim typem wyjątku. Przykładowy `Error` składnik oferuje tylko jedną `ProcessError` metodę, ale składnik przetwarzania błędów może zapewnić dowolną liczbę metod przetwarzania błędów, aby rozwiązać alternatywne wymagania dotyczące przetwarzania błędów w całej aplikacji.

  ```csharp
  try
  {
      ...
  }
  catch (Exception ex)
  {
      Error.ProcessError(ex);
  }
  ```

Korzystając z powyższego przykładowego `Error` składnika i `ProcessError` metody, konsola narzędzi deweloperskich w przeglądarce wskazuje zarejestrowanego błędu:

> Niepowodzenie: Blazor Sample. Shared. Error [0] Error: ProcessError-Type: System. NullReferenceException Message: odwołanie do obiektu nie jest ustawione na wystąpienie obiektu.

Jeśli `ProcessError` Metoda bezpośrednio uczestniczy w renderowaniu, na przykład wyświetlając niestandardowy pasek komunikatów o błędach lub zmieniając style CSS renderowane elementy, wywołaj [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) na końcu `ProcessErrors` metody, aby przetworzyć interfejs użytkownika.
