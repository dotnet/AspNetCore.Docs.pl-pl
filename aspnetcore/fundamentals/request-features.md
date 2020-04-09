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
# <a name="request-features-in-aspnet-core"></a>Żądaj funkcji w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

Szczegóły implementacji serwera sieci Web związane z żądaniami i odpowiedziami HTTP są definiowane w interfejsach. Interfejsy te są używane przez implementacje serwera i oprogramowanie pośredniczące do tworzenia i modyfikowania potoku hostingu aplikacji.

## <a name="feature-interfaces"></a>Interfejsy funkcji

ASP.NET Core definiuje szereg interfejsów funkcji HTTP, w `Microsoft.AspNetCore.Http.Features` których są używane przez serwery do identyfikowania obsługiwanych przez nie funkcji. Następujące interfejsy funkcji obsługują żądania i zwracaj odpowiedzi:

`IHttpRequestFeature`Definiuje strukturę żądania HTTP, w tym protokołu, ścieżki, ciągu zapytania, nagłówków i treści.

`IHttpResponseFeature`Definiuje strukturę odpowiedzi HTTP, w tym kod stanu, nagłówki i treść odpowiedzi.

`IHttpAuthenticationFeature`Definiuje obsługę identyfikowania użytkowników `ClaimsPrincipal` na podstawie i określania obsługi uwierzytelniania.

`IHttpUpgradeFeature`Definiuje obsługę [uaktualnień HTTP,](https://tools.ietf.org/html/rfc2616.html#section-14.42)które umożliwiają klientowi określenie, które dodatkowe protokoły, których chciałby użyć, jeśli serwer chce przełączać protokoły.

`IHttpBufferingFeature`Definiuje metody wyłączania buforowania żądań i/lub odpowiedzi.

`IHttpConnectionFeature`Definiuje właściwości dla adresów i portów lokalnych i zdalnych.

`IHttpRequestLifetimeFeature`Definiuje obsługę przerywania połączeń lub wykrywania, czy żądanie zostało zakończone przedwcześnie, na przykład przez rozłączenie klienta.

`IHttpSendFileFeature`Definiuje metodę asynchronicznie wysyłania plików.

`IHttpWebSocketFeature`Definiuje interfejs API do obsługi gniazd internetowych.

`IHttpRequestIdentifierFeature`Dodaje właściwość, która może być zaimplementowana do jednoznacznej identyfikacji żądań.

`ISessionFeature`Definiuje `ISessionFactory` i `ISession` abstrakcje do obsługi sesji użytkownika.

`ITlsConnectionFeature`Definiuje interfejs API do pobierania certyfikatów klientów.

`ITlsTokenBindingFeature`Definiuje metody pracy z parametrami wiązania tokenu TLS.

> [!NOTE]
> `ISessionFeature`nie jest funkcją serwera, ale jest `SessionMiddleware` implementowana przez (zobacz [Zarządzanie stanem aplikacji](app-state.md)).

## <a name="feature-collections"></a>Kolekcje funkcji

Właściwość `Features` `HttpContext` udostępnia interfejs do uzyskiwania i ustawiania dostępnych funkcji HTTP dla bieżącego żądania. Ponieważ kolekcja funkcji jest modyfikowalna nawet w kontekście żądania, oprogramowanie pośredniczące może służyć do modyfikowania kolekcji i dodawania obsługi dodatkowych funkcji.

## <a name="middleware-and-request-features"></a>Oprogramowanie pośredniczące i funkcje żądania

Podczas gdy serwery są odpowiedzialne za tworzenie kolekcji funkcji, oprogramowanie pośredniczące można zarówno dodać do tej kolekcji i zużywają funkcje z kolekcji. Na przykład `StaticFileMiddleware` uzyskuje dostęp `IHttpSendFileFeature` do funkcji. Jeśli funkcja istnieje, jest używana do wysyłania żądanego pliku statycznego ze ścieżki fizycznej. W przeciwnym razie do wysyłania pliku używana jest wolniejsza metoda alternatywna. Jeśli jest `IHttpSendFileFeature` to możliwe, system operacyjny może otworzyć plik i wykonać bezpośrednią kopię w trybie jądra na kartę sieciową.

Ponadto oprogramowanie pośredniczące można dodać do kolekcji funkcji ustanowionych przez serwer. Istniejące funkcje można nawet zastąpić oprogramowaniem pośredniczącym, dzięki czemu oprogramowanie pośredniczące rozszerza funkcjonalność serwera. Funkcje dodane do kolekcji są natychmiast dostępne dla innego oprogramowania pośredniczącego lub podstawowej aplikacji się później w potoku żądania.

Łącząc implementacje serwerów niestandardowych i określone ulepszenia oprogramowania pośredniczącego, można utworzyć dokładny zestaw funkcji, które wymaga aplikacja. Dzięki temu brakujące funkcje mogą być dodawane bez konieczności zmiany serwera i zapewnia tylko minimalną ilość funkcji są widoczne, co ogranicza obszar ataku i zwiększenie wydajności.

## <a name="summary"></a>Podsumowanie

Interfejsy funkcji definiują określone funkcje HTTP, które mogą obsługiwać dane żądanie. Serwery definiują kolekcje funkcji i początkowy zestaw funkcji obsługiwanych przez ten serwer, ale oprogramowanie pośredniczące może służyć do ulepszania tych funkcji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Serwery](xref:fundamentals/servers/index)
* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Otwarty interfejs internetowy dla platformy .NET (OWIN)](xref:fundamentals/owin)
