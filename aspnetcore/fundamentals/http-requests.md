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
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="717ef-103">Tworzenie żądań HTTP przy użyciu programu IHttpClientFactory w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="717ef-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="717ef-104">Glenn [Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), Steve [Gordon](https://github.com/stevejgordon), Rick [Anderson](https://twitter.com/RickAndMSFT)i [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="717ef-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="717ef-105">Można <xref:System.Net.Http.IHttpClientFactory> zarejestrować i używać do <xref:System.Net.Http.HttpClient> konfigurowania i tworzenia wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="717ef-106">`IHttpClientFactory`oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="717ef-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="717ef-107">Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="717ef-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="717ef-108">Na przykład klient o nazwie *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="717ef-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="717ef-109">Domyślny klient może być zarejestrowany dla ogólnego dostępu.</span><span class="sxs-lookup"><span data-stu-id="717ef-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="717ef-110">Codifies koncepcji wychodzącego oprogramowania pośredniczącego `HttpClient`za pośrednictwem delegowania obsługi w .</span><span class="sxs-lookup"><span data-stu-id="717ef-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="717ef-111">Udostępnia rozszerzenia oprogramowania pośredniczącego opartego na polly, `HttpClient`aby skorzystać z delegowaniu programów obsługi w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="717ef-112">Zarządza buforowania i okresu `HttpClientMessageHandler` istnienia wystąpienia bazowych.</span><span class="sxs-lookup"><span data-stu-id="717ef-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="717ef-113">Automatyczne zarządzanie pozwala uniknąć typowych problemów z systemem DNS `HttpClient` (Domain Name System), które występują podczas ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="717ef-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="717ef-114">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="717ef-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="717ef-115">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(jak pobrać).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="717ef-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="717ef-116">Przykładowy kod w tej <xref:System.Text.Json> wersji tematu używa do deserializacji zawartości JSON zwróconej w odpowiedziach HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="717ef-117">W przypadku przykładów, które używają `Json.NET` i `ReadAsAsync<T>`, użyj selektora wersji, aby wybrać wersję 2.x tego tematu.</span><span class="sxs-lookup"><span data-stu-id="717ef-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="717ef-118">Konsumpcji</span><span class="sxs-lookup"><span data-stu-id="717ef-118">Consumption patterns</span></span>

<span data-ttu-id="717ef-119">W aplikacji `IHttpClientFactory` można używać na kilka sposobów:</span><span class="sxs-lookup"><span data-stu-id="717ef-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="717ef-120">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="717ef-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="717ef-121">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="717ef-122">Klienci wpisywane</span><span class="sxs-lookup"><span data-stu-id="717ef-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="717ef-123">Wygenerowani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="717ef-124">Najlepsze podejście zależy od wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="717ef-125">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="717ef-125">Basic usage</span></span>

<span data-ttu-id="717ef-126">`IHttpClientFactory`można zarejestrować `AddHttpClient`dzwoniąc:</span><span class="sxs-lookup"><span data-stu-id="717ef-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="717ef-127">Można `IHttpClientFactory` zażądać za pomocą [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="717ef-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="717ef-128">Do utworzenia `IHttpClientFactory` wystąpienia użyto następującego `HttpClient` kodu:</span><span class="sxs-lookup"><span data-stu-id="717ef-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="717ef-129">Korzystanie `IHttpClientFactory` z podobnych w poprzednim przykładzie jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="717ef-130">Nie ma wpływu `HttpClient` na sposób jego stosowania.</span><span class="sxs-lookup"><span data-stu-id="717ef-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="717ef-131">W miejscach, w których `HttpClient` w istniejącej aplikacji tworzone są <xref:System.Net.Http.IHttpClientFactory.CreateClient*>wystąpienia, zastąp te zdarzenia wywołaniem .</span><span class="sxs-lookup"><span data-stu-id="717ef-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="717ef-132">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-132">Named clients</span></span>

<span data-ttu-id="717ef-133">Nazwani klienci są dobrym wyborem, gdy:</span><span class="sxs-lookup"><span data-stu-id="717ef-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="717ef-134">Aplikacja wymaga wielu różnych `HttpClient`zastosowań .</span><span class="sxs-lookup"><span data-stu-id="717ef-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="717ef-135">Wiele `HttpClient`s mają inną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="717ef-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="717ef-136">Konfigurację dla `HttpClient` nazwanego można `Startup.ConfigureServices`określić podczas rejestracji w:</span><span class="sxs-lookup"><span data-stu-id="717ef-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="717ef-137">W poprzednim kodzie klient jest skonfigurowany za pomocą:</span><span class="sxs-lookup"><span data-stu-id="717ef-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="717ef-138">Adres `https://api.github.com/`podstawowy .</span><span class="sxs-lookup"><span data-stu-id="717ef-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="717ef-139">Dwa nagłówki wymagane do pracy z interfejsem API GitHub.</span><span class="sxs-lookup"><span data-stu-id="717ef-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="717ef-140">Utwórzclient</span><span class="sxs-lookup"><span data-stu-id="717ef-140">CreateClient</span></span>

<span data-ttu-id="717ef-141">Za <xref:System.Net.Http.IHttpClientFactory.CreateClient*> każdym razem jest nazywany:</span><span class="sxs-lookup"><span data-stu-id="717ef-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="717ef-142">Zostanie utworzone `HttpClient` nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="717ef-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="717ef-143">Wywołana jest akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="717ef-143">The configuration action is called.</span></span>

<span data-ttu-id="717ef-144">Aby utworzyć nazwanego klienta, `CreateClient`przekaż jego nazwę na:</span><span class="sxs-lookup"><span data-stu-id="717ef-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="717ef-145">W poprzednim kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="717ef-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="717ef-146">Kod może przekazać tylko ścieżkę, ponieważ adres podstawowy skonfigurowany dla klienta jest używany.</span><span class="sxs-lookup"><span data-stu-id="717ef-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="717ef-147">Klienci wpisywane</span><span class="sxs-lookup"><span data-stu-id="717ef-147">Typed clients</span></span>

<span data-ttu-id="717ef-148">Wpisywane klientów:</span><span class="sxs-lookup"><span data-stu-id="717ef-148">Typed clients:</span></span>

* <span data-ttu-id="717ef-149">Zapewnij te same możliwości, co nazwani klienci bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="717ef-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="717ef-150">Zapewnia pomoc IntelliSense i kompilatora podczas korzystania z klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="717ef-151">Podaj jedną lokalizację, aby skonfigurować i współdziałać z określonym `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="717ef-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="717ef-152">Na przykład można użyć pojedynczego wpisanego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="717ef-153">Dla pojedynczego punktu końcowego wewnętrznej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="717ef-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="717ef-154">Aby hermetyzować wszystkie logiki do czynienia z punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="717ef-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="717ef-155">Praca z DI i może być wstrzykiwany w razie potrzeby w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="717ef-156">Typowany klient akceptuje `HttpClient` parametr w konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="717ef-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="717ef-157">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="717ef-157">In the preceding code:</span></span>

