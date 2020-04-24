---
title: Wywoływanie internetowego interfejsu API z Blazor programu ASP.NET Core webassembly
author: guardrex
description: Dowiedz się, jak wywołać interfejs API sieci Blazor Web z aplikacji webassembly przy użyciu pomocników JSON, w tym do tworzenia żądań wymiany zasobów między źródłami (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122217"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Wywoływanie interfejsu API sieci Web z ASP.NET Core Blazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor aplikacje Webassembly](xref:blazor/hosting-models#blazor-webassembly) wywołują interfejsy API sieci Web przy użyciu `HttpClient` wstępnie skonfigurowanej usługi. Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) języka JavaScript, za pomocą pomocników JSON Blazor <xref:System.Net.Http.HttpRequestMessage>lub za pomocą polecenia. `HttpClient` Usługa w aplikacjach webassembly Blazor koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia. Wskazówki zawarte w tym temacie dotyczą tylko aplikacji Blazor webassembly.

Aplikacje [serwera Blazor](xref:blazor/hosting-models#blazor-server) wywołują interfejsy API <xref:System.Net.Http.HttpClient> sieci Web przy użyciu wystąpień <xref:System.Net.Http.IHttpClientFactory>, zwykle utworzonych przy użyciu. Wskazówki zawarte w tym temacie nie odnoszą się do aplikacji serwera Blazor. Podczas tworzenia aplikacji serwera Blazor postępuj zgodnie ze wskazówkami w <xref:fundamentals/http-requests>temacie.

[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) &ndash; wybierz aplikację *BlazorWebAssemblySample* .

Zobacz następujące składniki w przykładowej aplikacji *BlazorWebAssemblySample* :

* Wywoływanie interfejsu API sieci Web (*strony/CallWebAPI. Razor*)
* Tester żądania HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pakiety

Odwołuje się do pakietu NuGet [systemu .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) w pliku projektu.

## <a name="add-the-httpclient-service"></a>Dodaj usługę HttpClient

W `Program.Main`programie Dodaj `HttpClient` usługę, jeśli jeszcze nie istnieje:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient i pomocnicy JSON

W aplikacji Blazor webassembly [HttpClient](xref:fundamentals/http-requests) jest dostępna jako usługa wstępnie skonfigurowana do wykonywania żądań z powrotem do serwera pochodzenia.

Aplikacja serwera Blazor nie domyślnie zawiera `HttpClient` usługi. `HttpClient` Udostępnienie aplikacji przy użyciu [infrastruktury fabryki HttpClient](xref:fundamentals/http-requests).

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

[Przykładowa aplikacja Blazor webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstruje użycie mechanizmu CORS w składniku API wywołania (*Pages/CallWebAPI. Razor*).

Aby umożliwić innym lokacjom wykonywanie żądań funkcji udostępniania zasobów między źródłami (CORS) w aplikacji, zobacz <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient i HttpRequestMessage za pomocą opcji żądania interfejsu API pobierania

W przypadku uruchamiania w zestawie webassembly w aplikacji Blazor webassembly Użyj [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> aby dostosować żądania. Na przykład można określić identyfikator URI żądania, metodę HTTP i wszystkie żądane nagłówki żądania.

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Implementacja programu `HttpClient` .NET webassembly używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Opcje żądania pobrania HTTP można skonfigurować przy użyciu `HttpRequestMessage` metod rozszerzających pokazanych w poniższej tabeli.

| `HttpRequestMessage`Metoda rozszerzenia | Właściwość żądania pobrania |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [uwierzytelniające](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [Chow](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [wyst](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [spójn](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Można ustawić dodatkowe opcje przy użyciu bardziej generycznej `SetBrowserRequestOption` metody rozszerzenia.
 
Odpowiedź HTTP jest zwykle buforowana w Blazor aplikacji webassembly, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi. Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy `SetBrowserResponseStreamingEnabled` użyć metody rozszerzenia w żądaniu.

Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj metody `SetBrowserRequestCredentials` rozszerzenia:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Podczas wysyłania poświadczeń (plików cookie/nagłówki autoryzacji) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.

Następujące zasady obejmują konfigurację programu:

* Pochodzenie żądania (`http://localhost:5000`, `https://localhost:5001`).
* Dowolna Metoda (czasownik).
* `Content-Type`i `Authorization` nagłówki. Aby zezwolić na nagłówek niestandardowy (na przykład `x-custom-header`), Wyświetl nagłówek podczas wywoływania. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>
* Poświadczenia ustawione przez kod JavaScript po stronie klienta (`credentials` Właściwość ustawiona na `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Aby uzyskać więcej informacji, <xref:security/cors> Zobacz i składnik Tester żądania HTTP aplikacji przykładowej (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Żądaj dodatkowych tokenów dostępu](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Dołącz tokeny do żądań wychodzących](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfiguracja punktu końcowego HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Współużytkowanie zasobów między źródłami (CORS) w formacie W3C](https://www.w3.org/TR/cors/)
