---
title: Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core
author: stevejgordon
description: Dowiedz się więcej o używaniu interfejsu IHttpClientFactory do zarządzania wystąpieniami logicznych HttpClient w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057333"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Kirka Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc)i [Ryan Nowak](https://github.com/rynowak).

<xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji. `IHttpClientFactory` oferuje następujące korzyści:

* Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych. Na przykład klient o nazwie  *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/). Domyślny klient można zarejestrować na potrzeby ogólnego dostępu.
* Kodyfikacja koncepcji wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` . Udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z delegowania programów obsługi w programie `HttpClient` .
* Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` wystąpień podstawowych. Automatyczne zarządzanie pozwala uniknąć typowych problemów z usługą DNS (Domain Name System) występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).

Przykładowy kod w tym temacie używa <xref:System.Text.Json> do deserializacji zawartości JSON zwróconej w odpowiedziach http. Aby zapoznać się z przykładami używanymi przez `Json.NET` `ReadAsAsync<T>` program i, użyj selektora wersji, aby wybrać wersję 2. x tego tematu.

## <a name="consumption-patterns"></a>Wzorce zużycia

`IHttpClientFactory`W aplikacji można używać kilku sposobów:

* [Podstawowy sposób użycia](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Wpisane komputery klienckie](#typed-clients)
* [Wygenerowane klienci](#generated-clients)

Najlepsze podejście zależy od wymagań aplikacji.

### <a name="basic-usage"></a>Podstawowy sposób użycia

`IHttpClientFactory` można zarejestrować, wywołując `AddHttpClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

`IHttpClientFactory`Można zażądać przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection). Następujący kod używa `IHttpClientFactory` do tworzenia `HttpClient` wystąpienia:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Użycie `IHttpClientFactory` podobnej do powyższego przykładu jest dobrym sposobem refaktoryzacji istniejącej aplikacji. Nie ma to wpływu na sposób `HttpClient` użycia. W miejscach, w których `HttpClient` wystąpienia są tworzone w istniejącej aplikacji, Zastąp te wystąpienia wywołaniami do <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Nazwani klienci

Nazwani klienci są dobrym wyborem w przypadku:

* Aplikacja wymaga wielu odrębnych użycia `HttpClient` .
* Wiele `HttpClient` s ma inną konfigurację.

Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W powyższym kodzie klient ma skonfigurowany program:

* Adres podstawowy `https://api.github.com/` .
* Dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.

#### <a name="createclient"></a>Klient

Za każdym razem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> jest wywoływana:

* Tworzone jest nowe wystąpienie `HttpClient` .
* Akcja konfiguracji jest wywoływana.

Aby utworzyć nazwanego klienta, Przekaż jego nazwę do `CreateClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W powyższym kodzie żądanie nie musi określać nazwy hosta. Kod może przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.

### <a name="typed-clients"></a>Wpisane komputery klienckie

Wpisane komputery klienckie:

* Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.
* Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.
* Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` . Na przykład może zostać użyty pojedynczy klient z określonym typem:
  * Dla pojedynczego punktu końcowego zaplecza.
  * Do hermetyzacji wszystkich logiki związanych z punktem końcowym.
* Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.

Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Powyższy kod ma następujące działanie:

* Konfiguracja zostanie przeniesiona do określonego klienta.
* `HttpClient`Obiekt jest ujawniany jako właściwość publiczna.

Można tworzyć metody specyficzne dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność. Na przykład `GetAspNetDocsIssues` Metoda hermetyzuje kod w celu pobrania otwartych problemów.

Następujący kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> w `Startup.ConfigureServices` celu zarejestrowania klasy klienta typu:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typ klienta jest rejestrowany jako przejściowy z DI. W poprzednim kodzie program `AddHttpClient` rejestruje `GitHubService` jako usługę przejściową. Ta rejestracja używa metody fabryki do:

1. Utwórz wystąpienie elementu `HttpClient`.
1. Utwórz wystąpienie `GitHubService` , przekazując w wystąpieniu `HttpClient` do jego konstruktora.

Określony klient może zostać dodany i wykorzystany bezpośrednio:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Konfigurację dla określonego klienta można określić podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient`Mogą być hermetyzowane w ramach określonego klienta. Zamiast uwidaczniać ją jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie, `HttpClient` jest przechowywany w prywatnym polu. Dostęp do programu `HttpClient` ma metoda publiczna `GetRepos` .

### <a name="generated-clients"></a>Wygenerowane klienci

`IHttpClientFactory` może być używany w połączeniu z bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit). REFIT to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.

Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a>Wykonywanie żądań POST, PUT i DELETE

W powyższych przykładach wszystkie żądania HTTP używają zlecenia GET HTTP. `HttpClient` Program obsługuje również inne zlecenia HTTP, w tym:

* POST
* PUT
* DELETE
* WYSŁANA

Aby uzyskać pełną listę obsługiwanych czasowników HTTP, zobacz <xref:System.Net.Http.HttpMethod> .

Poniższy przykład pokazuje, jak utworzyć żądanie HTTP POST:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

W poprzednim kodzie `CreateItemAsync` Metoda:

* Serializować `TodoItem` parametr do formatu JSON przy użyciu `System.Text.Json` . Powoduje to użycie wystąpienia programu <xref:System.Text.Json.JsonSerializerOptions> w celu skonfigurowania procesu serializacji.
* Tworzy wystąpienie <xref:System.Net.Http.StringContent> do pakowania serializowanego kodu JSON do wysłania w treści żądania HTTP.
* Wywołuje <xref:System.Net.Http.HttpClient.PostAsync%2A> wysyłanie zawartości JSON do określonego adresu URL. Jest to względny adres URL, który zostanie dodany do [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).
* Wywołuje, <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> Aby zgłosić wyjątek, jeśli kod stanu odpowiedzi nie wskazuje powodzenia.

`HttpClient` obsługuje również inne typy zawartości. Przykład: <xref:System.Net.Http.MultipartContent> i <xref:System.Net.Http.StreamContent>. Aby zapoznać się z pełną listą obsługiwanych zawartości, zobacz <xref:System.Net.Http.HttpContent> .

Poniższy przykład przedstawia żądanie HTTP PUT:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

Poprzedni kod jest bardzo podobny do przykładu POST. `SaveItemAsync`Metoda wywołuje <xref:System.Net.Http.HttpClient.PutAsync%2A> zamiast `PostAsync` .

Poniższy przykład przedstawia żądanie HTTP DELETE:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

W poprzednim kodzie `DeleteItemAsync` Metoda wywołuje metodę <xref:System.Net.Http.HttpClient.DeleteAsync%2A> . Ponieważ żądania HTTP DELETE zazwyczaj nie zawierają treści, `DeleteAsync` Metoda nie zapewnia przeciążenia, które akceptuje wystąpienie `HttpContent` .

Aby dowiedzieć się więcej o używaniu różnych zleceń HTTP w programie `HttpClient` , zobacz <xref:System.Net.Http.HttpClient> .

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzące

`HttpClient` ma koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP. `IHttpClientFactory`:

  * Upraszcza definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.
  * Obsługuje rejestrację i łańcuch wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym. Ten wzorzec:
  
    * Jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.
    * Program udostępnia mechanizm zarządzania problemami z obcinaniem między żądaniami HTTP, takimi jak:
      * pamięć
      * Obsługa błędów
      * serializacji
      * rejestrowanie

Aby utworzyć program obsługi delegowania:

* Pochodny od <xref:System.Net.Http.DelegatingHandler> .
* Zastąpienie <xref:System.Net.Http.DelegatingHandler.SendAsync*> . Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod sprawdza, czy `X-API-KEY` nagłówek jest w żądaniu. Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.

Do konfiguracji dla elementu with można dodać więcej niż jeden program obsługi `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di. Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.

Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane. Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a>Użyj programu "DI" dla oprogramowania pośredniczącego żądań wychodzących

Gdy `IHttpClientFactory` program tworzy nową procedurę delegowania, używa di do spełnienia parametrów konstruktora procedury obsługi. `IHttpClientFactory` tworzy **oddzielny** zakres di dla każdej procedury obsługi, co może prowadzić do zaskakujące zachowania, gdy program obsługi korzysta z usługi w *zakresie* .

Rozważmy na przykład następujący interfejs i jego implementacja, która reprezentuje zadanie jako operację z identyfikatorem `OperationId` :

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

W miarę jak nazwa sugeruje, `IOperationScoped` jest zarejestrowana za pomocą di przy użyciu okresu istnienia w *zakresie* :

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

Następująca procedura delegowania wykorzystuje i używa `IOperationScoped` do ustawiania `X-OPERATION-ID` nagłówka dla żądania wychodzącego:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

W obszarze [ `HttpRequestsSample` pobieranie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] Przejdź do `/Operation` strony i Odśwież stronę. Wartość zakresu żądania zmienia się dla każdego żądania, ale wartość zakresu procedury obsługi zmienia się co 5 sekund.

Programy obsługi mogą zależeć od usług dowolnego zakresu. Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.

Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:

* Przekaż dane do procedury obsługi przy użyciu [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Użyj, <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> Aby uzyskać dostęp do bieżącego żądania.
* Utwórz niestandardowy <xref:System.Threading.AsyncLocal`1> obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na Polly

`IHttpClientFactory` integruje się z biblioteką innych firm [Polly](https://github.com/App-vNext/Polly). Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET. Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.

Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami. Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na Polly do klientów. Polly wymaga pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .

### <a name="handle-transient-faults"></a>Obsługa błędów przejściowych

Błędy są zwykle wykonywane, gdy zewnętrzne wywołania HTTP są przejściowe. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych. Skonfigurowane zasady z `AddTransientHttpErrorPolicy` obsługą następujących odpowiedzi:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady. Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne Wybieranie zasad

Metody rozszerzające umożliwiają dodawanie programów obsługi opartych na Polly, na przykład <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> . Następujące `AddPolicyHandler` Przeciążenie przeprowadziło inspekcję żądania, aby zdecydować, które zasady zastosować:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu. Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodaj wiele programów obsługi Polly

Jest to typowy sposób zagnieżdżania zasad Pollyymi:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W powyższym przykładzie:

* Dodawane są dwa procedury obsługi.
* Pierwsza procedura obsługi używa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> do dodawania zasad ponawiania. Nieudane żądania są ponawiane do trzech razy.
* Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób wystąpi sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .

W poniższym kodzie:

* Są dodawane zasady "regularne" i "długie".
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> dodaje zasady "regularne" i "długie" z rejestru.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Aby uzyskać więcej informacji na temat `IHttpClientFactory` integracji z integracją i Polly, zobacz [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient i zarządzanie okresem istnienia

Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` . <xref:System.Net.Http.HttpMessageHandler>Tworzony jest na nazwany klient. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory` pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. `HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP. Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń. Niektóre programy obsługi powodują również, że połączenia są otwierane w nieskończoność, co może uniemożliwić oddziałanie programu obsługi w systemie DNS (Domain Name System).

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślną można przesłonić na podstawie nazwy klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient` wystąpienia mogą być zwykle traktowane jako obiekty .NET, które **nie** wymagają usuwania. Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> . `IHttpClientFactory` śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.

Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` . Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatywy do IHttpClientFactory

Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:

* Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.
* Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.

Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .

- Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.
- Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.
- Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.

Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.

- `SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami. To udostępnianie uniemożliwia wyczerpanie gniazda.
- `SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.

### <a name="no-loccookies"></a>Cookiewolumin

`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane. Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu. W przypadku aplikacji, które wymagają usługi cookie s, należy rozważyć następujące kwestie:

 - Wyłączanie cookie obsługi automatycznej
 - Unikanie `IHttpClientFactory`

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> wyłączania automatycznej cookie obsługi:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Rejestrowanie

Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu. Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler". Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań. Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku. W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.

Rejestrowanie odbywa się również w potoku obsługi żądania. W przykładzie *MyNamedClient* te komunikaty są rejestrowane z kategorią dziennika "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler". W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania. W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.

Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany w nagłówkach żądania lub w kodzie stanu odpowiedzi.

Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie HttpMessageHandler

Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.

`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata. Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Korzystanie z IHttpClientFactory w aplikacji konsolowej

W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory> jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .
* `MyService` tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` . `HttpClient` służy do pobierania strony sieci Web.
* `Main` tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Oprogramowanie pośredniczące propagacji nagłówka

Propagacja nagłówka to ASP.NET Core oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP. Aby użyć propagacji nagłówka:

* Odwołuje się do pakietu [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .
* Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Jak serializować i deserializować kod JSON w programie .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji. Oferuje następujące korzyści:

* Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych. Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/). Domyślny klient można zarejestrować do innych celów.
* Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.
* Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Wzorce zużycia

`IHttpClientFactory`W aplikacji można używać kilku sposobów:

* [Podstawowy sposób użycia](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Wpisane komputery klienckie](#typed-clients)
* [Wygenerowane klienci](#generated-clients)

Żadna z nich nie jest ściśle wyższa od siebie. Najlepsze podejście zależy od ograniczeń aplikacji.

### <a name="basic-usage"></a>Podstawowy sposób użycia

`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection). `IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji. Nie ma to wpływu na sposób `HttpClient` użycia. W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Nazwani klienci

Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**. Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*. Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.

Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.

Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` . Określ nazwę klienta, który ma zostać utworzony:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W powyższym kodzie żądanie nie musi określać nazwy hosta. Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.

### <a name="typed-clients"></a>Wpisane komputery klienckie

Wpisane komputery klienckie:

* Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.
* Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.
* Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` . Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.
* Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.

Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta. `HttpClient`Obiekt jest ujawniany jako właściwość publiczna. Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność. `GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.

Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typ klienta jest rejestrowany jako przejściowy z DI. Określony klient może zostać dodany i wykorzystany bezpośrednio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta. Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne. Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.

### <a name="generated-clients"></a>Wygenerowane klienci

`IHttpClientFactory` może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit). REFIT to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.

Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzące

`HttpClient` ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP. `IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta. Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym. Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.

Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> . Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod definiuje procedurę obsługi podstawowej. Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu. Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.

Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` . To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di. `IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi. Programy obsługi są bezpłatne, aby zależeć od usług dowolnego zakresu. Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.

Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.

Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane. Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:

* Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .
* Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.
* Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na Polly

`IHttpClientFactory` integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly). Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET. Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.

Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami. Rozszerzenia Polly:

* Obsługa dodawania programów obsługi opartych na Pollych do klientów.
* Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.

### <a name="handle-transient-faults"></a>Obsługa błędów przejściowych

Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe. Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych. Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.

`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` . Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady. Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne Wybieranie zasad

Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly. Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń. Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu. Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodaj wiele programów obsługi Polly

Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W poprzednim przykładzie dodawane są dwa programy obsługi. Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania. Nieudane żądania są ponawiane do trzech razy. Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` . Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` . Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.

Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient i zarządzanie okresem istnienia

Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` . Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory` pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. `HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP. Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń. Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślną można przesłonić na podstawie nazwy klienta. Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Usuwanie klienta nie jest wymagane. Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> . `IHttpClientFactory` śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia. `HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.

Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` . Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatywy do IHttpClientFactory

Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:

* Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.
* Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.

Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .

- Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.
- Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.
- Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.

Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.

- `SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami. To udostępnianie uniemożliwia wyczerpanie gniazda.
- `SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.

### <a name="no-loccookies"></a>Cookiewolumin

`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane. Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu. W przypadku aplikacji, które wymagają usługi cookie s, należy rozważyć następujące kwestie:

 - Wyłączanie cookie obsługi automatycznej
 - Unikanie `IHttpClientFactory`

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> wyłączania automatycznej cookie obsługi:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Rejestrowanie

Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu. Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` . Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań. Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku. W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.

Rejestrowanie odbywa się również w potoku obsługi żądania. W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` . W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci. W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.

Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.

Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie HttpMessageHandler

Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.

`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata. Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Korzystanie z IHttpClientFactory w aplikacji konsolowej

W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory> jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .
* `MyService` tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` . `HttpClient` służy do pobierania strony sieci Web.
* `Main` tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji. Oferuje następujące korzyści:

* Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych. Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/). Domyślny klient można zarejestrować do innych celów.
* Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.
* Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Projekty docelowe .NET Framework wymagają instalacji pakietu NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) . Projekty przeznaczone dla platformy .NET Core i odwołujące się do [pakietu Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) zawierają już `Microsoft.Extensions.Http` pakiet.

## <a name="consumption-patterns"></a>Wzorce zużycia

`IHttpClientFactory`W aplikacji można używać kilku sposobów:

* [Podstawowy sposób użycia](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Wpisane komputery klienckie](#typed-clients)
* [Wygenerowane klienci](#generated-clients)

Żadna z nich nie jest ściśle wyższa od siebie. Najlepsze podejście zależy od ograniczeń aplikacji.

### <a name="basic-usage"></a>Podstawowy sposób użycia

`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection). `IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji. Nie ma to wpływu na sposób `HttpClient` użycia. W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Nazwani klienci

Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**. Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*. Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.

Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.

Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` . Określ nazwę klienta, który ma zostać utworzony:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W powyższym kodzie żądanie nie musi określać nazwy hosta. Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.