* <span data-ttu-id="717ef-158">Konfiguracja zostanie przeniesiona do wpisanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="717ef-159">Obiekt `HttpClient` jest narażony jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="717ef-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="717ef-160">Można utworzyć metody specyficzne dla `HttpClient` interfejsu API, które udostępniają funkcje.</span><span class="sxs-lookup"><span data-stu-id="717ef-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="717ef-161">Na przykład `GetAspNetDocsIssues` metoda hermetyzuje kod, aby pobrać otwarte problemy.</span><span class="sxs-lookup"><span data-stu-id="717ef-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="717ef-162">Następujący kod <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> wywołuje, `Startup.ConfigureServices` aby zarejestrować klasę klienta wpisane:</span><span class="sxs-lookup"><span data-stu-id="717ef-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="717ef-163">Wpisany klient jest zarejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="717ef-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="717ef-164">W poprzednim kodzie `AddHttpClient` rejestruje `GitHubService` jako usługi przejściowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="717ef-165">Ta rejestracja używa metody fabrycznej do:</span><span class="sxs-lookup"><span data-stu-id="717ef-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="717ef-166">Utwórz wystąpienie elementu `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="717ef-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="717ef-167">Utwórz wystąpienie `GitHubService`, przekazywanie `HttpClient` w wystąpieniu do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="717ef-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="717ef-168">Wpisany klient może być wstrzykiwany i spożywany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="717ef-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="717ef-169">Konfigurację wpisanego klienta można określić `Startup.ConfigureServices`podczas rejestracji w programie , a nie w konstruktorze wpisanego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="717ef-170">Można `HttpClient` hermetyzowane w wpisanym kliencie.</span><span class="sxs-lookup"><span data-stu-id="717ef-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="717ef-171">Zamiast wystawiać go jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="717ef-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="717ef-172">W poprzednim kodzie `HttpClient` jest przechowywany w polu prywatnym.</span><span class="sxs-lookup"><span data-stu-id="717ef-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="717ef-173">Dostęp do `HttpClient` jest metodą publiczną. `GetRepos`</span><span class="sxs-lookup"><span data-stu-id="717ef-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="717ef-174">Wygenerowani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-174">Generated clients</span></span>

<span data-ttu-id="717ef-175">`IHttpClientFactory`mogą być używane w połączeniu z bibliotekami innych firm, takimi jak [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="717ef-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="717ef-176">Refit to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="717ef-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="717ef-177">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="717ef-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="717ef-178">Implementacja interfejsu jest generowana `RestService`dynamicznie `HttpClient` przez program , używając do wykonywania zewnętrznych wywołań HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="717ef-179">Interfejs i odpowiedź są zdefiniowane w celu reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="717ef-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="717ef-180">Typowany klient można dodać, za pomocą Refit do generowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="717ef-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="717ef-181">Zdefiniowany interfejs może być zużywany w razie potrzeby, z implementacją dostarczoną przez DI i Refit:</span><span class="sxs-lookup"><span data-stu-id="717ef-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="717ef-182">Oprogramowanie pośredniczące żądania wychodzącego</span><span class="sxs-lookup"><span data-stu-id="717ef-182">Outgoing request middleware</span></span>

<span data-ttu-id="717ef-183">`HttpClient`ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="717ef-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="717ef-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="717ef-185">Upraszcza definiowanie programów obsługi, aby zastosować dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="717ef-186">Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi w celu utworzenia potoku oprogramowania pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="717ef-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="717ef-187">Każdy z tych programów obsługi jest w stanie wykonać pracę przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="717ef-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="717ef-188">Ten wzór:</span><span class="sxs-lookup"><span data-stu-id="717ef-188">This pattern:</span></span>

  * <span data-ttu-id="717ef-189">Jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="717ef-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="717ef-190">Udostępnia mechanizm zarządzania przekrojowymi problemami wokół żądań HTTP, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="717ef-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="717ef-191">Buforowanie</span><span class="sxs-lookup"><span data-stu-id="717ef-191">caching</span></span>
    * <span data-ttu-id="717ef-192">obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="717ef-192">error handling</span></span>
    * <span data-ttu-id="717ef-193">Serializacji</span><span class="sxs-lookup"><span data-stu-id="717ef-193">serialization</span></span>
    * <span data-ttu-id="717ef-194">rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="717ef-194">logging</span></span>

<span data-ttu-id="717ef-195">Aby utworzyć program obsługi delegującej:</span><span class="sxs-lookup"><span data-stu-id="717ef-195">To create a delegating handler:</span></span>

* <span data-ttu-id="717ef-196">Wywodź z <xref:System.Net.Http.DelegatingHandler>pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="717ef-197">Zastąd wyschnieć <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="717ef-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="717ef-198">Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="717ef-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="717ef-199">Poprzedni kod sprawdza, `X-API-KEY` czy nagłówek znajduje się w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="717ef-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="717ef-200">Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="717ef-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="717ef-201">Więcej niż jeden program obsługi można `HttpClient` dodać <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>do konfiguracji dla with:</span><span class="sxs-lookup"><span data-stu-id="717ef-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="717ef-202">W poprzednim kodzie `ValidateHeaderHandler` jest zarejestrowany w DI.</span><span class="sxs-lookup"><span data-stu-id="717ef-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="717ef-203">Tworzy `IHttpClientFactory` oddzielny zakres DI dla każdego programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="717ef-204">Programy obsługi mogą zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="717ef-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="717ef-205">Usługi, od których zależą programy obsługi, są usuwane, gdy program obsługi jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="717ef-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="717ef-206">Po zarejestrowaniu, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazywanie w typie dla programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="717ef-207">Wiele programów obsługi mogą być rejestrowane w kolejności, w jakiej należy wykonać.</span><span class="sxs-lookup"><span data-stu-id="717ef-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="717ef-208">Każdy program obsługi zawija `HttpClientHandler` następny program obsługi, dopóki ostateczna wykonuje żądanie:</span><span class="sxs-lookup"><span data-stu-id="717ef-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="717ef-209">Użyj jednego z następujących metod, aby udostępnić stan na żądanie z programami obsługi wiadomości:</span><span class="sxs-lookup"><span data-stu-id="717ef-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="717ef-210">Przekazywanie danych do programu obsługi przy użyciu [httpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="717ef-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="717ef-211">Służy <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> do uzyskiwania dostępu do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="717ef-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="717ef-212">Utwórz <xref:System.Threading.AsyncLocal`1> niestandardowy obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="717ef-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="717ef-213">Korzystanie z programów obsługi opartych na polly</span><span class="sxs-lookup"><span data-stu-id="717ef-213">Use Polly-based handlers</span></span>

