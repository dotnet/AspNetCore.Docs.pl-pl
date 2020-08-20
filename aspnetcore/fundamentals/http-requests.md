---
title: Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core
author: stevejgordon
description: Dowiedz się więcej o używaniu interfejsu IHttpClientFactory do zarządzania wystąpieniami logicznych HttpClient w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
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
uid: fundamentals/http-requests
ms.openlocfilehash: ca52b6cf8646bced3a228341717f8ccb1edff582
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634205"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="139fe-103">Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="139fe-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="139fe-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="139fe-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="139fe-105"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="139fe-106">`IHttpClientFactory` oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="139fe-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="139fe-107">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="139fe-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="139fe-108">Na przykład klient o nazwie  *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="139fe-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="139fe-109">Domyślny klient można zarejestrować na potrzeby ogólnego dostępu.</span><span class="sxs-lookup"><span data-stu-id="139fe-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="139fe-110">Kodyfikacja koncepcji wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="139fe-111">Udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z delegowania programów obsługi w programie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="139fe-112">Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` wystąpień podstawowych.</span><span class="sxs-lookup"><span data-stu-id="139fe-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="139fe-113">Automatyczne zarządzanie pozwala uniknąć typowych problemów z usługą DNS (Domain Name System) występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="139fe-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="139fe-114">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="139fe-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="139fe-115">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="139fe-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="139fe-116">Przykładowy kod w tym temacie używa <xref:System.Text.Json> do deserializacji zawartości JSON zwróconej w odpowiedziach http.</span><span class="sxs-lookup"><span data-stu-id="139fe-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="139fe-117">Aby zapoznać się z przykładami używanymi przez `Json.NET` `ReadAsAsync<T>` program i, użyj selektora wersji, aby wybrać wersję 2. x tego tematu.</span><span class="sxs-lookup"><span data-stu-id="139fe-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="139fe-118">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="139fe-118">Consumption patterns</span></span>

<span data-ttu-id="139fe-119">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="139fe-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="139fe-120">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="139fe-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="139fe-121">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="139fe-122">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="139fe-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="139fe-123">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="139fe-124">Najlepsze podejście zależy od wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="139fe-125">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="139fe-125">Basic usage</span></span>

<span data-ttu-id="139fe-126">`IHttpClientFactory` można zarejestrować, wywołując `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="139fe-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="139fe-127">`IHttpClientFactory`Można zażądać przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="139fe-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="139fe-128">Następujący kod używa `IHttpClientFactory` do tworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="139fe-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="139fe-129">Użycie `IHttpClientFactory` podobnej do powyższego przykładu jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="139fe-130">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="139fe-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="139fe-131">W miejscach, w których `HttpClient` wystąpienia są tworzone w istniejącej aplikacji, Zastąp te wystąpienia wywołaniami do <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="139fe-132">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-132">Named clients</span></span>

<span data-ttu-id="139fe-133">Nazwani klienci są dobrym wyborem w przypadku:</span><span class="sxs-lookup"><span data-stu-id="139fe-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="139fe-134">Aplikacja wymaga wielu odrębnych użycia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="139fe-135">Wiele `HttpClient` s ma inną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="139fe-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="139fe-136">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="139fe-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="139fe-137">W powyższym kodzie klient ma skonfigurowany program:</span><span class="sxs-lookup"><span data-stu-id="139fe-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="139fe-138">Adres podstawowy `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="139fe-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="139fe-139">Dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="139fe-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="139fe-140">Klient</span><span class="sxs-lookup"><span data-stu-id="139fe-140">CreateClient</span></span>

<span data-ttu-id="139fe-141">Za każdym razem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="139fe-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="139fe-142">Tworzone jest nowe wystąpienie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="139fe-143">Akcja konfiguracji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="139fe-143">The configuration action is called.</span></span>

<span data-ttu-id="139fe-144">Aby utworzyć nazwanego klienta, Przekaż jego nazwę do `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="139fe-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="139fe-145">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="139fe-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="139fe-146">Kod może przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="139fe-147">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="139fe-147">Typed clients</span></span>

<span data-ttu-id="139fe-148">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="139fe-148">Typed clients:</span></span>

* <span data-ttu-id="139fe-149">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="139fe-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="139fe-150">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="139fe-151">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="139fe-152">Na przykład może zostać użyty pojedynczy klient z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="139fe-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="139fe-153">Dla pojedynczego punktu końcowego zaplecza.</span><span class="sxs-lookup"><span data-stu-id="139fe-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="139fe-154">Do hermetyzacji wszystkich logiki związanych z punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="139fe-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="139fe-155">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="139fe-156">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="139fe-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="139fe-157">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="139fe-157">In the preceding code:</span></span>

