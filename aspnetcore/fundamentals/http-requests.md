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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: fb9001c06228b4290ca1e0c7cfb6b1338f431cd6
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444118"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="49b5a-103">Wykonywanie żądań HTTP przy użyciu IHttpClientFactory w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b5a-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49b5a-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="49b5a-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="49b5a-105"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="49b5a-106">`IHttpClientFactory`oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="49b5a-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="49b5a-107">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-108">Na przykład klient o nazwie *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="49b5a-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="49b5a-109">Domyślny klient można zarejestrować na potrzeby ogólnego dostępu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="49b5a-110">Kodyfikacja koncepcji wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="49b5a-111">Udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z delegowania programów obsługi w programie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="49b5a-112">Zarządza buforowaniem i okresem istnienia `HttpClientMessageHandler` wystąpień podstawowych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="49b5a-113">Automatyczne zarządzanie pozwala uniknąć typowych problemów z usługą DNS (Domain Name System) występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="49b5a-114">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="49b5a-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="49b5a-115">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="49b5a-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="49b5a-116">Przykładowy kod w tym temacie używa <xref:System.Text.Json> do deserializacji zawartości JSON zwróconej w odpowiedziach http.</span><span class="sxs-lookup"><span data-stu-id="49b5a-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="49b5a-117">Aby zapoznać się z przykładami używanymi przez `Json.NET` `ReadAsAsync<T>` program i, użyj selektora wersji, aby wybrać wersję 2. x tego tematu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="49b5a-118">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-118">Consumption patterns</span></span>

<span data-ttu-id="49b5a-119">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="49b5a-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="49b5a-120">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="49b5a-121">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="49b5a-122">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="49b5a-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="49b5a-123">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="49b5a-124">Najlepsze podejście zależy od wymagań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="49b5a-125">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-125">Basic usage</span></span>

<span data-ttu-id="49b5a-126">`IHttpClientFactory`można zarejestrować, wywołując `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="49b5a-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="49b5a-127">`IHttpClientFactory`Można zażądać przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49b5a-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49b5a-128">Następujący kod używa `IHttpClientFactory` do tworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="49b5a-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="49b5a-129">Użycie `IHttpClientFactory` podobnej do powyższego przykładu jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="49b5a-130">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="49b5a-131">W miejscach, w których `HttpClient` wystąpienia są tworzone w istniejącej aplikacji, Zastąp te wystąpienia wywołaniami do <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="49b5a-132">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-132">Named clients</span></span>

<span data-ttu-id="49b5a-133">Nazwani klienci są dobrym wyborem w przypadku:</span><span class="sxs-lookup"><span data-stu-id="49b5a-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="49b5a-134">Aplikacja wymaga wielu odrębnych użycia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="49b5a-135">Wiele `HttpClient` s ma inną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="49b5a-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="49b5a-136">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="49b5a-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="49b5a-137">W powyższym kodzie klient ma skonfigurowany program:</span><span class="sxs-lookup"><span data-stu-id="49b5a-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="49b5a-138">Adres podstawowy `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="49b5a-139">Dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="49b5a-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="49b5a-140">Klient</span><span class="sxs-lookup"><span data-stu-id="49b5a-140">CreateClient</span></span>

<span data-ttu-id="49b5a-141">Za każdym razem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="49b5a-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="49b5a-142">Tworzone jest nowe wystąpienie `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="49b5a-143">Akcja konfiguracji jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="49b5a-143">The configuration action is called.</span></span>

<span data-ttu-id="49b5a-144">Aby utworzyć nazwanego klienta, Przekaż jego nazwę do `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="49b5a-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="49b5a-145">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="49b5a-146">Kod może przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="49b5a-147">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="49b5a-147">Typed clients</span></span>

<span data-ttu-id="49b5a-148">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-148">Typed clients:</span></span>

* <span data-ttu-id="49b5a-149">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="49b5a-150">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="49b5a-151">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="49b5a-152">Na przykład może zostać użyty pojedynczy klient z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="49b5a-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="49b5a-153">Dla pojedynczego punktu końcowego zaplecza.</span><span class="sxs-lookup"><span data-stu-id="49b5a-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="49b5a-154">Do hermetyzacji wszystkich logiki związanych z punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="49b5a-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="49b5a-155">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="49b5a-156">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="49b5a-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="49b5a-157">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-157">In the preceding code:</span></span>

* <span data-ttu-id="49b5a-158">Konfiguracja zostanie przeniesiona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="49b5a-159">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="49b5a-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="49b5a-160">Można tworzyć metody specyficzne dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="49b5a-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="49b5a-161">Na przykład `GetAspNetDocsIssues` Metoda hermetyzuje kod w celu pobrania otwartych problemów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="49b5a-162">Następujący kod wywołuje <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> w `Startup.ConfigureServices` celu zarejestrowania klasy klienta typu:</span><span class="sxs-lookup"><span data-stu-id="49b5a-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="49b5a-163">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="49b5a-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="49b5a-164">W poprzednim kodzie program `AddHttpClient` rejestruje `GitHubService` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="49b5a-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="49b5a-165">Ta rejestracja używa metody fabryki do:</span><span class="sxs-lookup"><span data-stu-id="49b5a-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="49b5a-166">Utwórz wystąpienie elementu `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="49b5a-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="49b5a-167">Utwórz wystąpienie `GitHubService` , przekazując w wystąpieniu `HttpClient` do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="49b5a-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="49b5a-168">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="49b5a-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="49b5a-169">Konfigurację dla określonego klienta można określić podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="49b5a-170">`HttpClient`Mogą być hermetyzowane w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="49b5a-171">Zamiast uwidaczniać ją jako właściwość, zdefiniuj metodę, która wywołuje `HttpClient` wystąpienie wewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="49b5a-172">W poprzednim kodzie, `HttpClient` jest przechowywany w prywatnym polu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="49b5a-173">Dostęp do programu `HttpClient` ma metoda publiczna `GetRepos` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="49b5a-174">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-174">Generated clients</span></span>

<span data-ttu-id="49b5a-175">`IHttpClientFactory`może być używany w połączeniu z bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="49b5a-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="49b5a-176">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="49b5a-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="49b5a-177">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="49b5a-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="49b5a-178">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="49b5a-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="49b5a-179">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="49b5a-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="49b5a-180">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="49b5a-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="49b5a-181">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="49b5a-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="49b5a-182">Wykonywanie żądań POST, PUT i DELETE</span><span class="sxs-lookup"><span data-stu-id="49b5a-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="49b5a-183">W powyższych przykładach wszystkie żądania HTTP używają zlecenia GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="49b5a-184">`HttpClient`Program obsługuje również inne zlecenia HTTP, w tym:</span><span class="sxs-lookup"><span data-stu-id="49b5a-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="49b5a-185">POST</span><span class="sxs-lookup"><span data-stu-id="49b5a-185">POST</span></span>
* <span data-ttu-id="49b5a-186">PUT</span><span class="sxs-lookup"><span data-stu-id="49b5a-186">PUT</span></span>
* <span data-ttu-id="49b5a-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="49b5a-187">DELETE</span></span>
* <span data-ttu-id="49b5a-188">WYSŁANA</span><span class="sxs-lookup"><span data-stu-id="49b5a-188">PATCH</span></span>

