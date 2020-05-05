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
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="3abda-103">Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3abda-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3abda-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3abda-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="3abda-105"><xref:System.Net.Http.IHttpClientFactory> Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="3abda-106">`IHttpClientFactory`oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="3abda-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="3abda-107">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="3abda-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="3abda-108">Na przykład klient o nazwie *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="3abda-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="3abda-109">Domyślny klient można zarejestrować na potrzeby ogólnego dostępu.</span><span class="sxs-lookup"><span data-stu-id="3abda-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="3abda-110">Kodyfikacja koncepcji wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi `HttpClient`w programie.</span><span class="sxs-lookup"><span data-stu-id="3abda-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="3abda-111">Udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z delegowania programów `HttpClient`obsługi w programie.</span><span class="sxs-lookup"><span data-stu-id="3abda-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="3abda-112">Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` wystąpień podstawowych.</span><span class="sxs-lookup"><span data-stu-id="3abda-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="3abda-113">Automatyczne zarządzanie pozwala uniknąć typowych problemów z usługą DNS (Domain Name System) występujących podczas `HttpClient` ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="3abda-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="3abda-114">Dodaje konfigurowalne środowisko rejestrowania (za `ILogger`pośrednictwem programu) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="3abda-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="3abda-115">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3abda-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3abda-116">Przykładowy kod w tym temacie używa <xref:System.Text.Json> do deserializacji zawartości JSON zwróconej w odpowiedziach http.</span><span class="sxs-lookup"><span data-stu-id="3abda-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="3abda-117">Aby zapoznać się z `Json.NET` przykładami używanymi przez program i `ReadAsAsync<T>`, użyj selektora wersji, aby wybrać wersję 2. x tego tematu.</span><span class="sxs-lookup"><span data-stu-id="3abda-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="3abda-118">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="3abda-118">Consumption patterns</span></span>

<span data-ttu-id="3abda-119">W aplikacji można używać `IHttpClientFactory` kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="3abda-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="3abda-120">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3abda-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="3abda-121">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="3abda-122">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="3abda-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="3abda-123">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="3abda-124">Najlepsze podejście zależy od wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="3abda-125">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3abda-125">Basic usage</span></span>

<span data-ttu-id="3abda-126">`IHttpClientFactory`można zarejestrować, wywołując `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="3abda-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="3abda-127">`IHttpClientFactory` Można zażądać przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3abda-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3abda-128">Następujący kod używa `IHttpClientFactory` do tworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="3abda-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="3abda-129">Użycie `IHttpClientFactory` podobnej do powyższego przykładu jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="3abda-130">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="3abda-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="3abda-131">W miejscach, `HttpClient` w których wystąpienia są tworzone w istniejącej aplikacji, Zastąp te wystąpienia wywołaniami <xref:System.Net.Http.IHttpClientFactory.CreateClient*>do.</span><span class="sxs-lookup"><span data-stu-id="3abda-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="3abda-132">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-132">Named clients</span></span>

<span data-ttu-id="3abda-133">Nazwani klienci są dobrym wyborem w przypadku:</span><span class="sxs-lookup"><span data-stu-id="3abda-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="3abda-134">Aplikacja wymaga wielu odrębnych użycia `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="3abda-135">Wiele `HttpClient`s ma inną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="3abda-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="3abda-136">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3abda-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="3abda-137">W powyższym kodzie klient ma skonfigurowany program:</span><span class="sxs-lookup"><span data-stu-id="3abda-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="3abda-138">Adres `https://api.github.com/`podstawowy.</span><span class="sxs-lookup"><span data-stu-id="3abda-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="3abda-139">Dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="3abda-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="3abda-140">Klient</span><span class="sxs-lookup"><span data-stu-id="3abda-140">CreateClient</span></span>

<span data-ttu-id="3abda-141">Za każdym <xref:System.Net.Http.IHttpClientFactory.CreateClient*> razem jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="3abda-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="3abda-142">Tworzone `HttpClient` jest nowe wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="3abda-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="3abda-143">Akcja konfiguracji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="3abda-143">The configuration action is called.</span></span>

