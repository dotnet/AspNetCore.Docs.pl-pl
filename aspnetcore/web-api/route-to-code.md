---
title: Podstawowe interfejsy API JSON z programem Route-to-code w ASP.NET Core
author: jamesnk
description: Dowiedz się, jak używać Route-to-code metod rozszerzenia i JSON w celu tworzenia lekkich interfejsów API sieci Web JSON.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556609"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>Podstawowe interfejsy API JSON z programem Route-to-code w ASP.NET Core

Przez [Kuba Kowalski-króla](https://github.com/jamesnk)

ASP.NET Core obsługuje wiele sposobów tworzenia interfejsów API sieci Web JSON:

* [Interfejs API sieci web ASP.NET Core](xref:web-api/index) zapewnia kompletną strukturę tworzenia interfejsów API. Usługa jest tworzona przez dziedziczenie z <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Niektóre funkcje udostępniane przez platformę obejmują powiązanie modelu, walidację, negocjowanie zawartości, formatowanie danych wejściowych i wyjściowych oraz OpenAPI.
* Route-to-code jest alternatywną platformą dla ASP.NET Core internetowego interfejsu API. Route-to-code łączy [ASP.NET Core routingu](xref:fundamentals/routing) bezpośrednio z kodem. Kod odczytuje z żądania i zapisuje odpowiedź. Route-to-code nie ma zaawansowanych funkcji interfejsu API sieci Web, ale nie jest wymagana żadna konfiguracja.

Route-to-code jest dobrym rozwiązaniem podczas tworzenia małych i podstawowych interfejsów API sieci Web JSON.

## <a name="create-json-web-apis"></a>Tworzenie interfejsów API sieci Web JSON

ASP.NET Core zawiera metody pomocnika, które ułatwiają tworzenie interfejsów API sieci Web JSON:

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> sprawdza `Content-Type` nagłówek dla typu zawartości JSON.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> odczytuje kod JSON z żądania i deserializacji go do określonego typu.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> zapisuje określoną wartość jako plik JSON w treści odpowiedzi i ustawia typ zawartości odpowiedzi na `application/json` .

Uproszczone, oparte na trasach interfejsy API JSON są określone w *Startup.cs*. Trasa i logika interfejsu API są konfigurowane w <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> ramach potoku żądania aplikacji.

### <a name="write-json-response"></a>Zapisz odpowiedź JSON

Rozważmy następujący kod, który konfiguruje interfejs API JSON dla aplikacji:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

Powyższy kod ma następujące działanie:

* Dodaje punkt końcowy interfejsu API pobierania HTTP z użyciem `/hello/{name:alpha}` jako szablonu trasy.
* Po dopasowaniu trasy interfejs API odczytuje `name` wartość trasy z żądania.
* Zapisuje typ anonimowy jako odpowiedź JSON przy użyciu `WriteAsJsonAsync` .

### <a name="read-json-request"></a>Odczytaj żądanie JSON

`HasJsonContentType` i `ReadFromJsonAsync` może służyć do deserializacji odpowiedzi JSON w interfejsie API JSON opartej na trasach:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

Powyższy kod ma następujące działanie:

* Dodaje punkt końcowy interfejsu API protokołu HTTP z użyciem `/weather` jako szablonu trasy.
* Po dopasowaniu trasy `HasJsonContentType` sprawdza typ zawartości żądania. Typ zawartości spoza JSON zwraca kod stanu 415.
* Jeśli typ zawartości to JSON, zawartość żądania jest deserializowana przez `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Konfigurowanie serializacji JSON

Istnieją dwa sposoby dostosowywania serializacji JSON:

* Domyślne opcje serializacji można skonfigurować za pomocą <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` metody.
* `WriteAsJsonAsync` i `ReadFromJsonAsync` mają przeciążenia, które akceptują <xref:System.Text.Json.JsonSerializerOptions> obiekt. Ten obiekt Options zastępuje opcje domyślne.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Route-to-code obsługuje uwierzytelnianie i autoryzację. Atrybuty, takie jak `[Authorize]` i `[AllowAnonymous]` , nie mogą być umieszczane w punktach końcowych mapowanych na delegata żądania. Zamiast tego metadane autoryzacji są dodawane przy użyciu <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> metod rozszerzenia i.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

[Wstrzykiwanie zależności (di)](xref:fundamentals/dependency-injection) przy użyciu konstruktora nie jest możliwe z Route-to-code . Internetowy interfejs API tworzy kontroler dla użytkownika z usługami wstrzykiwanymi do konstruktora. Typ nie jest tworzony, gdy punkt końcowy jest wykonywany, więc usługi muszą zostać rozwiązane ręcznie.

Interfejsy API oparte na trasach mogą służyć <xref:System.IServiceProvider> do rozpoznawania usług:

* Przejściowe i zakresowe usługi okresu istnienia, takie jak `DbContext` , muszą zostać rozpoznane z elementu [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) wewnątrz obiektu delegowanego żądania punktu końcowego.
* Pojedyncze usługi okresu istnienia, takie jak `ILogger` , można rozpoznać z [IEndpointRouteBuilder.](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider). Usługi mogą być rozwiązywane poza delegatami żądań i udostępniane między punktami końcowymi.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

Interfejsy API, które wykorzystują szeroką gamę należy rozważyć przy użyciu typu aplikacji ASP.NET Core, który obsługuje funkcję DI. Na przykład ASP.NET Core Web API. Wstrzyknięcie usług przy użyciu konstruktora kontrolera jest łatwiejsze niż ręczne rozpoznawanie usług.

## <a name="api-project-structure"></a>Struktura projektu interfejsu API

Interfejsy API oparte na trasach nie muszą znajdować się w *Startup.cs*. Interfejsy API mogą być umieszczane w innych plikach i mapowane przy uruchamianiu przy użyciu programu `UseEndpoints` . Takie podejście zmniejsza rozmiar pliku konfiguracji uruchomieniowej.

Rozważmy następującą klasę statyczną `UserApi` , która definiuje `Map` metodę. Metoda mapuje interfejsów API opartych na trasach.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

W `Startup.Configure` metodzie `Map` Metoda i metody statyczne innych klas są wywoływane w `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Ważne brakujące funkcje porównane z interfejsem API sieci Web

Route-to-code jest przeznaczony dla podstawowych interfejsów API JSON. Nie obsługuje on wielu zaawansowanych funkcji zapewnianych przez ASP.NET Core internetowy interfejs API.

Funkcje niedostarczone przez funkcję Route-to-code include:

* Powiązanie modelu
* Walidacja modelu
* OpenAPI/Swagger
* Negocjowanie zawartości
* Wstrzykiwanie zależności konstruktora
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

Rozważ użycie [ASP.NET Core Web API](xref:web-api/index) do utworzenia interfejsu API, jeśli wymaga ona niektórych funkcji z powyższej listy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
