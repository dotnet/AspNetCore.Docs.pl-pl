---
title: Wywoływanie internetowego interfejsu API z Blazor programu ASP.NET Core webassembly
author: guardrex
description: Dowiedz się, jak wywołać interfejs API sieci Blazor Web z aplikacji webassembly przy użyciu pomocników JSON, w tym do tworzenia żądań wymiany zasobów między źródłami (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767151"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikacje webassembly wywołują interfejsy API sieci Web przy użyciu `HttpClient` wstępnie skonfigurowanej usługi. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, za Blazor pomocą pomocników JSON lub <xref:System.Net.Http.HttpRequestMessage>z. `HttpClient` Usługa w Blazor aplikacjach webassembly koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia. Wskazówki zawarte w tym temacie dotyczą Blazor tylko aplikacji webassembly.

Aplikacje serwera wywołują interfejsy API <xref:System.Net.Http.HttpClient> sieci Web przy użyciu wystąpień <xref:System.Net.Http.IHttpClientFactory>, zwykle utworzonych przy użyciu. [ Blazor ](xref:blazor/hosting-models#blazor-server) Wskazówki zawarte w tym temacie nie odnoszą Blazor się do aplikacji serwerowych. Podczas opracowywania Blazor aplikacji serwerowych postępuj zgodnie <xref:fundamentals/http-requests>ze wskazówkami w temacie.

[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) &ndash; wybierz aplikację *BlazorWebAssemblySample* .

Zobacz następujące składniki w przykładowej aplikacji *BlazorWebAssemblySample* :

* Wywoływanie interfejsu API sieci Web (*strony/CallWebAPI. Razor*)
* Tester żądania HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pakiety

Odwołuje się do pakietu NuGet [systemu .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) w pliku projektu.

## <a name="add-the-httpclient-service"></a>Dodaj usługę HttpClient

W `Program.Main`programie Dodaj `HttpClient` usługę, jeśli jeszcze nie istnieje:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient i pomocnicy JSON

Blazor W aplikacji webassembly [HttpClient](xref:fundamentals/http-requests) jest dostępna jako usługa wstępnie skonfigurowana do wykonywania żądań z powrotem do serwera pochodzenia.

Aplikacja Blazor serwera domyślnie nie zawiera `HttpClient` usługi. `HttpClient` Udostępnienie aplikacji przy użyciu [infrastruktury fabryki HttpClient](xref:fundamentals/http-requests).

`HttpClient`i pomocniki JSON są również używane do wywoływania punktów końcowych interfejsu API sieci Web innych firm. `HttpClient`Program jest implementowany przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym wymuszania tych samych zasad pochodzenia.

Adres podstawowy klienta jest ustawiany na adres serwera źródłowego. Wstrzyknąć `HttpClient` wystąpienie przy użyciu `@inject` dyrektywy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

W poniższych przykładach przebieg internetowy interfejs API sieci Web przetwarza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD). Przykłady są oparte na `TodoItem` klasie, która przechowuje:

* Identyfikator (`Id`, `long`) &ndash; unikatowy identyfikator elementu.
* Nazwa (`Name`, `string`) &ndash; nazwa elementu.
* Stan (`IsComplete`, `bool`) &ndash; wskazujący, czy zadanie do wykonania zostało zakończone.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Metody pomocnika JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedzi:

* `GetFromJsonAsync`&ndash; Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

  W poniższym kodzie, `_todoItems` są wyświetlane przez składnik. Metoda jest wyzwalana, gdy składnik jest gotowy do renderowania (OnInitializedAsync).[OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) `GetTodoItems` Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w formacie JSON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

  W poniższym kodzie `_newItemName` jest dostarczany przez powiązany element składnika. `AddItem` Metoda jest wyzwalana przez wybranie `<button>` elementu. Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Wywołania do `PostAsJsonAsync` zwrócenia <xref:System.Net.Http.HttpResponseMessage>. Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć metody `ReadFromJsonAsync<T>` rozszerzenia:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; Wysyła żądanie HTTP Put, w tym zawartość zakodowaną w formacie JSON.

  W poniższym kodzie `_editItem` wartości dla `Name` i `IsCompleted` są dostarczane przez powiązane elementy składnika. Element `Id` jest ustawiany, gdy element jest wybrany w innej części interfejsu użytkownika i `EditItem` jest wywoływany. `SaveItem` Metoda jest wyzwalana przez wybranie elementu Save `<button>` . Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  Wywołania do `PutAsJsonAsync` zwrócenia <xref:System.Net.Http.HttpResponseMessage>. Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć metody `ReadFromJsonAsync<T>` rozszerzenia:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>zawiera dodatkowe metody rozszerzające do wysyłania żądań HTTP i otrzymywania odpowiedzi HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) służy do wysyłania żądania HTTP Delete do internetowego interfejsu API.

W poniższym kodzie element Delete `<button>` wywołuje `DeleteItem` metodę. Element `id` powiązany `<input>` zawiera element do usunięcia. Pełny przykład można znaleźć w przykładowej aplikacji.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która jest obsługiwana przez stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego źródła*. Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji. Aby żądania z przeglądarki były wysyłane do punktu końcowego z innym źródłem, *punkt końcowy* musi włączyć [udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).

[Przykładowa aplikacja webassembly (BlazorWebAssemblySample) demonstruje użycie mechanizmu CORS w składniku API wywołania (Pages/CallWebAPI. Razor). Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) *Pages/CallWebAPI.razor*

Aby umożliwić innym lokacjom wykonywanie żądań funkcji udostępniania zasobów między źródłami (CORS) w aplikacji, zobacz <xref:security/cors>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfiguracja punktu końcowego HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Współużytkowanie zasobów między źródłami (CORS) w formacie W3C](https://www.w3.org/TR/cors/)