### <a name="typed-clients"></a>Wpisane komputery klienckie

Wpisane komputery klienckie:

* Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.
* Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.
* Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` . Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.
* Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.

Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta. `HttpClient`Obiekt jest ujawniany jako właściwość publiczna. Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność. `GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.

Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typ klienta jest rejestrowany jako przejściowy z DI. Określony klient może zostać dodany i wykorzystany bezpośrednio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta. Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne. Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.

### <a name="generated-clients"></a>Wygenerowane klienci

`IHttpClientFactory` może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit). REFIT to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.

Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzące

`HttpClient` ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP. `IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta. Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym. Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.

Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> . Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod definiuje procedurę obsługi podstawowej. Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu. Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.

Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` . To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di. Procedura obsługi **musi** być zarejestrowana w programie di jako przejściowa usługa, nigdy nie jest objęta zakresem. Jeśli program obsługi jest zarejestrowany jako usługa objęta zakresem, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:

* Usługi programu obsługi mogły zostać zlikwidowane, zanim program obsługi znajdzie się poza zakresem.
* Usunięte usługi obsługi powodują niepowodzenie procedury obsługi.

Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując w typie procedury obsługi.

Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane. Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:

* Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .
* Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.
* Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na Polly

`IHttpClientFactory` integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly). Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET. Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.

Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami. Rozszerzenia Polly:

