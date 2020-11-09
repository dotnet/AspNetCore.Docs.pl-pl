---
title: Zażądaj funkcji w ASP.NET Core
author: ardalis
description: Dowiedz się więcej na temat szczegółów implementacji serwera sieci Web związanych z żądaniami HTTP i odpowiedziami, które są zdefiniowane w interfejsach dla ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053712"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="d5145-103">Zażądaj funkcji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5145-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="d5145-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d5145-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d5145-105">`HttpContext`Interfejs API używany przez aplikacje i oprogramowanie pośredniczące do przetwarzania żądań ma warstwę abstrakcji undernieth ją jako *interfejsy funkcji* .</span><span class="sxs-lookup"><span data-stu-id="d5145-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer undernieth it called *feature interfaces* .</span></span> <span data-ttu-id="d5145-106">Każdy interfejs funkcji zawiera szczegółowy podzestaw funkcji uwidocznionych przez program `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d5145-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="d5145-107">Te interfejsy mogą być dodawane, modyfikowane, opakowane, zastępowane lub nawet usuwane przez serwer lub oprogramowanie pośredniczące, gdy żądanie jest przetwarzane bez konieczności ponownego implementowania całego programu `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d5145-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="d5145-108">Mogą być również używane do makietowania funkcjonalności podczas testowania.</span><span class="sxs-lookup"><span data-stu-id="d5145-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="d5145-109">Kolekcje funkcji</span><span class="sxs-lookup"><span data-stu-id="d5145-109">Feature collections</span></span>

<span data-ttu-id="d5145-110"><xref:Microsoft.AspNetCore.Http.HttpContext.Features>Właściwość `HttpContext` zapewnia dostęp do kolekcji interfejsów funkcji dla bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="d5145-111">Ponieważ kolekcja funkcji jest modyfikowalna nawet w kontekście żądania, oprogramowanie pośredniczące może służyć do modyfikowania kolekcji i dodawania obsługi dodatkowych funkcji.</span><span class="sxs-lookup"><span data-stu-id="d5145-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="d5145-112">Niektóre funkcje zaawansowane są dostępne tylko przez uzyskanie dostępu do skojarzonego interfejsu za pomocą kolekcji funkcji.</span><span class="sxs-lookup"><span data-stu-id="d5145-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="d5145-113">Interfejsy funkcji</span><span class="sxs-lookup"><span data-stu-id="d5145-113">Feature interfaces</span></span>

