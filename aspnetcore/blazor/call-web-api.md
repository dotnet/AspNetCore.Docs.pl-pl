---
title: Wywoływanie internetowego interfejsu API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak wywołać interfejs API sieci Web z Blazor WebAssembly aplikacji za pomocą pomocników JSON, w tym do tworzenia żądań wymiany zasobów między źródłami (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 85b3ded6ec25310a573e99cbedf0df005d92bdbe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93234416"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a>Wywoływanie internetowego interfejsu API z ASP.NET Core Blazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)

> [!NOTE]
> Ten temat ma zastosowanie do programu Blazor WebAssembly . [Blazor Server](xref:blazor/hosting-models#blazor-server) aplikacje wywołują interfejsy API sieci Web przy użyciu <xref:System.Net.Http.HttpClient> wystąpień, zwykle utworzonych przy użyciu <xref:System.Net.Http.IHttpClientFactory> . Aby uzyskać wskazówki dotyczące programu Blazor Server , zobacz <xref:fundamentals/http-requests> .

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) aplikacje wywołują interfejsy API sieci Web przy użyciu wstępnie skonfigurowanej <xref:System.Net.Http.HttpClient> usługi. Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, za pomocą Blazor pomocników JSON lub z <xref:System.Net.Http.HttpRequestMessage> . <xref:System.Net.Http.HttpClient>Usługa w Blazor WebAssembly aplikacjach koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia. Wskazówki zawarte w tym temacie dotyczą tylko Blazor WebAssembly aplikacji.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)): Wybierz `BlazorWebAssemblySample` aplikację.

Zobacz następujące składniki w `BlazorWebAssemblySample` przykładowej aplikacji:

* Wywoływanie interfejsu API sieci Web ( `Pages/CallWebAPI.razor` )
* Tester żądania HTTP ( `Components/HTTPRequestTester.razor` )

## <a name="packages"></a>Pakiety

Odwołuje się do [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) pakietu NuGet w pliku projektu.

## <a name="add-the-httpclient-service"></a>Dodaj usługę HttpClient

W programie `Program.Main` Dodaj <xref:System.Net.Http.HttpClient> usługę, jeśli jeszcze nie istnieje:

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient i pomocnicy JSON

W Blazor WebAssembly aplikacji [`HttpClient`](xref:fundamentals/http-requests) jest dostępna jako wstępnie skonfigurowana usługa do wykonywania żądań z powrotem do serwera pochodzenia.

Blazor ServerAplikacja nie <xref:System.Net.Http.HttpClient> domyślnie zawiera usługi. Udostępnienie <xref:System.Net.Http.HttpClient> aplikacji przy użyciu [ `HttpClient` infrastruktury fabryki](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient> i pomocniki JSON są również używane do wywoływania punktów końcowych interfejsu API sieci Web innych firm. <xref:System.Net.Http.HttpClient> Program jest implementowany przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym wymuszania tych samych zasad pochodzenia.

Adres podstawowy klienta jest ustawiany na adres serwera źródłowego. Wstrzyknąć <xref:System.Net.Http.HttpClient> wystąpienie przy użyciu [`@inject`](xref:mvc/views/razor#inject) dyrektywy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

W poniższych przykładach przebieg internetowy interfejs API sieci Web przetwarza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD). Przykłady są oparte na `TodoItem` klasie, która przechowuje:

* ID ( `Id` , `long` ): unikatowy identyfikator elementu.
* Nazwa ( `Name` , `string` ): nazwa elementu.
* Stan ( `IsComplete` , `bool` ): wskazuje, czy zadanie do wykonania zostało zakończone.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Metody pomocnika JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedzi:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

  W poniższym kodzie, `todoItems` są wyświetlane przez składnik. `GetTodoItems`Metoda jest wyzwalana, gdy składnik jest gotowy do renderowania ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ). Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w formacie JSON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

  W poniższym kodzie `newItemName` jest dostarczany przez powiązany element składnika. `AddItem`Metoda jest wyzwalana przez wybranie `<button>` elementu. Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Wywołania do <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> zwrócenia <xref:System.Net.Http.HttpResponseMessage> . Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć `ReadFromJsonAsync<T>` metody rozszerzenia:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Wysyła żądanie HTTP PUT, w tym zawartość zakodowaną w formacie JSON.

  W poniższym kodzie `editItem` wartości dla `Name` i `IsCompleted` są dostarczane przez powiązane elementy składnika. Element `Id` jest ustawiany, gdy element jest wybrany w innej części interfejsu użytkownika i `EditItem` jest wywoływany. `SaveItem`Metoda jest wyzwalana przez wybranie elementu Save `<button>` . Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Wywołania do <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> zwrócenia <xref:System.Net.Http.HttpResponseMessage> . Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metody rozszerzenia:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> zawiera dodatkowe metody rozszerzające do wysyłania żądań HTTP i otrzymywania odpowiedzi HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> służy do wysyłania żądania HTTP DELETE do internetowego interfejsu API.