* <span data-ttu-id="139fe-158">Konfiguracja zostanie przeniesiona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="139fe-159">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="139fe-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="139fe-160">Można tworzyć metody specyficzne dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="139fe-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="139fe-161">Na przykład `GetAspNetDocsIssues` Metoda hermetyzuje kod w celu pobrania otwartych problemów.</span><span class="sxs-lookup"><span data-stu-id="139fe-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="139fe-162">Następujący kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> w `Startup.ConfigureServices` celu zarejestrowania klasy klienta typu:</span><span class="sxs-lookup"><span data-stu-id="139fe-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="139fe-163">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="139fe-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="139fe-164">W poprzednim kodzie program `AddHttpClient` rejestruje `GitHubService` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="139fe-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="139fe-165">Ta rejestracja używa metody fabryki do:</span><span class="sxs-lookup"><span data-stu-id="139fe-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="139fe-166">Utwórz wystąpienie elementu `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="139fe-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="139fe-167">Utwórz wystąpienie `GitHubService` , przekazując w wystąpieniu `HttpClient` do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="139fe-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="139fe-168">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="139fe-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="139fe-169">Konfigurację dla określonego klienta można określić podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="139fe-170">`HttpClient`Mogą być hermetyzowane w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="139fe-171">Zamiast uwidaczniać ją jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="139fe-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="139fe-172">W poprzednim kodzie, `HttpClient` jest przechowywany w prywatnym polu.</span><span class="sxs-lookup"><span data-stu-id="139fe-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="139fe-173">Dostęp do programu `HttpClient` ma metoda publiczna `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="139fe-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="139fe-174">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-174">Generated clients</span></span>

<span data-ttu-id="139fe-175">`IHttpClientFactory` może być używany w połączeniu z bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="139fe-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="139fe-176">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="139fe-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="139fe-177">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="139fe-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="139fe-178">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="139fe-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="139fe-179">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="139fe-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="139fe-180">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="139fe-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="139fe-181">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="139fe-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="139fe-182">Wykonywanie żądań POST, PUT i DELETE</span><span class="sxs-lookup"><span data-stu-id="139fe-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="139fe-183">W powyższych przykładach wszystkie żądania HTTP używają zlecenia GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="139fe-184">`HttpClient` Program obsługuje również inne zlecenia HTTP, w tym:</span><span class="sxs-lookup"><span data-stu-id="139fe-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="139fe-185">POST</span><span class="sxs-lookup"><span data-stu-id="139fe-185">POST</span></span>
* <span data-ttu-id="139fe-186">PUT</span><span class="sxs-lookup"><span data-stu-id="139fe-186">PUT</span></span>
* <span data-ttu-id="139fe-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="139fe-187">DELETE</span></span>
* <span data-ttu-id="139fe-188">WYSŁANA</span><span class="sxs-lookup"><span data-stu-id="139fe-188">PATCH</span></span>

<span data-ttu-id="139fe-189">Aby uzyskać pełną listę obsługiwanych czasowników HTTP, zobacz <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="139fe-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="139fe-190">Poniższy przykład pokazuje, jak utworzyć żądanie HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="139fe-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="139fe-191">W poprzednim kodzie `CreateItemAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="139fe-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="139fe-192">Serializować `TodoItem` parametr do formatu JSON przy użyciu `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="139fe-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="139fe-193">Powoduje to użycie wystąpienia programu <xref:System.Text.Json.JsonSerializerOptions> w celu skonfigurowania procesu serializacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="139fe-194">Tworzy wystąpienie <xref:System.Net.Http.StringContent> do pakowania serializowanego kodu JSON do wysłania w treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="139fe-195">Wywołuje <xref:System.Net.Http.HttpClient.PostAsync%2A> wysyłanie zawartości JSON do określonego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="139fe-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="139fe-196">Jest to względny adres URL, który zostanie dodany do [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="139fe-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="139fe-197">Wywołuje, <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> Aby zgłosić wyjątek, jeśli kod stanu odpowiedzi nie wskazuje powodzenia.</span><span class="sxs-lookup"><span data-stu-id="139fe-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="139fe-198">`HttpClient` obsługuje również inne typy zawartości.</span><span class="sxs-lookup"><span data-stu-id="139fe-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="139fe-199">Przykład: <xref:System.Net.Http.MultipartContent> i <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="139fe-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="139fe-200">Aby zapoznać się z pełną listą obsługiwanych zawartości, zobacz <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="139fe-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="139fe-201">Poniższy przykład przedstawia żądanie HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="139fe-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="139fe-202">Poprzedni kod jest bardzo podobny do przykładu POST.</span><span class="sxs-lookup"><span data-stu-id="139fe-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="139fe-203">`SaveItemAsync`Metoda wywołuje <xref:System.Net.Http.HttpClient.PutAsync%2A> zamiast `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="139fe-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="139fe-204">Poniższy przykład przedstawia żądanie HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="139fe-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="139fe-205">W poprzednim kodzie `DeleteItemAsync` Metoda wywołuje metodę <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="139fe-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="139fe-206">Ponieważ żądania HTTP DELETE zazwyczaj nie zawierają treści, `DeleteAsync` Metoda nie zapewnia przeciążenia, które akceptuje wystąpienie `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="139fe-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="139fe-207">Aby dowiedzieć się więcej o używaniu różnych zleceń HTTP w programie `HttpClient` , zobacz <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="139fe-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="139fe-208">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="139fe-208">Outgoing request middleware</span></span>

