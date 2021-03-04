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
<span data-ttu-id="6167d-101">Blazor jest strukturą po stronie klienta jednostronicowej aplikacji (SPA).</span><span class="sxs-lookup"><span data-stu-id="6167d-101">Blazor is a single-page application (SPA) client-side framework.</span></span> <span data-ttu-id="6167d-102">Przeglądarka pełni rolę hosta aplikacji i w ten sposób działa jako potok przetwarzania poszczególnych Razor składników w oparciu o żądania URI dotyczące nawigacji i zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="6167d-102">The browser serves as the app's host and thus acts as the processing pipeline for individual Razor components based on URI requests for navigation and static assets.</span></span> <span data-ttu-id="6167d-103">W przeciwieństwie do ASP.NET Core aplikacji uruchamianych na serwerze z potokiem przetwarzania oprogramowania pośredniczącego, nie istnieje potok oprogramowania pośredniczącego, który przetwarza żądania dotyczące Razor składników, których można użyć do obsługi błędów globalnych.</span><span class="sxs-lookup"><span data-stu-id="6167d-103">Unlike ASP.NET Core apps that run on the server with a middleware processing pipeline, there is no middleware pipeline that processes requests for Razor components that can be leveraged for global error handling.</span></span> <span data-ttu-id="6167d-104">Jednak aplikacja może używać składnika przetwarzania błędów jako wartości kaskadowej do przetwarzania błędów w scentralizowany sposób.</span><span class="sxs-lookup"><span data-stu-id="6167d-104">However, an app can use an error processing component as a cascading value to process errors in a centralized way.</span></span>

<span data-ttu-id="6167d-105">Poniższy `Error` składnik przekazuje sam siebie jako [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) składnik do elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="6167d-105">The following `Error` component passes itself as a [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) to child components.</span></span> <span data-ttu-id="6167d-106">Poniższy przykład powoduje jedynie rejestrowanie błędu, ale metody składnika mogą przetwarzać błędy w dowolny sposób wymagany przez aplikację, w tym za pomocą wielu metod przetwarzania błędów.</span><span class="sxs-lookup"><span data-stu-id="6167d-106">The following example merely logs the error, but methods of the component can process errors in any way required by the app, including through the use of multiple error processing methods.</span></span> <span data-ttu-id="6167d-107">Zaletą korzystania z składnika przez użycie [wstrzykniętej usługi](xref:blazor/fundamentals/dependency-injection) lub niestandardowej implementacji rejestratora jest to, że składnik kaskadowy może renderować zawartość i stosować style CSS w przypadku wystąpienia błędu.</span><span class="sxs-lookup"><span data-stu-id="6167d-107">An advantage of using a component over using an [injected service](xref:blazor/fundamentals/dependency-injection) or a custom logger implementation is that a cascaded component can render content and apply CSS styles when an error occurs.</span></span>

<span data-ttu-id="6167d-108">`Shared/Error.razor`:</span><span class="sxs-lookup"><span data-stu-id="6167d-108">`Shared/Error.razor`:</span></span>

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

<span data-ttu-id="6167d-109">W `App` składniku zawiń składnik na składnik `Router` `Error` .</span><span class="sxs-lookup"><span data-stu-id="6167d-109">In the `App` component, wrap the `Router` component with the `Error` component.</span></span> <span data-ttu-id="6167d-110">Pozwala to `Error` składnikowi na kaskadowe umieszczanie w dowolnym składniku aplikacji, w której `Error` składnik został odebrany jako [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) .</span><span class="sxs-lookup"><span data-stu-id="6167d-110">This permits the `Error` component to cascade down to any component of the app where the `Error` component is received as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute).</span></span>

<span data-ttu-id="6167d-111">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="6167d-111">`App.razor`:</span></span>

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

<span data-ttu-id="6167d-112">Aby przetworzyć błędy w składniku:</span><span class="sxs-lookup"><span data-stu-id="6167d-112">To process errors in a component:</span></span>

* <span data-ttu-id="6167d-113">Wyznacz `Error` składnik jako [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) w [`@code`](xref:mvc/views/razor#code) bloku:</span><span class="sxs-lookup"><span data-stu-id="6167d-113">Designate the `Error` component as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) in the [`@code`](xref:mvc/views/razor#code) block:</span></span>

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* <span data-ttu-id="6167d-114">Wywołaj metodę przetwarzania błędu w dowolnym `catch` bloku z odpowiednim typem wyjątku.</span><span class="sxs-lookup"><span data-stu-id="6167d-114">Call an error processing method in any `catch` block with an appropriate exception type.</span></span> <span data-ttu-id="6167d-115">Przykładowy `Error` składnik oferuje tylko jedną `ProcessError` metodę, ale składnik przetwarzania błędów może zapewnić dowolną liczbę metod przetwarzania błędów, aby rozwiązać alternatywne wymagania dotyczące przetwarzania błędów w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6167d-115">The example `Error` component only offers a single `ProcessError` method, but the error processing component can provide any number of error processing methods to address alternative error processing requirements throughout the app.</span></span>

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

<span data-ttu-id="6167d-116">Korzystając z powyższego przykładowego `Error` składnika i `ProcessError` metody, konsola narzędzi deweloperskich w przeglądarce wskazuje zarejestrowanego błędu:</span><span class="sxs-lookup"><span data-stu-id="6167d-116">Using the preceding example `Error` component and `ProcessError` method, the browser's developer tools console indicates the trapped, logged error:</span></span>

> <span data-ttu-id="6167d-117">Niepowodzenie: Blazor Sample. Shared. Error [0] Error: ProcessError-Type: System. NullReferenceException Message: odwołanie do obiektu nie jest ustawione na wystąpienie obiektu.</span><span class="sxs-lookup"><span data-stu-id="6167d-117">fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException Message: Object reference not set to an instance of an object.</span></span>

<span data-ttu-id="6167d-118">Jeśli `ProcessError` Metoda bezpośrednio uczestniczy w renderowaniu, na przykład wyświetlając niestandardowy pasek komunikatów o błędach lub zmieniając style CSS renderowane elementy, wywołaj [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) na końcu `ProcessErrors` metody, aby przetworzyć interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="6167d-118">If the `ProcessError` method directly participates in rendering, such as showing a custom error message bar or changing the CSS styles of the rendered elements, call [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) at the end of the `ProcessErrors` method to rerender the UI.</span></span>