<span data-ttu-id="3abda-144">Aby utworzyć nazwanego klienta, Przekaż jego nazwę do `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="3abda-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="3abda-145">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="3abda-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="3abda-146">Kod może przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="3abda-147">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="3abda-147">Typed clients</span></span>

<span data-ttu-id="3abda-148">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="3abda-148">Typed clients:</span></span>

* <span data-ttu-id="3abda-149">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="3abda-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="3abda-150">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="3abda-151">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="3abda-152">Na przykład może zostać użyty pojedynczy klient z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="3abda-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="3abda-153">Dla pojedynczego punktu końcowego zaplecza.</span><span class="sxs-lookup"><span data-stu-id="3abda-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="3abda-154">Do hermetyzacji wszystkich logiki związanych z punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="3abda-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="3abda-155">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="3abda-156">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="3abda-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3abda-157">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="3abda-157">In the preceding code:</span></span>

* <span data-ttu-id="3abda-158">Konfiguracja zostanie przeniesiona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="3abda-159">`HttpClient` Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="3abda-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="3abda-160">Można tworzyć metody specyficzne dla interfejsu API, które `HttpClient` uwidaczniają funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="3abda-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="3abda-161">Na przykład `GetAspNetDocsIssues` Metoda hermetyzuje kod w celu pobrania otwartych problemów.</span><span class="sxs-lookup"><span data-stu-id="3abda-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="3abda-162">Następujący kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> w `Startup.ConfigureServices` celu zarejestrowania klasy klienta typu:</span><span class="sxs-lookup"><span data-stu-id="3abda-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="3abda-163">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="3abda-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="3abda-164">W poprzednim kodzie program `AddHttpClient` rejestruje `GitHubService` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="3abda-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="3abda-165">Ta rejestracja używa metody fabryki do:</span><span class="sxs-lookup"><span data-stu-id="3abda-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="3abda-166">Utwórz wystąpienie elementu `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="3abda-167">Utwórz wystąpienie `GitHubService`, przekazując w wystąpieniu `HttpClient` do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="3abda-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="3abda-168">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="3abda-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="3abda-169">Konfigurację dla określonego klienta można określić podczas rejestracji w `Startup.ConfigureServices`, a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="3abda-170">`HttpClient` Mogą być hermetyzowane w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="3abda-171">Zamiast uwidaczniać ją jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="3abda-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="3abda-172">W poprzednim kodzie, `HttpClient` jest przechowywany w prywatnym polu.</span><span class="sxs-lookup"><span data-stu-id="3abda-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="3abda-173">Dostęp do programu `HttpClient` ma metoda publiczna `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="3abda-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="3abda-174">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-174">Generated clients</span></span>

<span data-ttu-id="3abda-175">`IHttpClientFactory`może być używany w połączeniu z bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="3abda-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="3abda-176">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="3abda-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="3abda-177">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="3abda-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="3abda-178">Implementacja interfejsu jest generowana dynamicznie przez `RestService`, przy użyciu polecenia `HttpClient` , aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="3abda-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="3abda-179">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="3abda-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="3abda-180">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="3abda-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="3abda-181">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="3abda-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="3abda-182">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="3abda-182">Outgoing request middleware</span></span>

<span data-ttu-id="3abda-183">`HttpClient`ma koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="3abda-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="3abda-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="3abda-185">Upraszcza definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="3abda-186">Obsługuje rejestrację i łańcuch wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="3abda-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="3abda-187">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="3abda-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="3abda-188">Ten wzorzec:</span><span class="sxs-lookup"><span data-stu-id="3abda-188">This pattern:</span></span>

  * <span data-ttu-id="3abda-189">Jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3abda-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="3abda-190">Program udostępnia mechanizm zarządzania problemami z obcinaniem między żądaniami HTTP, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="3abda-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="3abda-191">pamięć</span><span class="sxs-lookup"><span data-stu-id="3abda-191">caching</span></span>
    * <span data-ttu-id="3abda-192">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="3abda-192">error handling</span></span>
    * <span data-ttu-id="3abda-193">serializacji</span><span class="sxs-lookup"><span data-stu-id="3abda-193">serialization</span></span>
    * <span data-ttu-id="3abda-194">rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="3abda-194">logging</span></span>

<span data-ttu-id="3abda-195">Aby utworzyć program obsługi delegowania:</span><span class="sxs-lookup"><span data-stu-id="3abda-195">To create a delegating handler:</span></span>

* <span data-ttu-id="3abda-196">Pochodny <xref:System.Net.Http.DelegatingHandler>od.</span><span class="sxs-lookup"><span data-stu-id="3abda-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="3abda-197">Zastąpienie <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3abda-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="3abda-198">Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="3abda-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="3abda-199">Poprzedni kod sprawdza, czy `X-API-KEY` nagłówek jest w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="3abda-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="3abda-200">Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="3abda-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="3abda-201">Do konfiguracji dla elementu `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>można dodać więcej niż jeden program obsługi:</span><span class="sxs-lookup"><span data-stu-id="3abda-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="3abda-202">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="3abda-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="3abda-203">`IHttpClientFactory` Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="3abda-204">Programy obsługi mogą zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="3abda-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="3abda-205">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="3abda-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="3abda-206">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="3abda-207">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="3abda-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="3abda-208">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="3abda-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="3abda-209">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="3abda-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="3abda-210">Przekaż dane do procedury obsługi przy użyciu [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="3abda-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="3abda-211">Użyj <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="3abda-212">Utwórz niestandardowy <xref:System.Threading.AsyncLocal`1> obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="3abda-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="3abda-213">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-213">Use Polly-based handlers</span></span>

<span data-ttu-id="3abda-214">`IHttpClientFactory`integruje się z biblioteką innych firm [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="3abda-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="3abda-215">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="3abda-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="3abda-216">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="3abda-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="3abda-217">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="3abda-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="3abda-218">Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na Polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="3abda-219">Polly wymaga pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="3abda-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="3abda-220">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="3abda-220">Handle transient faults</span></span>

<span data-ttu-id="3abda-221">Błędy są zwykle wykonywane, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="3abda-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="3abda-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="3abda-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="3abda-223">Skonfigurowane zasady z `AddTransientHttpErrorPolicy` obsługą następujących odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="3abda-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="3abda-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="3abda-224">HTTP 5xx</span></span>
* <span data-ttu-id="3abda-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="3abda-225">HTTP 408</span></span>

<span data-ttu-id="3abda-226">`AddTransientHttpErrorPolicy`zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="3abda-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="3abda-227">W poprzednim kodzie zdefiniowane są `WaitAndRetryAsync` zasady.</span><span class="sxs-lookup"><span data-stu-id="3abda-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="3abda-228">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="3abda-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="3abda-229">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="3abda-229">Dynamically select policies</span></span>

<span data-ttu-id="3abda-230">Metody rozszerzające umożliwiają dodawanie programów obsługi opartych na Polly, na przykład <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="3abda-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="3abda-231">Następujące `AddPolicyHandler` Przeciążenie przeprowadziło inspekcję żądania, aby zdecydować, które zasady zastosować:</span><span class="sxs-lookup"><span data-stu-id="3abda-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="3abda-232">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="3abda-233">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="3abda-234">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="3abda-235">Jest to typowy sposób zagnieżdżania zasad Pollyymi:</span><span class="sxs-lookup"><span data-stu-id="3abda-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="3abda-236">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3abda-236">In the preceding example:</span></span>

* <span data-ttu-id="3abda-237">Dodawane są dwa procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-237">Two handlers are added.</span></span>
* <span data-ttu-id="3abda-238">Pierwsza procedura obsługi używa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> do dodawania zasad ponawiania.</span><span class="sxs-lookup"><span data-stu-id="3abda-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="3abda-239">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="3abda-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="3abda-240">Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="3abda-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="3abda-241">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="3abda-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="3abda-242">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="3abda-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="3abda-243">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="3abda-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="3abda-244">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="3abda-245">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za `PolicyRegistry`pomocą.</span><span class="sxs-lookup"><span data-stu-id="3abda-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="3abda-246">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3abda-246">In the following code:</span></span>

* <span data-ttu-id="3abda-247">Dodawane są zasady "regularne" i "długie".</span><span class="sxs-lookup"><span data-stu-id="3abda-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="3abda-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>dodaje zasady "regularne" i "długie" z rejestru.</span><span class="sxs-lookup"><span data-stu-id="3abda-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="3abda-249">Aby uzyskać więcej informacji `IHttpClientFactory` na temat integracji z integracją i Polly, zobacz [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="3abda-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="3abda-250">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="3abda-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="3abda-251">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , `IHttpClientFactory`gdy jest wywoływana w.</span><span class="sxs-lookup"><span data-stu-id="3abda-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="3abda-252">Tworzony <xref:System.Net.Http.HttpMessageHandler> jest na nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="3abda-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="3abda-253">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="3abda-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="3abda-254">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="3abda-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="3abda-255">`HttpMessageHandler` Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="3abda-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="3abda-256">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="3abda-257">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="3abda-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="3abda-258">Niektóre programy obsługi powodują również, że połączenia są otwierane w nieskończoność, co może uniemożliwić oddziałanie programu obsługi w systemie DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="3abda-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="3abda-259">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="3abda-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="3abda-260">Wartość domyślną można przesłonić na podstawie nazwy klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="3abda-261">`HttpClient`wystąpienia mogą być zwykle traktowane jako obiekty .NET, które **nie** wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="3abda-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="3abda-262">Likwidacja anuluje żądania wychodzące i `HttpClient` gwarantuje, że danego wystąpienia nie <xref:System.IDisposable.Dispose*>można użyć po wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="3abda-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="3abda-263">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="3abda-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="3abda-264">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3abda-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="3abda-265">Ten wzorzec będzie niepotrzebny po `IHttpClientFactory`migracji do programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="3abda-266">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3abda-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="3abda-267">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="3abda-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="3abda-268">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="3abda-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="3abda-269">Nieodświeżone problemy z `HttpMessageHandler` usługą DNS przez cykliczne wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="3abda-270">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu <xref:System.Net.Http.SocketsHttpHandler> wystąpienia długotrwałego.</span><span class="sxs-lookup"><span data-stu-id="3abda-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="3abda-271">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="3abda-272">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="3abda-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="3abda-273">Utwórz `HttpClient` wystąpienia przy `new HttpClient(handler, disposeHandler: false)` użyciu programu zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="3abda-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="3abda-274">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami `IHttpClientFactory` , które rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="3abda-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="3abda-275">`SocketsHttpHandler` Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="3abda-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="3abda-276">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="3abda-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="3abda-277">`SocketsHttpHandler` Cykle połączeń zgodnie `PooledConnectionLifetime` z, aby uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="3abda-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="3abda-278">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="3abda-278">Cookies</span></span>

<span data-ttu-id="3abda-279">`HttpMessageHandler` Wystąpienia w `CookieContainer` puli powoduje, że obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="3abda-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="3abda-280">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="3abda-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="3abda-281">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="3abda-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="3abda-282">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="3abda-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="3abda-283">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3abda-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="3abda-284">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="3abda-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="3abda-285">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="3abda-285">Logging</span></span>

<span data-ttu-id="3abda-286">Klienci utworzeni `IHttpClientFactory` za pomocą rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="3abda-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="3abda-287">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="3abda-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="3abda-288">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="3abda-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="3abda-289">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="3abda-290">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="3abda-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="3abda-291">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="3abda-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="3abda-292">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="3abda-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="3abda-293">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="3abda-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="3abda-294">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="3abda-295">W przykładzie *MyNamedClient* te komunikaty są rejestrowane z kategorią dziennika "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="3abda-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="3abda-296">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="3abda-297">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="3abda-298">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="3abda-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="3abda-299">Może to obejmować zmiany w nagłówkach żądania lub w kodzie stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3abda-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="3abda-300">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="3abda-301">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="3abda-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="3abda-302">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="3abda-303">`IHttpClientBuilder` Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="3abda-304">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="3abda-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="3abda-305">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="3abda-306">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="3abda-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="3abda-307">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="3abda-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="3abda-308">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="3abda-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="3abda-309">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="3abda-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="3abda-310">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3abda-310">In the following example:</span></span>

* <span data-ttu-id="3abda-311"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="3abda-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="3abda-312">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="3abda-313">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3abda-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="3abda-314">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="3abda-315">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="3abda-315">Header propagation middleware</span></span>

<span data-ttu-id="3abda-316">Propagacja nagłówka to ASP.NET Core oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="3abda-317">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="3abda-317">To use header propagation:</span></span>

* <span data-ttu-id="3abda-318">Odwołuje się do pakietu [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="3abda-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="3abda-319">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w `Startup`programie:</span><span class="sxs-lookup"><span data-stu-id="3abda-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="3abda-320">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="3abda-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="3abda-321">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3abda-321">Additional resources</span></span>

* [<span data-ttu-id="3abda-322">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="3abda-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="3abda-323">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="3abda-324">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="3abda-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="3abda-325">Jak serializować i deserializować kod JSON w programie .NET</span><span class="sxs-lookup"><span data-stu-id="3abda-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3abda-326">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="3abda-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="3abda-327"><xref:System.Net.Http.IHttpClientFactory> Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="3abda-328">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="3abda-328">It offers the following benefits:</span></span>

* <span data-ttu-id="3abda-329">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="3abda-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="3abda-330">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="3abda-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="3abda-331">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="3abda-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="3abda-332">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów `HttpClient` obsługi w programie i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="3abda-333">Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` podstawowych wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="3abda-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="3abda-334">Dodaje konfigurowalne środowisko rejestrowania (za `ILogger`pośrednictwem programu) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="3abda-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="3abda-335">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3abda-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="3abda-336">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="3abda-336">Consumption patterns</span></span>

