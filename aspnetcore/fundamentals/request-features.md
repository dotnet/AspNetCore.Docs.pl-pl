---
title: Żądaj funkcji w ASP.NET Core
author: ardalis
description: Dowiedz się więcej o szczegółach implementacji serwera sieci Web związanych z żądaniami HTTP i odpowiedziami zdefiniowanymi w interfejsach dla ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416227"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="d9dd1-103">Żądaj funkcji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9dd1-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="d9dd1-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d9dd1-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d9dd1-105">Szczegóły implementacji serwera sieci Web związane z żądaniami i odpowiedziami HTTP są definiowane w interfejsach.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="d9dd1-106">Interfejsy te są używane przez implementacje serwera i oprogramowanie pośredniczące do tworzenia i modyfikowania potoku hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="d9dd1-107">Interfejsy funkcji</span><span class="sxs-lookup"><span data-stu-id="d9dd1-107">Feature interfaces</span></span>

<span data-ttu-id="d9dd1-108">ASP.NET Core definiuje szereg interfejsów funkcji HTTP, w `Microsoft.AspNetCore.Http.Features` których są używane przez serwery do identyfikowania obsługiwanych przez nie funkcji.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="d9dd1-109">Następujące interfejsy funkcji obsługują żądania i zwracaj odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="d9dd1-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="d9dd1-110">`IHttpRequestFeature`Definiuje strukturę żądania HTTP, w tym protokołu, ścieżki, ciągu zapytania, nagłówków i treści.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="d9dd1-111">`IHttpResponseFeature`Definiuje strukturę odpowiedzi HTTP, w tym kod stanu, nagłówki i treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="d9dd1-112">`IHttpAuthenticationFeature`Definiuje obsługę identyfikowania użytkowników `ClaimsPrincipal` na podstawie i określania obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="d9dd1-113">`IHttpUpgradeFeature`Definiuje obsługę [uaktualnień HTTP,](https://tools.ietf.org/html/rfc2616.html#section-14.42)które umożliwiają klientowi określenie, które dodatkowe protokoły, których chciałby użyć, jeśli serwer chce przełączać protokoły.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="d9dd1-114">`IHttpBufferingFeature`Definiuje metody wyłączania buforowania żądań i/lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="d9dd1-115">`IHttpConnectionFeature`Definiuje właściwości dla adresów i portów lokalnych i zdalnych.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="d9dd1-116">`IHttpRequestLifetimeFeature`Definiuje obsługę przerywania połączeń lub wykrywania, czy żądanie zostało zakończone przedwcześnie, na przykład przez rozłączenie klienta.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="d9dd1-117">`IHttpSendFileFeature`Definiuje metodę asynchronicznie wysyłania plików.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="d9dd1-118">`IHttpWebSocketFeature`Definiuje interfejs API do obsługi gniazd internetowych.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="d9dd1-119">`IHttpRequestIdentifierFeature`Dodaje właściwość, która może być zaimplementowana do jednoznacznej identyfikacji żądań.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="d9dd1-120">`ISessionFeature`Definiuje `ISessionFactory` i `ISession` abstrakcje do obsługi sesji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="d9dd1-121">`ITlsConnectionFeature`Definiuje interfejs API do pobierania certyfikatów klientów.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="d9dd1-122">`ITlsTokenBindingFeature`Definiuje metody pracy z parametrami wiązania tokenu TLS.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="d9dd1-123">`ISessionFeature`nie jest funkcją serwera, ale jest `SessionMiddleware` implementowana przez (zobacz [Zarządzanie stanem aplikacji](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="d9dd1-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="d9dd1-124">Kolekcje funkcji</span><span class="sxs-lookup"><span data-stu-id="d9dd1-124">Feature collections</span></span>

<span data-ttu-id="d9dd1-125">Właściwość `Features` `HttpContext` udostępnia interfejs do uzyskiwania i ustawiania dostępnych funkcji HTTP dla bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="d9dd1-126">Ponieważ kolekcja funkcji jest modyfikowalna nawet w kontekście żądania, oprogramowanie pośredniczące może służyć do modyfikowania kolekcji i dodawania obsługi dodatkowych funkcji.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="d9dd1-127">Oprogramowanie pośredniczące i funkcje żądania</span><span class="sxs-lookup"><span data-stu-id="d9dd1-127">Middleware and request features</span></span>

<span data-ttu-id="d9dd1-128">Podczas gdy serwery są odpowiedzialne za tworzenie kolekcji funkcji, oprogramowanie pośredniczące można zarówno dodać do tej kolekcji i zużywają funkcje z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="d9dd1-129">Na przykład `StaticFileMiddleware` uzyskuje dostęp `IHttpSendFileFeature` do funkcji.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="d9dd1-130">Jeśli funkcja istnieje, jest używana do wysyłania żądanego pliku statycznego ze ścieżki fizycznej.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="d9dd1-131">W przeciwnym razie do wysyłania pliku używana jest wolniejsza metoda alternatywna.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="d9dd1-132">Jeśli jest `IHttpSendFileFeature` to możliwe, system operacyjny może otworzyć plik i wykonać bezpośrednią kopię w trybie jądra na kartę sieciową.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="d9dd1-133">Ponadto oprogramowanie pośredniczące można dodać do kolekcji funkcji ustanowionych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="d9dd1-134">Istniejące funkcje można nawet zastąpić oprogramowaniem pośredniczącym, dzięki czemu oprogramowanie pośredniczące rozszerza funkcjonalność serwera.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="d9dd1-135">Funkcje dodane do kolekcji są natychmiast dostępne dla innego oprogramowania pośredniczącego lub podstawowej aplikacji się później w potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="d9dd1-136">Łącząc implementacje serwerów niestandardowych i określone ulepszenia oprogramowania pośredniczącego, można utworzyć dokładny zestaw funkcji, które wymaga aplikacja.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="d9dd1-137">Dzięki temu brakujące funkcje mogą być dodawane bez konieczności zmiany serwera i zapewnia tylko minimalną ilość funkcji są widoczne, co ogranicza obszar ataku i zwiększenie wydajności.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="d9dd1-138">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="d9dd1-138">Summary</span></span>

<span data-ttu-id="d9dd1-139">Interfejsy funkcji definiują określone funkcje HTTP, które mogą obsługiwać dane żądanie.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="d9dd1-140">Serwery definiują kolekcje funkcji i początkowy zestaw funkcji obsługiwanych przez ten serwer, ale oprogramowanie pośredniczące może służyć do ulepszania tych funkcji.</span><span class="sxs-lookup"><span data-stu-id="d9dd1-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9dd1-141">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d9dd1-141">Additional resources</span></span>

* [<span data-ttu-id="d9dd1-142">Serwery</span><span class="sxs-lookup"><span data-stu-id="d9dd1-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="d9dd1-143">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="d9dd1-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d9dd1-144">Otwarty interfejs internetowy dla platformy .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="d9dd1-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
