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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="2b685-103">Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="2b685-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="2b685-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="2b685-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2b685-105">Aplikacje webassembly wywołują interfejsy API sieci Web przy użyciu `HttpClient` wstępnie skonfigurowanej usługi. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="2b685-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="2b685-106">Twórz żądania, które mogą obejmować opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, za Blazor pomocą pomocników JSON lub <xref:System.Net.Http.HttpRequestMessage>z.</span><span class="sxs-lookup"><span data-stu-id="2b685-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="2b685-107">`HttpClient` Usługa w Blazor aplikacjach webassembly koncentruje się na przesyłaniu żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="2b685-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="2b685-108">Wskazówki zawarte w tym temacie dotyczą Blazor tylko aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="2b685-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2b685-109">Aplikacje serwera wywołują interfejsy API <xref:System.Net.Http.HttpClient> sieci Web przy użyciu wystąpień <xref:System.Net.Http.IHttpClientFactory>, zwykle utworzonych przy użyciu. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="2b685-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="2b685-110">Wskazówki zawarte w tym temacie nie odnoszą Blazor się do aplikacji serwerowych.</span><span class="sxs-lookup"><span data-stu-id="2b685-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="2b685-111">Podczas opracowywania Blazor aplikacji serwerowych postępuj zgodnie <xref:fundamentals/http-requests>ze wskazówkami w temacie.</span><span class="sxs-lookup"><span data-stu-id="2b685-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="2b685-112">[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) &ndash; wybierz aplikację *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="2b685-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="2b685-113">Zobacz następujące składniki w przykładowej aplikacji *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="2b685-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="2b685-114">Wywoływanie interfejsu API sieci Web (*strony/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="2b685-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="2b685-115">Tester żądania HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="2b685-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="2b685-116">Pakiety</span><span class="sxs-lookup"><span data-stu-id="2b685-116">Packages</span></span>

<span data-ttu-id="2b685-117">Odwołuje się do pakietu NuGet [systemu .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="2b685-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="2b685-118">Dodaj usługę HttpClient</span><span class="sxs-lookup"><span data-stu-id="2b685-118">Add the HttpClient service</span></span>

<span data-ttu-id="2b685-119">W `Program.Main`programie Dodaj `HttpClient` usługę, jeśli jeszcze nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="2b685-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="2b685-120">HttpClient i pomocnicy JSON</span><span class="sxs-lookup"><span data-stu-id="2b685-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="2b685-121">Blazor W aplikacji webassembly [HttpClient](xref:fundamentals/http-requests) jest dostępna jako usługa wstępnie skonfigurowana do wykonywania żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="2b685-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="2b685-122">Aplikacja Blazor serwera domyślnie nie zawiera `HttpClient` usługi.</span><span class="sxs-lookup"><span data-stu-id="2b685-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="2b685-123">`HttpClient` Udostępnienie aplikacji przy użyciu [infrastruktury fabryki HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="2b685-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="2b685-124">`HttpClient`i pomocniki JSON są również używane do wywoływania punktów końcowych interfejsu API sieci Web innych firm.</span><span class="sxs-lookup"><span data-stu-id="2b685-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="2b685-125">`HttpClient`Program jest implementowany przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym wymuszania tych samych zasad pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="2b685-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="2b685-126">Adres podstawowy klienta jest ustawiany na adres serwera źródłowego.</span><span class="sxs-lookup"><span data-stu-id="2b685-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="2b685-127">Wstrzyknąć `HttpClient` wystąpienie przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="2b685-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="2b685-128">W poniższych przykładach przebieg internetowy interfejs API sieci Web przetwarza operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD).</span><span class="sxs-lookup"><span data-stu-id="2b685-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="2b685-129">Przykłady są oparte na `TodoItem` klasie, która przechowuje:</span><span class="sxs-lookup"><span data-stu-id="2b685-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="2b685-130">Identyfikator (`Id`, `long`) &ndash; unikatowy identyfikator elementu.</span><span class="sxs-lookup"><span data-stu-id="2b685-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="2b685-131">Nazwa (`Name`, `string`) &ndash; nazwa elementu.</span><span class="sxs-lookup"><span data-stu-id="2b685-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="2b685-132">Stan (`IsComplete`, `bool`) &ndash; wskazujący, czy zadanie do wykonania zostało zakończone.</span><span class="sxs-lookup"><span data-stu-id="2b685-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="2b685-133">Metody pomocnika JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="2b685-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="2b685-134">`GetFromJsonAsync`&ndash; Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="2b685-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2b685-135">W poniższym kodzie, `_todoItems` są wyświetlane przez składnik.</span><span class="sxs-lookup"><span data-stu-id="2b685-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="2b685-136">Metoda jest wyzwalana, gdy składnik jest gotowy do renderowania (OnInitializedAsync).[OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) `GetTodoItems`</span><span class="sxs-lookup"><span data-stu-id="2b685-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="2b685-137">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2b685-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="2b685-138">`PostAsJsonAsync`&ndash; Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w formacie JSON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="2b685-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2b685-139">W poniższym kodzie `_newItemName` jest dostarczany przez powiązany element składnika.</span><span class="sxs-lookup"><span data-stu-id="2b685-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="2b685-140">`AddItem` Metoda jest wyzwalana przez wybranie `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="2b685-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="2b685-141">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2b685-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2b685-142">Wywołania do `PostAsJsonAsync` zwrócenia <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="2b685-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="2b685-143">Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć metody `ReadFromJsonAsync<T>` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="2b685-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="2b685-144">`PutAsJsonAsync`&ndash; Wysyła żądanie HTTP Put, w tym zawartość zakodowaną w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="2b685-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="2b685-145">W poniższym kodzie `_editItem` wartości dla `Name` i `IsCompleted` są dostarczane przez powiązane elementy składnika.</span><span class="sxs-lookup"><span data-stu-id="2b685-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="2b685-146">Element `Id` jest ustawiany, gdy element jest wybrany w innej części interfejsu użytkownika i `EditItem` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="2b685-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="2b685-147">`SaveItem` Metoda jest wyzwalana przez wybranie elementu Save `<button>` .</span><span class="sxs-lookup"><span data-stu-id="2b685-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="2b685-148">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2b685-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2b685-149">Wywołania do `PutAsJsonAsync` zwrócenia <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="2b685-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="2b685-150">Aby zdeserializować zawartość JSON z komunikatu odpowiedzi, należy użyć metody `ReadFromJsonAsync<T>` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="2b685-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="2b685-151"><xref:System.Net.Http>zawiera dodatkowe metody rozszerzające do wysyłania żądań HTTP i otrzymywania odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b685-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="2b685-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) służy do wysyłania żądania HTTP Delete do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="2b685-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="2b685-153">W poniższym kodzie element Delete `<button>` wywołuje `DeleteItem` metodę.</span><span class="sxs-lookup"><span data-stu-id="2b685-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="2b685-154">Element `id` powiązany `<input>` zawiera element do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="2b685-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="2b685-155">Pełny przykład można znaleźć w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2b685-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="2b685-156">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="2b685-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="2b685-157">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która jest obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="2b685-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="2b685-158">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="2b685-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="2b685-159">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="2b685-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2b685-160">Aby żądania z przeglądarki były wysyłane do punktu końcowego z innym źródłem, *punkt końcowy* musi włączyć [udostępnianie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="2b685-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="2b685-161">[Przykładowa aplikacja webassembly (BlazorWebAssemblySample) demonstruje użycie mechanizmu CORS w składniku API wywołania (Pages/CallWebAPI. Razor). Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) *Pages/CallWebAPI.razor*</span><span class="sxs-lookup"><span data-stu-id="2b685-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="2b685-162">Aby umożliwić innym lokacjom wykonywanie żądań funkcji udostępniania zasobów między źródłami (CORS) w aplikacji, zobacz <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="2b685-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b685-163">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2b685-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="2b685-164">Konfiguracja punktu końcowego HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="2b685-164">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="2b685-165">Współużytkowanie zasobów między źródłami (CORS) w formacie W3C</span><span class="sxs-lookup"><span data-stu-id="2b685-165">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