<span data-ttu-id="3abda-337">W aplikacji można używać `IHttpClientFactory` kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="3abda-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="3abda-338">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3abda-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="3abda-339">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="3abda-340">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="3abda-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="3abda-341">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="3abda-342">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="3abda-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="3abda-343">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="3abda-344">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3abda-344">Basic usage</span></span>

<span data-ttu-id="3abda-345">Można zarejestrować `IServiceCollection`, wywołując metodę `Startup.ConfigureServices` `AddHttpClient` rozszerzenia na, wewnątrz metody. `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3abda-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="3abda-346">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3abda-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3abda-347">`IHttpClientFactory` Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="3abda-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="3abda-348">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="3abda-349">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="3abda-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="3abda-350">W miejscach, `HttpClient` w których są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="3abda-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="3abda-351">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-351">Named clients</span></span>

<span data-ttu-id="3abda-352">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient`, z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="3abda-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="3abda-353">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3abda-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="3abda-354">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="3abda-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="3abda-355">Ten klient ma zainstalowaną domyślną&mdash;konfigurację, a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="3abda-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="3abda-356">`CreateClient` Przy każdym wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="3abda-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="3abda-357">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem `CreateClient`do.</span><span class="sxs-lookup"><span data-stu-id="3abda-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="3abda-358">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="3abda-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="3abda-359">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="3abda-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="3abda-360">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="3abda-361">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="3abda-361">Typed clients</span></span>

