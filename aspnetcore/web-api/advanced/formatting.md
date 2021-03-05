---
title: Formatowanie danych odpowiedzi w ASP.NET Core Web API
author: rick-anderson
description: Dowiedz się, jak sformatować dane odpowiedzi w ASP.NET Core Web API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5fa7bc15bac2609c89fea54f8a788aaf9f5ad055
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119035"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="413a3-103">Formatowanie danych odpowiedzi w ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="413a3-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="413a3-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="413a3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="413a3-105">ASP.NET Core MVC obsługuje formatowanie danych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="413a3-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="413a3-106">Dane odpowiedzi można sformatować przy użyciu określonych formatów lub w odpowiedzi na żądany format klienta.</span><span class="sxs-lookup"><span data-stu-id="413a3-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="413a3-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="413a3-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="413a3-108">Wyniki akcji specyficzne dla formatu</span><span class="sxs-lookup"><span data-stu-id="413a3-108">Format-specific Action Results</span></span>

<span data-ttu-id="413a3-109">Niektóre typy wyników akcji są specyficzne dla określonego formatu, takiego jak <xref:Microsoft.AspNetCore.Mvc.JsonResult> i <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="413a3-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="413a3-110">Akcje mogą zwracać wyniki sformatowane w określonym formacie, niezależnie od preferencji klienta.</span><span class="sxs-lookup"><span data-stu-id="413a3-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="413a3-111">Na przykład zwracanie `JsonResult` zwraca dane w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="413a3-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="413a3-112">Zwracanie `ContentResult` lub ciąg zwraca dane ciągu w formacie zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="413a3-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="413a3-113">Akcja nie jest wymagana do zwrócenia żadnego określonego typu.</span><span class="sxs-lookup"><span data-stu-id="413a3-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="413a3-114">ASP.NET Core obsługuje dowolną wartość zwracaną przez obiekt.</span><span class="sxs-lookup"><span data-stu-id="413a3-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="413a3-115">Wyniki akcji, które zwracają obiekty, które nie są <xref:Microsoft.AspNetCore.Mvc.IActionResult> typami, są serializowane przy użyciu odpowiedniej <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementacji.</span><span class="sxs-lookup"><span data-stu-id="413a3-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="413a3-116">Aby uzyskać więcej informacji, zobacz <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="413a3-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="413a3-117">Wbudowana metoda pomocnika <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> zwraca dane sformatowane w formacie JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="413a3-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="413a3-118">Pobieranie próbek zwraca listę autorów.</span><span class="sxs-lookup"><span data-stu-id="413a3-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="413a3-119">Za pomocą narzędzi deweloperskich przeglądarki F12 lub [po](https://www.getpostman.com/tools) powyższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="413a3-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="413a3-120">Zostanie wyświetlony nagłówek odpowiedzi zawierający **Typ zawartości:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="413a3-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="413a3-121">Wyświetlane są nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="413a3-121">The request headers are displayed.</span></span> <span data-ttu-id="413a3-122">Na przykład `Accept` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="413a3-122">For example, the `Accept` header.</span></span> <span data-ttu-id="413a3-123">`Accept`Nagłówek jest ignorowany przez poprzedni kod.</span><span class="sxs-lookup"><span data-stu-id="413a3-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="413a3-124">Aby zwrócić dane w formacie zwykłego tekstu, użyj <xref:Microsoft.AspNetCore.Mvc.ContentResult> i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> pomocnika:</span><span class="sxs-lookup"><span data-stu-id="413a3-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="413a3-125">W powyższym kodzie `Content-Type` zwraca wartość `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="413a3-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="413a3-126">Zwracanie ciągu `Content-Type` z `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="413a3-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="413a3-127">W przypadku akcji z wieloma zwracanymi typami zwracamy `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="413a3-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="413a3-128">Na przykład zwrócenie różnych kodów stanu HTTP w oparciu o wynik wykonanych operacji.</span><span class="sxs-lookup"><span data-stu-id="413a3-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="413a3-129">Negocjowanie zawartości</span><span class="sxs-lookup"><span data-stu-id="413a3-129">Content negotiation</span></span>

<span data-ttu-id="413a3-130">Negocjowanie zawartości odbywa się, gdy klient określi [nagłówek Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="413a3-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="413a3-131">Domyślny format używany przez ASP.NET Core to [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="413a3-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="413a3-132">Negocjowanie zawartości:</span><span class="sxs-lookup"><span data-stu-id="413a3-132">Content negotiation is:</span></span>

* <span data-ttu-id="413a3-133">Zaimplementowane przez <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="413a3-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="413a3-134">Wbudowane wyniki akcji specyficzne dla kodu stanu zwracane z metod pomocnika.</span><span class="sxs-lookup"><span data-stu-id="413a3-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="413a3-135">Metody pomocnika wyników akcji są oparte na `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="413a3-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="413a3-136">Po zwróceniu typu modelu zwracanym typem jest `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="413a3-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="413a3-137">W poniższej metodzie działania są `Ok` stosowane `NotFound` metody pomocnika i:</span><span class="sxs-lookup"><span data-stu-id="413a3-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="413a3-138">Domyślnie ASP.NET Core obsługuje `application/json` `text/json` `text/plain` typy nośników, i.</span><span class="sxs-lookup"><span data-stu-id="413a3-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="413a3-139">Narzędzia takie jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/tools) mogą ustawić `Accept` nagłówek żądania w celu określenia formatu zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="413a3-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="413a3-140">Gdy `Accept` Nagłówek zawiera typ obsługiwany przez serwer, zwracany jest ten typ.</span><span class="sxs-lookup"><span data-stu-id="413a3-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="413a3-141">W następnej sekcji pokazano, jak dodać dodatkowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="413a3-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="413a3-142">Akcje kontrolera mogą zwracać POCOs (zwykłe stare obiekty CLR).</span><span class="sxs-lookup"><span data-stu-id="413a3-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="413a3-143">Gdy zostanie zwrócona wartość POCO, środowisko uruchomieniowe automatycznie tworzy `ObjectResult` , które zawija obiekt.</span><span class="sxs-lookup"><span data-stu-id="413a3-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="413a3-144">Klient pobiera sformatowany obiekt Zserializowany.</span><span class="sxs-lookup"><span data-stu-id="413a3-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="413a3-145">Jeśli zwracany obiekt jest `null` `204 No Content` zwracany, zwracana jest odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="413a3-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="413a3-146">Zwracanie typu obiektu:</span><span class="sxs-lookup"><span data-stu-id="413a3-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="413a3-147">W poprzednim kodzie żądanie poprawnego aliasu autora zwraca `200 OK` odpowiedź z danymi autora.</span><span class="sxs-lookup"><span data-stu-id="413a3-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="413a3-148">Żądanie dotyczące nieprawidłowego aliasu zwraca `204 No Content` odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="413a3-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="413a3-149">Nagłówek Accept</span><span class="sxs-lookup"><span data-stu-id="413a3-149">The Accept header</span></span>

<span data-ttu-id="413a3-150">*Negocjowanie* zawartości odbywa się po `Accept` pojawieniu się nagłówka w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="413a3-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="413a3-151">Gdy żądanie zawiera nagłówek Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="413a3-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="413a3-152">Wylicza typy nośników w nagłówku Accept w kolejności preferencji.</span><span class="sxs-lookup"><span data-stu-id="413a3-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="413a3-153">Próbuje znaleźć program formatujący, który może wygenerować odpowiedź w jednym z określonych formatów.</span><span class="sxs-lookup"><span data-stu-id="413a3-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="413a3-154">Jeśli nie zostanie znaleziony żaden program formatujący, który może spełnić żądanie klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="413a3-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="413a3-155">Zwraca `406 Not Acceptable` wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> , jeśli jest ustawiona na `true` , lub-</span><span class="sxs-lookup"><span data-stu-id="413a3-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="413a3-156">Próbuje znaleźć pierwszy program formatujący, który może wygenerować odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="413a3-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="413a3-157">Jeśli nie skonfigurowano programu formatującego dla żądanego formatu, jest używany pierwszy program formatujący, który może formatować obiekt.</span><span class="sxs-lookup"><span data-stu-id="413a3-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="413a3-158">Jeśli `Accept` w żądaniu nie zostanie wyświetlony nagłówek:</span><span class="sxs-lookup"><span data-stu-id="413a3-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="413a3-159">Pierwszy program formatujący, który może obsłużyć obiekt, jest używany do serializacji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="413a3-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="413a3-160">Nie ma żadnej negocjacji.</span><span class="sxs-lookup"><span data-stu-id="413a3-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="413a3-161">Serwer określa format do zwrócenia.</span><span class="sxs-lookup"><span data-stu-id="413a3-161">The server is determining what format to return.</span></span>

<span data-ttu-id="413a3-162">Jeśli nagłówek Accept zawiera `*/*` , nagłówek jest ignorowany, chyba że `RespectBrowserAcceptHeader` jest ustawiona na wartość true (prawda) <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="413a3-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="413a3-163">Przeglądarki i negocjacje zawartości</span><span class="sxs-lookup"><span data-stu-id="413a3-163">Browsers and content negotiation</span></span>

<span data-ttu-id="413a3-164">W przeciwieństwie do typowych klientów interfejsu API, przeglądarki sieci Web dostarczają `Accept` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="413a3-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="413a3-165">Przeglądarka sieci Web określa wiele formatów, w tym symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="413a3-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="413a3-166">Domyślnie, gdy struktura wykryje, że żądanie pochodzi z przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="413a3-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="413a3-167">`Accept`Nagłówek jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="413a3-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="413a3-168">Zawartość jest zwracana w formacie JSON, o ile nie została skonfigurowana inaczej.</span><span class="sxs-lookup"><span data-stu-id="413a3-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="413a3-169">Zapewnia to bardziej spójne środowisko w przeglądarkach podczas używania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="413a3-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="413a3-170">Aby skonfigurować aplikację do honorowania nagłówków akceptowanych przez przeglądarkę, ustaw wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true` :</span><span class="sxs-lookup"><span data-stu-id="413a3-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="413a3-171">Konfigurowanie elementów formatujących</span><span class="sxs-lookup"><span data-stu-id="413a3-171">Configure formatters</span></span>

<span data-ttu-id="413a3-172">Aplikacje, które muszą obsługiwać dodatkowe formaty, mogą dodać odpowiednie pakiety NuGet i skonfigurować obsługę.</span><span class="sxs-lookup"><span data-stu-id="413a3-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="413a3-173">Istnieją osobne elementy formatujące dla danych wejściowych i wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="413a3-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="413a3-174">Wejściowe elementy formatującego są używane przez [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="413a3-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="413a3-175">Wyjściowe elementy formatujące są używane do formatowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="413a3-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="413a3-176">Aby uzyskać informacje na temat tworzenia niestandardowego programu formatującego, zobacz [niestandardowe elementy formatujące](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="413a3-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="413a3-177">Dodawanie obsługi formatu XML</span><span class="sxs-lookup"><span data-stu-id="413a3-177">Add XML format support</span></span>

<span data-ttu-id="413a3-178">Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="413a3-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="413a3-179">Poprzedni kod serializacji wyników przy użyciu `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="413a3-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="413a3-180">W przypadku korzystania z powyższego kodu metody kontrolera zwracają odpowiedni format na podstawie nagłówka żądania `Accept` .</span><span class="sxs-lookup"><span data-stu-id="413a3-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="413a3-181">Konfigurowanie `System.Text.Json` opartych na programie formatującego</span><span class="sxs-lookup"><span data-stu-id="413a3-181">Configure `System.Text.Json` based formatters</span></span>

<span data-ttu-id="413a3-182">Funkcje dla elementów `System.Text.Json` formatujących opartych na programie można skonfigurować przy użyciu polecenia <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="413a3-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="413a3-183">Domyślne formatowanie to camelCase.</span><span class="sxs-lookup"><span data-stu-id="413a3-183">The default formatting is camelCase.</span></span> <span data-ttu-id="413a3-184">Następujący wyróżniony kod PascalCase formatowanie:</span><span class="sxs-lookup"><span data-stu-id="413a3-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="413a3-185">Następująca metoda akcji wywołuje [ControllerBase. problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) w celu utworzenia <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="413a3-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="413a3-186">O powyższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="413a3-186">With the preceding code:</span></span>

  * <span data-ttu-id="413a3-187">`https://localhost:5001/WeatherForecast/temperature` zwraca PascalCase.</span><span class="sxs-lookup"><span data-stu-id="413a3-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="413a3-188">`https://localhost:5001/WeatherForecast/error` zwraca camelCase.</span><span class="sxs-lookup"><span data-stu-id="413a3-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="413a3-189">Odpowiedź na błąd jest zawsze camelCase, nawet jeśli aplikacja ustawia format na PascalCase.</span><span class="sxs-lookup"><span data-stu-id="413a3-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="413a3-190">`ProblemDetails` zgodnie ze [specyfikacją RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), która określa małe litery</span><span class="sxs-lookup"><span data-stu-id="413a3-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="413a3-191">Poniższy kod ustawia PascalCase i dodaje niestandardowy konwerter:</span><span class="sxs-lookup"><span data-stu-id="413a3-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="413a3-192">Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu polecenia `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="413a3-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="413a3-193">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="413a3-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="413a3-194">Dodawanie obsługi formatu JSON Newtonsoft.Jsna podstawie</span><span class="sxs-lookup"><span data-stu-id="413a3-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="413a3-195">Przed ASP.NET Core 3,0 stosowane są domyślne elementy formatujące JSON zaimplementowane przy użyciu `Newtonsoft.Json` pakietu.</span><span class="sxs-lookup"><span data-stu-id="413a3-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="413a3-196">W ASP.NET Core 3,0 lub nowszych domyślne elementy formatujące JSON są oparte na `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="413a3-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="413a3-197">Obsługa `Newtonsoft.Json` opartych na programie formatującego i funkcji jest dostępna przez zainstalowanie [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pakietu NuGet i skonfigurowanie go w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="413a3-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="413a3-198">W powyższym kodzie, wywołanie do `AddNewtonsoftJson` konfigurowania następujących funkcji Web API, MVC i Razor Pages do użycia `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="413a3-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="413a3-199">Wejściowe i wyjściowe elementy formatujące, które odczytują i zapisują kod JSON</span><span class="sxs-lookup"><span data-stu-id="413a3-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="413a3-200">Poprawka JSON</span><span class="sxs-lookup"><span data-stu-id="413a3-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="413a3-201">TempData</span><span class="sxs-lookup"><span data-stu-id="413a3-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="413a3-202">Niektóre funkcje mogą nie współdziałać z modułami `System.Text.Json` formatującego opartymi na języku i wymagają odwołania do elementów `Newtonsoft.Json` formatujących opartych na bazie.</span><span class="sxs-lookup"><span data-stu-id="413a3-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="413a3-203">Kontynuuj korzystanie z programu `Newtonsoft.Json` formatującego w oparciu o aplikacje:</span><span class="sxs-lookup"><span data-stu-id="413a3-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="413a3-204">Używa `Newtonsoft.Json` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="413a3-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="413a3-205">Na przykład: `[JsonProperty]` lub `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="413a3-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="413a3-206">Dostosowuje ustawienia serializacji.</span><span class="sxs-lookup"><span data-stu-id="413a3-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="413a3-207">Opiera się na `Newtonsoft.Json` udostępnianych funkcjach.</span><span class="sxs-lookup"><span data-stu-id="413a3-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="413a3-208">Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="413a3-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="413a3-209">Przed ASP.NET Core 3,0, `JsonResult.SerializerSettings` akceptuje wystąpienie `JsonSerializerSettings` , które jest specyficzne dla `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="413a3-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="413a3-210">Generuje dokumentację [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="413a3-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="413a3-211">Funkcje dla elementów `Newtonsoft.Json` formatujących opartych na programie można skonfigurować przy użyciu `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="413a3-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="413a3-212">Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu polecenia `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="413a3-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="413a3-213">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="413a3-213">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="413a3-214">Dodawanie obsługi formatu XML</span><span class="sxs-lookup"><span data-stu-id="413a3-214">Add XML format support</span></span>

<span data-ttu-id="413a3-215">Formatowanie XML wymaga pakietu NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="413a3-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="413a3-216">Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="413a3-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="413a3-217">Poprzedni kod serializacji wyników przy użyciu `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="413a3-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="413a3-218">W przypadku korzystania z powyższego kodu metody kontrolera powinny zwrócić odpowiedni format na podstawie nagłówka żądania `Accept` .</span><span class="sxs-lookup"><span data-stu-id="413a3-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="413a3-219">Określ format</span><span class="sxs-lookup"><span data-stu-id="413a3-219">Specify a format</span></span>

<span data-ttu-id="413a3-220">Aby ograniczyć formaty odpowiedzi, Zastosuj [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr.</span><span class="sxs-lookup"><span data-stu-id="413a3-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="413a3-221">Podobnie jak większość [filtrów](xref:mvc/controllers/filters), `[Produces]` można zastosować do akcji, kontrolera lub zakresu globalnego:</span><span class="sxs-lookup"><span data-stu-id="413a3-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="413a3-222">Poprzedni [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr:</span><span class="sxs-lookup"><span data-stu-id="413a3-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="413a3-223">Wymusza, aby wszystkie akcje w ramach kontrolera zwracały odpowiedzi w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="413a3-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="413a3-224">Jeśli inne elementy formatujące są skonfigurowane, a klient określi inny format, zwracany jest kod JSON.</span><span class="sxs-lookup"><span data-stu-id="413a3-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="413a3-225">Aby uzyskać więcej informacji, zobacz [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="413a3-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="413a3-226">Specjalne elementy formatujące Case</span><span class="sxs-lookup"><span data-stu-id="413a3-226">Special case formatters</span></span>

<span data-ttu-id="413a3-227">Niektóre specjalne przypadki są implementowane przy użyciu wbudowanych elementów formatujących.</span><span class="sxs-lookup"><span data-stu-id="413a3-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="413a3-228">Domyślnie `string` typy zwracane są formatowane jako *tekst/zwykły* (*text/html* , jeśli żąda się za pośrednictwem `Accept` nagłówka).</span><span class="sxs-lookup"><span data-stu-id="413a3-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="413a3-229">Takie zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="413a3-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="413a3-230">Elementy formatujące są usuwane w `ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="413a3-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="413a3-231">Akcje, które mają typ zwracany obiektu modelu zwracają, `204 No Content` gdy zwracają `null` .</span><span class="sxs-lookup"><span data-stu-id="413a3-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="413a3-232">Takie zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="413a3-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="413a3-233">Poniższy kod usuwa `StringOutputFormatter` i `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="413a3-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="413a3-234">Bez `StringOutputFormatter` , wbudowany typ programu FORMATUJĄCEGO JSON formatuje `string` typy zwracane.</span><span class="sxs-lookup"><span data-stu-id="413a3-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="413a3-235">Jeśli wbudowany program formatujący JSON jest usuwany, a element formatujący XML jest dostępny, format XML programu formatującego jest `string` typem zwracanym.</span><span class="sxs-lookup"><span data-stu-id="413a3-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="413a3-236">W przeciwnym razie zwracane `string` typy zwracają `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="413a3-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="413a3-237">Bez `HttpNoContentOutputFormatter` obiektów o wartości null są formatowane przy użyciu skonfigurowanego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="413a3-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="413a3-238">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="413a3-238">For example:</span></span>

* <span data-ttu-id="413a3-239">Program formatujący JSON zwraca odpowiedź z treścią `null` .</span><span class="sxs-lookup"><span data-stu-id="413a3-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="413a3-240">Program formatujący XML zwraca pusty element XML z `xsi:nil="true"` zestawem atrybutów.</span><span class="sxs-lookup"><span data-stu-id="413a3-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="413a3-241">Mapowania adresów URL w formacie odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="413a3-241">Response format URL mappings</span></span>

<span data-ttu-id="413a3-242">Klienci mogą zażądać określonego formatu w ramach adresu URL, na przykład:</span><span class="sxs-lookup"><span data-stu-id="413a3-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="413a3-243">W ciągu zapytania lub części ścieżki.</span><span class="sxs-lookup"><span data-stu-id="413a3-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="413a3-244">Przy użyciu rozszerzenia pliku specyficznego dla formatu, takiego jak. XML lub. JSON.</span><span class="sxs-lookup"><span data-stu-id="413a3-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="413a3-245">Mapowanie ze ścieżki żądania należy określić w marszrucie używanej przez interfejs API.</span><span class="sxs-lookup"><span data-stu-id="413a3-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="413a3-246">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="413a3-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="413a3-247">Poprzednia trasa pozwala określić żądany format jako opcjonalne rozszerzenie pliku.</span><span class="sxs-lookup"><span data-stu-id="413a3-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="413a3-248">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Atrybut sprawdza obecność wartości format w `RouteData` i mapuje format odpowiedzi do odpowiedniego programu formatującego podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="413a3-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="413a3-249">Trasa</span><span class="sxs-lookup"><span data-stu-id="413a3-249">Route</span></span>        |             <span data-ttu-id="413a3-250">EQ</span><span class="sxs-lookup"><span data-stu-id="413a3-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="413a3-251">Domyślny program formatujący dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="413a3-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="413a3-252">Program formatujący JSON (jeśli jest skonfigurowany)</span><span class="sxs-lookup"><span data-stu-id="413a3-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="413a3-253">Program formatujący XML (jeśli jest skonfigurowany)</span><span class="sxs-lookup"><span data-stu-id="413a3-253">The XML formatter (if configured)</span></span>  |