<span data-ttu-id="139fe-209">`HttpClient` ma koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="139fe-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="139fe-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="139fe-211">Upraszcza definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="139fe-212">Obsługuje rejestrację i łańcuch wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="139fe-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="139fe-213">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="139fe-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="139fe-214">Ten wzorzec:</span><span class="sxs-lookup"><span data-stu-id="139fe-214">This pattern:</span></span>

  * <span data-ttu-id="139fe-215">Jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="139fe-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="139fe-216">Program udostępnia mechanizm zarządzania problemami z obcinaniem między żądaniami HTTP, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="139fe-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="139fe-217">pamięć</span><span class="sxs-lookup"><span data-stu-id="139fe-217">caching</span></span>
    * <span data-ttu-id="139fe-218">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="139fe-218">error handling</span></span>
    * <span data-ttu-id="139fe-219">serializacji</span><span class="sxs-lookup"><span data-stu-id="139fe-219">serialization</span></span>
    * <span data-ttu-id="139fe-220">rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="139fe-220">logging</span></span>

<span data-ttu-id="139fe-221">Aby utworzyć program obsługi delegowania:</span><span class="sxs-lookup"><span data-stu-id="139fe-221">To create a delegating handler:</span></span>

* <span data-ttu-id="139fe-222">Pochodny od <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="139fe-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="139fe-223">Zastąpienie <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="139fe-224">Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="139fe-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="139fe-225">Poprzedni kod sprawdza, czy `X-API-KEY` nagłówek jest w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="139fe-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="139fe-226">Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="139fe-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="139fe-227">Do konfiguracji dla elementu with można dodać więcej niż jeden program obsługi `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="139fe-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="139fe-228">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="139fe-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="139fe-229">`IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="139fe-230">Programy obsługi mogą zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="139fe-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="139fe-231">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="139fe-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="139fe-232">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="139fe-233">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="139fe-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="139fe-234">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="139fe-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="139fe-235">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="139fe-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="139fe-236">Przekaż dane do procedury obsługi przy użyciu [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="139fe-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="139fe-237">Użyj, <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="139fe-238">Utwórz niestandardowy <xref:System.Threading.AsyncLocal`1> obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="139fe-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="139fe-239">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-239">Use Polly-based handlers</span></span>

<span data-ttu-id="139fe-240">`IHttpClientFactory` integruje się z biblioteką innych firm [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="139fe-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="139fe-241">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="139fe-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="139fe-242">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="139fe-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="139fe-243">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="139fe-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="139fe-244">Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na Polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="139fe-245">Polly wymaga pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="139fe-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="139fe-246">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="139fe-246">Handle transient faults</span></span>

<span data-ttu-id="139fe-247">Błędy są zwykle wykonywane, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="139fe-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="139fe-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="139fe-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="139fe-249">Skonfigurowane zasady z `AddTransientHttpErrorPolicy` obsługą następujących odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="139fe-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="139fe-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="139fe-250">HTTP 5xx</span></span>
* <span data-ttu-id="139fe-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="139fe-251">HTTP 408</span></span>

<span data-ttu-id="139fe-252">`AddTransientHttpErrorPolicy` zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="139fe-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="139fe-253">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="139fe-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="139fe-254">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="139fe-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="139fe-255">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="139fe-255">Dynamically select policies</span></span>

<span data-ttu-id="139fe-256">Metody rozszerzające umożliwiają dodawanie programów obsługi opartych na Polly, na przykład <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="139fe-257">Następujące `AddPolicyHandler` Przeciążenie przeprowadziło inspekcję żądania, aby zdecydować, które zasady zastosować:</span><span class="sxs-lookup"><span data-stu-id="139fe-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="139fe-258">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="139fe-259">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="139fe-260">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="139fe-261">Jest to typowy sposób zagnieżdżania zasad Pollyymi:</span><span class="sxs-lookup"><span data-stu-id="139fe-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="139fe-262">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="139fe-262">In the preceding example:</span></span>

* <span data-ttu-id="139fe-263">Dodawane są dwa procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-263">Two handlers are added.</span></span>
* <span data-ttu-id="139fe-264">Pierwsza procedura obsługi używa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> do dodawania zasad ponawiania.</span><span class="sxs-lookup"><span data-stu-id="139fe-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="139fe-265">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="139fe-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="139fe-266">Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="139fe-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="139fe-267">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="139fe-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="139fe-268">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="139fe-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="139fe-269">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="139fe-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="139fe-270">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="139fe-271">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="139fe-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="139fe-272">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="139fe-272">In the following code:</span></span>

* <span data-ttu-id="139fe-273">Są dodawane zasady "regularne" i "długie".</span><span class="sxs-lookup"><span data-stu-id="139fe-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="139fe-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> dodaje zasady "regularne" i "długie" z rejestru.</span><span class="sxs-lookup"><span data-stu-id="139fe-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="139fe-275">Aby uzyskać więcej informacji na temat `IHttpClientFactory` integracji z integracją i Polly, zobacz [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="139fe-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="139fe-276">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="139fe-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="139fe-277">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="139fe-278"><xref:System.Net.Http.HttpMessageHandler>Tworzony jest na nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="139fe-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="139fe-279">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="139fe-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="139fe-280">`IHttpClientFactory` pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="139fe-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="139fe-281">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="139fe-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="139fe-282">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="139fe-283">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="139fe-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="139fe-284">Niektóre programy obsługi powodują również, że połączenia są otwierane w nieskończoność, co może uniemożliwić oddziałanie programu obsługi w systemie DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="139fe-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="139fe-285">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="139fe-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="139fe-286">Wartość domyślną można przesłonić na podstawie nazwy klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="139fe-287">`HttpClient` wystąpienia mogą być zwykle traktowane jako obiekty .NET, które **nie** wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="139fe-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="139fe-288">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="139fe-289">`IHttpClientFactory` śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="139fe-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="139fe-290">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="139fe-291">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="139fe-292">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="139fe-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="139fe-293">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="139fe-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="139fe-294">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="139fe-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="139fe-295">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="139fe-296">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="139fe-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="139fe-297">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="139fe-298">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="139fe-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="139fe-299">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="139fe-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="139fe-300">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="139fe-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="139fe-301">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="139fe-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="139fe-302">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="139fe-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="139fe-303">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="139fe-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="139fe-304">Cookiewolumin</span><span class="sxs-lookup"><span data-stu-id="139fe-304">Cookies</span></span>

<span data-ttu-id="139fe-305">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="139fe-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="139fe-306">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="139fe-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="139fe-307">W przypadku aplikacji, które wymagają usługi cookie s, należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="139fe-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="139fe-308">Wyłączanie cookie obsługi automatycznej</span><span class="sxs-lookup"><span data-stu-id="139fe-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="139fe-309">Unikanie `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="139fe-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="139fe-310">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> wyłączania automatycznej cookie obsługi:</span><span class="sxs-lookup"><span data-stu-id="139fe-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="139fe-311">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="139fe-311">Logging</span></span>

