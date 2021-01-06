---
title: Zażądaj funkcji w ASP.NET Core
author: ardalis
description: Dowiedz się więcej na temat szczegółów implementacji serwera sieci Web związanych z żądaniami HTTP i odpowiedziami, które są zdefiniowane w interfejsach dla ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "95870428"
---
# <a name="request-features-in-aspnet-core"></a>Zażądaj funkcji w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

`HttpContext`Interfejs API używany przez aplikacje i oprogramowanie pośredniczące do przetwarzania żądań ma warstwę abstrakcji poniżej o nazwie *interfejsy funkcji*. Każdy interfejs funkcji zawiera szczegółowy podzestaw funkcji uwidocznionych przez program `HttpContext` . Te interfejsy mogą być dodawane, modyfikowane, opakowane, zastępowane lub nawet usuwane przez serwer lub oprogramowanie pośredniczące, gdy żądanie jest przetwarzane bez konieczności ponownego implementowania całego programu `HttpContext` . Mogą być również używane do makietowania funkcjonalności podczas testowania.

## <a name="feature-collections"></a>Kolekcje funkcji

<xref:Microsoft.AspNetCore.Http.HttpContext.Features>Właściwość `HttpContext` zapewnia dostęp do kolekcji interfejsów funkcji dla bieżącego żądania. Ponieważ kolekcja funkcji jest modyfikowalna nawet w kontekście żądania, oprogramowanie pośredniczące może służyć do modyfikowania kolekcji i dodawania obsługi dodatkowych funkcji. Niektóre funkcje zaawansowane są dostępne tylko przez uzyskanie dostępu do skojarzonego interfejsu za pomocą kolekcji funkcji.

## <a name="feature-interfaces"></a>Interfejsy funkcji

ASP.NET Core definiuje wiele popularnych interfejsów funkcji protokołu HTTP w programie <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , które są współużytkowane przez różne serwery i oprogramowanie pośredniczące w celu zidentyfikowania obsługiwanych przez nich funkcji. Serwery i oprogramowanie pośredniczące mogą również udostępniać własne interfejsy z dodatkowymi funkcjami.

Większość interfejsów funkcji zapewnia opcjonalną, bardziej uproszczoną funkcjonalność i skojarzone `HttpContext` z nimi interfejsy API zapewniają wartości domyślne, jeśli ta funkcja nie jest obecna. Niektóre interfejsy są wskazane w poniższej zawartości zgodnie z wymaganiami, ponieważ zapewniają podstawowe funkcje żądań i odpowiedzi, które muszą zostać zaimplementowane w celu przetworzenia żądania.

Następujące interfejsy funkcji pochodzą z <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Określa strukturę żądania HTTP, łącznie z protokołem, ścieżką, ciągiem zapytania, nagłówkami i treścią. Ta funkcja jest wymagana, aby przetwarzać żądania.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Określa strukturę odpowiedzi HTTP, w tym kod stanu, nagłówki i treść odpowiedzi. Ta funkcja jest wymagana, aby przetwarzać żądania.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Definiuje różne sposoby pisania treści odpowiedzi przy użyciu `Stream` , a `PipeWriter` lub pliku. Ta funkcja jest wymagana, aby przetwarzać żądania. Spowoduje to zastąpienie `IHttpResponseFeature.Body` i `IHttpSendFileFeature` .

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Zawiera <xref:System.Security.Claims.ClaimsPrincipal> aktualnie skojarzone z żądaniem.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Służy do analizowania i buforowania przychodzących przesłanych formularzy HTTP i wieloczęściowego.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Służy do kontrolowania, czy synchroniczne operacje we/wy są dozwolone dla treści żądania lub odpowiedzi.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Definiuje metody służące do wyłączania buforowania żądań i/lub odpowiedzi.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Definiuje właściwości identyfikatora połączenia oraz adresów i portów lokalnych i zdalnych.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Określa maksymalny dozwolony rozmiar treści żądania dla bieżącego żądania.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: Wskazuje, czy żądanie może mieć treść.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Dodaje właściwość, która może zostać zaimplementowana w celu jednoznacznego identyfikowania żądań.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Definiuje obsługę przerywania połączeń lub wykrywania, czy żądanie zostało zakończone przedwcześnie, na przykład przez odłączenie klienta.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Zapewnia dostęp do nagłówków naczepy żądania (jeśli istnieją).

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Służy do wysyłania komunikatów resetowania dla protokołów, które obsługują je, takich jak HTTP/2 lub HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Umożliwia aplikacji dostarczanie nagłówków przyczepy odpowiedzi, jeśli są obsługiwane.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Definiuje metodę asynchronicznego wysyłania plików.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Definiuje obsługę [uaktualnień http](https://tools.ietf.org/html/rfc2616.html#section-14.42), które umożliwiają klientowi określenie dodatkowych protokołów, które mają być używane, jeśli serwer chce przełączyć protokoły.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Definiuje interfejs API do obsługi gniazd sieci Web.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Kontroluje, czy kompresja odpowiedzi powinna być używana przez połączenia HTTPS.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Przechowuje <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> kolekcję dla stanu aplikacji na żądanie.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analizuje i buforuje ciąg zapytania.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Reprezentuje treść żądania jako <xref:System.IO.Pipelines.PipeReader> .
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analizuje i buforuje `Cookie` wartości nagłówka żądania.

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Kontroluje sposób cookie zastosowania odpowiedzi s do `Set-Cookie` nagłówka.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Ta funkcja zapewnia dostęp do zmiennych serwerowych żądania, takich jak te udostępniane przez usługi IIS.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Zapewnia dostęp do <xref:System.IServiceProvider> usługi z żądaniami z zakresem.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Definiuje `ISessionFactory` i <xref:Microsoft.AspNetCore.Http.ISession> abstrakcje dla pomocniczych sesji użytkowników. `ISessionFeature` jest zaimplementowany przez <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (zobacz <xref:fundamentals/app-state> ).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Definiuje interfejs API do pobierania certyfikatów klienta.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Definiuje metody pracy z parametrami powiązania tokenu TLS.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Służy do wykonywania zapytań, udzielania i wycofywania zgody użytkownika na przechowywanie informacji o użytkowniku związanych z działaniem i funkcjonalnością witryny.
   
::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
