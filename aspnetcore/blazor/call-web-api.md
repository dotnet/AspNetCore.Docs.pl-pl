---
title: Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak wywoływać Blazor internetowy interfejs API z aplikacji przy użyciu pomocników JSON, w tym wykonywanie żądań udostępniania zasobów między źródłami (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660147"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="7c6bd-103">Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="7c6bd-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="7c6bd-104">Przez [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="7c6bd-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7c6bd-105">Aplikacje `HttpClient` WebAssembly dzwonią do internetowych interfejsów API przy użyciu wstępnie skonfigurowaną usługą. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="7c6bd-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="7c6bd-106">Redagowanie żądań, które mogą zawierać opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) języka JavaScript, przy użyciu Blazor pomocników JSON lub z <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="7c6bd-107">Usługa `HttpClient` w Blazor aplikacjach WebAssembly koncentruje się na żądaniu z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="7c6bd-108">Wskazówki w tym temacie dotyczą Blazor tylko aplikacji WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="7c6bd-109">Aplikacje serwera wywołują internetowe interfejsy <xref:System.Net.Http.IHttpClientFactory>API przy użyciu <xref:System.Net.Http.HttpClient> wystąpień, zwykle tworzonych przy użyciu programu . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="7c6bd-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="7c6bd-110">Wskazówki w tym temacie nie Blazor odnoszą się do aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="7c6bd-111">Podczas Blazor tworzenia aplikacji serwera postępuj zgodnie ze wskazówkami w pliku <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="7c6bd-112">[Wyświetl lub pobierz przykładowy](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) kod &ndash; [(jak pobrać)](xref:index#how-to-download-a-sample)Wybierz aplikację *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="7c6bd-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="7c6bd-113">Zobacz następujące składniki w *aplikacji przykładowej BlazorWebAssemblySample:*</span><span class="sxs-lookup"><span data-stu-id="7c6bd-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="7c6bd-114">Wywołaj interfejs API sieci Web *(Pages/CallWebAPI.brzytwa)*</span><span class="sxs-lookup"><span data-stu-id="7c6bd-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="7c6bd-115">Tester żądań HTTP *(składniki/HTTPRequestTester.brzytwa)*</span><span class="sxs-lookup"><span data-stu-id="7c6bd-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="7c6bd-116">Pakiety</span><span class="sxs-lookup"><span data-stu-id="7c6bd-116">Packages</span></span>

<span data-ttu-id="7c6bd-117">Odwoływać się do *eksperymentalnego* [Microsoft.AspNetCore.Blazor. Pakiet HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-117">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="7c6bd-118">`Microsoft.AspNetCore.Blazor.HttpClient`opiera się `HttpClient` na systemie i [system.text.json](https://www.nuget.org/packages/System.Text.Json/).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-118">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="7c6bd-119">Aby użyć stabilnego interfejsu API, należy użyć pakietu [Microsoft.AspNet.WebApi.Client,](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) który używa [Json.NET Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-119">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="7c6bd-120">Za pomocą stabilnego interfejsu API w `Microsoft.AspNet.WebApi.Client` nie zapewnia pomocników JSON opisane w `Microsoft.AspNetCore.Blazor.HttpClient` tym temacie, które są unikatowe dla pakietu eksperymentalnego.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-120">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="7c6bd-121">Pomocnicy HttpClient i JSON</span><span class="sxs-lookup"><span data-stu-id="7c6bd-121">HttpClient and JSON helpers</span></span>

<span data-ttu-id="7c6bd-122">W Blazor aplikacji WebAssembly [HttpClient](xref:fundamentals/http-requests) jest dostępny jako wstępnie skonfigurowana usługa do wysyłania żądań z powrotem do serwera pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-122">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="7c6bd-123">Aplikacja Blazor Serwer domyślnie nie `HttpClient` zawiera usługi.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-123">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="7c6bd-124">Podaj `HttpClient` aplikację za pomocą [infrastruktury fabrycznej HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-124">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="7c6bd-125">`HttpClient`i JSON pomocników są również używane do wywoływania punktów końcowych web API innych firm.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-125">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="7c6bd-126">`HttpClient`jest implementowany przy użyciu interfejsu [API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym egzekwowaniu tej samej zasady pochodzenia.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-126">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="7c6bd-127">Adres podstawowy klienta jest ustawiony na adres serwera źródłowego.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-127">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="7c6bd-128">Wstrzyknąć `@inject` wystąpienie `HttpClient` przy użyciu dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="7c6bd-128">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="7c6bd-129">W poniższych przykładach procesy interfejsu API sieci Web Todo tworzą, odczytywać, aktualizować i usuwać (CRUD) operacje.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-129">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="7c6bd-130">Przykłady są oparte `TodoItem` na klasie, która przechowuje:</span><span class="sxs-lookup"><span data-stu-id="7c6bd-130">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="7c6bd-131">Identyfikator (`Id` `long`), &ndash; unikatowy identyfikator elementu.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-131">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="7c6bd-132">Nazwa`Name`( `string` &ndash; , ) Nazwa elementu.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-132">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="7c6bd-133">Status`IsComplete`( `bool` &ndash; , ) Wskazanie, czy element Todo jest gotowy.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-133">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="7c6bd-134">Metody pomocnicze JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="7c6bd-134">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="7c6bd-135">`GetJsonAsync`&ndash; Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON, aby utworzyć obiekt.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-135">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7c6bd-136">W poniższym `_todoItems` kodzie są wyświetlane przez składnik.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-136">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="7c6bd-137">Metoda `GetTodoItems` jest wyzwalana po zakończeniu renderowania składnika ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-137">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="7c6bd-138">Zobacz przykładową aplikację, aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-138">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="7c6bd-139">`PostJsonAsync`&ndash; Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w USON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-139">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7c6bd-140">W poniższym `_newItemName` kodzie jest dostarczany przez powiązany element składnika.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-140">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="7c6bd-141">Metoda `AddItem` jest wyzwalana przez `<button>` wybranie elementu.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-141">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="7c6bd-142">Zobacz przykładową aplikację, aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-142">See the sample app for a complete example.</span></span>

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
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* <span data-ttu-id="7c6bd-143">`PutJsonAsync`&ndash; Wysyła żądanie HTTP PUT, w tym zawartość zakodowaną w UO.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-143">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="7c6bd-144">W poniższym `_editItem` kodzie `Name` `IsCompleted` wartości i są dostarczane przez powiązane elementy składnika.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="7c6bd-145">Element `Id` jest ustawiany, gdy element jest zaznaczony w `EditItem` innej części interfejsu użytkownika i jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="7c6bd-146">Metoda `SaveItem` jest wyzwalana przez wybranie Zapisz `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="7c6bd-147">Zobacz przykładową aplikację, aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-147">See the sample app for a complete example.</span></span>

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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="7c6bd-148"><xref:System.Net.Http>zawiera dodatkowe metody rozszerzenia do wysyłania żądań HTTP i odbierania odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-148"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="7c6bd-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) służy do wysyłania żądania HTTP DELETE do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="7c6bd-150">W poniższym kodzie `<button>` Delete `DeleteItem` element wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-150">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="7c6bd-151">Element `<input>` związany dostarcza `id` element do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-151">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="7c6bd-152">Zobacz przykładową aplikację, aby uzyskać pełny przykład.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-152">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="7c6bd-153">Współużytkowy przydział zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="7c6bd-153">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="7c6bd-154">Zabezpieczenia przeglądarki uniemożliwiają stronie sieci Web składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-154">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="7c6bd-155">To ograniczenie jest nazywane *zasadami tego samego pochodzenia*.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-155">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7c6bd-156">Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-156">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7c6bd-157">Aby żądania z przeglądarki do punktu końcowego o innym *pochodzeniu, punkt końcowy* musi włączyć [współdzielenie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-157">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="7c6bd-158">[ Blazor Przykładowa aplikacja WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) pokazuje użycie cors w składniku interfejsu API sieci Web wywołania *(Pages/CallWebAPI.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-158">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="7c6bd-159">Aby zezwolić innym witrynom na kierowanie do aplikacji żądań <xref:security/cors>udostępniania zasobów między źródłami (CORS), zobacz .</span><span class="sxs-lookup"><span data-stu-id="7c6bd-159">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="7c6bd-160">HttpClient i HttpRequestMessage z opcjami żądania interfejsu API pobierania</span><span class="sxs-lookup"><span data-stu-id="7c6bd-160">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="7c6bd-161">Podczas uruchamiania w sieci WebAssembly w aplikacji Blazor WebAssembly, należy użyć [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> dostosować żądania.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-161">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="7c6bd-162">Na przykład można określić identyfikator URI żądania, metodę HTTP i wszystkie żądane nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-162">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="7c6bd-163">Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [dokumenty sieci Web MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="7c6bd-164">Podczas wysyłania poświadczeń (pliki cookie autoryzacji/nagłówki) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="7c6bd-165">Następujące zasady obejmują konfigurację dla:</span><span class="sxs-lookup"><span data-stu-id="7c6bd-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="7c6bd-166">Wniosek o`http://localhost:5000`pochodzenie `https://localhost:5001`( , ).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="7c6bd-167">Dowolna metoda (czasownik).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-167">Any method (verb).</span></span>
* <span data-ttu-id="7c6bd-168">`Content-Type`i `Authorization` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="7c6bd-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="7c6bd-169">Aby zezwolić na niestandardowy `x-custom-header`nagłówek (na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>przykład ), wyświetl nagłówek podczas wywoływania .</span><span class="sxs-lookup"><span data-stu-id="7c6bd-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="7c6bd-170">Poświadczenia ustawione przez kod JavaScript`credentials` po stronie `include`klienta (właściwość ustawiona na ).</span><span class="sxs-lookup"><span data-stu-id="7c6bd-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="7c6bd-171">Aby uzyskać więcej <xref:security/cors> informacji, zobacz i przykładowy składnik testera żądań HTTP aplikacji *(Components/HTTPRequestTester.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="7c6bd-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c6bd-172">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7c6bd-172">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="7c6bd-173">Konfiguracja punktu końcowego HTTPS kestrelu</span><span class="sxs-lookup"><span data-stu-id="7c6bd-173">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="7c6bd-174">Cross Origin Resource Sharing (CORS) w: W3C</span><span class="sxs-lookup"><span data-stu-id="7c6bd-174">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