<span data-ttu-id="139fe-312">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="139fe-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="139fe-313">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="139fe-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="139fe-314">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="139fe-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="139fe-315">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="139fe-316">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="139fe-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="139fe-317">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="139fe-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="139fe-318">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="139fe-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="139fe-319">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="139fe-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="139fe-320">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="139fe-321">W przykładzie *MyNamedClient* te komunikaty są rejestrowane z kategorią dziennika "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="139fe-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="139fe-322">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="139fe-323">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="139fe-324">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="139fe-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="139fe-325">Może to obejmować zmiany w nagłówkach żądania lub w kodzie stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="139fe-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="139fe-326">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="139fe-327">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="139fe-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="139fe-328">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="139fe-329">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="139fe-330"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="139fe-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="139fe-331">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="139fe-332">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="139fe-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="139fe-333">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="139fe-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="139fe-334">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="139fe-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="139fe-335">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="139fe-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="139fe-336">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="139fe-336">In the following example:</span></span>

* <span data-ttu-id="139fe-337"><xref:System.Net.Http.IHttpClientFactory> jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="139fe-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="139fe-338">`MyService` tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="139fe-339">`HttpClient` służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="139fe-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="139fe-340">`Main` tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="139fe-341">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="139fe-341">Header propagation middleware</span></span>

