---
title: Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core
author: stevejgordon
description: Dowiedz się więcej o używaniu interfejsu IHttpClientFactory do zarządzania wystąpieniami logicznych HttpClient w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766553"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="af5cf-103">Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af5cf-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af5cf-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="af5cf-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="af5cf-105"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="af5cf-106">`IHttpClientFactory`oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="af5cf-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="af5cf-107">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-108">Na przykład klient o nazwie *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="af5cf-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="af5cf-109">Domyślny klient można zarejestrować na potrzeby ogólnego dostępu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="af5cf-110">Kodyfikacja koncepcji wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="af5cf-111">Udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z delegowania programów obsługi w programie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="af5cf-112">Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` wystąpień podstawowych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="af5cf-113">Automatyczne zarządzanie pozwala uniknąć typowych problemów z usługą DNS (Domain Name System) występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="af5cf-114">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="af5cf-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="af5cf-115">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="af5cf-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="af5cf-116">Przykładowy kod w tym temacie używa <xref:System.Text.Json> do deserializacji zawartości JSON zwróconej w odpowiedziach http.</span><span class="sxs-lookup"><span data-stu-id="af5cf-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="af5cf-117">Aby zapoznać się z przykładami używanymi przez `Json.NET` `ReadAsAsync<T>` program i, użyj selektora wersji, aby wybrać wersję 2. x tego tematu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="af5cf-118">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-118">Consumption patterns</span></span>

<span data-ttu-id="af5cf-119">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="af5cf-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="af5cf-120">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="af5cf-121">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="af5cf-122">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="af5cf-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="af5cf-123">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="af5cf-124">Najlepsze podejście zależy od wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="af5cf-125">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-125">Basic usage</span></span>

<span data-ttu-id="af5cf-126">`IHttpClientFactory`można zarejestrować, wywołując `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="af5cf-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="af5cf-127">`IHttpClientFactory`Można zażądać przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="af5cf-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="af5cf-128">Następujący kod używa `IHttpClientFactory` do tworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="af5cf-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="af5cf-129">Użycie `IHttpClientFactory` podobnej do powyższego przykładu jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="af5cf-130">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="af5cf-131">W miejscach, w których `HttpClient` wystąpienia są tworzone w istniejącej aplikacji, Zastąp te wystąpienia wywołaniami do <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="af5cf-132">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-132">Named clients</span></span>

<span data-ttu-id="af5cf-133">Nazwani klienci są dobrym wyborem w przypadku:</span><span class="sxs-lookup"><span data-stu-id="af5cf-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="af5cf-134">Aplikacja wymaga wielu odrębnych użycia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="af5cf-135">Wiele `HttpClient` s ma inną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="af5cf-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="af5cf-136">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="af5cf-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="af5cf-137">W powyższym kodzie klient ma skonfigurowany program:</span><span class="sxs-lookup"><span data-stu-id="af5cf-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="af5cf-138">Adres podstawowy `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="af5cf-139">Dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="af5cf-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="af5cf-140">Klient</span><span class="sxs-lookup"><span data-stu-id="af5cf-140">CreateClient</span></span>

<span data-ttu-id="af5cf-141">Za każdym razem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="af5cf-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="af5cf-142">Tworzone jest nowe wystąpienie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="af5cf-143">Akcja konfiguracji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="af5cf-143">The configuration action is called.</span></span>

