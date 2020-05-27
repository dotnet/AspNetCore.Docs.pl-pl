---
title: "wywoływanie internetowego interfejsu API z ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak wywołać interfejs API sieci Web z Blazor aplikacji webassembly przy użyciu pomocników JSON, w tym do tworzenia żądań współużytkowania zasobów między źródłami (CORS).
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)

Aplikacje [ Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) wywołują interfejsy API sieci Web przy użyciu wstępnie skonfigurowanej <xref:System.Net.Http.HttpClient> usługi. Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, za pomocą Blazor pomocników JSON lub z <xref:System.Net.Http.HttpRequestMessage> . <xref:System.Net.Http.HttpClient>Usługa w Blazor aplikacjach webassembly koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia. Wskazówki zawarte w tym temacie dotyczą tylko Blazor aplikacji webassembly.

Aplikacje [ Blazor serwera](xref:blazor/hosting-models#blazor-server) wywołują interfejsy API sieci Web przy użyciu <xref:System.Net.Http.HttpClient> wystąpień, zwykle utworzonych przy użyciu <xref:System.Net.Http.IHttpClientFactory> . Wskazówki zawarte w tym temacie nie odnoszą się do Blazor aplikacji serwerowych. Podczas opracowywania Blazor aplikacji serwerowych postępuj zgodnie ze wskazówkami w temacie <xref:fundamentals/http-requests> .

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)): Wybierz aplikację *BlazorWebAssemblySample* .

Zobacz następujące składniki w przykładowej aplikacji *BlazorWebAssemblySample* :

* Wywoływanie interfejsu API sieci Web (*strony/CallWebAPI. Razor*)
* Tester żądania HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pakiety

Odwołuje się do pakietu NuGet [systemu .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) w pliku projektu.

## <a name="add-the-httpclient-service"></a>Dodaj usługę HttpClient

W programie `Program.Main` Dodaj <xref:System.Net.Http.HttpClient> usługę, jeśli jeszcze nie istnieje:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient i pomocnicy JSON

W Blazor aplikacji Webassembly [HttpClient](xref:fundamentals/http-requests) jest dostępna jako usługa wstępnie skonfigurowana do wykonywania żądań z powrotem do serwera pochodzenia.

BlazorAplikacja serwera domyślnie nie zawiera <xref:System.Net.Http.HttpClient> usługi. Udostępnienie <xref:System.Net.Http.HttpClient> aplikacji przy użyciu [infrastruktury fabryki HttpClient](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient>i pomocniki JSON są również używane do wywoływania punktów końcowych interfejsu API sieci Web innych firm. <xref:System.Net.Http.HttpClient>Program jest implementowany przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym wymuszania tych samych zasad pochodzenia.

Adres podstawowy klienta jest ustawiany na adres serwera źródłowego. Wstrzyknąć <xref:System.Net.Http.HttpClient> wystąpienie przy użyciu [`@inject`](xref:mvc/views/razor#inject) dyrektywy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

W poniższych przykładach przebieg internetowy interfejs API sieci Web przetwarza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD). Przykłady są oparte na `TodoItem` klasie, która przechowuje:

* Identyfikator ( `Id` , `long` ) &ndash; unikatowy identyfikator elementu.
* Nazwa ( `Name` , `string` ) &ndash; Nazwa elementu.
* Stan ( `IsComplete` , `bool` ) &ndash; wskazujący, czy zadanie do wykonania zostało zakończone.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Metody pomocnika JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedzi:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>&ndash;Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

  W poniższym kodzie, `todoItems` są wyświetlane przez składnik. `GetTodoItems`Metoda jest wyzwalana, gdy składnik jest gotowy do renderowania ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Pełny przykład można znaleźć w przykładowej aplikacji.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>&ndash;Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w formacie JSON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

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

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>&ndash;Wysyła żądanie HTTP Put, w tym zawartość zakodowaną w formacie JSON.

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
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>zawiera dodatkowe metody rozszerzające do wysyłania żądań HTTP i otrzymywania odpowiedzi HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>służy do wysyłania żądania HTTP DELETE do internetowego interfejsu API.

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

<xref:System.Net.Http.IHttpClientFactory>Obsługiwane są usługi i konfiguracja nazwanych <xref:System.Net.Http.HttpClient> .

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData`składnik (*strony/FetchData. Razor*):

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

*WeatherForecastClient.cs*:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Składniki wprowadzają wpisane <xref:System.Net.Http.HttpClient> do wywołania interfejsu API sieci Web.

`FetchData`składnik (*strony/FetchData. Razor*):

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

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
> Poprzedni przykład jest przeznaczony do celów demonstracyjnych. Aplikację serwerową interfejsu API sieci Web można skonfigurować tak, aby zwracała kod JSON nawet wtedy, gdy punkt końcowy nie istnieje lub wystąpił nieobsługiwany wdrażaniem na serwerze.

Aby uzyskać więcej informacji, zobacz <xref:blazor/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która jest obsługiwana przez stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego źródła*. Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji. Aby żądania z przeglądarki były wysyłane do punktu końcowego z innym źródłem, *punkt końcowy* musi włączyć [udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).

[ Blazor Przykładowa aplikacja webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) DEMONSTRUJE użycie mechanizmu CORS w składniku API wywołania (*Pages/CallWebAPI. Razor*).

Aby umożliwić innym lokacjom wykonywanie żądań funkcji udostępniania zasobów między źródłami (CORS) w aplikacji, zobacz <xref:security/cors> .

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/blazor/webassembly/additional-scenarios>&ndash;Obejmuje pokrycie przy użyciu <xref:System.Net.Http.HttpClient> programu w celu zapewnienia bezpiecznych żądań interfejsu API sieci Web.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfiguracja punktu końcowego HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Współużytkowanie zasobów między źródłami (CORS) w formacie W3C](https://www.w3.org/TR/cors/)