<span data-ttu-id="3abda-362">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="3abda-362">Typed clients:</span></span>

* <span data-ttu-id="3abda-363">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="3abda-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="3abda-364">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="3abda-365">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="3abda-366">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="3abda-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="3abda-367">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="3abda-368">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="3abda-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="3abda-369">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="3abda-370">`HttpClient` Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="3abda-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="3abda-371">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, `HttpClient` które uwidaczniają funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="3abda-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="3abda-372">`GetAspNetDocsIssues` Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="3abda-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="3abda-373">Aby zarejestrować klienta z określonym typem, można <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> użyć generycznej metody rozszerzenia w `Startup.ConfigureServices`programie, określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="3abda-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="3abda-374">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="3abda-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="3abda-375">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="3abda-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="3abda-376">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas `Startup.ConfigureServices`rejestracji w, a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="3abda-377">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="3abda-378">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="3abda-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="3abda-379">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="3abda-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="3abda-380">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się `GetRepos` za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="3abda-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="3abda-381">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-381">Generated clients</span></span>

<span data-ttu-id="3abda-382">`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="3abda-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="3abda-383">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="3abda-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="3abda-384">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="3abda-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="3abda-385">Implementacja interfejsu jest generowana dynamicznie przez `RestService`, przy użyciu polecenia `HttpClient` , aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="3abda-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="3abda-386">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="3abda-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="3abda-387">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="3abda-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="3abda-388">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="3abda-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="3abda-389">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="3abda-389">Outgoing request middleware</span></span>

<span data-ttu-id="3abda-390">`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="3abda-391">`IHttpClientFactory` Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="3abda-392">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="3abda-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="3abda-393">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="3abda-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="3abda-394">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3abda-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="3abda-395">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="3abda-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="3abda-396">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z <xref:System.Net.Http.DelegatingHandler>elementu.</span><span class="sxs-lookup"><span data-stu-id="3abda-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="3abda-397">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="3abda-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="3abda-398">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="3abda-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="3abda-399">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="3abda-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="3abda-400">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="3abda-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="3abda-401">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="3abda-402">To zadanie jest realizowane za pośrednictwem metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>na.</span><span class="sxs-lookup"><span data-stu-id="3abda-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="3abda-403">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="3abda-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="3abda-404">`IHttpClientFactory` Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="3abda-405">Programy obsługi są bezpłatne, aby zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="3abda-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="3abda-406">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="3abda-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="3abda-407">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="3abda-408">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="3abda-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="3abda-409">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="3abda-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="3abda-410">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="3abda-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="3abda-411">Przekaż dane do procedury obsługi przy `HttpRequestMessage.Properties`użyciu polecenia.</span><span class="sxs-lookup"><span data-stu-id="3abda-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="3abda-412">Użyj `IHttpContextAccessor` , aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="3abda-413">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="3abda-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="3abda-414">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-414">Use Polly-based handlers</span></span>