<span data-ttu-id="af5cf-144">Aby utworzyć nazwanego klienta, Przekaż jego nazwę do `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="af5cf-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="af5cf-145">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="af5cf-146">Kod może przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="af5cf-147">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="af5cf-147">Typed clients</span></span>

<span data-ttu-id="af5cf-148">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-148">Typed clients:</span></span>

* <span data-ttu-id="af5cf-149">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="af5cf-150">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="af5cf-151">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="af5cf-152">Na przykład może zostać użyty pojedynczy klient z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="af5cf-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="af5cf-153">Dla pojedynczego punktu końcowego zaplecza.</span><span class="sxs-lookup"><span data-stu-id="af5cf-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="af5cf-154">Do hermetyzacji wszystkich logiki związanych z punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="af5cf-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="af5cf-155">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="af5cf-156">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="af5cf-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="af5cf-157">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-157">In the preceding code:</span></span>

* <span data-ttu-id="af5cf-158">Konfiguracja zostanie przeniesiona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="af5cf-159">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="af5cf-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="af5cf-160">Można tworzyć metody specyficzne dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="af5cf-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="af5cf-161">Na przykład `GetAspNetDocsIssues` Metoda hermetyzuje kod w celu pobrania otwartych problemów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="af5cf-162">Następujący kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> w `Startup.ConfigureServices` celu zarejestrowania klasy klienta typu:</span><span class="sxs-lookup"><span data-stu-id="af5cf-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="af5cf-163">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="af5cf-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="af5cf-164">W poprzednim kodzie program `AddHttpClient` rejestruje `GitHubService` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="af5cf-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="af5cf-165">Ta rejestracja używa metody fabryki do:</span><span class="sxs-lookup"><span data-stu-id="af5cf-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="af5cf-166">Utwórz wystąpienie elementu `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="af5cf-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="af5cf-167">Utwórz wystąpienie `GitHubService` , przekazując w wystąpieniu `HttpClient` do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="af5cf-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="af5cf-168">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="af5cf-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="af5cf-169">Konfigurację dla określonego klienta można określić podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="af5cf-170">`HttpClient`Mogą być hermetyzowane w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="af5cf-171">Zamiast uwidaczniać ją jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="af5cf-172">W poprzednim kodzie, `HttpClient` jest przechowywany w prywatnym polu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="af5cf-173">Dostęp do programu `HttpClient` ma metoda publiczna `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="af5cf-174">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-174">Generated clients</span></span>

<span data-ttu-id="af5cf-175">`IHttpClientFactory`może być używany w połączeniu z bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="af5cf-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="af5cf-176">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="af5cf-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="af5cf-177">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="af5cf-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="af5cf-178">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="af5cf-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="af5cf-179">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="af5cf-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="af5cf-180">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="af5cf-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="af5cf-181">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="af5cf-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="af5cf-182">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="af5cf-182">Outgoing request middleware</span></span>

<span data-ttu-id="af5cf-183">`HttpClient`ma koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="af5cf-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="af5cf-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="af5cf-185">Upraszcza definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="af5cf-186">Obsługuje rejestrację i łańcuch wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="af5cf-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="af5cf-187">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="af5cf-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="af5cf-188">Ten wzorzec:</span><span class="sxs-lookup"><span data-stu-id="af5cf-188">This pattern:</span></span>

  * <span data-ttu-id="af5cf-189">Jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af5cf-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="af5cf-190">Program udostępnia mechanizm zarządzania problemami z obcinaniem między żądaniami HTTP, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="af5cf-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="af5cf-191">pamięć</span><span class="sxs-lookup"><span data-stu-id="af5cf-191">caching</span></span>
    * <span data-ttu-id="af5cf-192">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="af5cf-192">error handling</span></span>
    * <span data-ttu-id="af5cf-193">serializacji</span><span class="sxs-lookup"><span data-stu-id="af5cf-193">serialization</span></span>
    * <span data-ttu-id="af5cf-194">rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="af5cf-194">logging</span></span>

<span data-ttu-id="af5cf-195">Aby utworzyć program obsługi delegowania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-195">To create a delegating handler:</span></span>