<span data-ttu-id="717ef-214">`IHttpClientFactory`integruje się z biblioteką firmy [Polly.](https://github.com/App-vNext/Polly)</span><span class="sxs-lookup"><span data-stu-id="717ef-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="717ef-215">Polly to kompleksowa biblioteka do obsługi błędów i stanów przemijających dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="717ef-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="717ef-216">Umożliwia deweloperom do wyrażania zasad, takich jak Ponowienie próby, Wyłącznik, Limit czasu, Izolacja grodzi i Rezerwa w sposób płynny i bezpieczny dla wątków.</span><span class="sxs-lookup"><span data-stu-id="717ef-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="717ef-217">Metody rozszerzenia są dostarczane w celu umożliwienia `HttpClient` korzystania z zasad Polly ze skonfigurowanych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="717ef-218">Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="717ef-219">Polly wymaga pakietu [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="717ef-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="717ef-220">Obsługa usterek przejściowych</span><span class="sxs-lookup"><span data-stu-id="717ef-220">Handle transient faults</span></span>

<span data-ttu-id="717ef-221">Błędy zazwyczaj występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="717ef-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="717ef-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="717ef-223">Zasady skonfigurowane `AddTransientHttpErrorPolicy` z obsługą następujących odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="717ef-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="717ef-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="717ef-224">HTTP 5xx</span></span>
* <span data-ttu-id="717ef-225">Protokół HTTP 408</span><span class="sxs-lookup"><span data-stu-id="717ef-225">HTTP 408</span></span>

<span data-ttu-id="717ef-226">`AddTransientHttpErrorPolicy`zapewnia dostęp `PolicyBuilder` do obiektu skonfigurowanego do obsługi błędów reprezentujących ewentualny błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="717ef-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="717ef-227">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowano zasadę.</span><span class="sxs-lookup"><span data-stu-id="717ef-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="717ef-228">Żądania nie powiodły się są ponawiane do trzech razy z opóźnieniem 600 ms między próbami.</span><span class="sxs-lookup"><span data-stu-id="717ef-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="717ef-229">Dynamiczne wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="717ef-229">Dynamically select policies</span></span>

<span data-ttu-id="717ef-230">Metody rozszerzenia są dostarczane w celu dodania programów obsługi opartych na polly, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>na przykład .</span><span class="sxs-lookup"><span data-stu-id="717ef-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="717ef-231">Następujące `AddPolicyHandler` przeciążenie sprawdza żądanie, aby zdecydować, które zasady mają być stosowane:</span><span class="sxs-lookup"><span data-stu-id="717ef-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="717ef-232">W poprzednim kodzie, jeśli żądanie wychodzące jest HTTP GET, 10-sekundowy limit czasu jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="717ef-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="717ef-233">Dla każdej innej metody HTTP używany jest 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="717ef-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="717ef-234">Dodawanie wielu programów obsługi polly</span><span class="sxs-lookup"><span data-stu-id="717ef-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="717ef-235">Często zagnieżdżamy zasady Polly:</span><span class="sxs-lookup"><span data-stu-id="717ef-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="717ef-236">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="717ef-236">In the preceding example:</span></span>

* <span data-ttu-id="717ef-237">Dwa programy obsługi są dodawane.</span><span class="sxs-lookup"><span data-stu-id="717ef-237">Two handlers are added.</span></span>
* <span data-ttu-id="717ef-238">Pierwszy program obsługi <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> używa do dodania zasad ponawiania.</span><span class="sxs-lookup"><span data-stu-id="717ef-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="717ef-239">Żądania nieudane są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="717ef-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="717ef-240">Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="717ef-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="717ef-241">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób występuje sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="717ef-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="717ef-242">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="717ef-243">Wszystkie wywołania za pośrednictwem tego klienta współużytkują ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="717ef-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="717ef-244">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="717ef-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="717ef-245">Podejście do zarządzania regularnie używanymi zasadami polega na `PolicyRegistry`zdefiniowaniu ich raz i zarejestrowaniu ich w pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="717ef-246">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="717ef-246">In the following code:</span></span>

* <span data-ttu-id="717ef-247">Dodaje się "zwykłą" i "długą" policję.</span><span class="sxs-lookup"><span data-stu-id="717ef-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="717ef-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>dodaje "regularne" i "długie" zasady z rejestru.</span><span class="sxs-lookup"><span data-stu-id="717ef-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="717ef-249">Aby uzyskać `IHttpClientFactory` więcej informacji na temat integracji i Polly, zobacz [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="717ef-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="717ef-250">Zarządzanie protokołem i okresem istnienia httpclient</span><span class="sxs-lookup"><span data-stu-id="717ef-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="717ef-251">Nowe `HttpClient` wystąpienie jest `CreateClient` zwracane za `IHttpClientFactory`każdym razem, gdy jest wywoływana na .</span><span class="sxs-lookup"><span data-stu-id="717ef-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="717ef-252">Tworzony <xref:System.Net.Http.HttpMessageHandler> jest na nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="717ef-253">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="717ef-254">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień utworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="717ef-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="717ef-255">Wystąpienie `HttpMessageHandler` może być ponownie użyczony z `HttpClient` puli podczas tworzenia nowego wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="717ef-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="717ef-256">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi podstawowymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="717ef-257">Tworzenie większej liczby programów obsługi niż jest to konieczne, może spowodować opóźnienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="717ef-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="717ef-258">Niektóre programy obsługi również zachować połączenia otwarte przez czas nieokreślony, co może uniemożliwić program obsługi reagowania na DNS (Domain Name System) zmiany.</span><span class="sxs-lookup"><span data-stu-id="717ef-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="717ef-259">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="717ef-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="717ef-260">Wartość domyślną można zastąpić na podstawie nazwanego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="717ef-261">`HttpClient`wystąpienia mogą być zazwyczaj traktowane jako obiekty **.NET, które nie** wymagają utylizacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="717ef-262">Likwidacja anuluje wychodzące `HttpClient` żądania i gwarantuje, <xref:System.IDisposable.Dispose*>że danego wystąpienia nie można użyć po wywołaniu .</span><span class="sxs-lookup"><span data-stu-id="717ef-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="717ef-263">`IHttpClientFactory`usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="717ef-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="717ef-264">Utrzymywanie `HttpClient` jednego wystąpienia przy życiu przez długi czas jest typowym wzorcem używanym przed powstaniem . `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="717ef-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="717ef-265">Ten wzorzec staje `IHttpClientFactory`się niepotrzebny po migracji do .</span><span class="sxs-lookup"><span data-stu-id="717ef-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="717ef-266">Alternatywy dla IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="717ef-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="717ef-267">Używanie `IHttpClientFactory` w aplikacji obsługującej technologię DI pozwala uniknąć:</span><span class="sxs-lookup"><span data-stu-id="717ef-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="717ef-268">Problemy z wyczerpaniem `HttpMessageHandler` zasobów przez buforowanie wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="717ef-269">Nieaktualne `HttpMessageHandler` problemy z systemem DNS przez wystąpienia rowerowe w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="717ef-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="717ef-270">Istnieją alternatywne sposoby rozwiązywania poprzednich problemów <xref:System.Net.Http.SocketsHttpHandler> przy użyciu wystąpienia długotrwałego.</span><span class="sxs-lookup"><span data-stu-id="717ef-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="717ef-271">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiony i używać go przez cały okres użytkowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="717ef-272">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> do odpowiedniej wartości na podstawie czasów odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="717ef-273">Utwórz `HttpClient` wystąpienia `new HttpClient(handler, disposeHandler: false)` przy użyciu w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="717ef-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="717ef-274">Powyższe podejścia rozwiązują problemy `IHttpClientFactory` z zarządzaniem zasobami, które rozwiązuje się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="717ef-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="717ef-275">Współudzieli `SocketsHttpHandler` `HttpClient` połączenia między wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="717ef-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="717ef-276">To udostępnianie zapobiega wyczerpaniu gniazda.</span><span class="sxs-lookup"><span data-stu-id="717ef-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="717ef-277">Cykle `SocketsHttpHandler` połączeń zgodnie `PooledConnectionLifetime` z aby uniknąć starych problemów z systemem DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="717ef-278">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="717ef-278">Cookies</span></span>

<span data-ttu-id="717ef-279">W puli `HttpMessageHandler` wystąpień `CookieContainer` powoduje obiekty są współużytkowane.</span><span class="sxs-lookup"><span data-stu-id="717ef-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="717ef-280">Udostępnianie nieoczekiwanych `CookieContainer` obiektów często powoduje niepoprawny kod.</span><span class="sxs-lookup"><span data-stu-id="717ef-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="717ef-281">W przypadku aplikacji, które wymagają plików cookie, należy wziąć pod uwagę:</span><span class="sxs-lookup"><span data-stu-id="717ef-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="717ef-282">Wyłączenie automatycznej obsługi plików cookie</span><span class="sxs-lookup"><span data-stu-id="717ef-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="717ef-283">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="717ef-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="717ef-284">Wywołanie, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="717ef-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="717ef-285">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="717ef-285">Logging</span></span>

<span data-ttu-id="717ef-286">Klienci utworzone `IHttpClientFactory` za pomocą komunikatów dziennika rekordów dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="717ef-287">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="717ef-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="717ef-288">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="717ef-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="717ef-289">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="717ef-290">Klient o nazwie *MyNamedClient*, na przykład rejestruje wiadomości z kategorii "System.Net.http.httpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="717ef-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="717ef-291">Komunikaty sufiksy z *LogicalHandler* występują poza potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="717ef-292">W żądaniu wiadomości są rejestrowane, zanim inne programy obsługi w potoku przetworzyły go.</span><span class="sxs-lookup"><span data-stu-id="717ef-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="717ef-293">W odpowiedzi wiadomości są rejestrowane po innych programów obsługi potoku otrzymały odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="717ef-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="717ef-294">Rejestrowanie występuje również wewnątrz potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="717ef-295">W przykładzie *MyNamedClient* te wiadomości są rejestrowane z kategorią dziennika "System.Net.http.httpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="717ef-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="717ef-296">Dla żądania występuje po uruchomieniu wszystkich innych programów obsługi i bezpośrednio przed wysłaniem żądania.</span><span class="sxs-lookup"><span data-stu-id="717ef-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="717ef-297">W odpowiedzi to rejestrowanie zawiera stan odpowiedzi, zanim przejdzie z powrotem za pośrednictwem potoku obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="717ef-298">Włączenie rejestrowania na zewnątrz i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="717ef-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="717ef-299">Może to obejmować zmiany w nagłówkach żądań lub kod stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="717ef-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="717ef-300">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dziennika dla określonych nazwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="717ef-301">Konfigurowanie usługi HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="717ef-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="717ef-302">Może być konieczne kontrolowanie konfiguracji `HttpMessageHandler` wewnętrznej używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="717ef-303">Jest `IHttpClientBuilder` zwracany podczas dodawania klientów nazwanych lub wpisanych.</span><span class="sxs-lookup"><span data-stu-id="717ef-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="717ef-304">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzenia może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="717ef-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="717ef-305">Pełnomocnik jest używany do tworzenia i `HttpMessageHandler` konfigurowania podstawowego używanego przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="717ef-306">Używanie programu IHttpClientFactory w aplikacji konsoli</span><span class="sxs-lookup"><span data-stu-id="717ef-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="717ef-307">W aplikacji konsoli dodaj do projektu następujące odwołania do pakietu:</span><span class="sxs-lookup"><span data-stu-id="717ef-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="717ef-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="717ef-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="717ef-309">Microsoft.Extensions.http</span><span class="sxs-lookup"><span data-stu-id="717ef-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="717ef-310">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="717ef-310">In the following example:</span></span>

* <span data-ttu-id="717ef-311"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="717ef-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="717ef-312">`MyService`tworzy wystąpienie fabryki klienta z usługi, która `HttpClient`jest używana do tworzenia pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="717ef-313">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="717ef-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="717ef-314">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisu pierwszych 500 znaków zawartości strony sieci Web do konsoli.</span><span class="sxs-lookup"><span data-stu-id="717ef-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="717ef-315">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="717ef-315">Header propagation middleware</span></span>

<span data-ttu-id="717ef-316">Propagacja nagłówka jest ASP.NET core oprogramowanie pośredniczące do propagacji nagłówków HTTP z żądania przychodzącego do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="717ef-317">Aby użyć propagacji nagłówka, należy:</span><span class="sxs-lookup"><span data-stu-id="717ef-317">To use header propagation:</span></span>

* <span data-ttu-id="717ef-318">Odwołanie się do pakietu [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)</span><span class="sxs-lookup"><span data-stu-id="717ef-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="717ef-319">Skonfiguruj `HttpClient` oprogramowanie `Startup`pośredniczące i w :</span><span class="sxs-lookup"><span data-stu-id="717ef-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="717ef-320">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="717ef-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="717ef-321">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="717ef-321">Additional resources</span></span>

* [<span data-ttu-id="717ef-322">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="717ef-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="717ef-323">Implementowanie ponownych prób wywołania HTTP z wykładniczym wycofywania z httpclientfactory i polly zasad</span><span class="sxs-lookup"><span data-stu-id="717ef-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="717ef-324">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="717ef-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="717ef-325">Jak serializować i deserialize JSON w .NET</span><span class="sxs-lookup"><span data-stu-id="717ef-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="717ef-326">Przez [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i Steve [Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="717ef-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="717ef-327">Można <xref:System.Net.Http.IHttpClientFactory> zarejestrować i używać do <xref:System.Net.Http.HttpClient> konfigurowania i tworzenia wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="717ef-328">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="717ef-328">It offers the following benefits:</span></span>

* <span data-ttu-id="717ef-329">Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="717ef-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="717ef-330">Na przykład klient *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="717ef-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="717ef-331">Domyślny klient może być zarejestrowany do innych celów.</span><span class="sxs-lookup"><span data-stu-id="717ef-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="717ef-332">Codifies koncepcji wychodzącego oprogramowania pośredniczącego `HttpClient` za pośrednictwem delegowania obsługi i zapewnia rozszerzenia oprogramowania pośredniczącego opartego na polly, aby skorzystać z tego.</span><span class="sxs-lookup"><span data-stu-id="717ef-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="717ef-333">Zarządza buforowanie i okres `HttpClientMessageHandler` istnienia wystąpienia podstawowe, aby uniknąć typowych `HttpClient` problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="717ef-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="717ef-334">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="717ef-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="717ef-335">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="717ef-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="717ef-336">Konsumpcji</span><span class="sxs-lookup"><span data-stu-id="717ef-336">Consumption patterns</span></span>

<span data-ttu-id="717ef-337">W aplikacji `IHttpClientFactory` można używać na kilka sposobów:</span><span class="sxs-lookup"><span data-stu-id="717ef-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="717ef-338">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="717ef-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="717ef-339">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="717ef-340">Klienci wpisywane</span><span class="sxs-lookup"><span data-stu-id="717ef-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="717ef-341">Wygenerowani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="717ef-342">Żaden z nich nie jest ściśle lepszy od drugiego.</span><span class="sxs-lookup"><span data-stu-id="717ef-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="717ef-343">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="717ef-344">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="717ef-344">Basic usage</span></span>

<span data-ttu-id="717ef-345">Można `IHttpClientFactory` zarejestrować, wywołując `AddHttpClient` metodę rozszerzenia `IServiceCollection`na `Startup.ConfigureServices` , wewnątrz metody.</span><span class="sxs-lookup"><span data-stu-id="717ef-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="717ef-346">Po zarejestrowaniu, kod `IHttpClientFactory` może zaakceptować w dowolnym miejscu usługi mogą być wstrzykiwane z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="717ef-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="717ef-347">Może `IHttpClientFactory` służyć do tworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="717ef-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="717ef-348">Korzystanie `IHttpClientFactory` w ten sposób jest dobrym sposobem na refaktoryzator istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="717ef-349">Nie ma wpływu na `HttpClient` sposób jest używany.</span><span class="sxs-lookup"><span data-stu-id="717ef-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="717ef-350">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, <xref:System.Net.Http.IHttpClientFactory.CreateClient*>zastąp te zdarzenia wywołaniem .</span><span class="sxs-lookup"><span data-stu-id="717ef-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="717ef-351">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-351">Named clients</span></span>

<span data-ttu-id="717ef-352">Jeśli aplikacja wymaga wielu różnych `HttpClient`zastosowań , każdy z innej konfiguracji, opcja jest użycie **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="717ef-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="717ef-353">Konfigurację dla `HttpClient` nazwanego można `Startup.ConfigureServices`określić podczas rejestracji w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="717ef-354">W poprzednim kodzie, `AddHttpClient` jest wywoływana, podając nazwę *github*.</span><span class="sxs-lookup"><span data-stu-id="717ef-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="717ef-355">Ten klient ma pewną&mdash;domyślną konfigurację zastosowaną mianowicie adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API GitHub.</span><span class="sxs-lookup"><span data-stu-id="717ef-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="717ef-356">Za `CreateClient` każdym razem jest wywoływana, nowe wystąpienie `HttpClient` jest tworzony i wywoływana jest akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="717ef-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="717ef-357">Aby zużywać nazwany klient, parametr ciągu `CreateClient`może być przekazywany do .</span><span class="sxs-lookup"><span data-stu-id="717ef-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="717ef-358">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="717ef-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="717ef-359">W poprzednim kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="717ef-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="717ef-360">Może przekazać tylko ścieżkę, ponieważ używany jest adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="717ef-361">Klienci wpisywane</span><span class="sxs-lookup"><span data-stu-id="717ef-361">Typed clients</span></span>

<span data-ttu-id="717ef-362">Wpisywane klientów:</span><span class="sxs-lookup"><span data-stu-id="717ef-362">Typed clients:</span></span>

* <span data-ttu-id="717ef-363">Zapewnij te same możliwości, co nazwani klienci bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="717ef-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="717ef-364">Zapewnia pomoc IntelliSense i kompilatora podczas korzystania z klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="717ef-365">Podaj jedną lokalizację, aby skonfigurować i współdziałać z określonym `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="717ef-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="717ef-366">Na przykład pojedynczy typowany klient może być używany dla pojedynczego punktu końcowego wewnętrznej bazy danych i hermetyzować całą logikę dotyczącą tego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="717ef-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="717ef-367">Praca z DI i mogą być wstrzykiwane w razie potrzeby w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="717ef-368">Typowany klient akceptuje `HttpClient` parametr w konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="717ef-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="717ef-369">W poprzednim kodzie konfiguracja jest przenoszona do wpisanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="717ef-370">Obiekt `HttpClient` jest narażony jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="717ef-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="717ef-371">Jest możliwe zdefiniowanie metod specyficznych `HttpClient` dla interfejsu API, które udostępniają funkcje.</span><span class="sxs-lookup"><span data-stu-id="717ef-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="717ef-372">Metoda `GetAspNetDocsIssues` hermetyzuje kod potrzebny do wykonywania zapytań i analizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="717ef-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="717ef-373">Aby zarejestrować wpisanego <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, można użyć `Startup.ConfigureServices`ogólnej metody rozszerzenia w ramach , określając typizowana klasa klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="717ef-374">Wpisany klient jest zarejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="717ef-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="717ef-375">Wpisany klient może być wstrzykiwany i spożywany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="717ef-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="717ef-376">Jeśli jest to preferowane, konfiguracja wpisanego `Startup.ConfigureServices`klienta może być określona podczas rejestracji w programie , a nie w konstruktorze wpisanego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="717ef-377">Jest możliwe, aby całkowicie hermetyzować `HttpClient` w obrębie klienta wpisane.</span><span class="sxs-lookup"><span data-stu-id="717ef-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="717ef-378">Zamiast wystawiać go jako właściwość, metody publiczne mogą `HttpClient` być dostarczane, które wywołują wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="717ef-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="717ef-379">W poprzednim kodzie `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="717ef-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="717ef-380">Cały dostęp do wykonywania połączeń `GetRepos` zewnętrznych przechodzi przez metodę.</span><span class="sxs-lookup"><span data-stu-id="717ef-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="717ef-381">Wygenerowani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-381">Generated clients</span></span>

<span data-ttu-id="717ef-382">`IHttpClientFactory`mogą być używane w połączeniu z innymi bibliotekami innych firm, takimi jak [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="717ef-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="717ef-383">Refit to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="717ef-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="717ef-384">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="717ef-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="717ef-385">Implementacja interfejsu jest generowana `RestService`dynamicznie `HttpClient` przez program , używając do wykonywania zewnętrznych wywołań HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="717ef-386">Interfejs i odpowiedź są zdefiniowane w celu reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="717ef-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="717ef-387">Typowany klient można dodać, za pomocą Refit do generowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="717ef-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="717ef-388">Zdefiniowany interfejs może być zużywany w razie potrzeby, z implementacją dostarczoną przez DI i Refit:</span><span class="sxs-lookup"><span data-stu-id="717ef-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="717ef-389">Oprogramowanie pośredniczące żądania wychodzącego</span><span class="sxs-lookup"><span data-stu-id="717ef-389">Outgoing request middleware</span></span>

<span data-ttu-id="717ef-390">`HttpClient`już ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="717ef-391">Ułatwia `IHttpClientFactory` definiowanie programów obsługi, aby zastosować dla każdego klienta o nazwie.</span><span class="sxs-lookup"><span data-stu-id="717ef-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="717ef-392">Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="717ef-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="717ef-393">Każdy z tych programów obsługi jest w stanie wykonać pracę przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="717ef-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="717ef-394">Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="717ef-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="717ef-395">Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy wokół żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="717ef-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="717ef-396">Aby utworzyć program obsługi, należy <xref:System.Net.Http.DelegatingHandler>zdefiniować klasę wywodzącą się z programu .</span><span class="sxs-lookup"><span data-stu-id="717ef-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="717ef-397">Zastąp `SendAsync` metodę wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="717ef-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="717ef-398">Poprzedni kod definiuje podstawowy program obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="717ef-399">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="717ef-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="717ef-400">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="717ef-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="717ef-401">Podczas rejestracji można dodać jeden lub więcej programów `HttpClient`obsługi do konfiguracji programu .</span><span class="sxs-lookup"><span data-stu-id="717ef-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="717ef-402">To zadanie jest realizowane za <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>pomocą metod rozszerzenia w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="717ef-403">W poprzednim kodzie `ValidateHeaderHandler` jest zarejestrowany w DI.</span><span class="sxs-lookup"><span data-stu-id="717ef-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="717ef-404">Tworzy `IHttpClientFactory` oddzielny zakres DI dla każdego programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="717ef-405">Programy obsługi mogą polegać na usługach dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="717ef-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="717ef-406">Usługi, od których zależą programy obsługi, są usuwane, gdy program obsługi jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="717ef-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="717ef-407">Po zarejestrowaniu, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazywanie w typie dla programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="717ef-408">Wiele programów obsługi mogą być rejestrowane w kolejności, w jakiej należy wykonać.</span><span class="sxs-lookup"><span data-stu-id="717ef-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="717ef-409">Każdy program obsługi zawija `HttpClientHandler` następny program obsługi, dopóki ostateczna wykonuje żądanie:</span><span class="sxs-lookup"><span data-stu-id="717ef-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="717ef-410">Użyj jednego z następujących metod, aby udostępnić stan na żądanie z programami obsługi wiadomości:</span><span class="sxs-lookup"><span data-stu-id="717ef-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="717ef-411">Przekazywanie danych do `HttpRequestMessage.Properties`programu obsługi przy użyciu programu .</span><span class="sxs-lookup"><span data-stu-id="717ef-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="717ef-412">Służy `IHttpContextAccessor` do uzyskiwania dostępu do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="717ef-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="717ef-413">Utwórz `AsyncLocal` niestandardowy obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="717ef-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="717ef-414">Korzystanie z programów obsługi opartych na polly</span><span class="sxs-lookup"><span data-stu-id="717ef-414">Use Polly-based handlers</span></span>

<span data-ttu-id="717ef-415">`IHttpClientFactory`integruje się z popularną biblioteką firm o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="717ef-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="717ef-416">Polly to kompleksowa biblioteka do obsługi błędów i stanów przemijających dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="717ef-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="717ef-417">Umożliwia deweloperom do wyrażania zasad, takich jak Ponowienie próby, Wyłącznik, Limit czasu, Izolacja grodzi i Rezerwa w sposób płynny i bezpieczny dla wątków.</span><span class="sxs-lookup"><span data-stu-id="717ef-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="717ef-418">Metody rozszerzenia są dostarczane w celu umożliwienia `HttpClient` korzystania z zasad Polly ze skonfigurowanych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="717ef-419">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="717ef-419">The Polly extensions:</span></span>

* <span data-ttu-id="717ef-420">Obsługa dodawania programów obsługi opartych na polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="717ef-421">Może być używany po zainstalowaniu pakietu [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="717ef-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="717ef-422">Pakiet nie jest uwzględniony w ASP.NET core udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="717ef-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="717ef-423">Obsługa usterek przejściowych</span><span class="sxs-lookup"><span data-stu-id="717ef-423">Handle transient faults</span></span>

<span data-ttu-id="717ef-424">Najczęściej błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="717ef-425">Wygodna metoda `AddTransientHttpErrorPolicy` rozszerzenia o nazwie jest dołączona, która umożliwia definiowanie zasad do obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="717ef-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="717ef-426">Zasady skonfigurowane przy użyciu `HttpRequestException`tej metody rozszerzenia obsługują odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="717ef-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="717ef-427">Rozszerzenie `AddTransientHttpErrorPolicy` może być `Startup.ConfigureServices`używane w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="717ef-428">Rozszerzenie zapewnia dostęp `PolicyBuilder` do obiektu skonfigurowanego do obsługi błędów reprezentujących ewentualny błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="717ef-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="717ef-429">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowano zasadę.</span><span class="sxs-lookup"><span data-stu-id="717ef-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="717ef-430">Żądania nie powiodły się są ponawiane do trzech razy z opóźnieniem 600 ms między próbami.</span><span class="sxs-lookup"><span data-stu-id="717ef-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="717ef-431">Dynamiczne wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="717ef-431">Dynamically select policies</span></span>

<span data-ttu-id="717ef-432">Istnieją dodatkowe metody rozszerzenia, które mogą służyć do dodawania programów obsługi opartych na polly.</span><span class="sxs-lookup"><span data-stu-id="717ef-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="717ef-433">Jednym z `AddPolicyHandler`takich rozszerzeń jest , który ma wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="717ef-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="717ef-434">Jedno przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają być stosowane:</span><span class="sxs-lookup"><span data-stu-id="717ef-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="717ef-435">W poprzednim kodzie, jeśli żądanie wychodzące jest HTTP GET, 10-sekundowy limit czasu jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="717ef-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="717ef-436">Dla każdej innej metody HTTP używany jest 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="717ef-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="717ef-437">Dodawanie wielu programów obsługi polly</span><span class="sxs-lookup"><span data-stu-id="717ef-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="717ef-438">Często zagnieżdżasz zasady Polly, aby zapewnić ulepszoną funkcjonalność:</span><span class="sxs-lookup"><span data-stu-id="717ef-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="717ef-439">W poprzednim przykładzie są dodawane dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="717ef-440">Pierwszy używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="717ef-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="717ef-441">Żądania nieudane są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="717ef-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="717ef-442">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="717ef-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="717ef-443">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób występuje sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="717ef-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="717ef-444">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="717ef-445">Wszystkie wywołania za pośrednictwem tego klienta współużytkują ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="717ef-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="717ef-446">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="717ef-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="717ef-447">Podejście do zarządzania regularnie używanymi zasadami polega na `PolicyRegistry`zdefiniowaniu ich raz i zarejestrowaniu ich w pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="717ef-448">Dostępna jest metoda rozszerzenia, która umożliwia program obsługi, który ma zostać dodany przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="717ef-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="717ef-449">W poprzednim kodzie dwie zasady są `PolicyRegistry` rejestrowane po `ServiceCollection`dodaniu do pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="717ef-450">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` używana jest metoda, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="717ef-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="717ef-451">Więcej informacji `IHttpClientFactory` na temat integracji Polly można znaleźć na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="717ef-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="717ef-452">Zarządzanie protokołem i okresem istnienia httpclient</span><span class="sxs-lookup"><span data-stu-id="717ef-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="717ef-453">Nowe `HttpClient` wystąpienie jest `CreateClient` zwracane za `IHttpClientFactory`każdym razem, gdy jest wywoływana na .</span><span class="sxs-lookup"><span data-stu-id="717ef-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="717ef-454">Istnieje <xref:System.Net.Http.HttpMessageHandler> per nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="717ef-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="717ef-455">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="717ef-456">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień utworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="717ef-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="717ef-457">Wystąpienie `HttpMessageHandler` może być ponownie użyczony z `HttpClient` puli podczas tworzenia nowego wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="717ef-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="717ef-458">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi podstawowymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="717ef-459">Tworzenie większej liczby programów obsługi niż jest to konieczne, może spowodować opóźnienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="717ef-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="717ef-460">Niektóre programy obsługi również zachować połączenia otwarte przez czas nieokreślony, co może uniemożliwić program obsługi reagowania na zmiany DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="717ef-461">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="717ef-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="717ef-462">Wartość domyślna może zostać zastąpiona na podstawie nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="717ef-463">Aby go zastąpić, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> wywołaj `IHttpClientBuilder` to, co jest zwracane podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="717ef-464">Utylizacja klienta nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="717ef-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="717ef-465">Likwidacja anuluje wychodzące `HttpClient` żądania i gwarantuje, <xref:System.IDisposable.Dispose*>że danego wystąpienia nie można użyć po wywołaniu .</span><span class="sxs-lookup"><span data-stu-id="717ef-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="717ef-466">`IHttpClientFactory`usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="717ef-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="717ef-467">Wystąpienia `HttpClient` można ogólnie traktować jako obiekty .NET nie wymagające usuwania.</span><span class="sxs-lookup"><span data-stu-id="717ef-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="717ef-468">Utrzymywanie `HttpClient` jednego wystąpienia przy życiu przez długi czas jest typowym wzorcem używanym przed powstaniem . `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="717ef-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="717ef-469">Ten wzorzec staje `IHttpClientFactory`się niepotrzebny po migracji do .</span><span class="sxs-lookup"><span data-stu-id="717ef-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="717ef-470">Alternatywy dla IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="717ef-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="717ef-471">Używanie `IHttpClientFactory` w aplikacji obsługującej technologię DI pozwala uniknąć:</span><span class="sxs-lookup"><span data-stu-id="717ef-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="717ef-472">Problemy z wyczerpaniem `HttpMessageHandler` zasobów przez buforowanie wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="717ef-473">Nieaktualne `HttpMessageHandler` problemy z systemem DNS przez wystąpienia rowerowe w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="717ef-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="717ef-474">Istnieją alternatywne sposoby rozwiązywania poprzednich problemów <xref:System.Net.Http.SocketsHttpHandler> przy użyciu wystąpienia długotrwałego.</span><span class="sxs-lookup"><span data-stu-id="717ef-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="717ef-475">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiony i używać go przez cały okres użytkowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="717ef-476">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> do odpowiedniej wartości na podstawie czasów odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="717ef-477">Utwórz `HttpClient` wystąpienia `new HttpClient(handler, disposeHandler: false)` przy użyciu w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="717ef-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="717ef-478">Powyższe podejścia rozwiązują problemy `IHttpClientFactory` z zarządzaniem zasobami, które rozwiązuje się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="717ef-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="717ef-479">Współudzieli `SocketsHttpHandler` `HttpClient` połączenia między wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="717ef-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="717ef-480">To udostępnianie zapobiega wyczerpaniu gniazda.</span><span class="sxs-lookup"><span data-stu-id="717ef-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="717ef-481">Cykle `SocketsHttpHandler` połączeń zgodnie `PooledConnectionLifetime` z aby uniknąć starych problemów z systemem DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="717ef-482">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="717ef-482">Cookies</span></span>

<span data-ttu-id="717ef-483">W puli `HttpMessageHandler` wystąpień `CookieContainer` powoduje obiekty są współużytkowane.</span><span class="sxs-lookup"><span data-stu-id="717ef-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="717ef-484">Udostępnianie nieoczekiwanych `CookieContainer` obiektów często powoduje niepoprawny kod.</span><span class="sxs-lookup"><span data-stu-id="717ef-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="717ef-485">W przypadku aplikacji, które wymagają plików cookie, należy wziąć pod uwagę:</span><span class="sxs-lookup"><span data-stu-id="717ef-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="717ef-486">Wyłączenie automatycznej obsługi plików cookie</span><span class="sxs-lookup"><span data-stu-id="717ef-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="717ef-487">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="717ef-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="717ef-488">Wywołanie, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="717ef-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="717ef-489">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="717ef-489">Logging</span></span>

<span data-ttu-id="717ef-490">Klienci utworzone `IHttpClientFactory` za pomocą komunikatów dziennika rekordów dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="717ef-491">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="717ef-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="717ef-492">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="717ef-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="717ef-493">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="717ef-494">Klient o nazwie *MyNamedClient*, na przykład, rejestruje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`wiadomości z kategorii .</span><span class="sxs-lookup"><span data-stu-id="717ef-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="717ef-495">Komunikaty sufiksy z *LogicalHandler* występują poza potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="717ef-496">W żądaniu wiadomości są rejestrowane, zanim inne programy obsługi w potoku przetworzyły go.</span><span class="sxs-lookup"><span data-stu-id="717ef-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="717ef-497">W odpowiedzi wiadomości są rejestrowane po innych programów obsługi potoku otrzymały odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="717ef-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="717ef-498">Rejestrowanie występuje również wewnątrz potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="717ef-499">W przykładzie *MyNamedClient* te komunikaty są rejestrowane `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`względem kategorii dziennika .</span><span class="sxs-lookup"><span data-stu-id="717ef-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="717ef-500">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i bezpośrednio przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="717ef-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="717ef-501">W odpowiedzi to rejestrowanie zawiera stan odpowiedzi, zanim przejdzie z powrotem za pośrednictwem potoku obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="717ef-502">Włączenie rejestrowania na zewnątrz i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="717ef-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="717ef-503">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="717ef-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="717ef-504">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dziennika dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="717ef-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="717ef-505">Konfigurowanie usługi HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="717ef-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="717ef-506">Może być konieczne kontrolowanie konfiguracji `HttpMessageHandler` wewnętrznej używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="717ef-507">Jest `IHttpClientBuilder` zwracany podczas dodawania klientów nazwanych lub wpisanych.</span><span class="sxs-lookup"><span data-stu-id="717ef-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="717ef-508">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzenia może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="717ef-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="717ef-509">Pełnomocnik jest używany do tworzenia i `HttpMessageHandler` konfigurowania podstawowego używanego przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="717ef-510">Używanie programu IHttpClientFactory w aplikacji konsoli</span><span class="sxs-lookup"><span data-stu-id="717ef-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="717ef-511">W aplikacji konsoli dodaj do projektu następujące odwołania do pakietu:</span><span class="sxs-lookup"><span data-stu-id="717ef-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="717ef-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="717ef-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="717ef-513">Microsoft.Extensions.http</span><span class="sxs-lookup"><span data-stu-id="717ef-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="717ef-514">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="717ef-514">In the following example:</span></span>

* <span data-ttu-id="717ef-515"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="717ef-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="717ef-516">`MyService`tworzy wystąpienie fabryki klienta z usługi, która `HttpClient`jest używana do tworzenia pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="717ef-517">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="717ef-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="717ef-518">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisu pierwszych 500 znaków zawartości strony sieci Web do konsoli.</span><span class="sxs-lookup"><span data-stu-id="717ef-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="717ef-519">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="717ef-519">Additional resources</span></span>

* [<span data-ttu-id="717ef-520">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="717ef-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="717ef-521">Implementowanie ponownych prób wywołania HTTP z wykładniczym wycofywania z httpclientfactory i polly zasad</span><span class="sxs-lookup"><span data-stu-id="717ef-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="717ef-522">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="717ef-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="717ef-523">Przez [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i Steve [Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="717ef-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="717ef-524">Można <xref:System.Net.Http.IHttpClientFactory> zarejestrować i używać do <xref:System.Net.Http.HttpClient> konfigurowania i tworzenia wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="717ef-525">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="717ef-525">It offers the following benefits:</span></span>

* <span data-ttu-id="717ef-526">Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="717ef-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="717ef-527">Na przykład klient *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="717ef-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="717ef-528">Domyślny klient może być zarejestrowany do innych celów.</span><span class="sxs-lookup"><span data-stu-id="717ef-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="717ef-529">Codifies koncepcji wychodzącego oprogramowania pośredniczącego `HttpClient` za pośrednictwem delegowania obsługi i zapewnia rozszerzenia oprogramowania pośredniczącego opartego na polly, aby skorzystać z tego.</span><span class="sxs-lookup"><span data-stu-id="717ef-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="717ef-530">Zarządza buforowanie i okres `HttpClientMessageHandler` istnienia wystąpienia podstawowe, aby uniknąć typowych `HttpClient` problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="717ef-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="717ef-531">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="717ef-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="717ef-532">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="717ef-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="717ef-533">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="717ef-533">Prerequisites</span></span>

<span data-ttu-id="717ef-534">Projekty przeznaczone dla platformy .NET Framework wymagają instalacji pakietu [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="717ef-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="717ef-535">Projekty, które są przeznaczone dla platformy .NET Core i odwołują `Microsoft.Extensions.Http` się do [metapakiety Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app) zawierają już pakiet.</span><span class="sxs-lookup"><span data-stu-id="717ef-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="717ef-536">Konsumpcji</span><span class="sxs-lookup"><span data-stu-id="717ef-536">Consumption patterns</span></span>

<span data-ttu-id="717ef-537">W aplikacji `IHttpClientFactory` można używać na kilka sposobów:</span><span class="sxs-lookup"><span data-stu-id="717ef-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="717ef-538">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="717ef-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="717ef-539">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="717ef-540">Klienci wpisywane</span><span class="sxs-lookup"><span data-stu-id="717ef-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="717ef-541">Wygenerowani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="717ef-542">Żaden z nich nie jest ściśle lepszy od drugiego.</span><span class="sxs-lookup"><span data-stu-id="717ef-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="717ef-543">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="717ef-544">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="717ef-544">Basic usage</span></span>

<span data-ttu-id="717ef-545">Można `IHttpClientFactory` zarejestrować, wywołując `AddHttpClient` metodę rozszerzenia `IServiceCollection`na `Startup.ConfigureServices` , wewnątrz metody.</span><span class="sxs-lookup"><span data-stu-id="717ef-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="717ef-546">Po zarejestrowaniu, kod `IHttpClientFactory` może zaakceptować w dowolnym miejscu usługi mogą być wstrzykiwane z [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="717ef-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="717ef-547">Może `IHttpClientFactory` służyć do tworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="717ef-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="717ef-548">Korzystanie `IHttpClientFactory` w ten sposób jest dobrym sposobem na refaktoryzator istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="717ef-549">Nie ma wpływu na `HttpClient` sposób jest używany.</span><span class="sxs-lookup"><span data-stu-id="717ef-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="717ef-550">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, <xref:System.Net.Http.IHttpClientFactory.CreateClient*>zastąp te zdarzenia wywołaniem .</span><span class="sxs-lookup"><span data-stu-id="717ef-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="717ef-551">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-551">Named clients</span></span>

<span data-ttu-id="717ef-552">Jeśli aplikacja wymaga wielu różnych `HttpClient`zastosowań , każdy z innej konfiguracji, opcja jest użycie **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="717ef-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="717ef-553">Konfigurację dla `HttpClient` nazwanego można `Startup.ConfigureServices`określić podczas rejestracji w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="717ef-554">W poprzednim kodzie, `AddHttpClient` jest wywoływana, podając nazwę *github*.</span><span class="sxs-lookup"><span data-stu-id="717ef-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="717ef-555">Ten klient ma pewną&mdash;domyślną konfigurację zastosowaną mianowicie adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API GitHub.</span><span class="sxs-lookup"><span data-stu-id="717ef-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="717ef-556">Za `CreateClient` każdym razem jest wywoływana, nowe wystąpienie `HttpClient` jest tworzony i wywoływana jest akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="717ef-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="717ef-557">Aby zużywać nazwany klient, parametr ciągu `CreateClient`może być przekazywany do .</span><span class="sxs-lookup"><span data-stu-id="717ef-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="717ef-558">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="717ef-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="717ef-559">W poprzednim kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="717ef-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="717ef-560">Może przekazać tylko ścieżkę, ponieważ używany jest adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="717ef-561">Klienci wpisywane</span><span class="sxs-lookup"><span data-stu-id="717ef-561">Typed clients</span></span>

<span data-ttu-id="717ef-562">Wpisywane klientów:</span><span class="sxs-lookup"><span data-stu-id="717ef-562">Typed clients:</span></span>

* <span data-ttu-id="717ef-563">Zapewnij te same możliwości, co nazwani klienci bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="717ef-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="717ef-564">Zapewnia pomoc IntelliSense i kompilatora podczas korzystania z klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="717ef-565">Podaj jedną lokalizację, aby skonfigurować i współdziałać z określonym `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="717ef-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="717ef-566">Na przykład pojedynczy typowany klient może być używany dla pojedynczego punktu końcowego wewnętrznej bazy danych i hermetyzować całą logikę dotyczącą tego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="717ef-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="717ef-567">Praca z DI i mogą być wstrzykiwane w razie potrzeby w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="717ef-568">Typowany klient akceptuje `HttpClient` parametr w konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="717ef-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="717ef-569">W poprzednim kodzie konfiguracja jest przenoszona do wpisanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="717ef-570">Obiekt `HttpClient` jest narażony jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="717ef-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="717ef-571">Jest możliwe zdefiniowanie metod specyficznych `HttpClient` dla interfejsu API, które udostępniają funkcje.</span><span class="sxs-lookup"><span data-stu-id="717ef-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="717ef-572">Metoda `GetAspNetDocsIssues` hermetyzuje kod potrzebny do wykonywania zapytań i analizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="717ef-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="717ef-573">Aby zarejestrować wpisanego <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, można użyć `Startup.ConfigureServices`ogólnej metody rozszerzenia w ramach , określając typizowana klasa klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="717ef-574">Wpisany klient jest zarejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="717ef-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="717ef-575">Wpisany klient może być wstrzykiwany i spożywany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="717ef-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="717ef-576">Jeśli jest to preferowane, konfiguracja wpisanego `Startup.ConfigureServices`klienta może być określona podczas rejestracji w programie , a nie w konstruktorze wpisanego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="717ef-577">Jest możliwe, aby całkowicie hermetyzować `HttpClient` w obrębie klienta wpisane.</span><span class="sxs-lookup"><span data-stu-id="717ef-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="717ef-578">Zamiast wystawiać go jako właściwość, metody publiczne mogą `HttpClient` być dostarczane, które wywołują wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="717ef-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="717ef-579">W poprzednim kodzie `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="717ef-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="717ef-580">Cały dostęp do wykonywania połączeń `GetRepos` zewnętrznych przechodzi przez metodę.</span><span class="sxs-lookup"><span data-stu-id="717ef-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="717ef-581">Wygenerowani klienci</span><span class="sxs-lookup"><span data-stu-id="717ef-581">Generated clients</span></span>

<span data-ttu-id="717ef-582">`IHttpClientFactory`mogą być używane w połączeniu z innymi bibliotekami innych firm, takimi jak [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="717ef-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="717ef-583">Refit to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="717ef-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="717ef-584">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="717ef-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="717ef-585">Implementacja interfejsu jest generowana `RestService`dynamicznie `HttpClient` przez program , używając do wykonywania zewnętrznych wywołań HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="717ef-586">Interfejs i odpowiedź są zdefiniowane w celu reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="717ef-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="717ef-587">Typowany klient można dodać, za pomocą Refit do generowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="717ef-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="717ef-588">Zdefiniowany interfejs może być zużywany w razie potrzeby, z implementacją dostarczoną przez DI i Refit:</span><span class="sxs-lookup"><span data-stu-id="717ef-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="717ef-589">Oprogramowanie pośredniczące żądania wychodzącego</span><span class="sxs-lookup"><span data-stu-id="717ef-589">Outgoing request middleware</span></span>

<span data-ttu-id="717ef-590">`HttpClient`już ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="717ef-591">Ułatwia `IHttpClientFactory` definiowanie programów obsługi, aby zastosować dla każdego klienta o nazwie.</span><span class="sxs-lookup"><span data-stu-id="717ef-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="717ef-592">Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="717ef-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="717ef-593">Każdy z tych programów obsługi jest w stanie wykonać pracę przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="717ef-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="717ef-594">Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="717ef-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="717ef-595">Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy wokół żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="717ef-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="717ef-596">Aby utworzyć program obsługi, należy <xref:System.Net.Http.DelegatingHandler>zdefiniować klasę wywodzącą się z programu .</span><span class="sxs-lookup"><span data-stu-id="717ef-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="717ef-597">Zastąp `SendAsync` metodę wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="717ef-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="717ef-598">Poprzedni kod definiuje podstawowy program obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="717ef-599">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="717ef-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="717ef-600">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="717ef-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="717ef-601">Podczas rejestracji można dodać jeden lub więcej programów `HttpClient`obsługi do konfiguracji programu .</span><span class="sxs-lookup"><span data-stu-id="717ef-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="717ef-602">To zadanie jest realizowane za <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>pomocą metod rozszerzenia w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="717ef-603">W poprzednim kodzie `ValidateHeaderHandler` jest zarejestrowany w DI.</span><span class="sxs-lookup"><span data-stu-id="717ef-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="717ef-604">Program obsługi **musi** być zarejestrowany w DI jako usługi przejściowe, nigdy nie o zakresie.</span><span class="sxs-lookup"><span data-stu-id="717ef-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="717ef-605">Jeśli program obsługi jest zarejestrowany jako usługa o określonym zakresie, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:</span><span class="sxs-lookup"><span data-stu-id="717ef-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="717ef-606">Usługi programu obsługi można usunąć, zanim program obsługi wykracza poza zakres.</span><span class="sxs-lookup"><span data-stu-id="717ef-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="717ef-607">Usługi obsługi dysponowana powoduje, że program obsługi nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="717ef-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="717ef-608">Po zarejestrowaniu, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazywanie w typie obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="717ef-609">Wiele programów obsługi mogą być rejestrowane w kolejności, w jakiej należy wykonać.</span><span class="sxs-lookup"><span data-stu-id="717ef-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="717ef-610">Każdy program obsługi zawija `HttpClientHandler` następny program obsługi, dopóki ostateczna wykonuje żądanie:</span><span class="sxs-lookup"><span data-stu-id="717ef-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="717ef-611">Użyj jednego z następujących metod, aby udostępnić stan na żądanie z programami obsługi wiadomości:</span><span class="sxs-lookup"><span data-stu-id="717ef-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="717ef-612">Przekazywanie danych do `HttpRequestMessage.Properties`programu obsługi przy użyciu programu .</span><span class="sxs-lookup"><span data-stu-id="717ef-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="717ef-613">Służy `IHttpContextAccessor` do uzyskiwania dostępu do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="717ef-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="717ef-614">Utwórz `AsyncLocal` niestandardowy obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="717ef-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="717ef-615">Korzystanie z programów obsługi opartych na polly</span><span class="sxs-lookup"><span data-stu-id="717ef-615">Use Polly-based handlers</span></span>

<span data-ttu-id="717ef-616">`IHttpClientFactory`integruje się z popularną biblioteką firm o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="717ef-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="717ef-617">Polly to kompleksowa biblioteka do obsługi błędów i stanów przemijających dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="717ef-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="717ef-618">Umożliwia deweloperom do wyrażania zasad, takich jak Ponowienie próby, Wyłącznik, Limit czasu, Izolacja grodzi i Rezerwa w sposób płynny i bezpieczny dla wątków.</span><span class="sxs-lookup"><span data-stu-id="717ef-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="717ef-619">Metody rozszerzenia są dostarczane w celu umożliwienia `HttpClient` korzystania z zasad Polly ze skonfigurowanych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="717ef-620">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="717ef-620">The Polly extensions:</span></span>

* <span data-ttu-id="717ef-621">Obsługa dodawania programów obsługi opartych na polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="717ef-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="717ef-622">Może być używany po zainstalowaniu pakietu [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="717ef-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="717ef-623">Pakiet nie jest uwzględniony w ASP.NET core udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="717ef-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="717ef-624">Obsługa usterek przejściowych</span><span class="sxs-lookup"><span data-stu-id="717ef-624">Handle transient faults</span></span>

<span data-ttu-id="717ef-625">Najczęściej błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="717ef-626">Wygodna metoda `AddTransientHttpErrorPolicy` rozszerzenia o nazwie jest dołączona, która umożliwia definiowanie zasad do obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="717ef-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="717ef-627">Zasady skonfigurowane przy użyciu `HttpRequestException`tej metody rozszerzenia obsługują odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="717ef-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="717ef-628">Rozszerzenie `AddTransientHttpErrorPolicy` może być `Startup.ConfigureServices`używane w programie .</span><span class="sxs-lookup"><span data-stu-id="717ef-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="717ef-629">Rozszerzenie zapewnia dostęp `PolicyBuilder` do obiektu skonfigurowanego do obsługi błędów reprezentujących ewentualny błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="717ef-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="717ef-630">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowano zasadę.</span><span class="sxs-lookup"><span data-stu-id="717ef-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="717ef-631">Żądania nie powiodły się są ponawiane do trzech razy z opóźnieniem 600 ms między próbami.</span><span class="sxs-lookup"><span data-stu-id="717ef-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="717ef-632">Dynamiczne wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="717ef-632">Dynamically select policies</span></span>

<span data-ttu-id="717ef-633">Istnieją dodatkowe metody rozszerzenia, które mogą służyć do dodawania programów obsługi opartych na polly.</span><span class="sxs-lookup"><span data-stu-id="717ef-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="717ef-634">Jednym z `AddPolicyHandler`takich rozszerzeń jest , który ma wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="717ef-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="717ef-635">Jedno przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają być stosowane:</span><span class="sxs-lookup"><span data-stu-id="717ef-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="717ef-636">W poprzednim kodzie, jeśli żądanie wychodzące jest HTTP GET, 10-sekundowy limit czasu jest stosowany.</span><span class="sxs-lookup"><span data-stu-id="717ef-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="717ef-637">Dla każdej innej metody HTTP używany jest 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="717ef-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="717ef-638">Dodawanie wielu programów obsługi polly</span><span class="sxs-lookup"><span data-stu-id="717ef-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="717ef-639">Często zagnieżdżasz zasady Polly, aby zapewnić ulepszoną funkcjonalność:</span><span class="sxs-lookup"><span data-stu-id="717ef-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="717ef-640">W poprzednim przykładzie są dodawane dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="717ef-641">Pierwszy używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="717ef-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="717ef-642">Żądania nieudane są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="717ef-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="717ef-643">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="717ef-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="717ef-644">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób występuje sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="717ef-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="717ef-645">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="717ef-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="717ef-646">Wszystkie wywołania za pośrednictwem tego klienta współużytkują ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="717ef-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="717ef-647">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="717ef-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="717ef-648">Podejście do zarządzania regularnie używanymi zasadami polega na `PolicyRegistry`zdefiniowaniu ich raz i zarejestrowaniu ich w pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="717ef-649">Dostępna jest metoda rozszerzenia, która umożliwia program obsługi, który ma zostać dodany przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="717ef-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="717ef-650">W poprzednim kodzie dwie zasady są `PolicyRegistry` rejestrowane po `ServiceCollection`dodaniu do pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="717ef-651">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` używana jest metoda, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="717ef-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="717ef-652">Więcej informacji `IHttpClientFactory` na temat integracji Polly można znaleźć na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="717ef-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="717ef-653">Zarządzanie protokołem i okresem istnienia httpclient</span><span class="sxs-lookup"><span data-stu-id="717ef-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="717ef-654">Nowe `HttpClient` wystąpienie jest `CreateClient` zwracane za `IHttpClientFactory`każdym razem, gdy jest wywoływana na .</span><span class="sxs-lookup"><span data-stu-id="717ef-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="717ef-655">Istnieje <xref:System.Net.Http.HttpMessageHandler> per nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="717ef-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="717ef-656">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="717ef-657">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień utworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="717ef-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="717ef-658">Wystąpienie `HttpMessageHandler` może być ponownie użyczony z `HttpClient` puli podczas tworzenia nowego wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="717ef-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="717ef-659">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi podstawowymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="717ef-660">Tworzenie większej liczby programów obsługi niż jest to konieczne, może spowodować opóźnienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="717ef-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="717ef-661">Niektóre programy obsługi również zachować połączenia otwarte przez czas nieokreślony, co może uniemożliwić program obsługi reagowania na zmiany DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="717ef-662">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="717ef-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="717ef-663">Wartość domyślna może zostać zastąpiona na podstawie nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="717ef-664">Aby go zastąpić, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> wywołaj `IHttpClientBuilder` to, co jest zwracane podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="717ef-665">Utylizacja klienta nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="717ef-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="717ef-666">Likwidacja anuluje wychodzące `HttpClient` żądania i gwarantuje, <xref:System.IDisposable.Dispose*>że danego wystąpienia nie można użyć po wywołaniu .</span><span class="sxs-lookup"><span data-stu-id="717ef-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="717ef-667">`IHttpClientFactory`usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="717ef-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="717ef-668">Wystąpienia `HttpClient` można ogólnie traktować jako obiekty .NET nie wymagające usuwania.</span><span class="sxs-lookup"><span data-stu-id="717ef-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="717ef-669">Utrzymywanie `HttpClient` jednego wystąpienia przy życiu przez długi czas jest typowym wzorcem używanym przed powstaniem . `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="717ef-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="717ef-670">Ten wzorzec staje `IHttpClientFactory`się niepotrzebny po migracji do .</span><span class="sxs-lookup"><span data-stu-id="717ef-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="717ef-671">Alternatywy dla IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="717ef-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="717ef-672">Używanie `IHttpClientFactory` w aplikacji obsługującej technologię DI pozwala uniknąć:</span><span class="sxs-lookup"><span data-stu-id="717ef-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="717ef-673">Problemy z wyczerpaniem `HttpMessageHandler` zasobów przez buforowanie wystąpień.</span><span class="sxs-lookup"><span data-stu-id="717ef-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="717ef-674">Nieaktualne `HttpMessageHandler` problemy z systemem DNS przez wystąpienia rowerowe w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="717ef-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="717ef-675">Istnieją alternatywne sposoby rozwiązywania poprzednich problemów <xref:System.Net.Http.SocketsHttpHandler> przy użyciu wystąpienia długotrwałego.</span><span class="sxs-lookup"><span data-stu-id="717ef-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="717ef-676">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiony i używać go przez cały okres użytkowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="717ef-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="717ef-677">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> do odpowiedniej wartości na podstawie czasów odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="717ef-678">Utwórz `HttpClient` wystąpienia `new HttpClient(handler, disposeHandler: false)` przy użyciu w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="717ef-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="717ef-679">Powyższe podejścia rozwiązują problemy `IHttpClientFactory` z zarządzaniem zasobami, które rozwiązuje się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="717ef-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="717ef-680">Współudzieli `SocketsHttpHandler` `HttpClient` połączenia między wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="717ef-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="717ef-681">To udostępnianie zapobiega wyczerpaniu gniazda.</span><span class="sxs-lookup"><span data-stu-id="717ef-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="717ef-682">Cykle `SocketsHttpHandler` połączeń zgodnie `PooledConnectionLifetime` z aby uniknąć starych problemów z systemem DNS.</span><span class="sxs-lookup"><span data-stu-id="717ef-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="717ef-683">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="717ef-683">Cookies</span></span>

<span data-ttu-id="717ef-684">W puli `HttpMessageHandler` wystąpień `CookieContainer` powoduje obiekty są współużytkowane.</span><span class="sxs-lookup"><span data-stu-id="717ef-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="717ef-685">Udostępnianie nieoczekiwanych `CookieContainer` obiektów często powoduje niepoprawny kod.</span><span class="sxs-lookup"><span data-stu-id="717ef-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="717ef-686">W przypadku aplikacji, które wymagają plików cookie, należy wziąć pod uwagę:</span><span class="sxs-lookup"><span data-stu-id="717ef-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="717ef-687">Wyłączenie automatycznej obsługi plików cookie</span><span class="sxs-lookup"><span data-stu-id="717ef-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="717ef-688">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="717ef-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="717ef-689">Wywołanie, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="717ef-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="717ef-690">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="717ef-690">Logging</span></span>

<span data-ttu-id="717ef-691">Klienci utworzone `IHttpClientFactory` za pomocą komunikatów dziennika rekordów dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="717ef-692">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="717ef-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="717ef-693">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="717ef-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="717ef-694">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="717ef-695">Klient o nazwie *MyNamedClient*, na przykład, rejestruje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`wiadomości z kategorii .</span><span class="sxs-lookup"><span data-stu-id="717ef-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="717ef-696">Komunikaty sufiksy z *LogicalHandler* występują poza potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="717ef-697">W żądaniu wiadomości są rejestrowane, zanim inne programy obsługi w potoku przetworzyły go.</span><span class="sxs-lookup"><span data-stu-id="717ef-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="717ef-698">W odpowiedzi wiadomości są rejestrowane po innych programów obsługi potoku otrzymały odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="717ef-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="717ef-699">Rejestrowanie występuje również wewnątrz potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="717ef-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="717ef-700">W przykładzie *MyNamedClient* te komunikaty są rejestrowane `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`względem kategorii dziennika .</span><span class="sxs-lookup"><span data-stu-id="717ef-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="717ef-701">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i bezpośrednio przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="717ef-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="717ef-702">W odpowiedzi to rejestrowanie zawiera stan odpowiedzi, zanim przejdzie z powrotem za pośrednictwem potoku obsługi.</span><span class="sxs-lookup"><span data-stu-id="717ef-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="717ef-703">Włączenie rejestrowania na zewnątrz i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="717ef-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="717ef-704">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="717ef-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="717ef-705">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dziennika dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="717ef-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="717ef-706">Konfigurowanie usługi HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="717ef-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="717ef-707">Może być konieczne kontrolowanie konfiguracji `HttpMessageHandler` wewnętrznej używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="717ef-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="717ef-708">Jest `IHttpClientBuilder` zwracany podczas dodawania klientów nazwanych lub wpisanych.</span><span class="sxs-lookup"><span data-stu-id="717ef-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="717ef-709">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzenia może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="717ef-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="717ef-710">Pełnomocnik jest używany do tworzenia i `HttpMessageHandler` konfigurowania podstawowego używanego przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="717ef-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="717ef-711">Używanie programu IHttpClientFactory w aplikacji konsoli</span><span class="sxs-lookup"><span data-stu-id="717ef-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="717ef-712">W aplikacji konsoli dodaj do projektu następujące odwołania do pakietu:</span><span class="sxs-lookup"><span data-stu-id="717ef-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="717ef-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="717ef-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="717ef-714">Microsoft.Extensions.http</span><span class="sxs-lookup"><span data-stu-id="717ef-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="717ef-715">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="717ef-715">In the following example:</span></span>

* <span data-ttu-id="717ef-716"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="717ef-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="717ef-717">`MyService`tworzy wystąpienie fabryki klienta z usługi, która `HttpClient`jest używana do tworzenia pliku .</span><span class="sxs-lookup"><span data-stu-id="717ef-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="717ef-718">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="717ef-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="717ef-719">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisu pierwszych 500 znaków zawartości strony sieci Web do konsoli.</span><span class="sxs-lookup"><span data-stu-id="717ef-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="717ef-720">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="717ef-720">Header propagation middleware</span></span>

<span data-ttu-id="717ef-721">Propagacja nagłówka jest obsługiwanym przez społeczność oprogramowaniem pośredniczącym do propagowania nagłówków HTTP z żądania przychodzącego do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="717ef-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="717ef-722">Aby użyć propagacji nagłówka, należy:</span><span class="sxs-lookup"><span data-stu-id="717ef-722">To use header propagation:</span></span>

* <span data-ttu-id="717ef-723">Odwoływać się do społeczności obsługiwane portu pakietu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="717ef-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="717ef-724">ASP.NET Core 3.1 i nowsze obsługuje [microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="717ef-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="717ef-725">Skonfiguruj `HttpClient` oprogramowanie `Startup`pośredniczące i w :</span><span class="sxs-lookup"><span data-stu-id="717ef-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="717ef-726">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="717ef-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="717ef-727">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="717ef-727">Additional resources</span></span>

* [<span data-ttu-id="717ef-728">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="717ef-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="717ef-729">Implementowanie ponownych prób wywołania HTTP z wykładniczym wycofywania z httpclientfactory i polly zasad</span><span class="sxs-lookup"><span data-stu-id="717ef-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="717ef-730">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="717ef-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