<span data-ttu-id="3abda-415">`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="3abda-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="3abda-416">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="3abda-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="3abda-417">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="3abda-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="3abda-418">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="3abda-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="3abda-419">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="3abda-419">The Polly extensions:</span></span>

* <span data-ttu-id="3abda-420">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="3abda-421">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="3abda-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="3abda-422">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="3abda-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="3abda-423">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="3abda-423">Handle transient faults</span></span>

<span data-ttu-id="3abda-424">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="3abda-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="3abda-425">Dołączono wygodną metodę `AddTransientHttpErrorPolicy` rozszerzenia, która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="3abda-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="3abda-426">Zasady skonfigurowane przy użyciu tego uchwytu `HttpRequestException`metody rozszerzenia, odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="3abda-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="3abda-427">`AddTransientHttpErrorPolicy` Rozszerzenie może być używane w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3abda-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3abda-428">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="3abda-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="3abda-429">W poprzednim kodzie zdefiniowane są `WaitAndRetryAsync` zasady.</span><span class="sxs-lookup"><span data-stu-id="3abda-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="3abda-430">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="3abda-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="3abda-431">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="3abda-431">Dynamically select policies</span></span>

<span data-ttu-id="3abda-432">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="3abda-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="3abda-433">Jedno takie rozszerzenie ma `AddPolicyHandler`wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="3abda-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="3abda-434">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="3abda-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="3abda-435">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="3abda-436">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="3abda-437">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="3abda-438">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="3abda-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="3abda-439">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="3abda-440">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="3abda-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="3abda-441">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="3abda-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="3abda-442">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="3abda-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="3abda-443">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="3abda-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="3abda-444">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="3abda-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="3abda-445">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="3abda-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="3abda-446">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="3abda-447">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za `PolicyRegistry`pomocą.</span><span class="sxs-lookup"><span data-stu-id="3abda-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="3abda-448">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="3abda-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="3abda-449">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do. `ServiceCollection`</span><span class="sxs-lookup"><span data-stu-id="3abda-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="3abda-450">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="3abda-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="3abda-451">Więcej informacji na `IHttpClientFactory` temat integracji z programem i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="3abda-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="3abda-452">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="3abda-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="3abda-453">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , `IHttpClientFactory`gdy jest wywoływana w.</span><span class="sxs-lookup"><span data-stu-id="3abda-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="3abda-454">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="3abda-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="3abda-455">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="3abda-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="3abda-456">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="3abda-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="3abda-457">`HttpMessageHandler` Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="3abda-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="3abda-458">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="3abda-459">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="3abda-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="3abda-460">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="3abda-461">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="3abda-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="3abda-462">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="3abda-463">Aby zastąpić ten element, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> Wywołaj `IHttpClientBuilder` metodę zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="3abda-464">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="3abda-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="3abda-465">Likwidacja anuluje żądania wychodzące i `HttpClient` gwarantuje, że danego wystąpienia nie <xref:System.IDisposable.Dispose*>można użyć po wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="3abda-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="3abda-466">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="3abda-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="3abda-467">`HttpClient` Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="3abda-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="3abda-468">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3abda-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="3abda-469">Ten wzorzec będzie niepotrzebny po `IHttpClientFactory`migracji do programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="3abda-470">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3abda-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="3abda-471">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="3abda-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="3abda-472">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="3abda-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="3abda-473">Nieodświeżone problemy z `HttpMessageHandler` usługą DNS przez cykliczne wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="3abda-474">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu <xref:System.Net.Http.SocketsHttpHandler> wystąpienia długotrwałego.</span><span class="sxs-lookup"><span data-stu-id="3abda-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="3abda-475">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="3abda-476">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="3abda-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="3abda-477">Utwórz `HttpClient` wystąpienia przy `new HttpClient(handler, disposeHandler: false)` użyciu programu zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="3abda-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="3abda-478">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami `IHttpClientFactory` , które rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="3abda-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="3abda-479">`SocketsHttpHandler` Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="3abda-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="3abda-480">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="3abda-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="3abda-481">`SocketsHttpHandler` Cykle połączeń zgodnie `PooledConnectionLifetime` z, aby uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="3abda-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="3abda-482">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="3abda-482">Cookies</span></span>

<span data-ttu-id="3abda-483">`HttpMessageHandler` Wystąpienia w `CookieContainer` puli powoduje, że obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="3abda-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="3abda-484">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="3abda-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="3abda-485">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="3abda-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="3abda-486">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="3abda-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="3abda-487">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3abda-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="3abda-488">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="3abda-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="3abda-489">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="3abda-489">Logging</span></span>

<span data-ttu-id="3abda-490">Klienci utworzeni `IHttpClientFactory` za pomocą rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="3abda-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="3abda-491">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="3abda-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="3abda-492">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="3abda-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="3abda-493">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="3abda-494">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="3abda-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="3abda-495">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="3abda-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="3abda-496">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="3abda-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="3abda-497">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="3abda-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="3abda-498">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="3abda-499">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`dzienników.</span><span class="sxs-lookup"><span data-stu-id="3abda-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="3abda-500">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="3abda-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="3abda-501">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="3abda-502">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="3abda-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="3abda-503">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3abda-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="3abda-504">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="3abda-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="3abda-505">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="3abda-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="3abda-506">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="3abda-507">`IHttpClientBuilder` Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="3abda-508">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="3abda-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="3abda-509">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="3abda-510">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="3abda-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="3abda-511">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="3abda-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="3abda-512">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="3abda-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="3abda-513">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="3abda-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="3abda-514">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3abda-514">In the following example:</span></span>