<span data-ttu-id="49b5a-189">Aby uzyskać pełną listę obsługiwanych czasowników HTTP, zobacz <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="49b5a-190">Poniższy przykład pokazuje, jak utworzyć żądanie HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="49b5a-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="49b5a-191">W poprzednim kodzie `CreateItemAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="49b5a-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="49b5a-192">Serializować `TodoItem` parametr do formatu JSON przy użyciu `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="49b5a-193">Powoduje to użycie wystąpienia programu <xref:System.Text.Json.JsonSerializerOptions> w celu skonfigurowania procesu serializacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="49b5a-194">Tworzy wystąpienie <xref:System.Net.Http.StringContent> do pakowania serializowanego kodu JSON do wysłania w treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="49b5a-195">Wywołuje <xref:System.Net.Http.HttpClient.PostAsync%2A> wysyłanie zawartości JSON do określonego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="49b5a-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="49b5a-196">Jest to względny adres URL, który zostanie dodany do [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="49b5a-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="49b5a-197">Wywołuje, <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> Aby zgłosić wyjątek, jeśli kod stanu odpowiedzi nie wskazuje powodzenia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="49b5a-198">`HttpClient`obsługuje również inne typy zawartości.</span><span class="sxs-lookup"><span data-stu-id="49b5a-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="49b5a-199">Na przykład <xref:System.Net.Http.MultipartContent> i <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="49b5a-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="49b5a-200">Aby zapoznać się z pełną listą obsługiwanych zawartości, zobacz <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="49b5a-201">Poniższy przykład przedstawia żądanie HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="49b5a-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="49b5a-202">Poprzedni kod jest bardzo podobny do przykładu POST.</span><span class="sxs-lookup"><span data-stu-id="49b5a-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="49b5a-203">`SaveItemAsync`Metoda wywołuje <xref:System.Net.Http.HttpClient.PutAsync%2A> zamiast `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="49b5a-204">Poniższy przykład przedstawia żądanie HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="49b5a-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="49b5a-205">W poprzednim kodzie `DeleteItemAsync` Metoda wywołuje metodę <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="49b5a-206">Ponieważ żądania HTTP DELETE zazwyczaj nie zawierają treści, `DeleteAsync` Metoda nie zapewnia przeciążenia, które akceptuje wystąpienie `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="49b5a-207">Aby dowiedzieć się więcej o używaniu różnych zleceń HTTP w programie `HttpClient` , zobacz <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="49b5a-208">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="49b5a-208">Outgoing request middleware</span></span>

<span data-ttu-id="49b5a-209">`HttpClient`ma koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="49b5a-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="49b5a-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="49b5a-211">Upraszcza definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="49b5a-212">Obsługuje rejestrację i łańcuch wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="49b5a-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="49b5a-213">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="49b5a-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="49b5a-214">Ten wzorzec:</span><span class="sxs-lookup"><span data-stu-id="49b5a-214">This pattern:</span></span>

  * <span data-ttu-id="49b5a-215">Jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49b5a-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="49b5a-216">Program udostępnia mechanizm zarządzania problemami z obcinaniem między żądaniami HTTP, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="49b5a-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="49b5a-217">pamięć</span><span class="sxs-lookup"><span data-stu-id="49b5a-217">caching</span></span>
    * <span data-ttu-id="49b5a-218">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="49b5a-218">error handling</span></span>
    * <span data-ttu-id="49b5a-219">serializacji</span><span class="sxs-lookup"><span data-stu-id="49b5a-219">serialization</span></span>
    * <span data-ttu-id="49b5a-220">rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="49b5a-220">logging</span></span>

<span data-ttu-id="49b5a-221">Aby utworzyć program obsługi delegowania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-221">To create a delegating handler:</span></span>

