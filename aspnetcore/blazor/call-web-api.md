---
title: Wywoływanie internetowego interfejsu API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak wywołać interfejs API sieci Web z Blazor WebAssembly aplikacji za pomocą pomocników JSON, w tym do tworzenia żądań wymiany zasobów między źródłami (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: aa769554f54d6f480815d65de18e3dba5729d07d
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876740"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a><span data-ttu-id="3af69-103">Wywoływanie internetowego interfejsu API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3af69-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="3af69-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="3af69-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="3af69-105">Ten temat ma zastosowanie do programu Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="3af69-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="3af69-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) aplikacje wywołują interfejsy API sieci Web przy użyciu <xref:System.Net.Http.HttpClient> wystąpień, zwykle utworzonych przy użyciu <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="3af69-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="3af69-107">Aby uzyskać wskazówki dotyczące programu Blazor Server , zobacz <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="3af69-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="3af69-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) aplikacje wywołują interfejsy API sieci Web przy użyciu wstępnie skonfigurowanej <xref:System.Net.Http.HttpClient> usługi.</span><span class="sxs-lookup"><span data-stu-id="3af69-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="3af69-109">Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, za pomocą Blazor pomocników JSON lub z <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="3af69-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="3af69-110"><xref:System.Net.Http.HttpClient>Usługa w Blazor WebAssembly aplikacjach koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="3af69-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="3af69-111">Wskazówki zawarte w tym temacie dotyczą tylko Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3af69-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="3af69-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)): Wybierz `BlazorWebAssemblySample` aplikację.</span><span class="sxs-lookup"><span data-stu-id="3af69-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="3af69-113">Zobacz następujące składniki w `BlazorWebAssemblySample` przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="3af69-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="3af69-114">Wywoływanie interfejsu API sieci Web ( `Pages/CallWebAPI.razor` )</span><span class="sxs-lookup"><span data-stu-id="3af69-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="3af69-115">Tester żądania HTTP ( `Components/HTTPRequestTester.razor` )</span><span class="sxs-lookup"><span data-stu-id="3af69-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="3af69-116">Pakiety</span><span class="sxs-lookup"><span data-stu-id="3af69-116">Packages</span></span>

<span data-ttu-id="3af69-117">Odwołuje się do [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) pakietu NuGet w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="3af69-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="3af69-118">Dodaj usługę HttpClient</span><span class="sxs-lookup"><span data-stu-id="3af69-118">Add the HttpClient service</span></span>