* <span data-ttu-id="3abda-515"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="3abda-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="3abda-516">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="3abda-517">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3abda-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="3abda-518">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="3abda-519">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3abda-519">Additional resources</span></span>

* [<span data-ttu-id="3abda-520">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="3abda-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="3abda-521">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="3abda-522">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="3abda-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3abda-523">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="3abda-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="3abda-524"><xref:System.Net.Http.IHttpClientFactory> Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="3abda-525">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="3abda-525">It offers the following benefits:</span></span>

* <span data-ttu-id="3abda-526">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="3abda-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="3abda-527">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="3abda-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="3abda-528">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="3abda-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="3abda-529">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów `HttpClient` obsługi w programie i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="3abda-530">Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` podstawowych wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="3abda-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="3abda-531">Dodaje konfigurowalne środowisko rejestrowania (za `ILogger`pośrednictwem programu) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="3abda-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="3abda-532">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3abda-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3abda-533">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3abda-533">Prerequisites</span></span>

<span data-ttu-id="3abda-534">Projekty docelowe .NET Framework wymagają instalacji pakietu NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) .</span><span class="sxs-lookup"><span data-stu-id="3abda-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="3abda-535">Projekty przeznaczone dla platformy .NET Core i odwołujące się do [pakietu Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) zawierają już `Microsoft.Extensions.Http` pakiet.</span><span class="sxs-lookup"><span data-stu-id="3abda-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="3abda-536">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="3abda-536">Consumption patterns</span></span>

<span data-ttu-id="3abda-537">W aplikacji można używać `IHttpClientFactory` kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="3abda-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="3abda-538">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3abda-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="3abda-539">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="3abda-540">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="3abda-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="3abda-541">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="3abda-542">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="3abda-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="3abda-543">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="3abda-544">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="3abda-544">Basic usage</span></span>

<span data-ttu-id="3abda-545">Można zarejestrować `IServiceCollection`, wywołując metodę `Startup.ConfigureServices` `AddHttpClient` rozszerzenia na, wewnątrz metody. `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3abda-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="3abda-546">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3abda-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3abda-547">`IHttpClientFactory` Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="3abda-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="3abda-548">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="3abda-549">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="3abda-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="3abda-550">W miejscach, `HttpClient` w których są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="3abda-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="3abda-551">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-551">Named clients</span></span>

<span data-ttu-id="3abda-552">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient`, z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="3abda-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="3abda-553">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3abda-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="3abda-554">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="3abda-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="3abda-555">Ten klient ma zainstalowaną domyślną&mdash;konfigurację, a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="3abda-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="3abda-556">`CreateClient` Przy każdym wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="3abda-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="3abda-557">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem `CreateClient`do.</span><span class="sxs-lookup"><span data-stu-id="3abda-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="3abda-558">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="3abda-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="3abda-559">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="3abda-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="3abda-560">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="3abda-561">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="3abda-561">Typed clients</span></span>