* <span data-ttu-id="49b5a-222">Pochodny od <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="49b5a-223">Zastąpienie <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="49b5a-224">Wykonaj kod przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="49b5a-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="49b5a-225">Poprzedni kod sprawdza, czy `X-API-KEY` nagłówek jest w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="49b5a-226">Jeśli `X-API-KEY` brakuje, <xref:System.Net.HttpStatusCode.BadRequest> jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="49b5a-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="49b5a-227">Do konfiguracji dla elementu with można dodać więcej niż jeden program obsługi `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="49b5a-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="49b5a-228">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="49b5a-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="49b5a-229">`IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="49b5a-230">Programy obsługi mogą zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="49b5a-231">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="49b5a-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="49b5a-232">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="49b5a-233">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="49b5a-234">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="49b5a-235">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="49b5a-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="49b5a-236">Przekaż dane do procedury obsługi przy użyciu [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="49b5a-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="49b5a-237">Użyj, <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="49b5a-238">Utwórz niestandardowy <xref:System.Threading.AsyncLocal`1> obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="49b5a-239">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-239">Use Polly-based handlers</span></span>

<span data-ttu-id="49b5a-240">`IHttpClientFactory`integruje się z biblioteką innych firm [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="49b5a-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="49b5a-241">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="49b5a-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="49b5a-242">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="49b5a-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="49b5a-243">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-244">Rozszerzenia Polly obsługują dodawanie programów obsługi opartych na Polly do klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="49b5a-245">Polly wymaga pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="49b5a-246">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="49b5a-246">Handle transient faults</span></span>

<span data-ttu-id="49b5a-247">Błędy są zwykle wykonywane, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="49b5a-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="49b5a-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="49b5a-249">Skonfigurowane zasady z `AddTransientHttpErrorPolicy` obsługą następujących odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="49b5a-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="49b5a-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="49b5a-250">HTTP 5xx</span></span>
* <span data-ttu-id="49b5a-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="49b5a-251">HTTP 408</span></span>

<span data-ttu-id="49b5a-252">`AddTransientHttpErrorPolicy`zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="49b5a-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="49b5a-253">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="49b5a-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="49b5a-254">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="49b5a-255">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="49b5a-255">Dynamically select policies</span></span>

<span data-ttu-id="49b5a-256">Metody rozszerzające umożliwiają dodawanie programów obsługi opartych na Polly, na przykład <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="49b5a-257">Następujące `AddPolicyHandler` Przeciążenie przeprowadziło inspekcję żądania, aby zdecydować, które zasady zastosować:</span><span class="sxs-lookup"><span data-stu-id="49b5a-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="49b5a-258">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="49b5a-259">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="49b5a-260">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="49b5a-261">Jest to typowy sposób zagnieżdżania zasad Pollyymi:</span><span class="sxs-lookup"><span data-stu-id="49b5a-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="49b5a-262">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-262">In the preceding example:</span></span>

* <span data-ttu-id="49b5a-263">Dodawane są dwa procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-263">Two handlers are added.</span></span>
* <span data-ttu-id="49b5a-264">Pierwsza procedura obsługi używa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> do dodawania zasad ponawiania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="49b5a-265">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="49b5a-266">Drugie `AddTransientHttpErrorPolicy` wywołanie dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="49b5a-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="49b5a-267">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli 5 nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="49b5a-268">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="49b5a-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="49b5a-269">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="49b5a-270">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="49b5a-271">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="49b5a-272">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-272">In the following code:</span></span>

* <span data-ttu-id="49b5a-273">Są dodawane zasady "regularne" i "długie".</span><span class="sxs-lookup"><span data-stu-id="49b5a-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="49b5a-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>dodaje zasady "regularne" i "długie" z rejestru.</span><span class="sxs-lookup"><span data-stu-id="49b5a-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="49b5a-275">Aby uzyskać więcej informacji na temat `IHttpClientFactory` integracji z integracją i Polly, zobacz [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="49b5a-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="49b5a-276">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="49b5a-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="49b5a-277">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="49b5a-278"><xref:System.Net.Http.HttpMessageHandler>Tworzony jest na nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="49b5a-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="49b5a-279">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="49b5a-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="49b5a-280">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="49b5a-281">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="49b5a-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="49b5a-282">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="49b5a-283">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="49b5a-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="49b5a-284">Niektóre programy obsługi powodują również, że połączenia są otwierane w nieskończoność, co może uniemożliwić oddziałanie programu obsługi w systemie DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="49b5a-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="49b5a-285">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="49b5a-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="49b5a-286">Wartość domyślną można przesłonić na podstawie nazwy klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="49b5a-287">`HttpClient`wystąpienia mogą być zwykle traktowane jako obiekty .NET, które **nie** wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="49b5a-288">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="49b5a-289">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="49b5a-290">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="49b5a-291">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="49b5a-292">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="49b5a-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="49b5a-293">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="49b5a-294">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="49b5a-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="49b5a-295">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="49b5a-296">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="49b5a-297">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="49b5a-298">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="49b5a-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="49b5a-299">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="49b5a-300">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="49b5a-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="49b5a-301">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-302">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="49b5a-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="49b5a-303">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="49b5a-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="49b5a-304">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="49b5a-304">Cookies</span></span>

<span data-ttu-id="49b5a-305">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="49b5a-306">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="49b5a-307">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="49b5a-308">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="49b5a-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="49b5a-309">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="49b5a-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="49b5a-310">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="49b5a-311">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="49b5a-311">Logging</span></span>

<span data-ttu-id="49b5a-312">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="49b5a-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="49b5a-313">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="49b5a-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="49b5a-314">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="49b5a-315">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="49b5a-316">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="49b5a-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="49b5a-317">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="49b5a-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="49b5a-318">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="49b5a-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="49b5a-319">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="49b5a-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="49b5a-320">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="49b5a-321">W przykładzie *MyNamedClient* te komunikaty są rejestrowane z kategorią dziennika "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="49b5a-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="49b5a-322">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="49b5a-323">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="49b5a-324">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="49b5a-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="49b5a-325">Może to obejmować zmiany w nagłówkach żądania lub w kodzie stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="49b5a-326">Uwzględnienie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="49b5a-327">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="49b5a-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="49b5a-328">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="49b5a-329">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="49b5a-330"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="49b5a-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="49b5a-331">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="49b5a-332">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="49b5a-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="49b5a-333">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="49b5a-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="49b5a-334">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="49b5a-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="49b5a-335">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="49b5a-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="49b5a-336">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-336">In the following example:</span></span>

* <span data-ttu-id="49b5a-337"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="49b5a-338">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="49b5a-339">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="49b5a-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="49b5a-340">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="49b5a-341">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="49b5a-341">Header propagation middleware</span></span>

<span data-ttu-id="49b5a-342">Propagacja nagłówka to ASP.NET Core oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="49b5a-343">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="49b5a-343">To use header propagation:</span></span>

* <span data-ttu-id="49b5a-344">Odwołuje się do pakietu [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="49b5a-345">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :</span><span class="sxs-lookup"><span data-stu-id="49b5a-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="49b5a-346">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="49b5a-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="49b5a-347">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="49b5a-347">Additional resources</span></span>

* [<span data-ttu-id="49b5a-348">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="49b5a-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="49b5a-349">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="49b5a-350">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="49b5a-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="49b5a-351">Jak serializować i deserializować kod JSON w programie .NET</span><span class="sxs-lookup"><span data-stu-id="49b5a-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="49b5a-352">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="49b5a-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="49b5a-353"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="49b5a-354">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="49b5a-354">It offers the following benefits:</span></span>

* <span data-ttu-id="49b5a-355">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-356">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="49b5a-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="49b5a-357">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="49b5a-358">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="49b5a-359">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="49b5a-360">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="49b5a-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="49b5a-361">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49b5a-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="49b5a-362">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-362">Consumption patterns</span></span>

<span data-ttu-id="49b5a-363">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="49b5a-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="49b5a-364">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="49b5a-365">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="49b5a-366">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="49b5a-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="49b5a-367">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="49b5a-368">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="49b5a-369">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="49b5a-370">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-370">Basic usage</span></span>

<span data-ttu-id="49b5a-371">`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="49b5a-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="49b5a-372">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49b5a-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49b5a-373">`IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="49b5a-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="49b5a-374">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="49b5a-375">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="49b5a-376">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="49b5a-377">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-377">Named clients</span></span>

<span data-ttu-id="49b5a-378">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="49b5a-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="49b5a-379">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="49b5a-380">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="49b5a-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="49b5a-381">Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="49b5a-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="49b5a-382">Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="49b5a-383">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="49b5a-384">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="49b5a-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="49b5a-385">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="49b5a-386">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="49b5a-387">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="49b5a-387">Typed clients</span></span>

<span data-ttu-id="49b5a-388">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-388">Typed clients:</span></span>

* <span data-ttu-id="49b5a-389">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="49b5a-390">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="49b5a-391">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="49b5a-392">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="49b5a-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="49b5a-393">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="49b5a-394">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="49b5a-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="49b5a-395">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="49b5a-396">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="49b5a-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="49b5a-397">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="49b5a-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="49b5a-398">`GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="49b5a-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="49b5a-399">Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="49b5a-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="49b5a-400">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="49b5a-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="49b5a-401">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="49b5a-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="49b5a-402">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="49b5a-403">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="49b5a-404">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="49b5a-405">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="49b5a-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="49b5a-406">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.</span><span class="sxs-lookup"><span data-stu-id="49b5a-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="49b5a-407">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-407">Generated clients</span></span>

<span data-ttu-id="49b5a-408">`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="49b5a-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="49b5a-409">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="49b5a-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="49b5a-410">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="49b5a-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="49b5a-411">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="49b5a-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="49b5a-412">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="49b5a-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="49b5a-413">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="49b5a-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="49b5a-414">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="49b5a-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="49b5a-415">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="49b5a-415">Outgoing request middleware</span></span>

<span data-ttu-id="49b5a-416">`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="49b5a-417">`IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="49b5a-418">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="49b5a-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="49b5a-419">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="49b5a-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="49b5a-420">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49b5a-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="49b5a-421">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="49b5a-422">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="49b5a-423">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="49b5a-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="49b5a-424">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="49b5a-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="49b5a-425">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="49b5a-426">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="49b5a-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="49b5a-427">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="49b5a-428">To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="49b5a-429">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="49b5a-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="49b5a-430">`IHttpClientFactory`Tworzy oddzielny zakres di dla każdej procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="49b5a-431">Programy obsługi są bezpłatne, aby zależeć od usług dowolnego zakresu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="49b5a-432">Usługi, które są zależne od programów obsługi, są usuwane, gdy program obsługi zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="49b5a-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="49b5a-433">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując typ do procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="49b5a-434">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="49b5a-435">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="49b5a-436">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="49b5a-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="49b5a-437">Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="49b5a-438">Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="49b5a-439">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="49b5a-440">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-440">Use Polly-based handlers</span></span>

<span data-ttu-id="49b5a-441">`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="49b5a-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="49b5a-442">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="49b5a-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="49b5a-443">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="49b5a-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="49b5a-444">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-445">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="49b5a-445">The Polly extensions:</span></span>

* <span data-ttu-id="49b5a-446">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="49b5a-447">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="49b5a-448">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="49b5a-449">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="49b5a-449">Handle transient faults</span></span>

<span data-ttu-id="49b5a-450">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="49b5a-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="49b5a-451">Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="49b5a-452">Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.</span><span class="sxs-lookup"><span data-stu-id="49b5a-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="49b5a-453">`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="49b5a-454">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="49b5a-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="49b5a-455">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="49b5a-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="49b5a-456">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="49b5a-457">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="49b5a-457">Dynamically select policies</span></span>

<span data-ttu-id="49b5a-458">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="49b5a-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="49b5a-459">Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="49b5a-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="49b5a-460">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="49b5a-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="49b5a-461">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="49b5a-462">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="49b5a-463">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="49b5a-464">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="49b5a-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="49b5a-465">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="49b5a-466">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="49b5a-467">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="49b5a-468">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="49b5a-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="49b5a-469">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="49b5a-470">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="49b5a-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="49b5a-471">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="49b5a-472">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="49b5a-473">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="49b5a-474">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="49b5a-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="49b5a-475">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="49b5a-476">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="49b5a-477">Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="49b5a-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="49b5a-478">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="49b5a-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="49b5a-479">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="49b5a-480">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="49b5a-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="49b5a-481">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="49b5a-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="49b5a-482">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="49b5a-483">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="49b5a-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="49b5a-484">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="49b5a-485">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="49b5a-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="49b5a-486">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="49b5a-487">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="49b5a-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="49b5a-488">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="49b5a-489">Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="49b5a-490">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="49b5a-491">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="49b5a-492">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-493">`HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="49b5a-494">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="49b5a-495">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="49b5a-496">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="49b5a-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="49b5a-497">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="49b5a-498">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="49b5a-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="49b5a-499">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="49b5a-500">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="49b5a-501">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="49b5a-502">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="49b5a-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="49b5a-503">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="49b5a-504">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="49b5a-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="49b5a-505">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-506">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="49b5a-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="49b5a-507">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="49b5a-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="49b5a-508">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="49b5a-508">Cookies</span></span>

<span data-ttu-id="49b5a-509">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="49b5a-510">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="49b5a-511">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="49b5a-512">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="49b5a-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="49b5a-513">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="49b5a-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="49b5a-514">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="49b5a-515">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="49b5a-515">Logging</span></span>

<span data-ttu-id="49b5a-516">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="49b5a-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="49b5a-517">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="49b5a-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="49b5a-518">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="49b5a-519">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="49b5a-520">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="49b5a-521">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="49b5a-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="49b5a-522">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="49b5a-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="49b5a-523">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="49b5a-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="49b5a-524">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="49b5a-525">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="49b5a-526">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="49b5a-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="49b5a-527">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="49b5a-528">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="49b5a-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="49b5a-529">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="49b5a-530">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="49b5a-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="49b5a-531">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="49b5a-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="49b5a-532">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="49b5a-533">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="49b5a-534"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="49b5a-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="49b5a-535">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="49b5a-536">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="49b5a-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="49b5a-537">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="49b5a-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="49b5a-538">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="49b5a-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="49b5a-539">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="49b5a-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="49b5a-540">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-540">In the following example:</span></span>

* <span data-ttu-id="49b5a-541"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="49b5a-542">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="49b5a-543">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="49b5a-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="49b5a-544">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="49b5a-545">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="49b5a-545">Additional resources</span></span>

* [<span data-ttu-id="49b5a-546">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="49b5a-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="49b5a-547">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="49b5a-548">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="49b5a-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="49b5a-549">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)i [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="49b5a-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="49b5a-550"><xref:System.Net.Http.IHttpClientFactory>Może być zarejestrowany i używany do konfigurowania i tworzenia <xref:System.Net.Http.HttpClient> wystąpień w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="49b5a-551">Oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="49b5a-551">It offers the following benefits:</span></span>

* <span data-ttu-id="49b5a-552">Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-553">Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="49b5a-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="49b5a-554">Domyślny klient można zarejestrować do innych celów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="49b5a-555">Skodyfikował koncepcję wychodzącego oprogramowania pośredniczącego przez delegowanie programów obsługi w programie `HttpClient` i udostępnia rozszerzenia dla oprogramowania pośredniczącego opartego na Polly, aby skorzystać z tego programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="49b5a-556">Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientMessageHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="49b5a-557">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="49b5a-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="49b5a-558">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49b5a-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49b5a-559">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="49b5a-559">Prerequisites</span></span>

<span data-ttu-id="49b5a-560">Projekty docelowe .NET Framework wymagają instalacji pakietu NuGet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="49b5a-561">Projekty przeznaczone dla platformy .NET Core i odwołujące się do [pakietu Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) zawierają już `Microsoft.Extensions.Http` pakiet.</span><span class="sxs-lookup"><span data-stu-id="49b5a-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="49b5a-562">Wzorce zużycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-562">Consumption patterns</span></span>

<span data-ttu-id="49b5a-563">`IHttpClientFactory`W aplikacji można używać kilku sposobów:</span><span class="sxs-lookup"><span data-stu-id="49b5a-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="49b5a-564">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="49b5a-565">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="49b5a-566">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="49b5a-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="49b5a-567">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="49b5a-568">Żadna z nich nie jest ściśle wyższa od siebie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="49b5a-569">Najlepsze podejście zależy od ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="49b5a-570">Podstawowy sposób użycia</span><span class="sxs-lookup"><span data-stu-id="49b5a-570">Basic usage</span></span>

<span data-ttu-id="49b5a-571">`IHttpClientFactory`Można zarejestrować `AddHttpClient` , wywołując metodę rozszerzenia na `IServiceCollection` , wewnątrz `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="49b5a-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="49b5a-572">Po zarejestrowaniu kod może zaakceptować `IHttpClientFactory` usługi z dowolnego miejsca przy użyciu [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="49b5a-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="49b5a-573">`IHttpClientFactory`Można go użyć do utworzenia `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="49b5a-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="49b5a-574">Użycie `IHttpClientFactory` w ten sposób jest dobrym sposobem refaktoryzacji istniejącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="49b5a-575">Nie ma to wpływu na sposób `HttpClient` użycia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="49b5a-576">W miejscach, w których `HttpClient` są obecnie tworzone wystąpienia, Zastąp te wystąpienia wywołaniem <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="49b5a-577">Nazwani klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-577">Named clients</span></span>

<span data-ttu-id="49b5a-578">Jeśli aplikacja wymaga wielu odrębnych użycia `HttpClient` , z których każdy ma inną konfigurację, opcja służy do korzystania z **nazwanych klientów**.</span><span class="sxs-lookup"><span data-stu-id="49b5a-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="49b5a-579">Konfigurację dla nazwy `HttpClient` można określić podczas rejestracji w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="49b5a-580">W powyższym kodzie `AddHttpClient` jest wywoływana, dostarczając nazwę *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="49b5a-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="49b5a-581">Ten klient ma zainstalowaną domyślną konfigurację &mdash; , a w tym adres podstawowy i dwa nagłówki wymagane do pracy z interfejsem API usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="49b5a-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="49b5a-582">Przy każdym `CreateClient` wywołaniu jest tworzone nowe wystąpienie programu `HttpClient` i zostanie wywołana akcja konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="49b5a-583">Aby użyć nazwanego klienta, parametr ciągu może być przekazaniem do `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="49b5a-584">Określ nazwę klienta, który ma zostać utworzony:</span><span class="sxs-lookup"><span data-stu-id="49b5a-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="49b5a-585">W powyższym kodzie żądanie nie musi określać nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="49b5a-586">Można przekazać tylko ścieżkę, ponieważ jest używany adres podstawowy skonfigurowany dla klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="49b5a-587">Wpisane komputery klienckie</span><span class="sxs-lookup"><span data-stu-id="49b5a-587">Typed clients</span></span>

<span data-ttu-id="49b5a-588">Wpisane komputery klienckie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-588">Typed clients:</span></span>

* <span data-ttu-id="49b5a-589">Podaj te same możliwości co nazwanych klientów bez konieczności używania ciągów jako kluczy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="49b5a-590">Zapewnia obsługę funkcji IntelliSense i kompilatora podczas konsumowania klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="49b5a-591">Podaj jedną lokalizację do konfiguracji i współpracy z konkretną `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="49b5a-592">Na przykład pojedynczy klient z określonym typem może być używany w przypadku pojedynczego punktu końcowego zaplecza i hermetyzował wszystkie logike związane z tym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="49b5a-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="49b5a-593">Pracuj z opcją DI i można ją wstrzyknąć, gdy jest to wymagane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="49b5a-594">Klient z określonym typem akceptuje `HttpClient` parametr w jego konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="49b5a-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="49b5a-595">W poprzednim kodzie konfiguracja jest przenoszona do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="49b5a-596">`HttpClient`Obiekt jest ujawniany jako właściwość publiczna.</span><span class="sxs-lookup"><span data-stu-id="49b5a-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="49b5a-597">Istnieje możliwość zdefiniowania metod specyficznych dla interfejsu API, które uwidaczniają `HttpClient` funkcjonalność.</span><span class="sxs-lookup"><span data-stu-id="49b5a-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="49b5a-598">`GetAspNetDocsIssues`Metoda hermetyzuje kod potrzebny do zbadania i wyanalizowania najnowszych otwartych problemów z repozytorium GitHub.</span><span class="sxs-lookup"><span data-stu-id="49b5a-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="49b5a-599">Aby zarejestrować klienta z określonym typem, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> można użyć generycznej metody rozszerzenia w programie `Startup.ConfigureServices` , określając klasę klienta z określonym typem:</span><span class="sxs-lookup"><span data-stu-id="49b5a-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="49b5a-600">Typ klienta jest rejestrowany jako przejściowy z DI.</span><span class="sxs-lookup"><span data-stu-id="49b5a-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="49b5a-601">Określony klient może zostać dodany i wykorzystany bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="49b5a-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="49b5a-602">Jeśli jest preferowana, konfiguracja dla klienta z określonym typem może być określona podczas rejestracji w `Startup.ConfigureServices` , a nie w konstruktorze określonego klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="49b5a-603">Można całkowicie hermetyzować `HttpClient` w ramach określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="49b5a-604">Zamiast uwidaczniać je jako właściwość, można dostarczyć metody publiczne, które wywołują `HttpClient` wystąpienie wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="49b5a-605">W poprzednim kodzie, `HttpClient` jest przechowywany jako pole prywatne.</span><span class="sxs-lookup"><span data-stu-id="49b5a-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="49b5a-606">Cały dostęp do wykonywania wywołań zewnętrznych odbywa się za pomocą `GetRepos` metody.</span><span class="sxs-lookup"><span data-stu-id="49b5a-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="49b5a-607">Wygenerowane klienci</span><span class="sxs-lookup"><span data-stu-id="49b5a-607">Generated clients</span></span>

<span data-ttu-id="49b5a-608">`IHttpClientFactory`może być używany w połączeniu z innymi bibliotekami innych firm, takimi jak [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="49b5a-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="49b5a-609">REFIT to biblioteka REST dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="49b5a-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="49b5a-610">Konwertuje interfejsy API REST na interfejsy na żywo.</span><span class="sxs-lookup"><span data-stu-id="49b5a-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="49b5a-611">Implementacja interfejsu jest generowana dynamicznie przez `RestService` , przy użyciu polecenia, `HttpClient` Aby utworzyć zewnętrzne wywołania http.</span><span class="sxs-lookup"><span data-stu-id="49b5a-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="49b5a-612">Interfejs i odpowiedź są zdefiniowane do reprezentowania zewnętrznego interfejsu API i jego odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="49b5a-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="49b5a-613">Można dodać klienta z określonym typem, używając REFIT do wygenerowania implementacji:</span><span class="sxs-lookup"><span data-stu-id="49b5a-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="49b5a-614">Zdefiniowany interfejs może być używany, w razie potrzeby, z implementacją podaną przez DI i REFIT:</span><span class="sxs-lookup"><span data-stu-id="49b5a-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="49b5a-615">Oprogramowanie pośredniczące żądania wychodzące</span><span class="sxs-lookup"><span data-stu-id="49b5a-615">Outgoing request middleware</span></span>

<span data-ttu-id="49b5a-616">`HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="49b5a-617">`IHttpClientFactory`Ułatwia definiowanie programów obsługi do zastosowania dla każdego nazwanego klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="49b5a-618">Obsługuje rejestrację i łańcuchowanie wielu programów obsługi w celu utworzenia potoku pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="49b5a-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="49b5a-619">Każdy z tych programów obsługi jest w stanie wykonać zadania przed i po żądaniu wychodzącym.</span><span class="sxs-lookup"><span data-stu-id="49b5a-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="49b5a-620">Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49b5a-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="49b5a-621">Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="49b5a-622">Aby utworzyć procedurę obsługi, zdefiniuj klasę pochodną z elementu <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="49b5a-623">Zastąp `SendAsync` metodę w celu wykonania kodu przed przekazaniem żądania do następnego programu obsługi w potoku:</span><span class="sxs-lookup"><span data-stu-id="49b5a-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="49b5a-624">Poprzedni kod definiuje procedurę obsługi podstawowej.</span><span class="sxs-lookup"><span data-stu-id="49b5a-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="49b5a-625">Sprawdza, czy `X-API-KEY` nagłówek został uwzględniony w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="49b5a-626">Jeśli brakuje nagłówka, można uniknąć wywołania HTTP i zwrócić odpowiednią odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="49b5a-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="49b5a-627">Podczas rejestracji do konfiguracji można dodać co najmniej jeden program obsługi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="49b5a-628">To zadanie jest realizowane za pośrednictwem metod rozszerzających na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="49b5a-629">W poprzednim kodzie, `ValidateHeaderHandler` jest zarejestrowany przy użyciu di.</span><span class="sxs-lookup"><span data-stu-id="49b5a-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="49b5a-630">Procedura obsługi **musi** być zarejestrowana w programie di jako przejściowa usługa, nigdy nie jest objęta zakresem.</span><span class="sxs-lookup"><span data-stu-id="49b5a-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="49b5a-631">Jeśli program obsługi jest zarejestrowany jako usługa objęta zakresem, a wszystkie usługi, od których zależy program obsługi, są jednorazowe:</span><span class="sxs-lookup"><span data-stu-id="49b5a-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="49b5a-632">Usługi programu obsługi mogły zostać zlikwidowane, zanim program obsługi znajdzie się poza zakresem.</span><span class="sxs-lookup"><span data-stu-id="49b5a-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="49b5a-633">Usunięte usługi obsługi powodują niepowodzenie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="49b5a-634">Po zarejestrowaniu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> można wywołać, przekazując w typie procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="49b5a-635">Wiele programów obsługi można zarejestrować w kolejności, w jakiej powinny być wykonywane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="49b5a-636">Każdy program obsługi otacza następną procedurę obsługi do momentu zakończenia `HttpClientHandler` żądania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="49b5a-637">Użyj jednego z następujących metod, aby udostępnić stan dla żądania za pomocą programów obsługi komunikatów:</span><span class="sxs-lookup"><span data-stu-id="49b5a-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="49b5a-638">Przekaż dane do procedury obsługi przy użyciu polecenia `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="49b5a-639">Użyj, `IHttpContextAccessor` Aby uzyskać dostęp do bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="49b5a-640">Utwórz niestandardowy `AsyncLocal` obiekt magazynu, aby przekazać dane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="49b5a-641">Korzystanie z programów obsługi opartych na Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-641">Use Polly-based handlers</span></span>

<span data-ttu-id="49b5a-642">`IHttpClientFactory`integruje się z popularną biblioteką innej firmy o nazwie [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="49b5a-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="49b5a-643">Polly to kompleksowa i przejściowa Biblioteka obsługi błędów dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="49b5a-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="49b5a-644">Pozwala to deweloperom na tworzenie zasad, takich jak ponawianie próby, wyłączniki, przekroczenie limitu czasu, izolacja grodziowa i rezerwa w sposób bezpieczny dla bezpieczeństwa i bezpiecznego wątkowo.</span><span class="sxs-lookup"><span data-stu-id="49b5a-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="49b5a-645">Dostarczone metody rozszerzające umożliwiają korzystanie z zasad Pollyymi ze skonfigurowanymi `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-646">Rozszerzenia Polly:</span><span class="sxs-lookup"><span data-stu-id="49b5a-646">The Polly extensions:</span></span>

* <span data-ttu-id="49b5a-647">Obsługa dodawania programów obsługi opartych na Pollych do klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="49b5a-648">Można go użyć po zainstalowaniu pakietu NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="49b5a-649">Pakiet nie znajduje się w ASP.NET Core udostępnionej platformy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="49b5a-650">Obsługa błędów przejściowych</span><span class="sxs-lookup"><span data-stu-id="49b5a-650">Handle transient faults</span></span>

<span data-ttu-id="49b5a-651">Najczęstsze błędy występują, gdy zewnętrzne wywołania HTTP są przejściowe.</span><span class="sxs-lookup"><span data-stu-id="49b5a-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="49b5a-652">Dołączono wygodną metodę rozszerzenia `AddTransientHttpErrorPolicy` , która umożliwia zdefiniowanie zasad w celu obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="49b5a-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="49b5a-653">Zasady skonfigurowane przy użyciu tego uchwytu metody rozszerzenia `HttpRequestException` , odpowiedzi HTTP 5xx i odpowiedzi http 408.</span><span class="sxs-lookup"><span data-stu-id="49b5a-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="49b5a-654">`AddTransientHttpErrorPolicy`Rozszerzenie może być używane w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="49b5a-655">Rozszerzenie zapewnia dostęp do `PolicyBuilder` obiektu skonfigurowanego do obsługi błędów reprezentujących możliwy błąd przejściowy:</span><span class="sxs-lookup"><span data-stu-id="49b5a-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="49b5a-656">W poprzednim kodzie `WaitAndRetryAsync` zdefiniowane są zasady.</span><span class="sxs-lookup"><span data-stu-id="49b5a-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="49b5a-657">Żądania zakończone niepowodzeniem są ponawiane do trzech razy z opóźnieniem 600 MS między próbami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="49b5a-658">Dynamiczne Wybieranie zasad</span><span class="sxs-lookup"><span data-stu-id="49b5a-658">Dynamically select policies</span></span>

<span data-ttu-id="49b5a-659">Istnieją dodatkowe metody rozszerzające, których można użyć do dodawania programów obsługi opartych na Polly.</span><span class="sxs-lookup"><span data-stu-id="49b5a-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="49b5a-660">Jedno takie rozszerzenie ma `AddPolicyHandler` wiele przeciążeń.</span><span class="sxs-lookup"><span data-stu-id="49b5a-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="49b5a-661">Jedno Przeciążenie umożliwia sprawdzenie żądania podczas definiowania zasad, które mają zostać zastosowane:</span><span class="sxs-lookup"><span data-stu-id="49b5a-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="49b5a-662">W poprzednim kodzie, jeśli żądanie wychodzące jest GET HTTP, zostanie zastosowany 10-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="49b5a-663">Dla każdej innej metody HTTP jest używany 30-sekundowy limit czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="49b5a-664">Dodaj wiele programów obsługi Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="49b5a-665">Jest to typowy sposób zagnieżdżania zasad Pollyymi w celu zapewnienia zwiększonej funkcjonalności:</span><span class="sxs-lookup"><span data-stu-id="49b5a-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="49b5a-666">W poprzednim przykładzie dodawane są dwa programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="49b5a-667">Pierwsze używa rozszerzenia, `AddTransientHttpErrorPolicy` Aby dodać zasady ponawiania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="49b5a-668">Nieudane żądania są ponawiane do trzech razy.</span><span class="sxs-lookup"><span data-stu-id="49b5a-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="49b5a-669">Drugie wywołanie `AddTransientHttpErrorPolicy` dodaje zasady wyłącznika.</span><span class="sxs-lookup"><span data-stu-id="49b5a-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="49b5a-670">Dalsze żądania zewnętrzne są blokowane przez 30 sekund, jeśli pięć nieudanych prób wystąpi sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="49b5a-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="49b5a-671">Zasady wyłącznika są stanowe.</span><span class="sxs-lookup"><span data-stu-id="49b5a-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="49b5a-672">Wszystkie wywołania przez tego klienta współdzielą ten sam stan obwodu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="49b5a-673">Dodawanie zasad z rejestru Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="49b5a-674">Podejście do zarządzania regularnie używanymi zasadami polega na ich definiowaniu i zarejestrowaniu za pomocą `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="49b5a-675">Zapewniana jest metoda rozszerzająca, która umożliwia dodanie programu obsługi przy użyciu zasad z rejestru:</span><span class="sxs-lookup"><span data-stu-id="49b5a-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="49b5a-676">W poprzednim kodzie dwie zasady są rejestrowane po `PolicyRegistry` dodaniu do `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="49b5a-677">Aby użyć zasad z rejestru, `AddPolicyHandlerFromRegistry` Metoda jest używana, przekazując nazwę zasad do zastosowania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="49b5a-678">Więcej informacji na temat integracji z programem `IHttpClientFactory` i Polly można znaleźć w witrynie [typu wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="49b5a-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="49b5a-679">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="49b5a-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="49b5a-680">Nowe `HttpClient` wystąpienie jest zwracane za każdym razem `CreateClient` , gdy jest wywoływana w `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="49b5a-681">Istnieje <xref:System.Net.Http.HttpMessageHandler> za nazwany klient.</span><span class="sxs-lookup"><span data-stu-id="49b5a-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="49b5a-682">Fabryka zarządza okresami istnienia `HttpMessageHandler` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="49b5a-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="49b5a-683">`IHttpClientFactory`pule `HttpMessageHandler` wystąpień tworzonych przez fabrykę w celu zmniejszenia zużycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="49b5a-684">`HttpMessageHandler`Wystąpienie może być ponownie używane z puli podczas tworzenia nowego `HttpClient` wystąpienia, jeśli jego okres istnienia nie wygasł.</span><span class="sxs-lookup"><span data-stu-id="49b5a-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="49b5a-685">Buforowanie programów obsługi jest pożądane, ponieważ każdy program obsługi zazwyczaj zarządza własnymi połączeniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="49b5a-686">Utworzenie większej liczby programów obsługi niż to konieczne może skutkować opóźnieniami połączeń.</span><span class="sxs-lookup"><span data-stu-id="49b5a-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="49b5a-687">Niektóre programy obsługi powodują również, że połączenia są otwarte w nieskończoność, co może uniemożliwić obsłużenie zmian DNS przez program obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="49b5a-688">Domyślny okres istnienia programu obsługi wynosi dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="49b5a-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="49b5a-689">Wartość domyślną można przesłonić na podstawie nazwy klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="49b5a-690">Aby zastąpić ten element, wywołaj metodę <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` zwracaną podczas tworzenia klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="49b5a-691">Usuwanie klienta nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="49b5a-692">Likwidacja anuluje żądania wychodzące i gwarantuje, że danego `HttpClient` wystąpienia nie można użyć po wywołaniu <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="49b5a-693">`IHttpClientFactory`śledzi i usuwa zasoby używane przez `HttpClient` wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-694">`HttpClient`Wystąpienia mogą być zwykle traktowane jako obiekty .NET, które nie wymagają usuwania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="49b5a-695">Utrzymywanie pojedynczego `HttpClient` wystąpienia aktywności przez długi czas jest typowym wzorcem używanym przed rozpoczęciem `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="49b5a-696">Ten wzorzec będzie niepotrzebny po migracji do programu `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="49b5a-697">Alternatywy do IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="49b5a-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="49b5a-698">Używanie `IHttpClientFactory` w aplikacji z obsługą podwójnego zapobiegania:</span><span class="sxs-lookup"><span data-stu-id="49b5a-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="49b5a-699">Problemy z wyczerpaniem zasobów przez `HttpMessageHandler` wystąpienia puli.</span><span class="sxs-lookup"><span data-stu-id="49b5a-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="49b5a-700">Nieodświeżone problemy z usługą DNS przez cykliczne `HttpMessageHandler` wystąpienia w regularnych odstępach czasu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="49b5a-701">Istnieją alternatywne sposoby rozwiązywania powyższych problemów przy użyciu wystąpienia długotrwałego <xref:System.Net.Http.SocketsHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="49b5a-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="49b5a-702">Utwórz wystąpienie, `SocketsHttpHandler` gdy aplikacja zostanie uruchomiona i użyje jej na potrzeby życia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="49b5a-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="49b5a-703">Skonfiguruj <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> odpowiednią wartość na podstawie czasu odświeżania DNS.</span><span class="sxs-lookup"><span data-stu-id="49b5a-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="49b5a-704">Utwórz `HttpClient` wystąpienia przy użyciu programu `new HttpClient(handler, disposeHandler: false)` zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="49b5a-705">Powyższe podejścia rozwiązują problemy z zarządzaniem zasobami, które `IHttpClientFactory` rozwiązują się w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="49b5a-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="49b5a-706">`SocketsHttpHandler`Udostępnia połączenia między `HttpClient` wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="49b5a-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="49b5a-707">To udostępnianie uniemożliwia wyczerpanie gniazda.</span><span class="sxs-lookup"><span data-stu-id="49b5a-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="49b5a-708">`SocketsHttpHandler`Cykle połączeń zgodnie z, aby `PooledConnectionLifetime` uniknąć nieodświeżonych problemów z usługą DNS.</span><span class="sxs-lookup"><span data-stu-id="49b5a-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="49b5a-709">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="49b5a-709">Cookies</span></span>

<span data-ttu-id="49b5a-710">`HttpMessageHandler`Wystąpienia w puli powoduje, że `CookieContainer` obiekty są udostępniane.</span><span class="sxs-lookup"><span data-stu-id="49b5a-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="49b5a-711">Nieoczekiwane `CookieContainer` udostępnianie obiektów często prowadzi do niepoprawnego kodu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="49b5a-712">W przypadku aplikacji wymagających plików cookie należy rozważyć następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="49b5a-713">Wyłączanie obsługi automatycznej plików cookie</span><span class="sxs-lookup"><span data-stu-id="49b5a-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="49b5a-714">Unikanie`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="49b5a-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="49b5a-715">Wywołaj <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> , aby wyłączyć automatyczną obsługę plików cookie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="49b5a-716">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="49b5a-716">Logging</span></span>

<span data-ttu-id="49b5a-717">Klienci utworzeni za pomocą `IHttpClientFactory` rejestrowania komunikatów dzienników dla wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="49b5a-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="49b5a-718">Włącz odpowiedni poziom informacji w konfiguracji rejestrowania, aby wyświetlić domyślne komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="49b5a-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="49b5a-719">Dodatkowe rejestrowanie, takie jak rejestrowanie nagłówków żądań, jest uwzględniane tylko na poziomie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="49b5a-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="49b5a-720">Kategoria dziennika używana dla każdego klienta zawiera nazwę klienta programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="49b5a-721">Klient o nazwie *MyNamedClient*, na przykład rejestruje komunikaty z kategorią `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="49b5a-722">Komunikaty z sufiksem *LogicalHandler* występują poza potokiem obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="49b5a-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="49b5a-723">Na żądanie komunikaty są rejestrowane przed przetworzeniem przez inne procedury obsługi w potoku.</span><span class="sxs-lookup"><span data-stu-id="49b5a-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="49b5a-724">W odpowiedzi komunikaty są rejestrowane po odebraniu odpowiedzi przez inne programy obsługi potoków.</span><span class="sxs-lookup"><span data-stu-id="49b5a-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="49b5a-725">Rejestrowanie odbywa się również w potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="49b5a-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="49b5a-726">W przykładzie *MyNamedClient* te komunikaty są rejestrowane w kategorii dzienników `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="49b5a-727">W przypadku żądania dzieje się tak po uruchomieniu wszystkich innych programów obsługi i natychmiast przed wysłaniem żądania w sieci.</span><span class="sxs-lookup"><span data-stu-id="49b5a-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="49b5a-728">W odpowiedzi to rejestrowanie obejmuje stan odpowiedzi, zanim przejdzie do powrotem za pomocą potoku programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="49b5a-729">Włączenie rejestrowania poza i wewnątrz potoku umożliwia inspekcję zmian wprowadzonych przez inne programy obsługi potoku.</span><span class="sxs-lookup"><span data-stu-id="49b5a-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="49b5a-730">Może to obejmować zmiany nagłówków żądań, na przykład lub do kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="49b5a-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="49b5a-731">Dołączenie nazwy klienta w kategorii dziennika umożliwia filtrowanie dzienników dla określonych nazwanych klientów, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="49b5a-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="49b5a-732">Konfigurowanie HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="49b5a-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="49b5a-733">Może być konieczne sterowanie konfiguracją wewnętrznej `HttpMessageHandler` używanej przez klienta.</span><span class="sxs-lookup"><span data-stu-id="49b5a-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="49b5a-734">`IHttpClientBuilder`Jest zwracany podczas dodawania nazwanych lub wpisanych klientów.</span><span class="sxs-lookup"><span data-stu-id="49b5a-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="49b5a-735"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metoda rozszerzająca może służyć do definiowania delegata.</span><span class="sxs-lookup"><span data-stu-id="49b5a-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="49b5a-736">Delegat służy do tworzenia i konfigurowania głównej `HttpMessageHandler` używanej przez tego klienta:</span><span class="sxs-lookup"><span data-stu-id="49b5a-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="49b5a-737">Korzystanie z IHttpClientFactory w aplikacji konsolowej</span><span class="sxs-lookup"><span data-stu-id="49b5a-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="49b5a-738">W aplikacji konsoli Dodaj następujące odwołania do pakietu do projektu:</span><span class="sxs-lookup"><span data-stu-id="49b5a-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="49b5a-739">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="49b5a-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="49b5a-740">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="49b5a-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="49b5a-741">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="49b5a-741">In the following example:</span></span>

* <span data-ttu-id="49b5a-742"><xref:System.Net.Http.IHttpClientFactory>jest zarejestrowany w kontenerze usługi [hosta ogólnego](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="49b5a-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="49b5a-743">`MyService`tworzy wystąpienie fabryki klienta na podstawie usługi, która jest używana do tworzenia `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="49b5a-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="49b5a-744">`HttpClient`służy do pobierania strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="49b5a-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="49b5a-745">`Main`tworzy zakres do wykonania `GetPage` metody usługi i zapisuje pierwsze 500 znaków zawartości strony sieci Web w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="49b5a-746">Oprogramowanie pośredniczące propagacji nagłówka</span><span class="sxs-lookup"><span data-stu-id="49b5a-746">Header propagation middleware</span></span>

<span data-ttu-id="49b5a-747">Propagacja nagłówka to społeczność obsługiwana przez oprogramowanie pośredniczące do propagowania nagłówków HTTP z przychodzącego żądania do wychodzących żądań klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="49b5a-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="49b5a-748">Aby użyć propagacji nagłówka:</span><span class="sxs-lookup"><span data-stu-id="49b5a-748">To use header propagation:</span></span>

* <span data-ttu-id="49b5a-749">Odwołuje się do obsługiwanego przez społeczność portu [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)pakietu.</span><span class="sxs-lookup"><span data-stu-id="49b5a-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="49b5a-750">ASP.NET Core 3,1 i nowsze obsługuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="49b5a-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="49b5a-751">Skonfiguruj oprogramowanie pośredniczące i `HttpClient` w programie `Startup` :</span><span class="sxs-lookup"><span data-stu-id="49b5a-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="49b5a-752">Klient zawiera skonfigurowane nagłówki w żądaniach wychodzących:</span><span class="sxs-lookup"><span data-stu-id="49b5a-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="49b5a-753">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="49b5a-753">Additional resources</span></span>

* [<span data-ttu-id="49b5a-754">Używanie elementu HttpClientFactory do implementowania odpornych na błędy żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="49b5a-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="49b5a-755">Zaimplementuj ponowne próby wywołania HTTP przy użyciu wykładniczej wycofywania z zasadami HttpClientFactory i Polly</span><span class="sxs-lookup"><span data-stu-id="49b5a-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="49b5a-756">Implementowanie wzorca wyłącznika</span><span class="sxs-lookup"><span data-stu-id="49b5a-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
