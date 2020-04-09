---
title: Tworzenie żądań HTTP przy użyciu programu IHttpClientFactory w ASP.NET Core
author: stevejgordon
description: Dowiedz się więcej o używaniu interfejsu IHttpClientFactory do zarządzania logicznymi wystąpieniami httpclient w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661687"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Tworzenie żądań HTTP przy użyciu programu IHttpClientFactory w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Glenn [Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), Steve [Gordon](https://github.com/stevejgordon), Rick [Anderson](https://twitter.com/RickAndMSFT)i [Kirk Larkin](https://github.com/serpent5)

Można <xref:System.Net.Http.IHttpClientFactory> zarejestrować i używać do <xref:System.Net.Http.HttpClient> konfigurowania i tworzenia wystąpień w aplikacji. `IHttpClientFactory`oferuje następujące korzyści:

* Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych. Na przykład klient o nazwie *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do [GitHub](https://github.com/). Domyślny klient może być zarejestrowany dla ogólnego dostępu.
* Codifies koncepcji wychodzącego oprogramowania pośredniczącego `HttpClient`za pośrednictwem delegowania obsługi w . Udostępnia rozszerzenia oprogramowania pośredniczącego opartego na polly, `HttpClient`aby skorzystać z delegowaniu programów obsługi w programie .
* Zarządza buforowania i okresu `HttpClientMessageHandler` istnienia wystąpienia bazowych. Automatyczne zarządzanie pozwala uniknąć typowych problemów z systemem DNS `HttpClient` (Domain Name System), które występują podczas ręcznego zarządzania okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(jak pobrać).](xref:index#how-to-download-a-sample)

Przykładowy kod w tej <xref:System.Text.Json> wersji tematu używa do deserializacji zawartości JSON zwróconej w odpowiedziach HTTP. W przypadku przykładów, które używają `Json.NET` i `ReadAsAsync<T>`, użyj selektora wersji, aby wybrać wersję 2.x tego tematu.

## <a name="consumption-patterns"></a>Konsumpcji

W aplikacji `IHttpClientFactory` można używać na kilka sposobów:

* [Podstawowy sposób użycia](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Klienci wpisywane](#typed-clients)
* [Wygenerowani klienci](#generated-clients)

Najlepsze podejście zależy od wymagań aplikacji.

### <a name="basic-usage"></a>Podstawowy sposób użycia

`IHttpClientFactory`można zarejestrować `AddHttpClient`dzwoniąc:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Można `IHttpClientFactory` zażądać za pomocą [iniekcji zależności (DI)](xref:fundamentals/dependency-injection). Do utworzenia `IHttpClientFactory` wystąpienia użyto następującego `HttpClient` kodu:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Korzystanie `IHttpClientFactory` z podobnych w poprzednim przykładzie jest dobrym sposobem refaktoryzacji istniejącej aplikacji. Nie ma wpływu `HttpClient` na sposób jego stosowania. W miejscach, w których `HttpClient` w istniejącej aplikacji tworzone są <xref:System.Net.Http.IHttpClientFactory.CreateClient*>wystąpienia, zastąp te zdarzenia wywołaniem .

### <a name="named-clients"></a>Nazwani klienci

Nazwani klienci są dobrym wyborem, gdy:

* Aplikacja wymaga wielu różnych `HttpClient`zastosowań .
* Wiele `HttpClient`s mają inną konfigurację.

Konfigurację dla `HttpClient` nazwanego można `Startup.ConfigureServices`określić podczas rejestracji w:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W poprzednim kodzie klient jest skonfigurowany za pomocą:

* Adres `https://api.github.com/`podstawowy .
* Dwa nagłówki wymagane do pracy z interfejsem API GitHub.

#### <a name="createclient"></a>Utwórzclient

Za <xref:System.Net.Http.IHttpClientFactory.CreateClient*> każdym razem jest nazywany:

* Zostanie utworzone `HttpClient` nowe wystąpienie.
* Wywołana jest akcja konfiguracji.

Aby utworzyć nazwanego klienta, `CreateClient`przekaż jego nazwę na:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W poprzednim kodzie żądanie nie musi określać nazwy hosta. Kod może przekazać tylko ścieżkę, ponieważ adres podstawowy skonfigurowany dla klienta jest używany.

### <a name="typed-clients"></a>Klienci wpisywane

Wpisywane klientów:

* Zapewnij te same możliwości, co nazwani klienci bez konieczności używania ciągów jako kluczy.
* Zapewnia pomoc IntelliSense i kompilatora podczas korzystania z klientów.
* Podaj jedną lokalizację, aby skonfigurować i współdziałać z określonym `HttpClient`. Na przykład można użyć pojedynczego wpisanego klienta:
  * Dla pojedynczego punktu końcowego wewnętrznej bazy danych.
  * Aby hermetyzować wszystkie logiki do czynienia z punktem końcowym.
* Praca z DI i może być wstrzykiwany w razie potrzeby w aplikacji.

Typowany klient akceptuje `HttpClient` parametr w konstruktorze:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Powyższy kod ma następujące działanie:

* Konfiguracja zostanie przeniesiona do wpisanego klienta.
* Obiekt `HttpClient` jest narażony jako właściwość publiczna.

Można utworzyć metody specyficzne dla `HttpClient` interfejsu API, które udostępniają funkcje. Na przykład `GetAspNetDocsIssues` metoda hermetyzuje kod, aby pobrać otwarte problemy.

Następujący kod <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> wywołuje, `Startup.ConfigureServices` aby zarejestrować klasę klienta wpisane:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Wpisany klient jest zarejestrowany jako przejściowy z DI. W poprzednim kodzie `AddHttpClient` rejestruje `GitHubService` jako usługi przejściowe. Ta rejestracja używa metody fabrycznej do:

1. Utwórz wystąpienie elementu `HttpClient`.
1. Utwórz wystąpienie `GitHubService`, przekazywanie `HttpClient` w wystąpieniu do jego konstruktora.

Wpisany klient może być wstrzykiwany i spożywany bezpośrednio:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Konfigurację wpisanego klienta można określić `Startup.ConfigureServices`podczas rejestracji w programie , a nie w konstruktorze wpisanego klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Można `HttpClient` hermetyzowane w wpisanym kliencie. Zamiast wystawiać go jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie `HttpClient` jest przechowywany w polu prywatnym. Dostęp do `HttpClient` jest metodą publiczną. `GetRepos`

### <a name="generated-clients"></a>Wygenerowani klienci

`IHttpClientFactory`mogą być używane w połączeniu z bibliotekami innych firm, takimi jak [Refit](https://github.com/paulcbetts/refit). Refit to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana `RestService`dynamicznie `HttpClient` przez program , używając do wykonywania zewnętrznych wywołań HTTP.

Interfejs i odpowiedź są zdefiniowane w celu reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

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

Typowany klient można dodać, za pomocą Refit do generowania implementacji:

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

Zdefiniowany interfejs może być zużywany w razie potrzeby, z implementacją dostarczoną przez DI i Refit:

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

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzącego

`HttpClient`ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP. `IHttpClientFactory`:

* Upraszcza definiowanie programów obsługi, aby zastosować dla każdego nazwanego klienta.
* Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi w celu utworzenia potoku oprogramowania pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać pracę przed i po żądaniu wychodzącym. Ten wzór:

  * Jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core.
  * Udostępnia mechanizm zarządzania przekrojowymi problemami wokół żądań HTTP, takimi jak:

    * Buforowanie
    * obsługa błędów
    * Serializacji
    * rejestrowanie

Aby utworzyć program obsługi delegującej:

* Wywodź z <xref:System.Net.Http.DelegatingHandler>pliku .
* Zastąd wyschnieć <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod sprawdza, `X-API-KEY` czy nagłówek znajduje się w żądaniu. Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.

Więcej niż jeden program obsługi można `HttpClient` dodać <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>do konfiguracji dla with:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

W poprzednim kodzie `ValidateHeaderHandler` jest zarejestrowany w DI. Tworzy `IHttpClientFactory` oddzielny zakres DI dla każdego programu obsługi. Programy obsługi mogą zależeć od usług dowolnego zakresu. Usługi, od których zależą programy obsługi, są usuwane, gdy program obsługi jest usuwany.

Po zarejestrowaniu, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazywanie w typie dla programu obsługi.

Wiele programów obsługi mogą być rejestrowane w kolejności, w jakiej należy wykonać. Każdy program obsługi zawija `HttpClientHandler` następny program obsługi, dopóki ostateczna wykonuje żądanie:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Użyj jednego z następujących metod, aby udostępnić stan na żądanie z programami obsługi wiadomości:

* Przekazywanie danych do programu obsługi przy użyciu [httpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Służy <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> do uzyskiwania dostępu do bieżącego żądania.
* Utwórz <xref:System.Threading.AsyncLocal`1> niestandardowy obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na polly

`IHttpClientFactory`integruje się z biblioteką firmy [Polly.](https://github.com/App-vNext/Polly) Polly to kompleksowa biblioteka do obsługi błędów i stanów przemijających dla platformy .NET. Umożliwia deweloperom do wyrażania zasad, takich jak Ponowienie próby, Wyłącznik, Limit czasu, Izolacja grodzi i Rezerwa w sposób płynny i bezpieczny dla wątków.

Metody rozszerzenia są dostarczane w celu umożliwienia `HttpClient` korzystania z zasad Polly ze skonfigurowanych wystąpień. Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na polly do klientów. Polly wymaga pakietu [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.

### <a name="handle-transient-faults"></a>Obsługa usterek przejściowych

Błędy zazwyczaj występują, gdy zewnętrzne wywołania HTTP są przejściowe. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych. Zasady skonfigurowane `AddTransientHttpErrorPolicy` z obsługą następujących odpowiedzi:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* Protokół HTTP 408

`AddTransientHttpErrorPolicy`zapewnia dostęp `PolicyBuilder` do obiektu skonfigurowanego do obsługi błędów reprezentujących ewentualny błąd przejściowy:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowano zasadę. Żądania nie powiodły się są ponawiane do trzech razy z opóźnieniem 600 ms między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne wybieranie zasad

Metody rozszerzenia są dostarczane w celu dodania programów obsługi opartych na polly, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>na przykład . Następujące `AddPolicyHandler` przeciążenie sprawdza żądanie, aby zdecydować, które zasady mają być stosowane:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest HTTP GET, 10-sekundowy limit czasu jest stosowany. Dla każdej innej metody HTTP używany jest 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodawanie wielu programów obsługi polly

Często zagnieżdżamy zasady Polly:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W poprzednim przykładzie:

* Dwa programy obsługi są dodawane.
* Pierwszy program obsługi <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> używa do dodania zasad ponawiania. Żądania nieudane są ponawiane do trzech razy.
* Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób występuje sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania za pośrednictwem tego klienta współużytkują ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na `PolicyRegistry`zdefiniowaniu ich raz i zarejestrowaniu ich w pliku .

W poniższym kodzie:

* Dodaje się "zwykłą" i "długą" policję.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>dodaje "regularne" i "długie" zasady z rejestru.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Aby uzyskać `IHttpClientFactory` więcej informacji na temat integracji i Polly, zobacz [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Zarządzanie protokołem i okresem istnienia httpclient

Nowe `HttpClient` wystąpienie jest `CreateClient` zwracane za `IHttpClientFactory`każdym razem, gdy jest wywoływana na . Tworzony <xref:System.Net.Http.HttpMessageHandler> jest na nazwanego klienta. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory`pule `HttpMessageHandler` wystąpień utworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. Wystąpienie `HttpMessageHandler` może być ponownie użyczony z `HttpClient` puli podczas tworzenia nowego wystąpienia, jeśli jego okres istnienia nie wygasł.

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi podstawowymi połączeniami HTTP. Tworzenie większej liczby programów obsługi niż jest to konieczne, może spowodować opóźnienia połączenia. Niektóre programy obsługi również zachować połączenia otwarte przez czas nieokreślony, co może uniemożliwić program obsługi reagowania na DNS (Domain Name System) zmiany.

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślną można zastąpić na podstawie nazwanego klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient`wystąpienia mogą być zazwyczaj traktowane jako obiekty **.NET, które nie** wymagają utylizacji. Likwidacja anuluje wychodzące `HttpClient` żądania i gwarantuje, <xref:System.IDisposable.Dispose*>że danego wystąpienia nie można użyć po wywołaniu . `IHttpClientFactory`usuwa zasoby używane przez `HttpClient` wystąpienia.

Utrzymywanie `HttpClient` jednego wystąpienia przy życiu przez długi czas jest typowym wzorcem używanym przed powstaniem . `IHttpClientFactory` Ten wzorzec staje `IHttpClientFactory`się niepotrzebny po migracji do .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatywy dla IHttpClientFactory

Używanie `IHttpClientFactory` w aplikacji obsługującej technologię DI pozwala uniknąć:

* Problemy z wyczerpaniem `HttpMessageHandler` zasobów przez buforowanie wystąpień.
* Nieaktualne `HttpMessageHandler` problemy z systemem DNS przez wystąpienia rowerowe w regularnych odstępach czasu.

Istnieją alternatywne sposoby rozwiązywania poprzednich problemów <xref:System.Net.Http.SocketsHttpHandler> przy użyciu wystąpienia długotrwałego.

- Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiony i używać go przez cały okres użytkowania aplikacji.
- Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> do odpowiedniej wartości na podstawie czasów odświeżania DNS.
- Utwórz `HttpClient` wystąpienia `new HttpClient(handler, disposeHandler: false)` przy użyciu w razie potrzeby.

Powyższe podejścia rozwiązują problemy `IHttpClientFactory` z zarządzaniem zasobami, które rozwiązuje się w podobny sposób.

- Współudzieli `SocketsHttpHandler` `HttpClient` połączenia między wystąpieniami. To udostępnianie zapobiega wyczerpaniu gniazda.
- Cykle `SocketsHttpHandler` połączeń zgodnie `PooledConnectionLifetime` z aby uniknąć starych problemów z systemem DNS.

### <a name="cookies"></a>Pliki cookie

W puli `HttpMessageHandler` wystąpień `CookieContainer` powoduje obiekty są współużytkowane. Udostępnianie nieoczekiwanych `CookieContainer` obiektów często powoduje niepoprawny kod. W przypadku aplikacji, które wymagają plików cookie, należy wziąć pod uwagę:

 - Wyłączenie automatycznej obsługi plików cookie
 - Unikanie`IHttpClientFactory`

Wywołanie, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> aby wyłączyć automatyczną obsługę plików cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Rejestrowanie

Klienci utworzone `IHttpClientFactory` za pomocą komunikatów dziennika rekordów dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta. Klient o nazwie *MyNamedClient*, na przykład rejestruje wiadomości z kategorii "System.Net.http.httpClient. **MyNamedClient**. LogicalHandler". Komunikaty sufiksy z *LogicalHandler* występują poza potoku obsługi żądań. W żądaniu wiadomości są rejestrowane, zanim inne programy obsługi w potoku przetworzyły go. W odpowiedzi wiadomości są rejestrowane po innych programów obsługi potoku otrzymały odpowiedź.

Rejestrowanie występuje również wewnątrz potoku obsługi żądań. W przykładzie *MyNamedClient* te wiadomości są rejestrowane z kategorią dziennika "System.Net.http.httpClient. **MyNamedClient**. ClientHandler". Dla żądania występuje po uruchomieniu wszystkich innych programów obsługi i bezpośrednio przed wysłaniem żądania. W odpowiedzi to rejestrowanie zawiera stan odpowiedzi, zanim przejdzie z powrotem za pośrednictwem potoku obsługi.

Włączenie rejestrowania na zewnątrz i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany w nagłówkach żądań lub kod stanu odpowiedzi.

Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dziennika dla określonych nazwanych klientów.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie usługi HttpMessageHandler

Może być konieczne kontrolowanie konfiguracji `HttpMessageHandler` wewnętrznej używanej przez klienta.

Jest `IHttpClientBuilder` zwracany podczas dodawania klientów nazwanych lub wpisanych. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzenia może służyć do definiowania delegata. Pełnomocnik jest używany do tworzenia i `HttpMessageHandler` konfigurowania podstawowego używanego przez tego klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Używanie programu IHttpClientFactory w aplikacji konsoli

W aplikacji konsoli dodaj do projektu następujące odwołania do pakietu:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego.](xref:fundamentals/host/generic-host)
* `MyService`tworzy wystąpienie fabryki klienta z usługi, która `HttpClient`jest używana do tworzenia pliku . `HttpClient`służy do pobierania strony sieci Web.
* `Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisu pierwszych 500 znaków zawartości strony sieci Web do konsoli.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Oprogramowanie pośredniczące propagacji nagłówka

Propagacja nagłówka jest ASP.NET core oprogramowanie pośredniczące do propagacji nagłówków HTTP z żądania przychodzącego do wychodzących żądań klienta HTTP. Aby użyć propagacji nagłówka, należy:

* Odwołanie się do pakietu [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)
* Skonfiguruj `HttpClient` oprogramowanie `Startup`pośredniczące i w :

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementowanie ponownych prób wywołania HTTP z wykładniczym wycofywania z httpclientfactory i polly zasad](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Jak serializować i deserialize JSON w .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Przez [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i Steve [Gordon](https://github.com/stevejgordon)

Można <xref:System.Net.Http.IHttpClientFactory> zarejestrować i używać do <xref:System.Net.Http.HttpClient> konfigurowania i tworzenia wystąpień w aplikacji. Oferuje następujące korzyści:

* Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych. Na przykład klient *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do [GitHub](https://github.com/). Domyślny klient może być zarejestrowany do innych celów.
* Codifies koncepcji wychodzącego oprogramowania pośredniczącego `HttpClient` za pośrednictwem delegowania obsługi i zapewnia rozszerzenia oprogramowania pośredniczącego opartego na polly, aby skorzystać z tego.
* Zarządza buforowanie i okres `HttpClientMessageHandler` istnienia wystąpienia podstawowe, aby uniknąć typowych `HttpClient` problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Konsumpcji

W aplikacji `IHttpClientFactory` można używać na kilka sposobów:

* [Podstawowy sposób użycia](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Klienci wpisywane](#typed-clients)
* [Wygenerowani klienci](#generated-clients)

Żaden z nich nie jest ściśle lepszy od drugiego. Najlepsze podejście zależy od ograniczeń aplikacji.

### <a name="basic-usage"></a>Podstawowy sposób użycia

Można `IHttpClientFactory` zarejestrować, wywołując `AddHttpClient` metodę rozszerzenia `IServiceCollection`na `Startup.ConfigureServices` , wewnątrz metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po zarejestrowaniu, kod `IHttpClientFactory` może zaakceptować w dowolnym miejscu usługi mogą być wstrzykiwane z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection). Może `IHttpClientFactory` służyć do tworzenia `HttpClient` wystąpienia:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Korzystanie `IHttpClientFactory` w ten sposób jest dobrym sposobem na refaktoryzator istniejącej aplikacji. Nie ma wpływu na `HttpClient` sposób jest używany. W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, <xref:System.Net.Http.IHttpClientFactory.CreateClient*>zastąp te zdarzenia wywołaniem .

### <a name="named-clients"></a>Nazwani klienci

Jeśli aplikacja wymaga wielu różnych `HttpClient`zastosowań , każdy z innej konfiguracji, opcja jest użycie **nazwanych klientów**. Konfigurację dla `HttpClient` nazwanego można `Startup.ConfigureServices`określić podczas rejestracji w programie .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W poprzednim kodzie, `AddHttpClient` jest wywoływana, podając nazwę *github*. Ten klient ma pewną&mdash;domyślną konfigurację zastosowaną mianowicie adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API GitHub.

Za `CreateClient` każdym razem jest wywoływana, nowe wystąpienie `HttpClient` jest tworzony i wywoływana jest akcja konfiguracji.

Aby zużywać nazwany klient, parametr ciągu `CreateClient`może być przekazywany do . Określ nazwę klienta, który ma zostać utworzony:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W poprzednim kodzie żądanie nie musi określać nazwy hosta. Może przekazać tylko ścieżkę, ponieważ używany jest adres podstawowy skonfigurowany dla klienta.

### <a name="typed-clients"></a>Klienci wpisywane

Wpisywane klientów:

* Zapewnij te same możliwości, co nazwani klienci bez konieczności używania ciągów jako kluczy.
* Zapewnia pomoc IntelliSense i kompilatora podczas korzystania z klientów.
* Podaj jedną lokalizację, aby skonfigurować i współdziałać z określonym `HttpClient`. Na przykład pojedynczy typowany klient może być używany dla pojedynczego punktu końcowego wewnętrznej bazy danych i hermetyzować całą logikę dotyczącą tego punktu końcowego.
* Praca z DI i mogą być wstrzykiwane w razie potrzeby w aplikacji.

Typowany klient akceptuje `HttpClient` parametr w konstruktorze:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

W poprzednim kodzie konfiguracja jest przenoszona do wpisanego klienta. Obiekt `HttpClient` jest narażony jako właściwość publiczna. Jest możliwe zdefiniowanie metod specyficznych `HttpClient` dla interfejsu API, które udostępniają funkcje. Metoda `GetAspNetDocsIssues` hermetyzuje kod potrzebny do wykonywania zapytań i analizowania najnowszych otwartych problemów z repozytorium GitHub.

Aby zarejestrować wpisanego <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, można użyć `Startup.ConfigureServices`ogólnej metody rozszerzenia w ramach , określając typizowana klasa klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Wpisany klient jest zarejestrowany jako przejściowy z DI. Wpisany klient może być wstrzykiwany i spożywany bezpośrednio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Jeśli jest to preferowane, konfiguracja wpisanego `Startup.ConfigureServices`klienta może być określona podczas rejestracji w programie , a nie w konstruktorze wpisanego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Jest możliwe, aby całkowicie hermetyzować `HttpClient` w obrębie klienta wpisane. Zamiast wystawiać go jako właściwość, metody publiczne mogą `HttpClient` być dostarczane, które wywołują wystąpienie wewnętrznie.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie `HttpClient` jest przechowywany jako pole prywatne. Cały dostęp do wykonywania połączeń `GetRepos` zewnętrznych przechodzi przez metodę.

### <a name="generated-clients"></a>Wygenerowani klienci

`IHttpClientFactory`mogą być używane w połączeniu z innymi bibliotekami innych firm, takimi jak [Refit](https://github.com/paulcbetts/refit). Refit to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana `RestService`dynamicznie `HttpClient` przez program , używając do wykonywania zewnętrznych wywołań HTTP.

Interfejs i odpowiedź są zdefiniowane w celu reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

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

Typowany klient można dodać, za pomocą Refit do generowania implementacji:

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

Zdefiniowany interfejs może być zużywany w razie potrzeby, z implementacją dostarczoną przez DI i Refit:

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

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzącego

`HttpClient`już ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP. Ułatwia `IHttpClientFactory` definiowanie programów obsługi, aby zastosować dla każdego klienta o nazwie. Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać pracę przed i po żądaniu wychodzącym. Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy wokół żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.

Aby utworzyć program obsługi, należy <xref:System.Net.Http.DelegatingHandler>zdefiniować klasę wywodzącą się z programu . Zastąp `SendAsync` metodę wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod definiuje podstawowy program obsługi. Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu. Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.

Podczas rejestracji można dodać jeden lub więcej programów `HttpClient`obsługi do konfiguracji programu . To zadanie jest realizowane za <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>pomocą metod rozszerzenia w programie .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

W poprzednim kodzie `ValidateHeaderHandler` jest zarejestrowany w DI. Tworzy `IHttpClientFactory` oddzielny zakres DI dla każdego programu obsługi. Programy obsługi mogą polegać na usługach dowolnego zakresu. Usługi, od których zależą programy obsługi, są usuwane, gdy program obsługi jest usuwany.

Po zarejestrowaniu, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazywanie w typie dla programu obsługi.

Wiele programów obsługi mogą być rejestrowane w kolejności, w jakiej należy wykonać. Każdy program obsługi zawija `HttpClientHandler` następny program obsługi, dopóki ostateczna wykonuje żądanie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Użyj jednego z następujących metod, aby udostępnić stan na żądanie z programami obsługi wiadomości:

* Przekazywanie danych do `HttpRequestMessage.Properties`programu obsługi przy użyciu programu .
* Służy `IHttpContextAccessor` do uzyskiwania dostępu do bieżącego żądania.
* Utwórz `AsyncLocal` niestandardowy obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na polly

`IHttpClientFactory`integruje się z popularną biblioteką firm o nazwie [Polly](https://github.com/App-vNext/Polly). Polly to kompleksowa biblioteka do obsługi błędów i stanów przemijających dla platformy .NET. Umożliwia deweloperom do wyrażania zasad, takich jak Ponowienie próby, Wyłącznik, Limit czasu, Izolacja grodzi i Rezerwa w sposób płynny i bezpieczny dla wątków.

Metody rozszerzenia są dostarczane w celu umożliwienia `HttpClient` korzystania z zasad Polly ze skonfigurowanych wystąpień. Rozszerzenia Polly:

* Obsługa dodawania programów obsługi opartych na polly do klientów.
* Może być używany po zainstalowaniu pakietu [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet. Pakiet nie jest uwzględniony w ASP.NET core udostępnionej struktury.

### <a name="handle-transient-faults"></a>Obsługa usterek przejściowych

Najczęściej błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe. Wygodna metoda `AddTransientHttpErrorPolicy` rozszerzenia o nazwie jest dołączona, która umożliwia definiowanie zasad do obsługi błędów przejściowych. Zasady skonfigurowane przy użyciu `HttpRequestException`tej metody rozszerzenia obsługują odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.

Rozszerzenie `AddTransientHttpErrorPolicy` może być `Startup.ConfigureServices`używane w programie . Rozszerzenie zapewnia dostęp `PolicyBuilder` do obiektu skonfigurowanego do obsługi błędów reprezentujących ewentualny błąd przejściowy:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowano zasadę. Żądania nie powiodły się są ponawiane do trzech razy z opóźnieniem 600 ms między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne wybieranie zasad

Istnieją dodatkowe metody rozszerzenia, które mogą służyć do dodawania programów obsługi opartych na polly. Jednym z `AddPolicyHandler`takich rozszerzeń jest , który ma wiele przeciążeń. Jedno przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają być stosowane:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest HTTP GET, 10-sekundowy limit czasu jest stosowany. Dla każdej innej metody HTTP używany jest 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodawanie wielu programów obsługi polly

Często zagnieżdżasz zasady Polly, aby zapewnić ulepszoną funkcjonalność:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W poprzednim przykładzie są dodawane dwa programy obsługi. Pierwszy używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania. Żądania nieudane są ponawiane do trzech razy. Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób występuje sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania za pośrednictwem tego klienta współużytkują ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na `PolicyRegistry`zdefiniowaniu ich raz i zarejestrowaniu ich w pliku . Dostępna jest metoda rozszerzenia, która umożliwia program obsługi, który ma zostać dodany przy użyciu zasad z rejestru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

W poprzednim kodzie dwie zasady są `PolicyRegistry` rejestrowane po `ServiceCollection`dodaniu do pliku . Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` używana jest metoda, przekazując nazwę zasad do zastosowania.

Więcej informacji `IHttpClientFactory` na temat integracji Polly można znaleźć na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Zarządzanie protokołem i okresem istnienia httpclient

Nowe `HttpClient` wystąpienie jest `CreateClient` zwracane za `IHttpClientFactory`każdym razem, gdy jest wywoływana na . Istnieje <xref:System.Net.Http.HttpMessageHandler> per nazwany klient. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory`pule `HttpMessageHandler` wystąpień utworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. Wystąpienie `HttpMessageHandler` może być ponownie użyczony z `HttpClient` puli podczas tworzenia nowego wystąpienia, jeśli jego okres istnienia nie wygasł.

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi podstawowymi połączeniami HTTP. Tworzenie większej liczby programów obsługi niż jest to konieczne, może spowodować opóźnienia połączenia. Niektóre programy obsługi również zachować połączenia otwarte przez czas nieokreślony, co może uniemożliwić program obsługi reagowania na zmiany DNS.

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślna może zostać zastąpiona na podstawie nazwanego klienta. Aby go zastąpić, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> wywołaj `IHttpClientBuilder` to, co jest zwracane podczas tworzenia klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Utylizacja klienta nie jest wymagana. Likwidacja anuluje wychodzące `HttpClient` żądania i gwarantuje, <xref:System.IDisposable.Dispose*>że danego wystąpienia nie można użyć po wywołaniu . `IHttpClientFactory`usuwa zasoby używane przez `HttpClient` wystąpienia. Wystąpienia `HttpClient` można ogólnie traktować jako obiekty .NET nie wymagające usuwania.

Utrzymywanie `HttpClient` jednego wystąpienia przy życiu przez długi czas jest typowym wzorcem używanym przed powstaniem . `IHttpClientFactory` Ten wzorzec staje `IHttpClientFactory`się niepotrzebny po migracji do .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatywy dla IHttpClientFactory

Używanie `IHttpClientFactory` w aplikacji obsługującej technologię DI pozwala uniknąć:

* Problemy z wyczerpaniem `HttpMessageHandler` zasobów przez buforowanie wystąpień.
* Nieaktualne `HttpMessageHandler` problemy z systemem DNS przez wystąpienia rowerowe w regularnych odstępach czasu.

Istnieją alternatywne sposoby rozwiązywania poprzednich problemów <xref:System.Net.Http.SocketsHttpHandler> przy użyciu wystąpienia długotrwałego.

- Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiony i używać go przez cały okres użytkowania aplikacji.
- Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> do odpowiedniej wartości na podstawie czasów odświeżania DNS.
- Utwórz `HttpClient` wystąpienia `new HttpClient(handler, disposeHandler: false)` przy użyciu w razie potrzeby.

Powyższe podejścia rozwiązują problemy `IHttpClientFactory` z zarządzaniem zasobami, które rozwiązuje się w podobny sposób.

- Współudzieli `SocketsHttpHandler` `HttpClient` połączenia między wystąpieniami. To udostępnianie zapobiega wyczerpaniu gniazda.
- Cykle `SocketsHttpHandler` połączeń zgodnie `PooledConnectionLifetime` z aby uniknąć starych problemów z systemem DNS.

### <a name="cookies"></a>Pliki cookie

W puli `HttpMessageHandler` wystąpień `CookieContainer` powoduje obiekty są współużytkowane. Udostępnianie nieoczekiwanych `CookieContainer` obiektów często powoduje niepoprawny kod. W przypadku aplikacji, które wymagają plików cookie, należy wziąć pod uwagę:

 - Wyłączenie automatycznej obsługi plików cookie
 - Unikanie`IHttpClientFactory`

Wywołanie, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> aby wyłączyć automatyczną obsługę plików cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Rejestrowanie

Klienci utworzone `IHttpClientFactory` za pomocą komunikatów dziennika rekordów dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta. Klient o nazwie *MyNamedClient*, na przykład, rejestruje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`wiadomości z kategorii . Komunikaty sufiksy z *LogicalHandler* występują poza potoku obsługi żądań. W żądaniu wiadomości są rejestrowane, zanim inne programy obsługi w potoku przetworzyły go. W odpowiedzi wiadomości są rejestrowane po innych programów obsługi potoku otrzymały odpowiedź.

Rejestrowanie występuje również wewnątrz potoku obsługi żądań. W przykładzie *MyNamedClient* te komunikaty są rejestrowane `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`względem kategorii dziennika . W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i bezpośrednio przed wysłaniem żądania w sieci. W odpowiedzi to rejestrowanie zawiera stan odpowiedzi, zanim przejdzie z powrotem za pośrednictwem potoku obsługi.

Włączenie rejestrowania na zewnątrz i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.

Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dziennika dla określonych nazwanych klientów, jeśli jest to konieczne.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie usługi HttpMessageHandler

Może być konieczne kontrolowanie konfiguracji `HttpMessageHandler` wewnętrznej używanej przez klienta.

Jest `IHttpClientBuilder` zwracany podczas dodawania klientów nazwanych lub wpisanych. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzenia może służyć do definiowania delegata. Pełnomocnik jest używany do tworzenia i `HttpMessageHandler` konfigurowania podstawowego używanego przez tego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Używanie programu IHttpClientFactory w aplikacji konsoli

W aplikacji konsoli dodaj do projektu następujące odwołania do pakietu:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego.](xref:fundamentals/host/generic-host)
* `MyService`tworzy wystąpienie fabryki klienta z usługi, która `HttpClient`jest używana do tworzenia pliku . `HttpClient`służy do pobierania strony sieci Web.
* `Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisu pierwszych 500 znaków zawartości strony sieci Web do konsoli.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementowanie ponownych prób wywołania HTTP z wykładniczym wycofywania z httpclientfactory i polly zasad](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Przez [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i Steve [Gordon](https://github.com/stevejgordon)

Można <xref:System.Net.Http.IHttpClientFactory> zarejestrować i używać do <xref:System.Net.Http.HttpClient> konfigurowania i tworzenia wystąpień w aplikacji. Oferuje następujące korzyści:

* Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych. Na przykład klient *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do [GitHub](https://github.com/). Domyślny klient może być zarejestrowany do innych celów.
* Codifies koncepcji wychodzącego oprogramowania pośredniczącego `HttpClient` za pośrednictwem delegowania obsługi i zapewnia rozszerzenia oprogramowania pośredniczącego opartego na polly, aby skorzystać z tego.
* Zarządza buforowanie i okres `HttpClientMessageHandler` istnienia wystąpienia podstawowe, aby uniknąć typowych `HttpClient` problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

Projekty przeznaczone dla platformy .NET Framework wymagają instalacji pakietu [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet. Projekty, które są przeznaczone dla platformy .NET Core i odwołują `Microsoft.Extensions.Http` się do [metapakiety Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app) zawierają już pakiet.

## <a name="consumption-patterns"></a>Konsumpcji

W aplikacji `IHttpClientFactory` można używać na kilka sposobów:

* [Podstawowy sposób użycia](#basic-usage)
* [Nazwani klienci](#named-clients)
* [Klienci wpisywane](#typed-clients)
* [Wygenerowani klienci](#generated-clients)

Żaden z nich nie jest ściśle lepszy od drugiego. Najlepsze podejście zależy od ograniczeń aplikacji.

### <a name="basic-usage"></a>Podstawowy sposób użycia

Można `IHttpClientFactory` zarejestrować, wywołując `AddHttpClient` metodę rozszerzenia `IServiceCollection`na `Startup.ConfigureServices` , wewnątrz metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po zarejestrowaniu, kod `IHttpClientFactory` może zaakceptować w dowolnym miejscu usługi mogą być wstrzykiwane z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection). Może `IHttpClientFactory` służyć do tworzenia `HttpClient` wystąpienia:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Korzystanie `IHttpClientFactory` w ten sposób jest dobrym sposobem na refaktoryzator istniejącej aplikacji. Nie ma wpływu na `HttpClient` sposób jest używany. W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, <xref:System.Net.Http.IHttpClientFactory.CreateClient*>zastąp te zdarzenia wywołaniem .

### <a name="named-clients"></a>Nazwani klienci

Jeśli aplikacja wymaga wielu różnych `HttpClient`zastosowań , każdy z innej konfiguracji, opcja jest użycie **nazwanych klientów**. Konfigurację dla `HttpClient` nazwanego można `Startup.ConfigureServices`określić podczas rejestracji w programie .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

W poprzednim kodzie, `AddHttpClient` jest wywoływana, podając nazwę *github*. Ten klient ma pewną&mdash;domyślną konfigurację zastosowaną mianowicie adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API GitHub.

Za `CreateClient` każdym razem jest wywoływana, nowe wystąpienie `HttpClient` jest tworzony i wywoływana jest akcja konfiguracji.

Aby zużywać nazwany klient, parametr ciągu `CreateClient`może być przekazywany do . Określ nazwę klienta, który ma zostać utworzony:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

W poprzednim kodzie żądanie nie musi określać nazwy hosta. Może przekazać tylko ścieżkę, ponieważ używany jest adres podstawowy skonfigurowany dla klienta.

### <a name="typed-clients"></a>Klienci wpisywane

Wpisywane klientów:

* Zapewnij te same możliwości, co nazwani klienci bez konieczności używania ciągów jako kluczy.
* Zapewnia pomoc IntelliSense i kompilatora podczas korzystania z klientów.
* Podaj jedną lokalizację, aby skonfigurować i współdziałać z określonym `HttpClient`. Na przykład pojedynczy typowany klient może być używany dla pojedynczego punktu końcowego wewnętrznej bazy danych i hermetyzować całą logikę dotyczącą tego punktu końcowego.
* Praca z DI i mogą być wstrzykiwane w razie potrzeby w aplikacji.

Typowany klient akceptuje `HttpClient` parametr w konstruktorze:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

W poprzednim kodzie konfiguracja jest przenoszona do wpisanego klienta. Obiekt `HttpClient` jest narażony jako właściwość publiczna. Jest możliwe zdefiniowanie metod specyficznych `HttpClient` dla interfejsu API, które udostępniają funkcje. Metoda `GetAspNetDocsIssues` hermetyzuje kod potrzebny do wykonywania zapytań i analizowania najnowszych otwartych problemów z repozytorium GitHub.

Aby zarejestrować wpisanego <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, można użyć `Startup.ConfigureServices`ogólnej metody rozszerzenia w ramach , określając typizowana klasa klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Wpisany klient jest zarejestrowany jako przejściowy z DI. Wpisany klient może być wstrzykiwany i spożywany bezpośrednio:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Jeśli jest to preferowane, konfiguracja wpisanego `Startup.ConfigureServices`klienta może być określona podczas rejestracji w programie , a nie w konstruktorze wpisanego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Jest możliwe, aby całkowicie hermetyzować `HttpClient` w obrębie klienta wpisane. Zamiast wystawiać go jako właściwość, metody publiczne mogą `HttpClient` być dostarczane, które wywołują wystąpienie wewnętrznie.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

W poprzednim kodzie `HttpClient` jest przechowywany jako pole prywatne. Cały dostęp do wykonywania połączeń `GetRepos` zewnętrznych przechodzi przez metodę.

### <a name="generated-clients"></a>Wygenerowani klienci

`IHttpClientFactory`mogą być używane w połączeniu z innymi bibliotekami innych firm, takimi jak [Refit](https://github.com/paulcbetts/refit). Refit to biblioteka REST dla platformy .NET. Konwertuje interfejsy API REST na interfejsy na żywo. Implementacja interfejsu jest generowana `RestService`dynamicznie `HttpClient` przez program , używając do wykonywania zewnętrznych wywołań HTTP.

Interfejs i odpowiedź są zdefiniowane w celu reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:

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

Typowany klient można dodać, za pomocą Refit do generowania implementacji:

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

Zdefiniowany interfejs może być zużywany w razie potrzeby, z implementacją dostarczoną przez DI i Refit:

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

## <a name="outgoing-request-middleware"></a>Oprogramowanie pośredniczące żądania wychodzącego

`HttpClient`już ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP. Ułatwia `IHttpClientFactory` definiowanie programów obsługi, aby zastosować dla każdego klienta o nazwie. Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego. Każdy z tych programów obsługi jest w stanie wykonać pracę przed i po żądaniu wychodzącym. Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy wokół żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.

Aby utworzyć program obsługi, należy <xref:System.Net.Http.DelegatingHandler>zdefiniować klasę wywodzącą się z programu . Zastąp `SendAsync` metodę wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Poprzedni kod definiuje podstawowy program obsługi. Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu. Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.

Podczas rejestracji można dodać jeden lub więcej programów `HttpClient`obsługi do konfiguracji programu . To zadanie jest realizowane za <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>pomocą metod rozszerzenia w programie .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

W poprzednim kodzie `ValidateHeaderHandler` jest zarejestrowany w DI. Program obsługi **musi** być zarejestrowany w DI jako usługi przejściowe, nigdy nie o zakresie. Jeśli program obsługi jest zarejestrowany jako usługa o określonym zakresie, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:

* Usługi programu obsługi można usunąć, zanim program obsługi wykracza poza zakres.
* Usługi obsługi dysponowana powoduje, że program obsługi nie powiedzie się.

Po zarejestrowaniu, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazywanie w typie obsługi.

Wiele programów obsługi mogą być rejestrowane w kolejności, w jakiej należy wykonać. Każdy program obsługi zawija `HttpClientHandler` następny program obsługi, dopóki ostateczna wykonuje żądanie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Użyj jednego z następujących metod, aby udostępnić stan na żądanie z programami obsługi wiadomości:

* Przekazywanie danych do `HttpRequestMessage.Properties`programu obsługi przy użyciu programu .
* Służy `IHttpContextAccessor` do uzyskiwania dostępu do bieżącego żądania.
* Utwórz `AsyncLocal` niestandardowy obiekt magazynu, aby przekazać dane.

## <a name="use-polly-based-handlers"></a>Korzystanie z programów obsługi opartych na polly

`IHttpClientFactory`integruje się z popularną biblioteką firm o nazwie [Polly](https://github.com/App-vNext/Polly). Polly to kompleksowa biblioteka do obsługi błędów i stanów przemijających dla platformy .NET. Umożliwia deweloperom do wyrażania zasad, takich jak Ponowienie próby, Wyłącznik, Limit czasu, Izolacja grodzi i Rezerwa w sposób płynny i bezpieczny dla wątków.

Metody rozszerzenia są dostarczane w celu umożliwienia `HttpClient` korzystania z zasad Polly ze skonfigurowanych wystąpień. Rozszerzenia Polly:

* Obsługa dodawania programów obsługi opartych na polly do klientów.
* Może być używany po zainstalowaniu pakietu [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet. Pakiet nie jest uwzględniony w ASP.NET core udostępnionej struktury.

### <a name="handle-transient-faults"></a>Obsługa usterek przejściowych

Najczęściej błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe. Wygodna metoda `AddTransientHttpErrorPolicy` rozszerzenia o nazwie jest dołączona, która umożliwia definiowanie zasad do obsługi błędów przejściowych. Zasady skonfigurowane przy użyciu `HttpRequestException`tej metody rozszerzenia obsługują odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.

Rozszerzenie `AddTransientHttpErrorPolicy` może być `Startup.ConfigureServices`używane w programie . Rozszerzenie zapewnia dostęp `PolicyBuilder` do obiektu skonfigurowanego do obsługi błędów reprezentujących ewentualny błąd przejściowy:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

W poprzednim kodzie `WaitAndRetryAsync` zdefiniowano zasadę. Żądania nie powiodły się są ponawiane do trzech razy z opóźnieniem 600 ms między próbami.

### <a name="dynamically-select-policies"></a>Dynamiczne wybieranie zasad

Istnieją dodatkowe metody rozszerzenia, które mogą służyć do dodawania programów obsługi opartych na polly. Jednym z `AddPolicyHandler`takich rozszerzeń jest , który ma wiele przeciążeń. Jedno przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają być stosowane:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

W poprzednim kodzie, jeśli żądanie wychodzące jest HTTP GET, 10-sekundowy limit czasu jest stosowany. Dla każdej innej metody HTTP używany jest 30-sekundowy limit czasu.

### <a name="add-multiple-polly-handlers"></a>Dodawanie wielu programów obsługi polly

Często zagnieżdżasz zasady Polly, aby zapewnić ulepszoną funkcjonalność:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

W poprzednim przykładzie są dodawane dwa programy obsługi. Pierwszy używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania. Żądania nieudane są ponawiane do trzech razy. Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika. Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób występuje sekwencyjnie. Zasady wyłącznika są stanowe. Wszystkie wywołania za pośrednictwem tego klienta współużytkują ten sam stan obwodu.

### <a name="add-policies-from-the-polly-registry"></a>Dodawanie zasad z rejestru Polly

Podejście do zarządzania regularnie używanymi zasadami polega na `PolicyRegistry`zdefiniowaniu ich raz i zarejestrowaniu ich w pliku . Dostępna jest metoda rozszerzenia, która umożliwia program obsługi, który ma zostać dodany przy użyciu zasad z rejestru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

W poprzednim kodzie dwie zasady są `PolicyRegistry` rejestrowane po `ServiceCollection`dodaniu do pliku . Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` używana jest metoda, przekazując nazwę zasad do zastosowania.

Więcej informacji `IHttpClientFactory` na temat integracji Polly można znaleźć na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Zarządzanie protokołem i okresem istnienia httpclient

Nowe `HttpClient` wystąpienie jest `CreateClient` zwracane za `IHttpClientFactory`każdym razem, gdy jest wywoływana na . Istnieje <xref:System.Net.Http.HttpMessageHandler> per nazwany klient. Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.

`IHttpClientFactory`pule `HttpMessageHandler` wystąpień utworzonych przez fabrykę w celu zmniejszenia zużycia zasobów. Wystąpienie `HttpMessageHandler` może być ponownie użyczony z `HttpClient` puli podczas tworzenia nowego wystąpienia, jeśli jego okres istnienia nie wygasł.

Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi podstawowymi połączeniami HTTP. Tworzenie większej liczby programów obsługi niż jest to konieczne, może spowodować opóźnienia połączenia. Niektóre programy obsługi również zachować połączenia otwarte przez czas nieokreślony, co może uniemożliwić program obsługi reagowania na zmiany DNS.

Domyślny okres istnienia programu obsługi wynosi dwie minuty. Wartość domyślna może zostać zastąpiona na podstawie nazwanego klienta. Aby go zastąpić, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> wywołaj `IHttpClientBuilder` to, co jest zwracane podczas tworzenia klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Utylizacja klienta nie jest wymagana. Likwidacja anuluje wychodzące `HttpClient` żądania i gwarantuje, <xref:System.IDisposable.Dispose*>że danego wystąpienia nie można użyć po wywołaniu . `IHttpClientFactory`usuwa zasoby używane przez `HttpClient` wystąpienia. Wystąpienia `HttpClient` można ogólnie traktować jako obiekty .NET nie wymagające usuwania.

Utrzymywanie `HttpClient` jednego wystąpienia przy życiu przez długi czas jest typowym wzorcem używanym przed powstaniem . `IHttpClientFactory` Ten wzorzec staje `IHttpClientFactory`się niepotrzebny po migracji do .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatywy dla IHttpClientFactory

Używanie `IHttpClientFactory` w aplikacji obsługującej technologię DI pozwala uniknąć:

* Problemy z wyczerpaniem `HttpMessageHandler` zasobów przez buforowanie wystąpień.
* Nieaktualne `HttpMessageHandler` problemy z systemem DNS przez wystąpienia rowerowe w regularnych odstępach czasu.

Istnieją alternatywne sposoby rozwiązywania poprzednich problemów <xref:System.Net.Http.SocketsHttpHandler> przy użyciu wystąpienia długotrwałego.

- Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiony i używać go przez cały okres użytkowania aplikacji.
- Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> do odpowiedniej wartości na podstawie czasów odświeżania DNS.
- Utwórz `HttpClient` wystąpienia `new HttpClient(handler, disposeHandler: false)` przy użyciu w razie potrzeby.

Powyższe podejścia rozwiązują problemy `IHttpClientFactory` z zarządzaniem zasobami, które rozwiązuje się w podobny sposób.

- Współudzieli `SocketsHttpHandler` `HttpClient` połączenia między wystąpieniami. To udostępnianie zapobiega wyczerpaniu gniazda.
- Cykle `SocketsHttpHandler` połączeń zgodnie `PooledConnectionLifetime` z aby uniknąć starych problemów z systemem DNS.

### <a name="cookies"></a>Pliki cookie

W puli `HttpMessageHandler` wystąpień `CookieContainer` powoduje obiekty są współużytkowane. Udostępnianie nieoczekiwanych `CookieContainer` obiektów często powoduje niepoprawny kod. W przypadku aplikacji, które wymagają plików cookie, należy wziąć pod uwagę:

 - Wyłączenie automatycznej obsługi plików cookie
 - Unikanie`IHttpClientFactory`

Wywołanie, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> aby wyłączyć automatyczną obsługę plików cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Rejestrowanie

Klienci utworzone `IHttpClientFactory` za pomocą komunikatów dziennika rekordów dla wszystkich żądań. Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika. Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.

Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta. Klient o nazwie *MyNamedClient*, na przykład, rejestruje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`wiadomości z kategorii . Komunikaty sufiksy z *LogicalHandler* występują poza potoku obsługi żądań. W żądaniu wiadomości są rejestrowane, zanim inne programy obsługi w potoku przetworzyły go. W odpowiedzi wiadomości są rejestrowane po innych programów obsługi potoku otrzymały odpowiedź.

Rejestrowanie występuje również wewnątrz potoku obsługi żądań. W przykładzie *MyNamedClient* te komunikaty są rejestrowane `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`względem kategorii dziennika . W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i bezpośrednio przed wysłaniem żądania w sieci. W odpowiedzi to rejestrowanie zawiera stan odpowiedzi, zanim przejdzie z powrotem za pośrednictwem potoku obsługi.

Włączenie rejestrowania na zewnątrz i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku. Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.

Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dziennika dla określonych nazwanych klientów, jeśli jest to konieczne.

## <a name="configure-the-httpmessagehandler"></a>Konfigurowanie usługi HttpMessageHandler

Może być konieczne kontrolowanie konfiguracji `HttpMessageHandler` wewnętrznej używanej przez klienta.

Jest `IHttpClientBuilder` zwracany podczas dodawania klientów nazwanych lub wpisanych. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzenia może służyć do definiowania delegata. Pełnomocnik jest używany do tworzenia i `HttpMessageHandler` konfigurowania podstawowego używanego przez tego klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Używanie programu IHttpClientFactory w aplikacji konsoli

W aplikacji konsoli dodaj do projektu następujące odwołania do pakietu:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

W poniższym przykładzie:

* <xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego.](xref:fundamentals/host/generic-host)
* `MyService`tworzy wystąpienie fabryki klienta z usługi, która `HttpClient`jest używana do tworzenia pliku . `HttpClient`służy do pobierania strony sieci Web.
* `Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisu pierwszych 500 znaków zawartości strony sieci Web do konsoli.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Oprogramowanie pośredniczące propagacji nagłówka

Propagacja nagłówka jest obsługiwanym przez społeczność oprogramowaniem pośredniczącym do propagowania nagłówków HTTP z żądania przychodzącego do wychodzących żądań klienta HTTP. Aby użyć propagacji nagłówka, należy:

* Odwoływać się do społeczności obsługiwane portu pakietu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3.1 i nowsze obsługuje [microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Skonfiguruj `HttpClient` oprogramowanie `Startup`pośredniczące i w :

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementowanie ponownych prób wywołania HTTP z wykładniczym wycofywania z httpclientfactory i polly zasad](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementowanie wzorca wyłącznika](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