<span data-ttu-id="3abda-562">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="3abda-562">Typed clients:</span></span>

* <span data-ttu-id="3abda-563">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="3abda-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="3abda-564">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="3abda-565">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="3abda-566">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="3abda-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="3abda-567">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="3abda-568">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="3abda-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="3abda-569">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="3abda-570">`HttpClient` Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="3abda-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="3abda-571">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, `HttpClient` które uwidaczniają funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="3abda-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="3abda-572">`GetAspNetDocsIssues` Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="3abda-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="3abda-573">Aby zarejestrować klienta z określonym typem, można <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> użyć generycznej metody rozszerzenia w `Startup.ConfigureServices`programie, określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="3abda-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="3abda-574">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="3abda-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="3abda-575">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="3abda-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="3abda-576">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas `Startup.ConfigureServices`rejestracji w, a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="3abda-577">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="3abda-578">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="3abda-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="3abda-579">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="3abda-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="3abda-580">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się `GetRepos` za pomocą metody.</span><span class="sxs-lookup"><span data-stu-id="3abda-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="3abda-581">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="3abda-581">Generated clients</span></span>

<span data-ttu-id="3abda-582">`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="3abda-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="3abda-583">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="3abda-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="3abda-584">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="3abda-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="3abda-585">Implementacja interfejsu jest generowana dynamicznie przez `RestService`, przy użyciu polecenia `HttpClient` , aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="3abda-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="3abda-586">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="3abda-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="3abda-587">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="3abda-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="3abda-588">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="3abda-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="3abda-589">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="3abda-589">Outgoing request middleware</span></span>

<span data-ttu-id="3abda-590">`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="3abda-591">`IHttpClientFactory` Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="3abda-592">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="3abda-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="3abda-593">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="3abda-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="3abda-594">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3abda-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="3abda-595">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="3abda-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="3abda-596">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z <xref:System.Net.Http.DelegatingHandler>elementu.</span><span class="sxs-lookup"><span data-stu-id="3abda-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="3abda-597">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="3abda-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="3abda-598">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="3abda-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="3abda-599">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="3abda-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="3abda-600">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="3abda-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="3abda-601">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="3abda-602">To zadanie jest realizowane za pośrednictwem metod rozszerzających <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>na.</span><span class="sxs-lookup"><span data-stu-id="3abda-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="3abda-603">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="3abda-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="3abda-604">Procedura obsługi **musi** być zarejestrowana w programie di jako przejściowa usługa, nigdy nie jest objęta zakresem.</span><span class="sxs-lookup"><span data-stu-id="3abda-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="3abda-605">Jeśli program obsługi jest zarejestrowany jako usługa objęta zakresem, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:</span><span class="sxs-lookup"><span data-stu-id="3abda-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="3abda-606">Usługi programu obsługi mogły zostać zlikwidowane, zanim program obsługi znajdzie się poza zakresem.</span><span class="sxs-lookup"><span data-stu-id="3abda-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="3abda-607">Usunięte usługi obsługi powodują niepowodzenie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="3abda-608">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując w typie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="3abda-609">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="3abda-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="3abda-610">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="3abda-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="3abda-611">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="3abda-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="3abda-612">Przekaż dane do procedury obsługi przy `HttpRequestMessage.Properties`użyciu polecenia.</span><span class="sxs-lookup"><span data-stu-id="3abda-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="3abda-613">Użyj `IHttpContextAccessor` , aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="3abda-614">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="3abda-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="3abda-615">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-615">Use Polly-based handlers</span></span>

