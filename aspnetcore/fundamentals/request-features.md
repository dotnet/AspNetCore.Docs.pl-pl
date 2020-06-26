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
# <a name="request-features-in-aspnet-core"></a>Zażądaj funkcji w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

Szczegóły implementacji serwera sieci Web związane z żądaniami HTTP i odpowiedziami są zdefiniowane w interfejsach. Te interfejsy są używane przez implementacje serwera i oprogramowanie pośredniczące do tworzenia i modyfikowania potoku hostingu aplikacji.

## <a name="feature-interfaces"></a>Interfejsy funkcji

ASP.NET Core definiuje wiele interfejsów funkcji HTTP `Microsoft.AspNetCore.Http.Features` , które są używane przez serwery do identyfikowania obsługiwanych funkcji. Poniższe interfejsy funkcji obsługują żądania i zwracają odpowiedzi:

`IHttpRequestFeature`Definiuje strukturę żądania HTTP, łącznie z protokołem, ścieżką, ciągiem zapytania, nagłówkami i treścią.

`IHttpResponseFeature`Definiuje strukturę odpowiedzi HTTP, w tym kod stanu, nagłówki i treść odpowiedzi.

`IHttpAuthenticationFeature`Definiuje obsługę identyfikacji użytkowników na podstawie `ClaimsPrincipal` i określania procedury obsługi uwierzytelniania.

`IHttpUpgradeFeature`Definiuje obsługę [uaktualnień http](https://tools.ietf.org/html/rfc2616.html#section-14.42), które umożliwiają klientowi określenie dodatkowych protokołów, które mają być używane, jeśli serwer chce przełączyć protokoły.

`IHttpBufferingFeature`Definiuje metody służące do wyłączania buforowania żądań i/lub odpowiedzi.

`IHttpConnectionFeature`Definiuje właściwości adresów i portów lokalnych i zdalnych.

`IHttpRequestLifetimeFeature`Definiuje obsługę przerywania połączeń lub wykrywanie, czy żądanie zostało zakończone przedwcześnie, na przykład przez odłączenie klienta.

`IHttpSendFileFeature`Definiuje metodę asynchronicznego wysyłania plików.

`IHttpWebSocketFeature`Definiuje interfejs API do obsługi gniazd sieci Web.

`IHttpRequestIdentifierFeature`Dodaje właściwość, która może zostać zaimplementowana w celu jednoznacznego identyfikowania żądań.

`ISessionFeature`Definiuje `ISessionFactory` i `ISession` abstrakcje dla pomocniczych sesji użytkowników.

`ITlsConnectionFeature`Definiuje interfejs API do pobierania certyfikatów klienta.

`ITlsTokenBindingFeature`Definiuje metody pracy z parametrami powiązania tokenu TLS.

> [!NOTE]
> `ISessionFeature`nie jest funkcją serwera, ale jest zaimplementowana przez program `SessionMiddleware` (zobacz [Zarządzanie stanem aplikacji](app-state.md)).

## <a name="feature-collections"></a>Kolekcje funkcji

`Features`Właściwość `HttpContext` zawiera interfejs do pobierania i ustawiania dostępnych funkcji http dla bieżącego żądania. Ponieważ kolekcja funkcji jest modyfikowalna nawet w kontekście żądania, oprogramowanie pośredniczące może służyć do modyfikowania kolekcji i dodawania obsługi dodatkowych funkcji.

## <a name="middleware-and-request-features"></a>Oprogramowanie pośredniczące i funkcje żądania

Chociaż serwery są odpowiedzialne za tworzenie kolekcji funkcji, oprogramowanie pośredniczące może dodawać do tej kolekcji i korzystać z funkcji z kolekcji. Na przykład `StaticFileMiddleware` program uzyskuje dostęp do `IHttpSendFileFeature` funkcji. Jeśli ta funkcja istnieje, jest używana do wysyłania żądanego pliku statycznego ze ścieżki fizycznej. W przeciwnym razie do wysłania pliku zostanie użyta mniejsza Metoda alternatywna. Jeśli jest dostępna, `IHttpSendFileFeature` umożliwia systemowi operacyjnemu otwarcie pliku i przeprowadzenie bezpośredniego kopiowania trybu jądra na kartę sieciową.

Ponadto oprogramowanie pośredniczące może dodać do kolekcji funkcji ustanowionej przez serwer. Istniejące funkcje mogą być nawet zastępowane przez oprogramowanie pośredniczące, co pozwala oprogramowaniu pośredniczącemu rozszerzyć funkcjonalność serwera. Funkcje dodane do kolekcji są natychmiast dostępne dla innych programów pośredniczących lub aplikacji bazowej w późniejszym czasie w potoku żądania.

Łącząc niestandardowe implementacje serwera i konkretne udoskonalenia oprogramowania pośredniczącego, można utworzyć precyzyjny zestaw funkcji wymaganych przez aplikację. Pozwala to na dodanie brakujących funkcji bez konieczności zmiany na serwerze i gwarantuje, że dostępna jest tylko minimalna liczba funkcji, co ogranicza obszar narażony na ataki i zwiększa wydajność.

## <a name="summary"></a>Podsumowanie

Interfejsy funkcji definiują określone funkcje HTTP, które mogą być obsługiwane przez dane żądanie. Serwery definiują kolekcje funkcji i początkowy zestaw funkcji obsługiwanych przez ten serwer, ale oprogramowanie pośredniczące może służyć do ulepszania tych funkcji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Serwery](xref:fundamentals/servers/index)
* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
* [Otwarty interfejs internetowy dla platformy .NET (OWIN)](xref:fundamentals/owin)