<span data-ttu-id="d5145-114">ASP.NET Core definiuje wiele popularnych interfejsów funkcji protokołu HTTP w programie <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , które są współużytkowane przez różne serwery i oprogramowanie pośredniczące w celu zidentyfikowania obsługiwanych przez nich funkcji.</span><span class="sxs-lookup"><span data-stu-id="d5145-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="d5145-115">Serwery i oprogramowanie pośredniczące mogą również udostępniać własne interfejsy z dodatkowymi funkcjami.</span><span class="sxs-lookup"><span data-stu-id="d5145-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="d5145-116">Większość interfejsów funkcji zapewnia opcjonalną, bardziej uproszczoną funkcjonalność i skojarzone `HttpContext` z nimi interfejsy API zapewniają wartości domyślne, jeśli ta funkcja nie jest obecna.</span><span class="sxs-lookup"><span data-stu-id="d5145-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="d5145-117">Niektóre interfejsy są wskazane w poniższej zawartości zgodnie z wymaganiami, ponieważ zapewniają podstawowe funkcje żądań i odpowiedzi, które muszą zostać zaimplementowane w celu przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="d5145-118">Następujące interfejsy funkcji pochodzą z <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="d5145-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="d5145-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Określa strukturę żądania HTTP, łącznie z protokołem, ścieżką, ciągiem zapytania, nagłówkami i treścią.</span><span class="sxs-lookup"><span data-stu-id="d5145-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="d5145-120">Ta funkcja jest wymagana, aby przetwarzać żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="d5145-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Określa strukturę odpowiedzi HTTP, w tym kod stanu, nagłówki i treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d5145-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="d5145-122">Ta funkcja jest wymagana, aby przetwarzać żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5145-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Definiuje różne sposoby pisania treści odpowiedzi przy użyciu `Stream` , a `PipeWriter` lub pliku.</span><span class="sxs-lookup"><span data-stu-id="d5145-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="d5145-124">Ta funkcja jest wymagana, aby przetwarzać żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="d5145-125">Spowoduje to zastąpienie `IHttpResponseFeature.Body` i `IHttpSendFileFeature` .</span><span class="sxs-lookup"><span data-stu-id="d5145-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="d5145-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Zawiera <xref:System.Security.Claims.ClaimsPrincipal> aktualnie skojarzone z żądaniem.</span><span class="sxs-lookup"><span data-stu-id="d5145-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="d5145-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Służy do analizowania i buforowania przychodzących przesłanych formularzy HTTP i wieloczęściowego.</span><span class="sxs-lookup"><span data-stu-id="d5145-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d5145-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Służy do kontrolowania, czy synchroniczne operacje we/wy są dozwolone dla treści żądania lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d5145-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5145-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Definiuje metody służące do wyłączania buforowania żądań i/lub odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="d5145-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="d5145-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Definiuje właściwości identyfikatora połączenia oraz adresów i portów lokalnych i zdalnych.</span><span class="sxs-lookup"><span data-stu-id="d5145-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d5145-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Określa maksymalny dozwolony rozmiar treści żądania dla bieżącego żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d5145-132">`IHttpRequestBodyDetectionFeature`: Wskazuje, czy żądanie może mieć treść.</span><span class="sxs-lookup"><span data-stu-id="d5145-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="d5145-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Dodaje właściwość, która może zostać zaimplementowana w celu jednoznacznego identyfikowania żądań.</span><span class="sxs-lookup"><span data-stu-id="d5145-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="d5145-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Definiuje obsługę przerywania połączeń lub wykrywania, czy żądanie zostało zakończone przedwcześnie, na przykład przez odłączenie klienta.</span><span class="sxs-lookup"><span data-stu-id="d5145-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5145-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Zapewnia dostęp do nagłówków naczepy żądania (jeśli istnieją).</span><span class="sxs-lookup"><span data-stu-id="d5145-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="d5145-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Służy do wysyłania komunikatów resetowania dla protokołów, które obsługują je, takich jak HTTP/2 lub HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="d5145-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d5145-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Umożliwia aplikacji dostarczanie nagłówków przyczepy odpowiedzi, jeśli są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d5145-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5145-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Definiuje metodę asynchronicznego wysyłania plików.</span><span class="sxs-lookup"><span data-stu-id="d5145-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="d5145-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Definiuje obsługę [uaktualnień http](https://tools.ietf.org/html/rfc2616.html#section-14.42), które umożliwiają klientowi określenie dodatkowych protokołów, które mają być używane, jeśli serwer chce przełączyć protokoły.</span><span class="sxs-lookup"><span data-stu-id="d5145-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="d5145-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Definiuje interfejs API do obsługi gniazd sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d5145-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5145-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Kontroluje, czy kompresja odpowiedzi powinna być używana przez połączenia HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d5145-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="d5145-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Przechowuje <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> kolekcję dla stanu aplikacji na żądanie.</span><span class="sxs-lookup"><span data-stu-id="d5145-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="d5145-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analizuje i buforuje ciąg zapytania.</span><span class="sxs-lookup"><span data-stu-id="d5145-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5145-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Reprezentuje treść żądania jako <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="d5145-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="d5145-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analizuje i buforuje `Cookie` wartości nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="d5145-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="d5145-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Kontroluje sposób cookie zastosowania odpowiedzi s do `Set-Cookie` nagłówka.</span><span class="sxs-lookup"><span data-stu-id="d5145-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d5145-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Ta funkcja zapewnia dostęp do zmiennych serwerowych żądania, takich jak te udostępniane przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="d5145-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="d5145-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Zapewnia dostęp do <xref:System.IServiceProvider> usługi z żądaniami z zakresem.</span><span class="sxs-lookup"><span data-stu-id="d5145-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="d5145-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Definiuje `ISessionFactory` i <xref:Microsoft.AspNetCore.Http.ISession> abstrakcje dla pomocniczych sesji użytkowników.</span><span class="sxs-lookup"><span data-stu-id="d5145-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="d5145-150">`ISessionFeature` jest zaimplementowany przez <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (zobacz <xref:fundamentals/app-state> ).</span><span class="sxs-lookup"><span data-stu-id="d5145-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="d5145-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Definiuje interfejs API do pobierania certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="d5145-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="d5145-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Definiuje metody pracy z parametrami powiązania tokenu TLS.</span><span class="sxs-lookup"><span data-stu-id="d5145-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="d5145-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Służy do wykonywania zapytań, udzielania i wycofywania zgody użytkownika na przechowywanie informacji o użytkowniku związanych z działaniem i funkcjonalnością witryny.</span><span class="sxs-lookup"><span data-stu-id="d5145-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d5145-154">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="d5145-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
