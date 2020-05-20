---
<span data-ttu-id="00e67-101">title: "wywoływanie internetowego interfejsu API z ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak wywołać interfejs API sieci Web z Blazor aplikacji webassembly przy użyciu pomocników JSON, w tym do tworzenia żądań współużytkowania zasobów między źródłami (CORS).</span><span class="sxs-lookup"><span data-stu-id="00e67-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="00e67-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="00e67-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="00e67-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="00e67-103">'Blazor'</span></span>
- <span data-ttu-id="00e67-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="00e67-104">'Identity'</span></span>
- <span data-ttu-id="00e67-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="00e67-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="00e67-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="00e67-106">'Razor'</span></span>
- <span data-ttu-id="00e67-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="00e67-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="00e67-108">Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="00e67-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="00e67-109">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="00e67-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="00e67-110">Aplikacje [ Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) wywołują interfejsy API sieci Web przy użyciu wstępnie skonfigurowanej `HttpClient` usługi.</span><span class="sxs-lookup"><span data-stu-id="00e67-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="00e67-111">Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, za pomocą Blazor pomocników JSON lub z <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="00e67-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="00e67-112">`HttpClient`Usługa w Blazor aplikacjach webassembly koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="00e67-112">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="00e67-113">Wskazówki zawarte w tym temacie dotyczą tylko Blazor aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="00e67-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="00e67-114">Aplikacje [ Blazor serwera](xref:blazor/hosting-models#blazor-server) wywołują interfejsy API sieci Web przy użyciu <xref:System.Net.Http.HttpClient> wystąpień, zwykle utworzonych przy użyciu <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="00e67-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="00e67-115">Wskazówki zawarte w tym temacie nie odnoszą się do Blazor aplikacji serwerowych.</span><span class="sxs-lookup"><span data-stu-id="00e67-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="00e67-116">Podczas opracowywania Blazor aplikacji serwerowych postępuj zgodnie ze wskazówkami w temacie <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="00e67-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="00e67-117">[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) &ndash; Wybierz aplikację *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="00e67-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="00e67-118">Zobacz następujące składniki w przykładowej aplikacji *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="00e67-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="00e67-119">Wywoływanie interfejsu API sieci Web (*strony/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="00e67-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="00e67-120">Tester żądania HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="00e67-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="00e67-121">Pakiety</span><span class="sxs-lookup"><span data-stu-id="00e67-121">Packages</span></span>

<span data-ttu-id="00e67-122">Odwołuje się do pakietu NuGet [systemu .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="00e67-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="00e67-123">Dodaj usługę HttpClient</span><span class="sxs-lookup"><span data-stu-id="00e67-123">Add the HttpClient service</span></span>

<span data-ttu-id="00e67-124">W programie `Program.Main` Dodaj `HttpClient` usługę, jeśli jeszcze nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="00e67-124">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="00e67-125">HttpClient i pomocnicy JSON</span><span class="sxs-lookup"><span data-stu-id="00e67-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="00e67-126">W Blazor aplikacji Webassembly [HttpClient](xref:fundamentals/http-requests) jest dostępna jako usługa wstępnie skonfigurowana do wykonywania żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="00e67-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="00e67-127">BlazorAplikacja serwera domyślnie nie zawiera `HttpClient` usługi.</span><span class="sxs-lookup"><span data-stu-id="00e67-127">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="00e67-128">Udostępnienie `HttpClient` aplikacji przy użyciu [infrastruktury fabryki HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="00e67-128">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="00e67-129">`HttpClient`i pomocniki JSON są również używane do wywoływania punktów końcowych interfejsu API sieci Web innych firm.</span><span class="sxs-lookup"><span data-stu-id="00e67-129">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="00e67-130">`HttpClient`Program jest implementowany przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym wymuszania tych samych zasad pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="00e67-130">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="00e67-131">Adres podstawowy klienta jest ustawiany na adres serwera źródłowego.</span><span class="sxs-lookup"><span data-stu-id="00e67-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="00e67-132">Wstrzyknąć `HttpClient` wystąpienie przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="00e67-132">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="00e67-133">W poniższych przykładach przebieg internetowy interfejs API sieci Web przetwarza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD).</span><span class="sxs-lookup"><span data-stu-id="00e67-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="00e67-134">Przykłady są oparte na `TodoItem` klasie, która przechowuje:</span><span class="sxs-lookup"><span data-stu-id="00e67-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="00e67-135">Identyfikator ( `Id` , `long` ) &ndash; unikatowy identyfikator elementu.</span><span class="sxs-lookup"><span data-stu-id="00e67-135">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="00e67-136">Nazwa ( `Name` , `string` ) &ndash; Nazwa elementu.</span><span class="sxs-lookup"><span data-stu-id="00e67-136">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="00e67-137">Stan ( `IsComplete` , `bool` ) &ndash; wskazujący, czy zadanie do wykonania zostało zakończone.</span><span class="sxs-lookup"><span data-stu-id="00e67-137">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="00e67-138">Metody pomocnika JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="00e67-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="00e67-139">`GetFromJsonAsync`&ndash;Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="00e67-139">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="00e67-140">W poniższym kodzie, `todoItems` są wyświetlane przez składnik.</span><span class="sxs-lookup"><span data-stu-id="00e67-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="00e67-141">`GetTodoItems`Metoda jest wyzwalana, gdy składnik jest gotowy do renderowania ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="00e67-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="00e67-142">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e67-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="00e67-143">`PostAsJsonAsync`&ndash;Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w formacie JSON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="00e67-143">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="00e67-144">W poniższym kodzie `newItemName` jest dostarczany przez powiązany element składnika.</span><span class="sxs-lookup"><span data-stu-id="00e67-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="00e67-145">`AddItem`Metoda jest wyzwalana przez wybranie `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="00e67-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="00e67-146">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e67-146">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="00e67-147">Wywołania do `PostAsJsonAsync` zwrócenia <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="00e67-147">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="00e67-148">Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć `ReadFromJsonAsync<T>` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="00e67-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="00e67-149">`PutAsJsonAsync`&ndash;Wysyła żądanie HTTP Put, w tym zawartość zakodowaną w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="00e67-149">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="00e67-150">W poniższym kodzie `editItem` wartości dla `Name` i `IsCompleted` są dostarczane przez powiązane elementy składnika.</span><span class="sxs-lookup"><span data-stu-id="00e67-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="00e67-151">Element `Id` jest ustawiany, gdy element jest wybrany w innej części interfejsu użytkownika i `EditItem` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="00e67-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="00e67-152">`SaveItem`Metoda jest wyzwalana przez wybranie elementu Save `<button>` .</span><span class="sxs-lookup"><span data-stu-id="00e67-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="00e67-153">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e67-153">See the sample app for a complete example.</span></span>

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
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="00e67-154">Wywołania do `PutAsJsonAsync` zwrócenia <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="00e67-154">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="00e67-155">Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć `ReadFromJsonAsync<T>` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="00e67-155">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="00e67-156"><xref:System.Net.Http>zawiera dodatkowe metody rozszerzające do wysyłania żądań HTTP i otrzymywania odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="00e67-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="00e67-157">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) służy do wysyłania żądania HTTP Delete do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="00e67-157">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="00e67-158">W poniższym kodzie `<button>` element DELETE wywołuje `DeleteItem` metodę.</span><span class="sxs-lookup"><span data-stu-id="00e67-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="00e67-159">Element powiązany zawiera `<input>` `id` element do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="00e67-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="00e67-160">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="00e67-160">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="00e67-161">Nazwane HttpClient z IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="00e67-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="00e67-162"><xref:System.Net.Http.IHttpClientFactory>Obsługiwane są usługi i konfiguracja nazwanych <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="00e67-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="00e67-163">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="00e67-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="00e67-164">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="00e67-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="00e67-165">Wpisane HttpClient</span><span class="sxs-lookup"><span data-stu-id="00e67-165">Typed HttpClient</span></span>

<span data-ttu-id="00e67-166">Typ <xref:System.Net.Http.HttpClient> używa co najmniej jednego <xref:System.Net.Http.HttpClient> wystąpienia aplikacji, domyślnego lub nazwanego, aby można było zwrócić dane z jednego lub wielu punktów końcowych interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="00e67-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="00e67-167">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="00e67-167">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="00e67-168">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="00e67-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="00e67-169">Składniki wprowadzają wpisane `HttpClient` do wywołania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="00e67-169">Components inject the typed `HttpClient` to call the web API.</span></span>

<span data-ttu-id="00e67-170">`FetchData`składnik (*strony/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="00e67-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="00e67-171">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="00e67-171">Handle errors</span></span>

<span data-ttu-id="00e67-172">Gdy wystąpią błędy podczas korzystania z internetowego interfejsu API, mogą one być obsługiwane przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="00e67-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="00e67-173">Na przykład `GetFromJsonAsync` oczekuje odpowiedzi JSON z interfejsu API serwera z `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="00e67-173">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="00e67-174">Jeśli odpowiedź nie jest w formacie JSON, walidacja zawartości zgłasza <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="00e67-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="00e67-175">W poniższym przykładzie punkt końcowy identyfikatora URI dla żądania danych prognozy pogody jest błędny.</span><span class="sxs-lookup"><span data-stu-id="00e67-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="00e67-176">Identyfikator URI powinien być `WeatherForecast` wyświetlany w wywołaniu jako `WeatherForcast` (brak "e").</span><span class="sxs-lookup"><span data-stu-id="00e67-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="00e67-177">`GetFromJsonAsync`Wywołanie oczekuje, że kod JSON ma zostać zwrócony, ale serwer zwraca kod HTML dla nieobsłużonego wyjątku na serwerze z `Content-Type` `text/html` .</span><span class="sxs-lookup"><span data-stu-id="00e67-177">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="00e67-178">Nieobsługiwany wyjątek występuje na serwerze, ponieważ nie odnaleziono ścieżki i oprogramowanie pośredniczące nie może obsłużyć strony lub widoku żądania.</span><span class="sxs-lookup"><span data-stu-id="00e67-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="00e67-179">W programie `OnInitializedAsync` na kliencie <xref:System.NotSupportedException> jest generowany w przypadku zweryfikowania zawartości odpowiedzi jako innej niż JSON.</span><span class="sxs-lookup"><span data-stu-id="00e67-179">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="00e67-180">Wyjątek jest przechwytywany w `catch` bloku, w którym logika niestandardowa może zarejestrować błąd lub przedstawić użytkownikowi przyjazny komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="00e67-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="00e67-181">Poprzedni przykład jest przeznaczony do celów demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="00e67-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="00e67-182">Aplikację serwerową interfejsu API sieci Web można skonfigurować tak, aby zwracała kod JSON nawet wtedy, gdy punkt końcowy nie istnieje lub wystąpił nieobsługiwany wdrażaniem na serwerze.</span><span class="sxs-lookup"><span data-stu-id="00e67-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="00e67-183">Aby uzyskać więcej informacji, zobacz <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="00e67-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="00e67-184">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="00e67-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="00e67-185">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która jest obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="00e67-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="00e67-186">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="00e67-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="00e67-187">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="00e67-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="00e67-188">Aby żądania z przeglądarki były wysyłane do punktu końcowego z innym źródłem, *punkt końcowy* musi włączyć [udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="00e67-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="00e67-189">[ Blazor Przykładowa aplikacja webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) DEMONSTRUJE użycie mechanizmu CORS w składniku API wywołania (*Pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="00e67-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="00e67-190">Aby umożliwić innym lokacjom wykonywanie żądań funkcji udostępniania zasobów między źródłami (CORS) w aplikacji, zobacz <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="00e67-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="00e67-191">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="00e67-191">Additional resources</span></span>

* <span data-ttu-id="00e67-192"><xref:security/blazor/webassembly/additional-scenarios>&ndash;Obejmuje pokrycie przy użyciu `HttpClient` programu w celu zapewnienia bezpiecznych żądań interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="00e67-192"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using `HttpClient` to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="00e67-193">Konfiguracja punktu końcowego HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="00e67-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="00e67-194">Współużytkowanie zasobów między źródłami (CORS) w formacie W3C</span><span class="sxs-lookup"><span data-stu-id="00e67-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