* <span data-ttu-id="af5cf-196">Pochodny od <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="af5cf-197">Zastąpienie <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="af5cf-198">Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="af5cf-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="af5cf-199">Poprzedni kod sprawdza, czy `X-API-KEY` nagłówek jest w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="af5cf-200">Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="af5cf-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="af5cf-201">Do konfiguracji dla elementu with można dodać więcej niż jeden program obsługi `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="af5cf-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="af5cf-202">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="af5cf-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="af5cf-203">`IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="af5cf-204">Programy obsługi mogą zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="af5cf-205">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="af5cf-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="af5cf-206">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="af5cf-207">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="af5cf-208">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="af5cf-209">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="af5cf-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="af5cf-210">Przekaż dane do procedury obsługi przy użyciu [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="af5cf-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="af5cf-211">Użyj, <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="af5cf-212">Utwórz niestandardowy <xref:System.Threading.AsyncLocal`1> obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="af5cf-213">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-213">Use Polly-based handlers</span></span>

<span data-ttu-id="af5cf-214">`IHttpClientFactory`integruje się z biblioteką innych firm [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="af5cf-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="af5cf-215">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="af5cf-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="af5cf-216">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="af5cf-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="af5cf-217">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-218">Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na Polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="af5cf-219">Polly wymaga pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="af5cf-220">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="af5cf-220">Handle transient faults</span></span>

<span data-ttu-id="af5cf-221">Błędy są zwykle wykonywane, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="af5cf-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="af5cf-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="af5cf-223">Skonfigurowane zasady z `AddTransientHttpErrorPolicy` obsługą następujących odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="af5cf-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="af5cf-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="af5cf-224">HTTP 5xx</span></span>
* <span data-ttu-id="af5cf-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="af5cf-225">HTTP 408</span></span>

<span data-ttu-id="af5cf-226">`AddTransientHttpErrorPolicy`zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="af5cf-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="af5cf-227">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="af5cf-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="af5cf-228">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="af5cf-229">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="af5cf-229">Dynamically select policies</span></span>

<span data-ttu-id="af5cf-230">Metody rozszerzające umożliwiają dodawanie programów obsługi opartych na Polly, na przykład <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="af5cf-231">Następujące `AddPolicyHandler` Przeciążenie przeprowadziło inspekcję żądania, aby zdecydować, które zasady zastosować:</span><span class="sxs-lookup"><span data-stu-id="af5cf-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="af5cf-232">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="af5cf-233">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="af5cf-234">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="af5cf-235">Jest to typowy sposób zagnieżdżania zasad Pollyymi:</span><span class="sxs-lookup"><span data-stu-id="af5cf-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="af5cf-236">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-236">In the preceding example:</span></span>

* <span data-ttu-id="af5cf-237">Dodawane są dwa procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-237">Two handlers are added.</span></span>
* <span data-ttu-id="af5cf-238">Pierwsza procedura obsługi używa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> do dodawania zasad ponawiania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="af5cf-239">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="af5cf-240">Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="af5cf-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="af5cf-241">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="af5cf-242">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="af5cf-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="af5cf-243">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="af5cf-244">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="af5cf-245">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="af5cf-246">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-246">In the following code:</span></span>

* <span data-ttu-id="af5cf-247">Dodawane są zasady "regularne" i "długie".</span><span class="sxs-lookup"><span data-stu-id="af5cf-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="af5cf-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>dodaje zasady "regularne" i "długie" z rejestru.</span><span class="sxs-lookup"><span data-stu-id="af5cf-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="af5cf-249">Aby uzyskać więcej informacji na temat `IHttpClientFactory` integracji z integracją i Polly, zobacz [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="af5cf-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="af5cf-250">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="af5cf-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="af5cf-251">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="af5cf-252"><xref:System.Net.Http.HttpMessageHandler>Tworzony jest na nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="af5cf-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="af5cf-253">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="af5cf-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="af5cf-254">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="af5cf-255">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="af5cf-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="af5cf-256">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="af5cf-257">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="af5cf-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="af5cf-258">Niektóre programy obsługi powodują również, że połączenia są otwierane w nieskończoność, co może uniemożliwić oddziałanie programu obsługi w systemie DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="af5cf-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="af5cf-259">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="af5cf-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="af5cf-260">Wartość domyślną można przesłonić na podstawie nazwy klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="af5cf-261">`HttpClient`wystąpienia mogą być zwykle traktowane jako obiekty .NET, które **nie** wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="af5cf-262">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="af5cf-263">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="af5cf-264">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="af5cf-265">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="af5cf-266">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="af5cf-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="af5cf-267">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="af5cf-268">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="af5cf-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="af5cf-269">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="af5cf-270">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="af5cf-271">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="af5cf-272">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="af5cf-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="af5cf-273">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="af5cf-274">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="af5cf-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="af5cf-275">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-276">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="af5cf-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="af5cf-277">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="af5cf-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="af5cf-278">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="af5cf-278">Cookies</span></span>

<span data-ttu-id="af5cf-279">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="af5cf-280">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="af5cf-281">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="af5cf-282">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="af5cf-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="af5cf-283">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="af5cf-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="af5cf-284">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="af5cf-285">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="af5cf-285">Logging</span></span>

<span data-ttu-id="af5cf-286">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="af5cf-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="af5cf-287">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="af5cf-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="af5cf-288">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="af5cf-289">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="af5cf-290">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="af5cf-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="af5cf-291">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="af5cf-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="af5cf-292">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="af5cf-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="af5cf-293">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="af5cf-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="af5cf-294">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="af5cf-295">W przykładzie *MyNamedClient* te komunikaty są rejestrowane z kategorią dziennika "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="af5cf-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="af5cf-296">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="af5cf-297">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="af5cf-298">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="af5cf-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="af5cf-299">Może to obejmować zmiany w nagłówkach żądania lub w kodzie stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="af5cf-300">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="af5cf-301">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="af5cf-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="af5cf-302">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="af5cf-303">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="af5cf-304"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="af5cf-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="af5cf-305">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="af5cf-306">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="af5cf-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="af5cf-307">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="af5cf-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="af5cf-308">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="af5cf-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="af5cf-309">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="af5cf-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="af5cf-310">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-310">In the following example:</span></span>

* <span data-ttu-id="af5cf-311"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="af5cf-312">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="af5cf-313">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="af5cf-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="af5cf-314">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="af5cf-315">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="af5cf-315">Header propagation middleware</span></span>

<span data-ttu-id="af5cf-316">Propagacja nagłówka to ASP.NET Core oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="af5cf-317">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="af5cf-317">To use header propagation:</span></span>

* <span data-ttu-id="af5cf-318">Odwołuje się do pakietu [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="af5cf-319">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :</span><span class="sxs-lookup"><span data-stu-id="af5cf-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="af5cf-320">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="af5cf-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="af5cf-321">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="af5cf-321">Additional resources</span></span>

* [<span data-ttu-id="af5cf-322">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="af5cf-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="af5cf-323">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="af5cf-324">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="af5cf-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="af5cf-325">Jak serializować i deserializować kod JSON w programie .NET</span><span class="sxs-lookup"><span data-stu-id="af5cf-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="af5cf-326">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="af5cf-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="af5cf-327"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="af5cf-328">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="af5cf-328">It offers the following benefits:</span></span>

* <span data-ttu-id="af5cf-329">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-330">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="af5cf-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="af5cf-331">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="af5cf-332">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="af5cf-333">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="af5cf-334">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="af5cf-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="af5cf-335">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af5cf-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="af5cf-336">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-336">Consumption patterns</span></span>

<span data-ttu-id="af5cf-337">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="af5cf-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="af5cf-338">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="af5cf-339">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="af5cf-340">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="af5cf-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="af5cf-341">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="af5cf-342">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="af5cf-343">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="af5cf-344">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-344">Basic usage</span></span>

<span data-ttu-id="af5cf-345">`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="af5cf-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="af5cf-346">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="af5cf-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="af5cf-347">`IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="af5cf-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="af5cf-348">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="af5cf-349">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="af5cf-350">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="af5cf-351">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-351">Named clients</span></span>

<span data-ttu-id="af5cf-352">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="af5cf-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="af5cf-353">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="af5cf-354">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="af5cf-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="af5cf-355">Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="af5cf-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="af5cf-356">Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="af5cf-357">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="af5cf-358">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="af5cf-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="af5cf-359">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="af5cf-360">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="af5cf-361">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="af5cf-361">Typed clients</span></span>

<span data-ttu-id="af5cf-362">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-362">Typed clients:</span></span>

* <span data-ttu-id="af5cf-363">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="af5cf-364">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="af5cf-365">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="af5cf-366">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="af5cf-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="af5cf-367">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="af5cf-368">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="af5cf-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="af5cf-369">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="af5cf-370">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="af5cf-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="af5cf-371">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="af5cf-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="af5cf-372">`GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="af5cf-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="af5cf-373">Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="af5cf-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="af5cf-374">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="af5cf-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="af5cf-375">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="af5cf-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="af5cf-376">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="af5cf-377">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="af5cf-378">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="af5cf-379">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="af5cf-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="af5cf-380">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.</span><span class="sxs-lookup"><span data-stu-id="af5cf-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="af5cf-381">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-381">Generated clients</span></span>

<span data-ttu-id="af5cf-382">`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="af5cf-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="af5cf-383">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="af5cf-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="af5cf-384">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="af5cf-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="af5cf-385">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="af5cf-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="af5cf-386">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="af5cf-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="af5cf-387">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="af5cf-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="af5cf-388">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="af5cf-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="af5cf-389">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="af5cf-389">Outgoing request middleware</span></span>

<span data-ttu-id="af5cf-390">`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="af5cf-391">`IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="af5cf-392">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="af5cf-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="af5cf-393">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="af5cf-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="af5cf-394">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af5cf-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="af5cf-395">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="af5cf-396">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="af5cf-397">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="af5cf-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="af5cf-398">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="af5cf-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="af5cf-399">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="af5cf-400">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="af5cf-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="af5cf-401">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="af5cf-402">To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="af5cf-403">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="af5cf-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="af5cf-404">`IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="af5cf-405">Programy obsługi są bezpłatne, aby zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="af5cf-406">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="af5cf-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="af5cf-407">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="af5cf-408">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="af5cf-409">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="af5cf-410">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="af5cf-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="af5cf-411">Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="af5cf-412">Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="af5cf-413">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="af5cf-414">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-414">Use Polly-based handlers</span></span>

<span data-ttu-id="af5cf-415">`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="af5cf-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="af5cf-416">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="af5cf-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="af5cf-417">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="af5cf-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="af5cf-418">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-419">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="af5cf-419">The Polly extensions:</span></span>

* <span data-ttu-id="af5cf-420">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="af5cf-421">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="af5cf-422">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="af5cf-423">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="af5cf-423">Handle transient faults</span></span>

<span data-ttu-id="af5cf-424">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="af5cf-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="af5cf-425">Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="af5cf-426">Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.</span><span class="sxs-lookup"><span data-stu-id="af5cf-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="af5cf-427">`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af5cf-428">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="af5cf-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="af5cf-429">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="af5cf-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="af5cf-430">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="af5cf-431">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="af5cf-431">Dynamically select policies</span></span>

<span data-ttu-id="af5cf-432">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="af5cf-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="af5cf-433">Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="af5cf-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="af5cf-434">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="af5cf-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="af5cf-435">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="af5cf-436">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="af5cf-437">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="af5cf-438">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="af5cf-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="af5cf-439">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="af5cf-440">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="af5cf-441">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="af5cf-442">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="af5cf-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="af5cf-443">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="af5cf-444">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="af5cf-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="af5cf-445">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="af5cf-446">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="af5cf-447">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="af5cf-448">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="af5cf-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="af5cf-449">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="af5cf-450">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="af5cf-451">Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="af5cf-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="af5cf-452">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="af5cf-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="af5cf-453">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="af5cf-454">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="af5cf-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="af5cf-455">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="af5cf-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="af5cf-456">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="af5cf-457">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="af5cf-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="af5cf-458">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="af5cf-459">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="af5cf-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="af5cf-460">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="af5cf-461">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="af5cf-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="af5cf-462">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="af5cf-463">Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="af5cf-464">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="af5cf-465">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="af5cf-466">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-467">`HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="af5cf-468">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="af5cf-469">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="af5cf-470">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="af5cf-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="af5cf-471">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="af5cf-472">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="af5cf-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="af5cf-473">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="af5cf-474">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="af5cf-475">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="af5cf-476">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="af5cf-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="af5cf-477">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="af5cf-478">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="af5cf-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="af5cf-479">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-480">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="af5cf-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="af5cf-481">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="af5cf-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="af5cf-482">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="af5cf-482">Cookies</span></span>

<span data-ttu-id="af5cf-483">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="af5cf-484">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="af5cf-485">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="af5cf-486">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="af5cf-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="af5cf-487">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="af5cf-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="af5cf-488">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="af5cf-489">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="af5cf-489">Logging</span></span>

<span data-ttu-id="af5cf-490">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="af5cf-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="af5cf-491">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="af5cf-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="af5cf-492">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="af5cf-493">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="af5cf-494">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="af5cf-495">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="af5cf-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="af5cf-496">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="af5cf-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="af5cf-497">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="af5cf-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="af5cf-498">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="af5cf-499">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="af5cf-500">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="af5cf-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="af5cf-501">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="af5cf-502">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="af5cf-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="af5cf-503">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="af5cf-504">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="af5cf-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="af5cf-505">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="af5cf-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="af5cf-506">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="af5cf-507">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="af5cf-508"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="af5cf-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="af5cf-509">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="af5cf-510">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="af5cf-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="af5cf-511">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="af5cf-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="af5cf-512">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="af5cf-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="af5cf-513">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="af5cf-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="af5cf-514">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-514">In the following example:</span></span>

* <span data-ttu-id="af5cf-515"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="af5cf-516">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="af5cf-517">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="af5cf-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="af5cf-518">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="af5cf-519">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="af5cf-519">Additional resources</span></span>

* [<span data-ttu-id="af5cf-520">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="af5cf-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="af5cf-521">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="af5cf-522">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="af5cf-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="af5cf-523">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="af5cf-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="af5cf-524"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="af5cf-525">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="af5cf-525">It offers the following benefits:</span></span>

* <span data-ttu-id="af5cf-526">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-527">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="af5cf-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="af5cf-528">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="af5cf-529">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="af5cf-530">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="af5cf-531">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="af5cf-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="af5cf-532">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af5cf-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af5cf-533">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="af5cf-533">Prerequisites</span></span>

<span data-ttu-id="af5cf-534">Projekty docelowe .NET Framework wymagają instalacji pakietu NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="af5cf-535">Projekty przeznaczone dla platformy .NET Core i odwołujące się do [pakietu Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) zawierają już `Microsoft.Extensions.Http` pakiet.</span><span class="sxs-lookup"><span data-stu-id="af5cf-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="af5cf-536">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-536">Consumption patterns</span></span>

<span data-ttu-id="af5cf-537">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="af5cf-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="af5cf-538">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="af5cf-539">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="af5cf-540">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="af5cf-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="af5cf-541">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="af5cf-542">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="af5cf-543">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="af5cf-544">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="af5cf-544">Basic usage</span></span>

<span data-ttu-id="af5cf-545">`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="af5cf-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="af5cf-546">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="af5cf-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="af5cf-547">`IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="af5cf-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="af5cf-548">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="af5cf-549">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="af5cf-550">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="af5cf-551">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-551">Named clients</span></span>

<span data-ttu-id="af5cf-552">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="af5cf-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="af5cf-553">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="af5cf-554">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="af5cf-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="af5cf-555">Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="af5cf-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="af5cf-556">Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="af5cf-557">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="af5cf-558">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="af5cf-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="af5cf-559">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="af5cf-560">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="af5cf-561">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="af5cf-561">Typed clients</span></span>

<span data-ttu-id="af5cf-562">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-562">Typed clients:</span></span>

* <span data-ttu-id="af5cf-563">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="af5cf-564">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="af5cf-565">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="af5cf-566">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="af5cf-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="af5cf-567">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="af5cf-568">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="af5cf-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="af5cf-569">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="af5cf-570">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="af5cf-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="af5cf-571">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="af5cf-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="af5cf-572">`GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="af5cf-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="af5cf-573">Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="af5cf-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="af5cf-574">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="af5cf-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="af5cf-575">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="af5cf-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="af5cf-576">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="af5cf-577">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="af5cf-578">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="af5cf-579">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="af5cf-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="af5cf-580">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.</span><span class="sxs-lookup"><span data-stu-id="af5cf-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="af5cf-581">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="af5cf-581">Generated clients</span></span>

<span data-ttu-id="af5cf-582">`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="af5cf-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="af5cf-583">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="af5cf-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="af5cf-584">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="af5cf-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="af5cf-585">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="af5cf-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="af5cf-586">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="af5cf-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="af5cf-587">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="af5cf-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="af5cf-588">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="af5cf-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="af5cf-589">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="af5cf-589">Outgoing request middleware</span></span>

<span data-ttu-id="af5cf-590">`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="af5cf-591">`IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="af5cf-592">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="af5cf-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="af5cf-593">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="af5cf-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="af5cf-594">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af5cf-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="af5cf-595">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="af5cf-596">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="af5cf-597">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="af5cf-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="af5cf-598">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="af5cf-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="af5cf-599">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="af5cf-600">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="af5cf-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="af5cf-601">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="af5cf-602">To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="af5cf-603">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="af5cf-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="af5cf-604">Procedura obsługi **musi** być zarejestrowana w programie di jako przejściowa usługa, nigdy nie jest objęta zakresem.</span><span class="sxs-lookup"><span data-stu-id="af5cf-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="af5cf-605">Jeśli program obsługi jest zarejestrowany jako usługa objęta zakresem, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:</span><span class="sxs-lookup"><span data-stu-id="af5cf-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="af5cf-606">Usługi programu obsługi mogły zostać zlikwidowane, zanim program obsługi znajdzie się poza zakresem.</span><span class="sxs-lookup"><span data-stu-id="af5cf-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="af5cf-607">Usunięte usługi obsługi powodują niepowodzenie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="af5cf-608">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując w typie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="af5cf-609">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="af5cf-610">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="af5cf-611">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="af5cf-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="af5cf-612">Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="af5cf-613">Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="af5cf-614">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="af5cf-615">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-615">Use Polly-based handlers</span></span>

<span data-ttu-id="af5cf-616">`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="af5cf-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="af5cf-617">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="af5cf-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="af5cf-618">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="af5cf-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="af5cf-619">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-620">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="af5cf-620">The Polly extensions:</span></span>

* <span data-ttu-id="af5cf-621">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="af5cf-622">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="af5cf-623">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="af5cf-624">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="af5cf-624">Handle transient faults</span></span>

<span data-ttu-id="af5cf-625">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="af5cf-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="af5cf-626">Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="af5cf-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="af5cf-627">Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.</span><span class="sxs-lookup"><span data-stu-id="af5cf-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="af5cf-628">`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af5cf-629">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="af5cf-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="af5cf-630">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="af5cf-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="af5cf-631">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="af5cf-632">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="af5cf-632">Dynamically select policies</span></span>

<span data-ttu-id="af5cf-633">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="af5cf-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="af5cf-634">Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="af5cf-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="af5cf-635">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="af5cf-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="af5cf-636">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="af5cf-637">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="af5cf-638">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="af5cf-639">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="af5cf-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="af5cf-640">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="af5cf-641">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="af5cf-642">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="af5cf-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="af5cf-643">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="af5cf-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="af5cf-644">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="af5cf-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="af5cf-645">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="af5cf-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="af5cf-646">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="af5cf-647">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="af5cf-648">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="af5cf-649">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="af5cf-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="af5cf-650">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="af5cf-651">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="af5cf-652">Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="af5cf-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="af5cf-653">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="af5cf-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="af5cf-654">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="af5cf-655">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="af5cf-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="af5cf-656">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="af5cf-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="af5cf-657">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="af5cf-658">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="af5cf-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="af5cf-659">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="af5cf-660">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="af5cf-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="af5cf-661">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="af5cf-662">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="af5cf-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="af5cf-663">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="af5cf-664">Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="af5cf-665">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="af5cf-666">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="af5cf-667">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-668">`HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="af5cf-669">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="af5cf-670">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="af5cf-671">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="af5cf-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="af5cf-672">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="af5cf-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="af5cf-673">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="af5cf-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="af5cf-674">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="af5cf-675">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="af5cf-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="af5cf-676">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af5cf-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="af5cf-677">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="af5cf-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="af5cf-678">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="af5cf-679">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="af5cf-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="af5cf-680">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="af5cf-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="af5cf-681">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="af5cf-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="af5cf-682">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="af5cf-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="af5cf-683">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="af5cf-683">Cookies</span></span>

<span data-ttu-id="af5cf-684">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="af5cf-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="af5cf-685">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="af5cf-686">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="af5cf-687">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="af5cf-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="af5cf-688">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="af5cf-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="af5cf-689">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="af5cf-690">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="af5cf-690">Logging</span></span>

<span data-ttu-id="af5cf-691">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="af5cf-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="af5cf-692">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="af5cf-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="af5cf-693">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="af5cf-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="af5cf-694">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="af5cf-695">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="af5cf-696">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="af5cf-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="af5cf-697">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="af5cf-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="af5cf-698">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="af5cf-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="af5cf-699">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="af5cf-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="af5cf-700">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="af5cf-701">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="af5cf-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="af5cf-702">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="af5cf-703">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="af5cf-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="af5cf-704">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="af5cf-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="af5cf-705">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="af5cf-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="af5cf-706">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="af5cf-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="af5cf-707">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="af5cf-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="af5cf-708">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="af5cf-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="af5cf-709"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="af5cf-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="af5cf-710">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="af5cf-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="af5cf-711">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="af5cf-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="af5cf-712">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="af5cf-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="af5cf-713">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="af5cf-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="af5cf-714">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="af5cf-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="af5cf-715">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="af5cf-715">In the following example:</span></span>

* <span data-ttu-id="af5cf-716"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="af5cf-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="af5cf-717">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="af5cf-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="af5cf-718">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="af5cf-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="af5cf-719">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="af5cf-720">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="af5cf-720">Header propagation middleware</span></span>

<span data-ttu-id="af5cf-721">Propagacja nagłówka to społeczność obsługiwana przez oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="af5cf-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="af5cf-722">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="af5cf-722">To use header propagation:</span></span>

* <span data-ttu-id="af5cf-723">Odwołuje się do obsługiwanego przez społeczność portu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)pakietu.</span><span class="sxs-lookup"><span data-stu-id="af5cf-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="af5cf-724">ASP.NET Core 3,1 i nowsze obsługuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="af5cf-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="af5cf-725">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :</span><span class="sxs-lookup"><span data-stu-id="af5cf-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="af5cf-726">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="af5cf-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="af5cf-727">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="af5cf-727">Additional resources</span></span>

* [<span data-ttu-id="af5cf-728">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="af5cf-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="af5cf-729">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="af5cf-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="af5cf-730">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="af5cf-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