W poniższym kodzie `<button>` element DELETE wywołuje `DeleteItem` metodę. Element powiązany zawiera `<input>` `id` element do usunięcia. Pełny przykład można znaleźć w przykładowej aplikacji.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>Nazwane HttpClient z IHttpClientFactory

<xref:System.Net.Http.IHttpClientFactory> Obsługiwane są usługi i konfiguracja nazwanych <xref:System.Net.Http.HttpClient> .

Odwołuje się do [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakietu NuGet w pliku projektu.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData` składnik ( `Pages/FetchData.razor` ):

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>Wpisane HttpClient

Typ <xref:System.Net.Http.HttpClient> używa co najmniej jednego <xref:System.Net.Http.HttpClient> wystąpienia aplikacji, domyślnego lub nazwanego, aby można było zwrócić dane z jednego lub wielu punktów końcowych interfejsu API sieci Web.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Składniki wprowadzają wpisane <xref:System.Net.Http.HttpClient> do wywołania interfejsu API sieci Web.

`FetchData` składnik ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` i `HttpRequestMessage` z opcjami żądania interfejsu API pobierania

W przypadku uruchamiania w zestawie webassembly w Blazor WebAssembly aplikacji [`HttpClient`](xref:fundamentals/http-requests) ([Dokumentacja interfejsu API](xref:System.Net.Http.HttpClient)) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań. Na przykład można określić metodę HTTP i nagłówki żądania. Poniższy składnik wysyła `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

Implementacja programu .NET webassembly <xref:System.Net.Http.HttpClient> używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Opcje żądania pobrania HTTP można skonfigurować przy użyciu <xref:System.Net.Http.HttpRequestMessage> metod rozszerzających pokazanych w poniższej tabeli.

| Metoda rozszerzenia | Właściwość żądania pobrania |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Można ustawić dodatkowe opcje przy użyciu bardziej generycznej <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozszerzenia.
 
Odpowiedź HTTP jest zwykle buforowana w Blazor WebAssembly aplikacji, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi. Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy użyć <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metody rozszerzenia w żądaniu.

Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metody rozszerzenia:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="handle-errors"></a>Obsługa błędów

Gdy wystąpią błędy podczas korzystania z internetowego interfejsu API, mogą one być obsługiwane przez kod dewelopera. Na przykład <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> oczekuje odpowiedzi JSON z interfejsu API serwera z `Content-Type` `application/json` . Jeśli odpowiedź nie jest w formacie JSON, walidacja zawartości zgłasza <xref:System.NotSupportedException> .

W poniższym przykładzie punkt końcowy identyfikatora URI dla żądania danych prognozy pogody jest błędny. Identyfikator URI powinien być `WeatherForecast` wyświetlany w wywołaniu jako `WeatherForcast` (brak "e").

<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Wywołanie oczekuje, że kod JSON ma zostać zwrócony, ale serwer zwraca kod HTML dla nieobsłużonego wyjątku na serwerze z `Content-Type` `text/html` . Nieobsługiwany wyjątek występuje na serwerze, ponieważ nie odnaleziono ścieżki i oprogramowanie pośredniczące nie może obsłużyć strony lub widoku żądania.

W programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> na kliencie <xref:System.NotSupportedException> jest generowany w przypadku zweryfikowania zawartości odpowiedzi jako innej niż JSON. Wyjątek jest przechwytywany w `catch` bloku, w którym logika niestandardowa może zarejestrować błąd lub przedstawić użytkownikowi przyjazny komunikat o błędzie:

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> Poprzedni przykład jest przeznaczony do celów demonstracyjnych. Aplikację serwerową interfejsu API sieci Web można skonfigurować tak, aby zwracała kod JSON nawet wtedy, gdy punkt końcowy nie istnieje lub wystąpił nieobsługiwany wyjątek na serwerze.

Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która jest obsługiwana przez stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego źródła*. Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji. Aby żądania z przeglądarki były wysyłane do punktu końcowego z innym źródłem, *punkt końcowy* musi włączyć [udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).

[ Blazor WebAssembly Aplikacja Przykładowa ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstruje użycie mechanizmu CORS w składniku API wywołania ( `Pages/CallWebAPI.razor` ).

Aby uzyskać więcej informacji na temat mechanizmu CORS z bezpiecznymi żądaniami w Blazor aplikacjach, zobacz <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .

Aby uzyskać ogólne informacje na temat mechanizmu CORS z aplikacjami ASP.NET Core, zobacz <xref:security/cors> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/security/webassembly/additional-scenarios>: Obejmuje pokrycie przy użyciu <xref:System.Net.Http.HttpClient> , aby zapewnić bezpieczne żądania interfejsu API sieci Web.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfiguracja punktu końcowego HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Współużytkowanie zasobów między źródłami (CORS) w formacie W3C](https://www.w3.org/TR/cors/)