<span data-ttu-id="3abda-616">`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="3abda-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="3abda-617">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="3abda-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="3abda-618">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="3abda-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="3abda-619">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="3abda-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="3abda-620">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="3abda-620">The Polly extensions:</span></span>

* <span data-ttu-id="3abda-621">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="3abda-622">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="3abda-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="3abda-623">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="3abda-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="3abda-624">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="3abda-624">Handle transient faults</span></span>

<span data-ttu-id="3abda-625">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="3abda-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="3abda-626">Dołączono wygodną metodę `AddTransientHttpErrorPolicy` rozszerzenia, która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="3abda-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="3abda-627">Zasady skonfigurowane przy użyciu tego uchwytu `HttpRequestException`metody rozszerzenia, odpowiedzi HTTP 5xx i odpowiedzi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="3abda-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="3abda-628">`AddTransientHttpErrorPolicy` Rozszerzenie może być używane w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3abda-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3abda-629">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="3abda-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="3abda-630">W poprzednim kodzie zdefiniowane są `WaitAndRetryAsync` zasady.</span><span class="sxs-lookup"><span data-stu-id="3abda-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="3abda-631">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="3abda-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="3abda-632">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="3abda-632">Dynamically select policies</span></span>

<span data-ttu-id="3abda-633">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="3abda-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="3abda-634">Jedno takie rozszerzenie ma `AddPolicyHandler`wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="3abda-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="3abda-635">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="3abda-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="3abda-636">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="3abda-637">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="3abda-638">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="3abda-639">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="3abda-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="3abda-640">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="3abda-641">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="3abda-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="3abda-642">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="3abda-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="3abda-643">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="3abda-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="3abda-644">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="3abda-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="3abda-645">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="3abda-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="3abda-646">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="3abda-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="3abda-647">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="3abda-648">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za `PolicyRegistry`pomocą.</span><span class="sxs-lookup"><span data-stu-id="3abda-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="3abda-649">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="3abda-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="3abda-650">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do. `ServiceCollection`</span><span class="sxs-lookup"><span data-stu-id="3abda-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="3abda-651">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="3abda-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="3abda-652">Więcej informacji na `IHttpClientFactory` temat integracji z programem i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="3abda-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="3abda-653">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="3abda-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="3abda-654">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , `IHttpClientFactory`gdy jest wywoływana w.</span><span class="sxs-lookup"><span data-stu-id="3abda-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="3abda-655">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="3abda-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="3abda-656">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="3abda-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="3abda-657">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="3abda-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="3abda-658">`HttpMessageHandler` Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="3abda-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="3abda-659">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="3abda-660">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="3abda-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="3abda-661">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="3abda-662">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="3abda-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="3abda-663">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="3abda-664">Aby zastąpić ten element, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> Wywołaj `IHttpClientBuilder` metodę zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="3abda-665">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="3abda-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="3abda-666">Likwidacja anuluje żądania wychodzące i `HttpClient` gwarantuje, że danego wystąpienia nie <xref:System.IDisposable.Dispose*>można użyć po wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="3abda-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="3abda-667">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="3abda-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="3abda-668">`HttpClient` Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="3abda-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="3abda-669">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="3abda-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="3abda-670">Ten wzorzec będzie niepotrzebny po `IHttpClientFactory`migracji do programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="3abda-671">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="3abda-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="3abda-672">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="3abda-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="3abda-673">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="3abda-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="3abda-674">Nieodświeżone problemy z `HttpMessageHandler` usługą DNS przez cykliczne wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="3abda-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="3abda-675">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu <xref:System.Net.Http.SocketsHttpHandler> wystąpienia długotrwałego.</span><span class="sxs-lookup"><span data-stu-id="3abda-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="3abda-676">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3abda-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="3abda-677">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="3abda-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="3abda-678">Utwórz `HttpClient` wystąpienia przy `new HttpClient(handler, disposeHandler: false)` użyciu programu zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="3abda-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="3abda-679">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami `IHttpClientFactory` , które rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="3abda-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="3abda-680">`SocketsHttpHandler` Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="3abda-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="3abda-681">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="3abda-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="3abda-682">`SocketsHttpHandler` Cykle połączeń zgodnie `PooledConnectionLifetime` z, aby uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="3abda-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="3abda-683">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="3abda-683">Cookies</span></span>

<span data-ttu-id="3abda-684">`HttpMessageHandler` Wystąpienia w `CookieContainer` puli powoduje, że obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="3abda-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="3abda-685">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="3abda-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="3abda-686">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="3abda-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="3abda-687">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="3abda-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="3abda-688">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="3abda-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="3abda-689">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="3abda-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="3abda-690">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="3abda-690">Logging</span></span>

<span data-ttu-id="3abda-691">Klienci utworzeni `IHttpClientFactory` za pomocą rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="3abda-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="3abda-692">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="3abda-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="3abda-693">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="3abda-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="3abda-694">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="3abda-695">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="3abda-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="3abda-696">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="3abda-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="3abda-697">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="3abda-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="3abda-698">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="3abda-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="3abda-699">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="3abda-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="3abda-700">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`dzienników.</span><span class="sxs-lookup"><span data-stu-id="3abda-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="3abda-701">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="3abda-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="3abda-702">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="3abda-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="3abda-703">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="3abda-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="3abda-704">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3abda-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="3abda-705">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="3abda-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="3abda-706">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="3abda-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="3abda-707">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="3abda-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="3abda-708">`IHttpClientBuilder` Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3abda-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="3abda-709">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="3abda-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="3abda-710">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="3abda-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="3abda-711">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="3abda-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="3abda-712">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="3abda-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="3abda-713">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="3abda-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="3abda-714">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="3abda-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="3abda-715">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3abda-715">In the following example:</span></span>

* <span data-ttu-id="3abda-716"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="3abda-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="3abda-717">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="3abda-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="3abda-718">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3abda-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="3abda-719">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="3abda-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="3abda-720">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="3abda-720">Header propagation middleware</span></span>

<span data-ttu-id="3abda-721">Propagacja nagłówka to społeczność obsługiwana przez oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="3abda-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="3abda-722">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="3abda-722">To use header propagation:</span></span>

* <span data-ttu-id="3abda-723">Odwołuje się do obsługiwanego przez społeczność portu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)pakietu.</span><span class="sxs-lookup"><span data-stu-id="3abda-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="3abda-724">ASP.NET Core 3,1 i nowsze obsługuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="3abda-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="3abda-725">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w `Startup`programie:</span><span class="sxs-lookup"><span data-stu-id="3abda-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="3abda-726">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="3abda-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="3abda-727">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3abda-727">Additional resources</span></span>

* [<span data-ttu-id="3abda-728">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="3abda-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="3abda-729">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="3abda-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="3abda-730">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="3abda-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
