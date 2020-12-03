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
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="1d40a-103">Podstawowe interfejsy API JSON z programem Route-to-code w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d40a-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="1d40a-104">Przez [Kuba Kowalski-króla](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1d40a-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="1d40a-105">ASP.NET Core obsługuje wiele sposobów tworzenia interfejsów API sieci Web JSON:</span><span class="sxs-lookup"><span data-stu-id="1d40a-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="1d40a-106">[Interfejs API sieci web ASP.NET Core](xref:web-api/index) zapewnia kompletną strukturę tworzenia interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="1d40a-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="1d40a-107">Usługa jest tworzona przez dziedziczenie z <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="1d40a-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1d40a-108">Niektóre funkcje udostępniane przez platformę obejmują powiązanie modelu, walidację, negocjowanie zawartości, formatowanie danych wejściowych i wyjściowych oraz OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="1d40a-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="1d40a-109">Route-to-code jest alternatywną platformą dla ASP.NET Core internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1d40a-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="1d40a-110">Route-to-code łączy [ASP.NET Core routingu](xref:fundamentals/routing) bezpośrednio z kodem.</span><span class="sxs-lookup"><span data-stu-id="1d40a-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="1d40a-111">Kod odczytuje z żądania i zapisuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="1d40a-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="1d40a-112">Route-to-code nie ma zaawansowanych funkcji interfejsu API sieci Web, ale nie jest wymagana żadna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="1d40a-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="1d40a-113">Route-to-code jest dobrym rozwiązaniem podczas tworzenia małych i podstawowych interfejsów API sieci Web JSON.</span><span class="sxs-lookup"><span data-stu-id="1d40a-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="1d40a-114">Tworzenie interfejsów API sieci Web JSON</span><span class="sxs-lookup"><span data-stu-id="1d40a-114">Create JSON web APIs</span></span>

<span data-ttu-id="1d40a-115">ASP.NET Core zawiera metody pomocnika, które ułatwiają tworzenie interfejsów API sieci Web JSON:</span><span class="sxs-lookup"><span data-stu-id="1d40a-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="1d40a-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> sprawdza `Content-Type` nagłówek dla typu zawartości JSON.</span><span class="sxs-lookup"><span data-stu-id="1d40a-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="1d40a-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> odczytuje kod JSON z żądania i deserializacji go do określonego typu.</span><span class="sxs-lookup"><span data-stu-id="1d40a-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="1d40a-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> zapisuje określoną wartość jako plik JSON w treści odpowiedzi i ustawia typ zawartości odpowiedzi na `application/json` .</span><span class="sxs-lookup"><span data-stu-id="1d40a-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="1d40a-119">Uproszczone, oparte na trasach interfejsy API JSON są określone w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1d40a-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="1d40a-120">Trasa i logika interfejsu API są konfigurowane w <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> ramach potoku żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1d40a-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="1d40a-121">Zapisz odpowiedź JSON</span><span class="sxs-lookup"><span data-stu-id="1d40a-121">Write JSON response</span></span>

<span data-ttu-id="1d40a-122">Rozważmy następujący kod, który konfiguruje interfejs API JSON dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1d40a-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="1d40a-123">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="1d40a-123">The preceding code:</span></span>

* <span data-ttu-id="1d40a-124">Dodaje punkt końcowy interfejsu API pobierania HTTP z użyciem `/hello/{name:alpha}` jako szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="1d40a-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="1d40a-125">Po dopasowaniu trasy interfejs API odczytuje `name` wartość trasy z żądania.</span><span class="sxs-lookup"><span data-stu-id="1d40a-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="1d40a-126">Zapisuje typ anonimowy jako odpowiedź JSON przy użyciu `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="1d40a-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="1d40a-127">Odczytaj żądanie JSON</span><span class="sxs-lookup"><span data-stu-id="1d40a-127">Read JSON request</span></span>

<span data-ttu-id="1d40a-128">`HasJsonContentType` i `ReadFromJsonAsync` może służyć do deserializacji odpowiedzi JSON w interfejsie API JSON opartej na trasach:</span><span class="sxs-lookup"><span data-stu-id="1d40a-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="1d40a-129">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="1d40a-129">The preceding code:</span></span>

* <span data-ttu-id="1d40a-130">Dodaje punkt końcowy interfejsu API protokołu HTTP z użyciem `/weather` jako szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="1d40a-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="1d40a-131">Po dopasowaniu trasy `HasJsonContentType` sprawdza typ zawartości żądania.</span><span class="sxs-lookup"><span data-stu-id="1d40a-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="1d40a-132">Typ zawartości spoza JSON zwraca kod stanu 415.</span><span class="sxs-lookup"><span data-stu-id="1d40a-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="1d40a-133">Jeśli typ zawartości to JSON, zawartość żądania jest deserializowana przez `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="1d40a-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="1d40a-134">Konfigurowanie serializacji JSON</span><span class="sxs-lookup"><span data-stu-id="1d40a-134">Configure JSON serialization</span></span>

<span data-ttu-id="1d40a-135">Istnieją dwa sposoby dostosowywania serializacji JSON:</span><span class="sxs-lookup"><span data-stu-id="1d40a-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="1d40a-136">Domyślne opcje serializacji można skonfigurować za pomocą <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="1d40a-136">Default serialization options can be configured with <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="1d40a-137">`WriteAsJsonAsync` i `ReadFromJsonAsync` mają przeciążenia, które akceptują <xref:System.Text.Json.JsonSerializerOptions> obiekt.</span><span class="sxs-lookup"><span data-stu-id="1d40a-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a <xref:System.Text.Json.JsonSerializerOptions> object.</span></span> <span data-ttu-id="1d40a-138">Ten obiekt Options zastępuje opcje domyślne.</span><span class="sxs-lookup"><span data-stu-id="1d40a-138">This options object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="1d40a-139">Uwierzytelnianie i autoryzacja</span><span class="sxs-lookup"><span data-stu-id="1d40a-139">Authentication and authorization</span></span>

<span data-ttu-id="1d40a-140">Route-to-code obsługuje uwierzytelnianie i autoryzację.</span><span class="sxs-lookup"><span data-stu-id="1d40a-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="1d40a-141">Atrybuty, takie jak `[Authorize]` i `[AllowAnonymous]` , nie mogą być umieszczane w punktach końcowych mapowanych na delegata żądania.</span><span class="sxs-lookup"><span data-stu-id="1d40a-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="1d40a-142">Zamiast tego metadane autoryzacji są dodawane przy użyciu <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> metod rozszerzenia i.</span><span class="sxs-lookup"><span data-stu-id="1d40a-142">Instead, authorization metadata is added using the <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="1d40a-143">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="1d40a-143">Dependency injection</span></span>

<span data-ttu-id="1d40a-144">[Wstrzykiwanie zależności (di)](xref:fundamentals/dependency-injection) przy użyciu konstruktora nie jest możliwe z Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="1d40a-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="1d40a-145">Internetowy interfejs API tworzy kontroler dla użytkownika z usługami wstrzykiwanymi do konstruktora.</span><span class="sxs-lookup"><span data-stu-id="1d40a-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="1d40a-146">Typ nie jest tworzony, gdy punkt końcowy jest wykonywany, więc usługi muszą zostać rozwiązane ręcznie.</span><span class="sxs-lookup"><span data-stu-id="1d40a-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="1d40a-147">Interfejsy API oparte na trasach mogą służyć <xref:System.IServiceProvider> do rozpoznawania usług:</span><span class="sxs-lookup"><span data-stu-id="1d40a-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="1d40a-148">Przejściowe i zakresowe usługi okresu istnienia, takie jak `DbContext` , muszą zostać rozpoznane z elementu [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) wewnątrz obiektu delegowanego żądania punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="1d40a-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="1d40a-149">Pojedyncze usługi okresu istnienia, takie jak `ILogger` , można rozpoznać z [IEndpointRouteBuilder.](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span><span class="sxs-lookup"><span data-stu-id="1d40a-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="1d40a-150">Usługi mogą być rozwiązywane poza delegatami żądań i udostępniane między punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="1d40a-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="1d40a-151">Interfejsy API, które wykorzystują szeroką gamę należy rozważyć przy użyciu typu aplikacji ASP.NET Core, który obsługuje funkcję DI.</span><span class="sxs-lookup"><span data-stu-id="1d40a-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="1d40a-152">Na przykład ASP.NET Core Web API.</span><span class="sxs-lookup"><span data-stu-id="1d40a-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="1d40a-153">Wstrzyknięcie usług przy użyciu konstruktora kontrolera jest łatwiejsze niż ręczne rozpoznawanie usług.</span><span class="sxs-lookup"><span data-stu-id="1d40a-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="1d40a-154">Struktura projektu interfejsu API</span><span class="sxs-lookup"><span data-stu-id="1d40a-154">API project structure</span></span>

<span data-ttu-id="1d40a-155">Interfejsy API oparte na trasach nie muszą znajdować się w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1d40a-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="1d40a-156">Interfejsy API mogą być umieszczane w innych plikach i mapowane przy uruchamianiu przy użyciu programu `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="1d40a-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="1d40a-157">Takie podejście zmniejsza rozmiar pliku konfiguracji uruchomieniowej.</span><span class="sxs-lookup"><span data-stu-id="1d40a-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="1d40a-158">Rozważmy następującą klasę statyczną `UserApi` , która definiuje `Map` metodę.</span><span class="sxs-lookup"><span data-stu-id="1d40a-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="1d40a-159">Metoda mapuje interfejsów API opartych na trasach.</span><span class="sxs-lookup"><span data-stu-id="1d40a-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="1d40a-160">W `Startup.Configure` metodzie `Map` Metoda i metody statyczne innych klas są wywoływane w `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="1d40a-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="1d40a-161">Ważne brakujące funkcje porównane z interfejsem API sieci Web</span><span class="sxs-lookup"><span data-stu-id="1d40a-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="1d40a-162">Route-to-code jest przeznaczony dla podstawowych interfejsów API JSON.</span><span class="sxs-lookup"><span data-stu-id="1d40a-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="1d40a-163">Nie obsługuje on wielu zaawansowanych funkcji zapewnianych przez ASP.NET Core internetowy interfejs API.</span><span class="sxs-lookup"><span data-stu-id="1d40a-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="1d40a-164">Funkcje niedostarczone przez funkcję Route-to-code include:</span><span class="sxs-lookup"><span data-stu-id="1d40a-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="1d40a-165">Powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="1d40a-165">Model binding</span></span>
* <span data-ttu-id="1d40a-166">Walidacja modelu</span><span class="sxs-lookup"><span data-stu-id="1d40a-166">Model validation</span></span>
* <span data-ttu-id="1d40a-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="1d40a-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="1d40a-168">Negocjowanie zawartości</span><span class="sxs-lookup"><span data-stu-id="1d40a-168">Content negotiation</span></span>
* <span data-ttu-id="1d40a-169">Wstrzykiwanie zależności konstruktora</span><span class="sxs-lookup"><span data-stu-id="1d40a-169">Constructor dependency injection</span></span>
* <span data-ttu-id="1d40a-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span><span class="sxs-lookup"><span data-stu-id="1d40a-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span></span>

<span data-ttu-id="1d40a-171">Rozważ użycie [ASP.NET Core Web API](xref:web-api/index) do utworzenia interfejsu API, jeśli wymaga ona niektórych funkcji z powyższej listy.</span><span class="sxs-lookup"><span data-stu-id="1d40a-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d40a-172">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1d40a-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