<span data-ttu-id="139fe-342">Propagacja nagłówka to ASP.NET Core oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="139fe-343">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="139fe-343">To use header propagation:</span></span>

* <span data-ttu-id="139fe-344">Odwołuje się do pakietu [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="139fe-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="139fe-345">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :</span><span class="sxs-lookup"><span data-stu-id="139fe-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="139fe-346">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="139fe-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="139fe-347">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="139fe-347">Additional resources</span></span>

* [<span data-ttu-id="139fe-348">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="139fe-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="139fe-349">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="139fe-350">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="139fe-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="139fe-351">Jak serializować i deserializować kod JSON w programie .NET</span><span class="sxs-lookup"><span data-stu-id="139fe-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="139fe-352">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="139fe-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="139fe-353"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="139fe-354">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="139fe-354">It offers the following benefits:</span></span>

* <span data-ttu-id="139fe-355">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="139fe-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="139fe-356">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="139fe-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="139fe-357">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="139fe-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="139fe-358">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="139fe-359">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="139fe-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="139fe-360">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="139fe-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="139fe-361">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="139fe-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="139fe-362">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="139fe-362">Consumption patterns</span></span>

<span data-ttu-id="139fe-363">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="139fe-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="139fe-364">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="139fe-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="139fe-365">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="139fe-366">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="139fe-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="139fe-367">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="139fe-368">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="139fe-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="139fe-369">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="139fe-370">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="139fe-370">Basic usage</span></span>

<span data-ttu-id="139fe-371">`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="139fe-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="139fe-372">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="139fe-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="139fe-373">`IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="139fe-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="139fe-374">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="139fe-375">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="139fe-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="139fe-376">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="139fe-377">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-377">Named clients</span></span>

<span data-ttu-id="139fe-378">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="139fe-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="139fe-379">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="139fe-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="139fe-380">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="139fe-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="139fe-381">Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="139fe-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="139fe-382">Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="139fe-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="139fe-383">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="139fe-384">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="139fe-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="139fe-385">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="139fe-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="139fe-386">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="139fe-387">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="139fe-387">Typed clients</span></span>

<span data-ttu-id="139fe-388">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="139fe-388">Typed clients:</span></span>

* <span data-ttu-id="139fe-389">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="139fe-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="139fe-390">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="139fe-391">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="139fe-392">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="139fe-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="139fe-393">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="139fe-394">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="139fe-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="139fe-395">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="139fe-396">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="139fe-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="139fe-397">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="139fe-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="139fe-398">`GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="139fe-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="139fe-399">Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="139fe-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="139fe-400">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="139fe-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="139fe-401">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="139fe-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="139fe-402">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="139fe-403">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="139fe-404">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="139fe-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="139fe-405">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="139fe-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="139fe-406">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.</span><span class="sxs-lookup"><span data-stu-id="139fe-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="139fe-407">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-407">Generated clients</span></span>

<span data-ttu-id="139fe-408">`IHttpClientFactory` może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="139fe-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="139fe-409">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="139fe-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="139fe-410">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="139fe-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="139fe-411">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="139fe-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="139fe-412">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="139fe-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="139fe-413">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="139fe-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="139fe-414">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="139fe-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="139fe-415">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="139fe-415">Outgoing request middleware</span></span>

<span data-ttu-id="139fe-416">`HttpClient` ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="139fe-417">`IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="139fe-418">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="139fe-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="139fe-419">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="139fe-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="139fe-420">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="139fe-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="139fe-421">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="139fe-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="139fe-422">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="139fe-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="139fe-423">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="139fe-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="139fe-424">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="139fe-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="139fe-425">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="139fe-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="139fe-426">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="139fe-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="139fe-427">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="139fe-428">To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="139fe-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="139fe-429">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="139fe-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="139fe-430">`IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="139fe-431">Programy obsługi są bezpłatne, aby zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="139fe-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="139fe-432">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="139fe-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="139fe-433">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="139fe-434">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="139fe-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="139fe-435">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="139fe-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="139fe-436">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="139fe-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="139fe-437">Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="139fe-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="139fe-438">Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="139fe-439">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="139fe-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="139fe-440">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-440">Use Polly-based handlers</span></span>

<span data-ttu-id="139fe-441">`IHttpClientFactory` integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="139fe-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="139fe-442">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="139fe-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="139fe-443">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="139fe-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="139fe-444">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="139fe-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="139fe-445">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="139fe-445">The Polly extensions:</span></span>

* <span data-ttu-id="139fe-446">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="139fe-447">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="139fe-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="139fe-448">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="139fe-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="139fe-449">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="139fe-449">Handle transient faults</span></span>

<span data-ttu-id="139fe-450">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="139fe-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="139fe-451">Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="139fe-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="139fe-452">Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.</span><span class="sxs-lookup"><span data-stu-id="139fe-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="139fe-453">`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="139fe-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="139fe-454">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="139fe-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="139fe-455">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="139fe-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="139fe-456">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="139fe-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="139fe-457">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="139fe-457">Dynamically select policies</span></span>

<span data-ttu-id="139fe-458">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="139fe-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="139fe-459">Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="139fe-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="139fe-460">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="139fe-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="139fe-461">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="139fe-462">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="139fe-463">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="139fe-464">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="139fe-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="139fe-465">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="139fe-466">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="139fe-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="139fe-467">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="139fe-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="139fe-468">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="139fe-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="139fe-469">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="139fe-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="139fe-470">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="139fe-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="139fe-471">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="139fe-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="139fe-472">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="139fe-473">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="139fe-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="139fe-474">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="139fe-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="139fe-475">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="139fe-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="139fe-476">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="139fe-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="139fe-477">Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="139fe-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="139fe-478">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="139fe-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="139fe-479">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="139fe-480">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="139fe-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="139fe-481">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="139fe-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="139fe-482">`IHttpClientFactory` pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="139fe-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="139fe-483">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="139fe-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="139fe-484">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="139fe-485">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="139fe-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="139fe-486">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="139fe-487">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="139fe-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="139fe-488">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="139fe-489">Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="139fe-490">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="139fe-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="139fe-491">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="139fe-492">`IHttpClientFactory` śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="139fe-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="139fe-493">`HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="139fe-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="139fe-494">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="139fe-495">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="139fe-496">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="139fe-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="139fe-497">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="139fe-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="139fe-498">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="139fe-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="139fe-499">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="139fe-500">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="139fe-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="139fe-501">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="139fe-502">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="139fe-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="139fe-503">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="139fe-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="139fe-504">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="139fe-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="139fe-505">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="139fe-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="139fe-506">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="139fe-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="139fe-507">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="139fe-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="139fe-508">Cookiewolumin</span><span class="sxs-lookup"><span data-stu-id="139fe-508">Cookies</span></span>

<span data-ttu-id="139fe-509">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="139fe-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="139fe-510">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="139fe-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="139fe-511">W przypadku aplikacji, które wymagają usługi cookie s, należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="139fe-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="139fe-512">Wyłączanie cookie obsługi automatycznej</span><span class="sxs-lookup"><span data-stu-id="139fe-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="139fe-513">Unikanie `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="139fe-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="139fe-514">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> wyłączania automatycznej cookie obsługi:</span><span class="sxs-lookup"><span data-stu-id="139fe-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="139fe-515">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="139fe-515">Logging</span></span>

<span data-ttu-id="139fe-516">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="139fe-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="139fe-517">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="139fe-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="139fe-518">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="139fe-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="139fe-519">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="139fe-520">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="139fe-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="139fe-521">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="139fe-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="139fe-522">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="139fe-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="139fe-523">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="139fe-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="139fe-524">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="139fe-525">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="139fe-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="139fe-526">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="139fe-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="139fe-527">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="139fe-528">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="139fe-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="139fe-529">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="139fe-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="139fe-530">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="139fe-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="139fe-531">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="139fe-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="139fe-532">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="139fe-533">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="139fe-534"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="139fe-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="139fe-535">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="139fe-536">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="139fe-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="139fe-537">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="139fe-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="139fe-538">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="139fe-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="139fe-539">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="139fe-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="139fe-540">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="139fe-540">In the following example:</span></span>

* <span data-ttu-id="139fe-541"><xref:System.Net.Http.IHttpClientFactory> jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="139fe-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="139fe-542">`MyService` tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="139fe-543">`HttpClient` służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="139fe-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="139fe-544">`Main` tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="139fe-545">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="139fe-545">Additional resources</span></span>

* [<span data-ttu-id="139fe-546">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="139fe-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="139fe-547">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="139fe-548">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="139fe-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="139fe-549">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="139fe-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="139fe-550"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="139fe-551">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="139fe-551">It offers the following benefits:</span></span>

* <span data-ttu-id="139fe-552">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="139fe-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="139fe-553">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="139fe-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="139fe-554">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="139fe-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="139fe-555">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="139fe-556">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="139fe-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="139fe-557">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="139fe-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="139fe-558">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="139fe-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="139fe-559">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="139fe-559">Prerequisites</span></span>

<span data-ttu-id="139fe-560">Projekty docelowe .NET Framework wymagają instalacji pakietu NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) .</span><span class="sxs-lookup"><span data-stu-id="139fe-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="139fe-561">Projekty przeznaczone dla platformy .NET Core i odwołujące się do [pakietu Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) zawierają już `Microsoft.Extensions.Http` pakiet.</span><span class="sxs-lookup"><span data-stu-id="139fe-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="139fe-562">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="139fe-562">Consumption patterns</span></span>

<span data-ttu-id="139fe-563">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="139fe-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="139fe-564">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="139fe-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="139fe-565">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="139fe-566">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="139fe-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="139fe-567">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="139fe-568">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="139fe-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="139fe-569">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="139fe-570">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="139fe-570">Basic usage</span></span>

<span data-ttu-id="139fe-571">`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="139fe-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="139fe-572">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="139fe-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="139fe-573">`IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="139fe-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="139fe-574">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="139fe-575">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="139fe-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="139fe-576">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="139fe-577">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-577">Named clients</span></span>

<span data-ttu-id="139fe-578">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="139fe-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="139fe-579">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="139fe-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="139fe-580">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="139fe-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="139fe-581">Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="139fe-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="139fe-582">Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="139fe-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="139fe-583">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="139fe-584">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="139fe-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="139fe-585">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="139fe-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="139fe-586">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="139fe-587">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="139fe-587">Typed clients</span></span>

<span data-ttu-id="139fe-588">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="139fe-588">Typed clients:</span></span>

* <span data-ttu-id="139fe-589">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="139fe-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="139fe-590">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="139fe-591">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="139fe-592">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="139fe-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="139fe-593">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="139fe-594">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="139fe-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="139fe-595">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="139fe-596">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="139fe-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="139fe-597">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="139fe-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="139fe-598">`GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="139fe-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="139fe-599">Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="139fe-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="139fe-600">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="139fe-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="139fe-601">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="139fe-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="139fe-602">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="139fe-603">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="139fe-604">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="139fe-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="139fe-605">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="139fe-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="139fe-606">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.</span><span class="sxs-lookup"><span data-stu-id="139fe-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="139fe-607">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="139fe-607">Generated clients</span></span>

<span data-ttu-id="139fe-608">`IHttpClientFactory` może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="139fe-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="139fe-609">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="139fe-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="139fe-610">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="139fe-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="139fe-611">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="139fe-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="139fe-612">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="139fe-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="139fe-613">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="139fe-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="139fe-614">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="139fe-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="139fe-615">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="139fe-615">Outgoing request middleware</span></span>

<span data-ttu-id="139fe-616">`HttpClient` ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="139fe-617">`IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="139fe-618">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="139fe-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="139fe-619">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="139fe-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="139fe-620">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="139fe-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="139fe-621">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="139fe-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="139fe-622">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="139fe-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="139fe-623">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="139fe-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="139fe-624">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="139fe-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="139fe-625">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="139fe-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="139fe-626">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="139fe-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="139fe-627">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="139fe-628">To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="139fe-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="139fe-629">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="139fe-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="139fe-630">Procedura obsługi **musi** być zarejestrowana w programie di jako przejściowa usługa, nigdy nie jest objęta zakresem.</span><span class="sxs-lookup"><span data-stu-id="139fe-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="139fe-631">Jeśli program obsługi jest zarejestrowany jako usługa objęta zakresem, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:</span><span class="sxs-lookup"><span data-stu-id="139fe-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="139fe-632">Usługi programu obsługi mogły zostać zlikwidowane, zanim program obsługi znajdzie się poza zakresem.</span><span class="sxs-lookup"><span data-stu-id="139fe-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="139fe-633">Usunięte usługi obsługi powodują niepowodzenie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="139fe-634">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując w typie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="139fe-635">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="139fe-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="139fe-636">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="139fe-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="139fe-637">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="139fe-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="139fe-638">Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="139fe-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="139fe-639">Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="139fe-640">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="139fe-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="139fe-641">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-641">Use Polly-based handlers</span></span>

<span data-ttu-id="139fe-642">`IHttpClientFactory` integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="139fe-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="139fe-643">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="139fe-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="139fe-644">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="139fe-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="139fe-645">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="139fe-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="139fe-646">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="139fe-646">The Polly extensions:</span></span>

* <span data-ttu-id="139fe-647">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="139fe-648">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="139fe-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="139fe-649">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="139fe-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="139fe-650">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="139fe-650">Handle transient faults</span></span>

<span data-ttu-id="139fe-651">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="139fe-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="139fe-652">Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="139fe-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="139fe-653">Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.</span><span class="sxs-lookup"><span data-stu-id="139fe-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="139fe-654">`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="139fe-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="139fe-655">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="139fe-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="139fe-656">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="139fe-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="139fe-657">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="139fe-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="139fe-658">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="139fe-658">Dynamically select policies</span></span>

<span data-ttu-id="139fe-659">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="139fe-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="139fe-660">Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="139fe-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="139fe-661">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="139fe-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="139fe-662">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="139fe-663">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="139fe-664">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="139fe-665">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="139fe-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="139fe-666">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="139fe-667">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="139fe-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="139fe-668">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="139fe-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="139fe-669">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="139fe-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="139fe-670">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="139fe-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="139fe-671">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="139fe-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="139fe-672">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="139fe-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="139fe-673">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="139fe-674">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="139fe-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="139fe-675">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="139fe-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="139fe-676">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="139fe-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="139fe-677">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="139fe-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="139fe-678">Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="139fe-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="139fe-679">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="139fe-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="139fe-680">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="139fe-681">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="139fe-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="139fe-682">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="139fe-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="139fe-683">`IHttpClientFactory` pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="139fe-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="139fe-684">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="139fe-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="139fe-685">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="139fe-686">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="139fe-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="139fe-687">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="139fe-688">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="139fe-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="139fe-689">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="139fe-690">Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="139fe-691">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="139fe-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="139fe-692">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="139fe-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="139fe-693">`IHttpClientFactory` śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="139fe-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="139fe-694">`HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="139fe-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="139fe-695">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="139fe-696">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="139fe-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="139fe-697">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="139fe-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="139fe-698">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="139fe-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="139fe-699">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="139fe-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="139fe-700">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="139fe-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="139fe-701">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="139fe-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="139fe-702">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="139fe-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="139fe-703">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="139fe-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="139fe-704">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="139fe-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="139fe-705">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="139fe-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="139fe-706">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="139fe-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="139fe-707">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="139fe-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="139fe-708">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="139fe-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="139fe-709">Cookiewolumin</span><span class="sxs-lookup"><span data-stu-id="139fe-709">Cookies</span></span>

<span data-ttu-id="139fe-710">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="139fe-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="139fe-711">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="139fe-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="139fe-712">W przypadku aplikacji, które wymagają usługi cookie s, należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="139fe-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="139fe-713">Wyłączanie cookie obsługi automatycznej</span><span class="sxs-lookup"><span data-stu-id="139fe-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="139fe-714">Unikanie `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="139fe-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="139fe-715">Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> wyłączania automatycznej cookie obsługi:</span><span class="sxs-lookup"><span data-stu-id="139fe-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="139fe-716">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="139fe-716">Logging</span></span>

<span data-ttu-id="139fe-717">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="139fe-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="139fe-718">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="139fe-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="139fe-719">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="139fe-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="139fe-720">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="139fe-721">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="139fe-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="139fe-722">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="139fe-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="139fe-723">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="139fe-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="139fe-724">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="139fe-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="139fe-725">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="139fe-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="139fe-726">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="139fe-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="139fe-727">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="139fe-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="139fe-728">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="139fe-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="139fe-729">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="139fe-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="139fe-730">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="139fe-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="139fe-731">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="139fe-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="139fe-732">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="139fe-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="139fe-733">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="139fe-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="139fe-734">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="139fe-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="139fe-735"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="139fe-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="139fe-736">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="139fe-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="139fe-737">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="139fe-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="139fe-738">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="139fe-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="139fe-739">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="139fe-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="139fe-740">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="139fe-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="139fe-741">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="139fe-741">In the following example:</span></span>

* <span data-ttu-id="139fe-742"><xref:System.Net.Http.IHttpClientFactory> jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="139fe-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="139fe-743">`MyService` tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="139fe-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="139fe-744">`HttpClient` służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="139fe-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="139fe-745">`Main` tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="139fe-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="139fe-746">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="139fe-746">Header propagation middleware</span></span>

<span data-ttu-id="139fe-747">Propagacja nagłówka to społeczność obsługiwana przez oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="139fe-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="139fe-748">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="139fe-748">To use header propagation:</span></span>

* <span data-ttu-id="139fe-749">Odwołuje się do obsługiwanego przez społeczność portu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)pakietu.</span><span class="sxs-lookup"><span data-stu-id="139fe-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="139fe-750">ASP.NET Core 3,1 i nowsze obsługuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="139fe-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="139fe-751">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :</span><span class="sxs-lookup"><span data-stu-id="139fe-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="139fe-752">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="139fe-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="139fe-753">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="139fe-753">Additional resources</span></span>

* [<span data-ttu-id="139fe-754">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="139fe-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="139fe-755">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="139fe-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="139fe-756">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="139fe-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