<span data-ttu-id="3af69-119">W programie `Program.Main` Dodaj <xref:System.Net.Http.HttpClient> usługę, jeśli jeszcze nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="3af69-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="3af69-120">HttpClient i pomocnicy JSON</span><span class="sxs-lookup"><span data-stu-id="3af69-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="3af69-121">W Blazor WebAssembly aplikacji [`HttpClient`](xref:fundamentals/http-requests) jest dostępna jako wstępnie skonfigurowana usługa do wykonywania żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="3af69-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="3af69-122">Blazor ServerAplikacja nie <xref:System.Net.Http.HttpClient> domyślnie zawiera usługi.</span><span class="sxs-lookup"><span data-stu-id="3af69-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="3af69-123">Udostępnienie <xref:System.Net.Http.HttpClient> aplikacji przy użyciu [ `HttpClient` infrastruktury fabryki](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="3af69-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="3af69-124"><xref:System.Net.Http.HttpClient> i pomocniki JSON są również używane do wywoływania punktów końcowych interfejsu API sieci Web innych firm.</span><span class="sxs-lookup"><span data-stu-id="3af69-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="3af69-125"><xref:System.Net.Http.HttpClient> Program jest implementowany przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym wymuszania tych samych zasad pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="3af69-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="3af69-126">Adres podstawowy klienta jest ustawiany na adres serwera źródłowego.</span><span class="sxs-lookup"><span data-stu-id="3af69-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="3af69-127">Wstrzyknąć <xref:System.Net.Http.HttpClient> wystąpienie przy użyciu [`@inject`](xref:mvc/views/razor#inject) dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="3af69-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="3af69-128">W poniższych przykładach przebieg internetowy interfejs API sieci Web przetwarza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD).</span><span class="sxs-lookup"><span data-stu-id="3af69-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="3af69-129">Przykłady są oparte na `TodoItem` klasie, która przechowuje:</span><span class="sxs-lookup"><span data-stu-id="3af69-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="3af69-130">ID ( `Id` , `long` ): unikatowy identyfikator elementu.</span><span class="sxs-lookup"><span data-stu-id="3af69-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="3af69-131">Nazwa ( `Name` , `string` ): nazwa elementu.</span><span class="sxs-lookup"><span data-stu-id="3af69-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="3af69-132">Stan ( `IsComplete` , `bool` ): wskazuje, czy zadanie do wykonania zostało zakończone.</span><span class="sxs-lookup"><span data-stu-id="3af69-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="3af69-133">Metody pomocnika JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="3af69-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="3af69-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="3af69-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="3af69-135">W poniższym kodzie, `todoItems` są wyświetlane przez składnik.</span><span class="sxs-lookup"><span data-stu-id="3af69-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="3af69-136">`GetTodoItems`Metoda jest wyzwalana, gdy składnik jest gotowy do renderowania ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ).</span><span class="sxs-lookup"><span data-stu-id="3af69-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="3af69-137">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3af69-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="3af69-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w formacie JSON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="3af69-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="3af69-139">W poniższym kodzie `newItemName` jest dostarczany przez powiązany element składnika.</span><span class="sxs-lookup"><span data-stu-id="3af69-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="3af69-140">`AddItem`Metoda jest wyzwalana przez wybranie `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="3af69-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="3af69-141">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3af69-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="3af69-142">Wywołania do <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> zwrócenia <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="3af69-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="3af69-143">Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć `ReadFromJsonAsync<T>` metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="3af69-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="3af69-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Wysyła żądanie HTTP PUT, w tym zawartość zakodowaną w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="3af69-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="3af69-145">W poniższym kodzie `editItem` wartości dla `Name` i `IsCompleted` są dostarczane przez powiązane elementy składnika.</span><span class="sxs-lookup"><span data-stu-id="3af69-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="3af69-146">Element `Id` jest ustawiany, gdy element jest wybrany w innej części interfejsu użytkownika i `EditItem` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="3af69-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="3af69-147">`SaveItem`Metoda jest wyzwalana przez wybranie elementu Save `<button>` .</span><span class="sxs-lookup"><span data-stu-id="3af69-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="3af69-148">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3af69-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="3af69-149">Wywołania do <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> zwrócenia <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="3af69-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="3af69-150">Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="3af69-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="3af69-151"><xref:System.Net.Http> zawiera dodatkowe metody rozszerzające do wysyłania żądań HTTP i otrzymywania odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="3af69-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="3af69-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> służy do wysyłania żądania HTTP DELETE do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3af69-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="3af69-153">W poniższym kodzie `<button>` element DELETE wywołuje `DeleteItem` metodę.</span><span class="sxs-lookup"><span data-stu-id="3af69-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="3af69-154">Element powiązany zawiera `<input>` `id` element do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="3af69-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="3af69-155">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3af69-155">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="3af69-156">Nazwane HttpClient z IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3af69-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="3af69-157"><xref:System.Net.Http.IHttpClientFactory> Obsługiwane są usługi i konfiguracja nazwanych <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="3af69-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="3af69-158">Odwołuje się do [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pakietu NuGet w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="3af69-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="3af69-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3af69-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="3af69-160">`FetchData` składnik ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="3af69-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="3af69-161">Wpisane HttpClient</span><span class="sxs-lookup"><span data-stu-id="3af69-161">Typed HttpClient</span></span>

<span data-ttu-id="3af69-162">Typ <xref:System.Net.Http.HttpClient> używa co najmniej jednego <xref:System.Net.Http.HttpClient> wystąpienia aplikacji, domyślnego lub nazwanego, aby można było zwrócić dane z jednego lub wielu punktów końcowych interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3af69-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="3af69-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="3af69-163">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="3af69-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3af69-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="3af69-165">Składniki wprowadzają wpisane <xref:System.Net.Http.HttpClient> do wywołania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3af69-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="3af69-166">`FetchData` składnik ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="3af69-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

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

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="3af69-167">`HttpClient` i `HttpRequestMessage` z opcjami żądania interfejsu API pobierania</span><span class="sxs-lookup"><span data-stu-id="3af69-167">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="3af69-168">W przypadku uruchamiania w zestawie webassembly w Blazor WebAssembly aplikacji [`HttpClient`](xref:fundamentals/http-requests) ([Dokumentacja interfejsu API](xref:System.Net.Http.HttpClient)) i <xref:System.Net.Http.HttpRequestMessage> może służyć do dostosowywania żądań.</span><span class="sxs-lookup"><span data-stu-id="3af69-168">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="3af69-169">Na przykład można określić metodę HTTP i nagłówki żądania.</span><span class="sxs-lookup"><span data-stu-id="3af69-169">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="3af69-170">Poniższy składnik wysyła `POST` żądanie do punktu końcowego interfejsu API listy do wykonania na serwerze i wyświetla treść odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="3af69-170">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="3af69-171">Implementacja programu .NET webassembly <xref:System.Net.Http.HttpClient> używa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="3af69-171">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="3af69-172">Polecenie Pobierz umożliwia skonfigurowanie kilku [opcji specyficznych dla żądania](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="3af69-172">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="3af69-173">Opcje żądania pobrania HTTP można skonfigurować przy użyciu <xref:System.Net.Http.HttpRequestMessage> metod rozszerzających pokazanych w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="3af69-173">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="3af69-174">Metoda rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="3af69-174">Extension method</span></span> | <span data-ttu-id="3af69-175">Właściwość żądania pobrania</span><span class="sxs-lookup"><span data-stu-id="3af69-175">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="3af69-176">Można ustawić dodatkowe opcje przy użyciu bardziej generycznej <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="3af69-176">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="3af69-177">Odpowiedź HTTP jest zwykle buforowana w Blazor WebAssembly aplikacji, aby umożliwić obsługę odczytów synchronizacji w zawartości odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3af69-177">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="3af69-178">Aby włączyć obsługę przesyłania strumieniowego odpowiedzi, należy użyć <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metody rozszerzenia w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="3af69-178">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="3af69-179">Aby uwzględnić poświadczenia w żądaniu między źródłami, użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="3af69-179">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="3af69-180">Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [powiadomienia MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="3af69-180">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="3af69-181">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="3af69-181">Handle errors</span></span>

<span data-ttu-id="3af69-182">Gdy wystąpią błędy podczas korzystania z internetowego interfejsu API, mogą one być obsługiwane przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="3af69-182">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="3af69-183">Na przykład <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> oczekuje odpowiedzi JSON z interfejsu API serwera z `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="3af69-183">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="3af69-184">Jeśli odpowiedź nie jest w formacie JSON, walidacja zawartości zgłasza <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="3af69-184">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="3af69-185">W poniższym przykładzie punkt końcowy identyfikatora URI dla żądania danych prognozy pogody jest błędny.</span><span class="sxs-lookup"><span data-stu-id="3af69-185">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="3af69-186">Identyfikator URI powinien być `WeatherForecast` wyświetlany w wywołaniu jako `WeatherForcast` (brak "e").</span><span class="sxs-lookup"><span data-stu-id="3af69-186">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="3af69-187"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Wywołanie oczekuje, że kod JSON ma zostać zwrócony, ale serwer zwraca kod HTML dla nieobsłużonego wyjątku na serwerze z `Content-Type` `text/html` .</span><span class="sxs-lookup"><span data-stu-id="3af69-187">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="3af69-188">Nieobsługiwany wyjątek występuje na serwerze, ponieważ nie odnaleziono ścieżki i oprogramowanie pośredniczące nie może obsłużyć strony lub widoku żądania.</span><span class="sxs-lookup"><span data-stu-id="3af69-188">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="3af69-189">W programie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> na kliencie <xref:System.NotSupportedException> jest generowany w przypadku zweryfikowania zawartości odpowiedzi jako innej niż JSON.</span><span class="sxs-lookup"><span data-stu-id="3af69-189">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="3af69-190">Wyjątek jest przechwytywany w `catch` bloku, w którym logika niestandardowa może zarejestrować błąd lub przedstawić użytkownikowi przyjazny komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="3af69-190">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="3af69-191">Poprzedni przykład jest przeznaczony do celów demonstracyjnych.</span><span class="sxs-lookup"><span data-stu-id="3af69-191">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="3af69-192">Aplikację serwerową interfejsu API sieci Web można skonfigurować tak, aby zwracała kod JSON nawet wtedy, gdy punkt końcowy nie istnieje lub wystąpił nieobsługiwany wyjątek na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3af69-192">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="3af69-193">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="3af69-193">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="3af69-194">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="3af69-194">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="3af69-195">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która jest obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3af69-195">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="3af69-196">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="3af69-196">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3af69-197">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="3af69-197">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3af69-198">Aby żądania z przeglądarki były wysyłane do punktu końcowego z innym źródłem, *punkt końcowy* musi włączyć [udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="3af69-198">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="3af69-199">[ Blazor WebAssembly Aplikacja Przykładowa ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstruje użycie mechanizmu CORS w składniku API wywołania ( `Pages/CallWebAPI.razor` ).</span><span class="sxs-lookup"><span data-stu-id="3af69-199">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="3af69-200">Aby uzyskać więcej informacji na temat mechanizmu CORS z bezpiecznymi żądaniami w Blazor aplikacjach, zobacz <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .</span><span class="sxs-lookup"><span data-stu-id="3af69-200">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="3af69-201">Aby uzyskać ogólne informacje na temat mechanizmu CORS z aplikacjami ASP.NET Core, zobacz <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="3af69-201">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3af69-202">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3af69-202">Additional resources</span></span>

* <span data-ttu-id="3af69-203"><xref:blazor/security/webassembly/additional-scenarios>: Obejmuje pokrycie przy użyciu <xref:System.Net.Http.HttpClient> , aby zapewnić bezpieczne żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3af69-203"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="3af69-204">Konfiguracja punktu końcowego HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="3af69-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="3af69-205">Współużytkowanie zasobów między źródłami (CORS) w formacie W3C</span><span class="sxs-lookup"><span data-stu-id="3af69-205">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
