---
title: Wywoływanie internetowego interfejsu API Blazor z ASP.NET Core WebAssembly
author: guardrex
description: Dowiedz się, jak wywoływać Blazor internetowy interfejs API z aplikacji WebAssembly przy użyciu pomocników JSON, w tym wykonywanie żądań udostępniania zasobów między źródłami (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 2f2d4150f4fa1e7f47310f2a88b816f445cd1d3a
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544859"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Wywoływanie internetowego interfejsu API z ASP.NET CoreBlazor

Przez [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i Juan De la [Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikacje `HttpClient` WebAssembly dzwonią do internetowych interfejsów API przy użyciu wstępnie skonfigurowaną usługą. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Redagowanie żądań, które mogą zawierać opcje [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) języka JavaScript, przy użyciu Blazor pomocników JSON lub z <xref:System.Net.Http.HttpRequestMessage>. Usługa `HttpClient` w Blazor aplikacjach WebAssembly koncentruje się na żądaniu z powrotem do serwera pochodzenia. Wskazówki w tym temacie dotyczą Blazor tylko aplikacji WebAssembly.

Aplikacje serwera wywołują internetowe interfejsy <xref:System.Net.Http.IHttpClientFactory>API przy użyciu <xref:System.Net.Http.HttpClient> wystąpień, zwykle tworzonych przy użyciu programu . [ Blazor ](xref:blazor/hosting-models#blazor-server) Wskazówki w tym temacie nie Blazor odnoszą się do aplikacji serwera. Podczas Blazor tworzenia aplikacji serwera postępuj zgodnie ze wskazówkami w pliku <xref:fundamentals/http-requests>.

[Wyświetl lub pobierz przykładowy](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) kod &ndash; [(jak pobrać)](xref:index#how-to-download-a-sample)Wybierz aplikację *BlazorWebAssemblySample.*

Zobacz następujące składniki w *aplikacji przykładowej BlazorWebAssemblySample:*

* Wywołaj interfejs API sieci Web *(Pages/CallWebAPI.brzytwa)*
* Tester żądań HTTP *(składniki/HTTPRequestTester.brzytwa)*

## <a name="packages"></a>Pakiety

Odwołanie się do [pakietu System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet w pliku projektu.

## <a name="add-the-httpclient-service"></a>Dodawanie usługi HttpClient

W `Program.Main`, `HttpClient` dodaj usługę, jeśli jeszcze nie istnieje:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Pomocnicy HttpClient i JSON

W Blazor aplikacji WebAssembly [HttpClient](xref:fundamentals/http-requests) jest dostępny jako wstępnie skonfigurowana usługa do wysyłania żądań z powrotem do serwera pochodzenia.

Aplikacja Blazor Serwer domyślnie nie `HttpClient` zawiera usługi. Podaj `HttpClient` aplikację za pomocą [infrastruktury fabrycznej HttpClient](xref:fundamentals/http-requests).

`HttpClient`i JSON pomocników są również używane do wywoływania punktów końcowych web API innych firm. `HttpClient`jest implementowany przy użyciu interfejsu [API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API) przeglądarki i podlega jego ograniczeniom, w tym egzekwowaniu tej samej zasady pochodzenia.

Adres podstawowy klienta jest ustawiony na adres serwera źródłowego. Wstrzyknąć `@inject` wystąpienie `HttpClient` przy użyciu dyrektywy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

W poniższych przykładach procesy interfejsu API sieci Web Todo tworzą, odczytywać, aktualizować i usuwać (CRUD) operacje. Przykłady są oparte `TodoItem` na klasie, która przechowuje:

* Identyfikator (`Id` `long`), &ndash; unikatowy identyfikator elementu.
* Nazwa`Name`( `string` &ndash; , ) Nazwa elementu.
* Status`IsComplete`( `bool` &ndash; , ) Wskazanie, czy element Todo jest gotowy.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Metody pomocnicze JSON wysyłają żądania do identyfikatora URI (internetowego interfejsu API w poniższych przykładach) i przetwarzają odpowiedź:

* `GetFromJsonAsync`&ndash; Wysyła żądanie HTTP GET i analizuje treść odpowiedzi JSON, aby utworzyć obiekt.

  W poniższym `_todoItems` kodzie są wyświetlane przez składnik. Metoda `GetTodoItems` jest wyzwalana po zakończeniu renderowania składnika ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Zobacz przykładową aplikację, aby uzyskać pełny przykład.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Wysyła żądanie HTTP POST, w tym zawartość zakodowaną w USON, i analizuje treść odpowiedzi JSON w celu utworzenia obiektu.

  W poniższym `_newItemName` kodzie jest dostarczany przez powiązany element składnika. Metoda `AddItem` jest wyzwalana przez `<button>` wybranie elementu. Zobacz przykładową aplikację, aby uzyskać pełny przykład.

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
  
  Połączenia `PostAsJsonAsync` z <xref:System.Net.Http.HttpResponseMessage>prośbą o zwrot pliku .

* `PutAsJsonAsync`&ndash; Wysyła żądanie HTTP PUT, w tym zawartość zakodowaną w UO.

  W poniższym `_editItem` kodzie `Name` `IsCompleted` wartości i są dostarczane przez powiązane elementy składnika. Element `Id` jest ustawiany, gdy element jest zaznaczony w `EditItem` innej części interfejsu użytkownika i jest wywoływany. Metoda `SaveItem` jest wyzwalana przez wybranie Zapisz `<button>` elementu. Zobacz przykładową aplikację, aby uzyskać pełny przykład.

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
  
  Połączenia `PutAsJsonAsync` z <xref:System.Net.Http.HttpResponseMessage>prośbą o zwrot pliku .

<xref:System.Net.Http>zawiera dodatkowe metody rozszerzenia do wysyłania żądań HTTP i odbierania odpowiedzi HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) służy do wysyłania żądania HTTP DELETE do internetowego interfejsu API.

W poniższym kodzie `<button>` Delete `DeleteItem` element wywołuje metodę. Element `<input>` związany dostarcza `id` element do usunięcia. Zobacz przykładową aplikację, aby uzyskać pełny przykład.

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

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowy przydział zasobów między źródłami (CORS)

Zabezpieczenia przeglądarki uniemożliwiają stronie sieci Web składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web. To ograniczenie jest nazywane *zasadami tego samego pochodzenia*. Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny. Aby żądania z przeglądarki do punktu końcowego o innym *pochodzeniu, punkt końcowy* musi włączyć [współdzielenie zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).

[ Blazor Przykładowa aplikacja WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) pokazuje użycie cors w składniku interfejsu API sieci Web wywołania *(Pages/CallWebAPI.brzytwa*).

Aby zezwolić innym witrynom na kierowanie do aplikacji żądań <xref:security/cors>udostępniania zasobów między źródłami (CORS), zobacz .

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient i HttpRequestMessage z opcjami żądania interfejsu API pobierania

Podczas uruchamiania w sieci WebAssembly w aplikacji Blazor WebAssembly, należy użyć [HttpClient](xref:fundamentals/http-requests) i <xref:System.Net.Http.HttpRequestMessage> dostosować żądania. Na przykład można określić identyfikator URI żądania, metodę HTTP i wszystkie żądane nagłówki żądań.

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

Aby uzyskać więcej informacji na temat opcji interfejsu API pobierania, zobacz [dokumenty sieci Web MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Podczas wysyłania poświadczeń (pliki cookie autoryzacji/nagłówki) w żądaniach CORS `Authorization` nagłówek musi być dozwolony przez zasady CORS.

Następujące zasady obejmują konfigurację dla:

* Wniosek o`http://localhost:5000`pochodzenie `https://localhost:5001`( , ).
* Dowolna metoda (czasownik).
* `Content-Type`i `Authorization` nagłówki. Aby zezwolić na niestandardowy `x-custom-header`nagłówek (na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>przykład ), wyświetl nagłówek podczas wywoływania .
* Poświadczenia ustawione przez kod JavaScript`credentials` po stronie `include`klienta (właściwość ustawiona na ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Aby uzyskać więcej <xref:security/cors> informacji, zobacz i przykładowy składnik testera żądań HTTP aplikacji *(Components/HTTPRequestTester.brzytwa).*

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfiguracja punktu końcowego HTTPS kestrelu](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) w: W3C](https://www.w3.org/TR/cors/)
