---
title: Zażądaj funkcji w ASP.NET Core
author: ardalis
description: Dowiedz się więcej na temat szczegółów implementacji serwera sieci Web związanych z żądaniami HTTP i odpowiedziami, które są zdefiniowane w interfejsach dla ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: 21e78c6c1d05eb3b2ce8ade1b950b15823fa0e83
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407281"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="c79ca-103">Zażądaj funkcji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c79ca-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="c79ca-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c79ca-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c79ca-105">Szczegóły implementacji serwera sieci Web związane z żądaniami HTTP i odpowiedziami są zdefiniowane w interfejsach.</span><span class="sxs-lookup"><span data-stu-id="c79ca-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="c79ca-106">Te interfejsy są używane przez implementacje serwera i oprogramowanie pośredniczące do tworzenia i modyfikowania potoku hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c79ca-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="c79ca-107">Interfejsy funkcji</span><span class="sxs-lookup"><span data-stu-id="c79ca-107">Feature interfaces</span></span>

<span data-ttu-id="c79ca-108">ASP.NET Core definiuje wiele interfejsów funkcji HTTP `Microsoft.AspNetCore.Http.Features` , które są używane przez serwery do identyfikowania obsługiwanych funkcji.</span><span class="sxs-lookup"><span data-stu-id="c79ca-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="c79ca-109">Poniższe interfejsy funkcji obsługują żądania i zwracają odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="c79ca-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="c79ca-110">`IHttpRequestFeature`Definiuje strukturę żądania HTTP, łącznie z protokołem, ścieżką, ciągiem zapytania, nagłówkami i treścią.</span><span class="sxs-lookup"><span data-stu-id="c79ca-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="c79ca-111">`IHttpResponseFeature`Definiuje strukturę odpowiedzi HTTP, w tym kod stanu, nagłówki i treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c79ca-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="c79ca-112">`IHttpAuthenticationFeature`Definiuje obsługę identyfikacji użytkowników na podstawie `ClaimsPrincipal` i określania procedury obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="c79ca-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="c79ca-113">`IHttpUpgradeFeature`Definiuje obsługę [uaktualnień http](https://tools.ietf.org/html/rfc2616.html#section-14.42), które umożliwiają klientowi określenie dodatkowych protokołów, które mają być używane, jeśli serwer chce przełączyć protokoły.</span><span class="sxs-lookup"><span data-stu-id="c79ca-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="c79ca-114">`IHttpBufferingFeature`Definiuje metody służące do wyłączania buforowania żądań i/lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c79ca-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="c79ca-115">`IHttpConnectionFeature`Definiuje właściwości adresów i portów lokalnych i zdalnych.</span><span class="sxs-lookup"><span data-stu-id="c79ca-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="c79ca-116">`IHttpRequestLifetimeFeature`Definiuje obsługę przerywania połączeń lub wykrywanie, czy żądanie zostało zakończone przedwcześnie, na przykład przez odłączenie klienta.</span><span class="sxs-lookup"><span data-stu-id="c79ca-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="c79ca-117">`IHttpSendFileFeature`Definiuje metodę asynchronicznego wysyłania plików.</span><span class="sxs-lookup"><span data-stu-id="c79ca-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="c79ca-118">`IHttpWebSocketFeature`Definiuje interfejs API do obsługi gniazd sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c79ca-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="c79ca-119">`IHttpRequestIdentifierFeature`Dodaje właściwość, która może zostać zaimplementowana w celu jednoznacznego identyfikowania żądań.</span><span class="sxs-lookup"><span data-stu-id="c79ca-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="c79ca-120">`ISessionFeature`Definiuje `ISessionFactory` i `ISession` abstrakcje dla pomocniczych sesji użytkowników.</span><span class="sxs-lookup"><span data-stu-id="c79ca-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="c79ca-121">`ITlsConnectionFeature`Definiuje interfejs API do pobierania certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="c79ca-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="c79ca-122">`ITlsTokenBindingFeature`Definiuje metody pracy z parametrami powiązania tokenu TLS.</span><span class="sxs-lookup"><span data-stu-id="c79ca-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="c79ca-123">`ISessionFeature`nie jest funkcją serwera, ale jest zaimplementowana przez program `SessionMiddleware` (zobacz [Zarządzanie stanem aplikacji](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="c79ca-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="c79ca-124">Kolekcje funkcji</span><span class="sxs-lookup"><span data-stu-id="c79ca-124">Feature collections</span></span>

<span data-ttu-id="c79ca-125">`Features`Właściwość `HttpContext` zawiera interfejs do pobierania i ustawiania dostępnych funkcji http dla bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="c79ca-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="c79ca-126">Ponieważ kolekcja funkcji jest modyfikowalna nawet w kontekście żądania, oprogramowanie pośredniczące może służyć do modyfikowania kolekcji i dodawania obsługi dodatkowych funkcji.</span><span class="sxs-lookup"><span data-stu-id="c79ca-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="c79ca-127">Oprogramowanie pośredniczące i funkcje żądania</span><span class="sxs-lookup"><span data-stu-id="c79ca-127">Middleware and request features</span></span>

<span data-ttu-id="c79ca-128">Chociaż serwery są odpowiedzialne za tworzenie kolekcji funkcji, oprogramowanie pośredniczące może dodawać do tej kolekcji i korzystać z funkcji z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="c79ca-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="c79ca-129">Na przykład `StaticFileMiddleware` program uzyskuje dostęp do `IHttpSendFileFeature` funkcji.</span><span class="sxs-lookup"><span data-stu-id="c79ca-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="c79ca-130">Jeśli ta funkcja istnieje, jest używana do wysyłania żądanego pliku statycznego ze ścieżki fizycznej.</span><span class="sxs-lookup"><span data-stu-id="c79ca-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="c79ca-131">W przeciwnym razie do wysłania pliku zostanie użyta mniejsza Metoda alternatywna.</span><span class="sxs-lookup"><span data-stu-id="c79ca-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="c79ca-132">Jeśli jest dostępna, `IHttpSendFileFeature` umożliwia systemowi operacyjnemu otwarcie pliku i przeprowadzenie bezpośredniego kopiowania trybu jądra na kartę sieciową.</span><span class="sxs-lookup"><span data-stu-id="c79ca-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="c79ca-133">Ponadto oprogramowanie pośredniczące może dodać do kolekcji funkcji ustanowionej przez serwer.</span><span class="sxs-lookup"><span data-stu-id="c79ca-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="c79ca-134">Istniejące funkcje mogą być nawet zastępowane przez oprogramowanie pośredniczące, co pozwala oprogramowaniu pośredniczącemu rozszerzyć funkcjonalność serwera.</span><span class="sxs-lookup"><span data-stu-id="c79ca-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="c79ca-135">Funkcje dodane do kolekcji są natychmiast dostępne dla innych programów pośredniczących lub aplikacji bazowej w późniejszym czasie w potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="c79ca-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="c79ca-136">Łącząc niestandardowe implementacje serwera i konkretne udoskonalenia oprogramowania pośredniczącego, można utworzyć precyzyjny zestaw funkcji wymaganych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="c79ca-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="c79ca-137">Pozwala to na dodanie brakujących funkcji bez konieczności zmiany na serwerze i gwarantuje, że dostępna jest tylko minimalna liczba funkcji, co ogranicza obszar narażony na ataki i zwiększa wydajność.</span><span class="sxs-lookup"><span data-stu-id="c79ca-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="c79ca-138">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="c79ca-138">Summary</span></span>

<span data-ttu-id="c79ca-139">Interfejsy funkcji definiują określone funkcje HTTP, które mogą być obsługiwane przez dane żądanie.</span><span class="sxs-lookup"><span data-stu-id="c79ca-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="c79ca-140">Serwery definiują kolekcje funkcji i początkowy zestaw funkcji obsługiwanych przez ten serwer, ale oprogramowanie pośredniczące może służyć do ulepszania tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="c79ca-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c79ca-141">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c79ca-141">Additional resources</span></span>

* [<span data-ttu-id="c79ca-142">Serwery</span><span class="sxs-lookup"><span data-stu-id="c79ca-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="c79ca-143">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="c79ca-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c79ca-144">Otwarty interfejs internetowy dla platformy .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="c79ca-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