* Obsługa dodawania programów obsługi opartych na Pollych do klientów.
* Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.

### <a name="handle-transient-faults"></a>Obsługa błędów przejściowych

Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe. Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych. Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.

`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` . Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady. Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne Wybieranie zasad

Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly. Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń. Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu. Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodaj wiele programów obsługi Polly

Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W poprzednim przykładzie dodawane są dwa programy obsługi. Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania. Nieudane żądania są ponawiane do trzech razy. Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` . Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` . Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.

Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient i zarządzanie okresem istnienia

Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` . Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory` pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. `HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP. Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń. Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślną można przesłonić na podstawie nazwy klienta. Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Usuwanie klienta nie jest wymagane. Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> . `IHttpClientFactory` śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia. `HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.

Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` . Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatywy do IHttpClientFactory

Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:

* Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.
* Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.

Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .

- Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.
- Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.
- Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.

Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.

- `SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami. To udostępnianie uniemożliwia wyczerpanie gniazda.
- `SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.

### <a name="no-loccookies"></a>Cookiewolumin

`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane. Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu. W przypadku aplikacji, które wymagają usługi cookie s, należy rozważyć następujące kwestie:

 - Wyłączanie cookie obsługi automatycznej
 - Unikanie `IHttpClientFactory`

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> wyłączania automatycznej cookie obsługi:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Rejestrowanie

Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu. Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` . Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań. Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku. W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.

Rejestrowanie odbywa się również w potoku obsługi żądania. W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` . W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci. W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.

Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.

Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie HttpMessageHandler

Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.

`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata. Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Korzystanie z IHttpClientFactory w aplikacji konsolowej

W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory> jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .
* `MyService` tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` . `HttpClient` służy do pobierania strony sieci Web.
* `Main` tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Oprogramowanie pośredniczące propagacji nagłówka

Propagacja nagłówka to społeczność obsługiwana przez oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP. Aby użyć propagacji nagłówka:

* Odwołuje się do obsługiwanego przez społeczność portu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)pakietu. ASP.NET Core 3,1 i nowsze obsługuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
