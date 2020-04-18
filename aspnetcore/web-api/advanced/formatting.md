---
title: Formatowanie danych odpowiedzi w ASP.NET Core Web API
author: ardalis
description: Dowiedz się, jak formatować dane odpowiedzi w ASP.NET Core Web API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642709"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="11791-103">Formatowanie danych odpowiedzi w ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="11791-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="11791-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Steve [Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="11791-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="11791-105">ASP.NET Core MVC obsługuje formatowanie danych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11791-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="11791-106">Dane odpowiedzi mogą być formatowane przy użyciu określonych formatów lub w odpowiedzi na żądany format klienta.</span><span class="sxs-lookup"><span data-stu-id="11791-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="11791-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11791-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="11791-108">Wyniki akcji specyficzne dla formatu</span><span class="sxs-lookup"><span data-stu-id="11791-108">Format-specific Action Results</span></span>

<span data-ttu-id="11791-109">Niektóre typy wyników akcji są specyficzne <xref:Microsoft.AspNetCore.Mvc.JsonResult> dla <xref:Microsoft.AspNetCore.Mvc.ContentResult>określonego formatu, na przykład i .</span><span class="sxs-lookup"><span data-stu-id="11791-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="11791-110">Akcje mogą zwracać wyniki sformatowane w określonym formacie, niezależnie od preferencji klienta.</span><span class="sxs-lookup"><span data-stu-id="11791-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="11791-111">Na przykład zwraca `JsonResult` dane w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="11791-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="11791-112">Zwracanie `ContentResult` lub ciąg zwraca dane ciągu w formacie zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="11791-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="11791-113">Akcja nie jest wymagana do zwrócenia dowolnego określonego typu.</span><span class="sxs-lookup"><span data-stu-id="11791-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="11791-114">ASP.NET Core obsługuje dowolną wartość zwracaną obiektu.</span><span class="sxs-lookup"><span data-stu-id="11791-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="11791-115">Wyniki z akcji, które <xref:Microsoft.AspNetCore.Mvc.IActionResult> zwracają obiekty, które <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> nie są typami są serializowane przy użyciu odpowiedniej implementacji.</span><span class="sxs-lookup"><span data-stu-id="11791-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="11791-116">Aby uzyskać więcej informacji, zobacz <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="11791-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="11791-117">Wbudowana metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> pomocnika zwraca dane w formacie JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="11791-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="11791-118">Przykładowe pobieranie zwraca listę autorów.</span><span class="sxs-lookup"><span data-stu-id="11791-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="11791-119">Korzystanie z narzędzi programistycznych przeglądarki F12 lub [Listonosz](https://www.getpostman.com/tools) z poprzednim kodem:</span><span class="sxs-lookup"><span data-stu-id="11791-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="11791-120">Zostanie wyświetlony nagłówek odpowiedzi zawierający **typ zawartości.** `application/json; charset=utf-8`</span><span class="sxs-lookup"><span data-stu-id="11791-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="11791-121">Zostaną wyświetlone nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="11791-121">The request headers are displayed.</span></span> <span data-ttu-id="11791-122">Na przykład `Accept` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="11791-122">For example, the `Accept` header.</span></span> <span data-ttu-id="11791-123">Nagłówek `Accept` jest ignorowany przez poprzedni kod.</span><span class="sxs-lookup"><span data-stu-id="11791-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="11791-124">Aby zwrócić dane sformatowane w postaci zwykłego tekstu, użyj <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> i <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocnika:</span><span class="sxs-lookup"><span data-stu-id="11791-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="11791-125">W poprzednim kodzie `Content-Type` zwracany `text/plain`jest .</span><span class="sxs-lookup"><span data-stu-id="11791-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="11791-126">Zwracanie ciągu `Content-Type` `text/plain`zapewnia:</span><span class="sxs-lookup"><span data-stu-id="11791-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="11791-127">W przypadku akcji z `IActionResult`wieloma typami zwracania należy zwrócić .</span><span class="sxs-lookup"><span data-stu-id="11791-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="11791-128">Na przykład zwracanie różnych kodów stanu HTTP na podstawie wyniku wykonanych operacji.</span><span class="sxs-lookup"><span data-stu-id="11791-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="11791-129">Negocjacje treści</span><span class="sxs-lookup"><span data-stu-id="11791-129">Content negotiation</span></span>

<span data-ttu-id="11791-130">Negocjacja zawartości odbywa się, gdy klient określa [nagłówek Akceptuj](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="11791-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="11791-131">Domyślnym formatem używanym przez ASP.NET Core jest [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="11791-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="11791-132">Negocjacje treści to:</span><span class="sxs-lookup"><span data-stu-id="11791-132">Content negotiation is:</span></span>

* <span data-ttu-id="11791-133">Zaimplementowane przez <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="11791-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="11791-134">Wbudowane w wyniki akcji specyficzne dla kodu stanu zwrócone z metod pomocnika.</span><span class="sxs-lookup"><span data-stu-id="11791-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="11791-135">Metody pomocnicze wyników akcji `ObjectResult`są oparte na programie .</span><span class="sxs-lookup"><span data-stu-id="11791-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="11791-136">Po zwróceniu typu modelu, `ObjectResult`typem zwracanym jest .</span><span class="sxs-lookup"><span data-stu-id="11791-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="11791-137">Następująca metoda akcji `Ok` używa `NotFound` i metody pomocnicze:</span><span class="sxs-lookup"><span data-stu-id="11791-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="11791-138">Domyślnie ASP.NET core obsługuje `application/json`i `text/json`typy `text/plain` nośników.</span><span class="sxs-lookup"><span data-stu-id="11791-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="11791-139">Narzędzia, takie jak [Fiddler](https://www.telerik.com/fiddler) lub `Accept` [Listonosz,](https://www.getpostman.com/tools) mogą ustawić nagłówek żądania, aby określić format zwracany.</span><span class="sxs-lookup"><span data-stu-id="11791-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="11791-140">Gdy `Accept` nagłówek zawiera typ, który obsługuje serwer, zwracany jest ten typ.</span><span class="sxs-lookup"><span data-stu-id="11791-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="11791-141">W następnej sekcji pokazano, jak dodać dodatkowe formaterów.</span><span class="sxs-lookup"><span data-stu-id="11791-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="11791-142">Akcje kontrolera mogą zwracać POCO (zwykły stary obiekt CLR).</span><span class="sxs-lookup"><span data-stu-id="11791-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="11791-143">Po poco jest zwracany, środowisko wykonawcze automatycznie `ObjectResult` tworzy, który zawija obiekt.</span><span class="sxs-lookup"><span data-stu-id="11791-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="11791-144">Klient pobiera sformatowany obiekt seryjny.</span><span class="sxs-lookup"><span data-stu-id="11791-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="11791-145">Jeśli zwracany obiekt `null`jest `204 No Content` zwracany, zwracana jest odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="11791-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="11791-146">Zwracanie typu obiektu:</span><span class="sxs-lookup"><span data-stu-id="11791-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="11791-147">W poprzednim kodzie żądanie prawidłowego aliasu `200 OK` autora zwraca odpowiedź z danymi autora.</span><span class="sxs-lookup"><span data-stu-id="11791-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="11791-148">Żądanie nieprawidłowego aliasu `204 No Content` zwraca odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="11791-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="11791-149">Nagłówek Zaakceptuj</span><span class="sxs-lookup"><span data-stu-id="11791-149">The Accept header</span></span>

<span data-ttu-id="11791-150">*Negocjacja zawartości* odbywa `Accept` się, gdy nagłówek pojawia się w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="11791-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="11791-151">Gdy żądanie zawiera nagłówek akceptowania, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="11791-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="11791-152">Wylicza typy nośników w nagłówku accept w kolejności preferencji.</span><span class="sxs-lookup"><span data-stu-id="11791-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="11791-153">Próbuje znaleźć formater, który może uzyskać odpowiedź w jednym z formatów określonych.</span><span class="sxs-lookup"><span data-stu-id="11791-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="11791-154">Jeśli nie zostanie znaleziony żaden program formater, który może spełnić żądanie klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="11791-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="11791-155">`406 Not Acceptable` Zwraca, <xref:Microsoft.AspNetCore.Mvc.MvcOptions> jeśli został ustawiony lub -</span><span class="sxs-lookup"><span data-stu-id="11791-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="11791-156">Próbuje znaleźć pierwszy program formatu, który może spowodować odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="11791-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="11791-157">Jeśli dla żądanego formatu nie skonfigurowano żadnego formatera, używany jest pierwszy formater, który może sformatować obiekt.</span><span class="sxs-lookup"><span data-stu-id="11791-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="11791-158">Jeśli `Accept` w żądaniu nie ma nagłówka:</span><span class="sxs-lookup"><span data-stu-id="11791-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="11791-159">Pierwszy formater, który może obsługiwać obiekt jest używany do serializacji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11791-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="11791-160">Nie ma żadnych negocjacji.</span><span class="sxs-lookup"><span data-stu-id="11791-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="11791-161">Serwer określa, jaki format ma zwrócić.</span><span class="sxs-lookup"><span data-stu-id="11791-161">The server is determining what format to return.</span></span>

<span data-ttu-id="11791-162">Jeśli nagłówek Akceptuj `*/*`zawiera , nagłówek `RespectBrowserAcceptHeader` jest ignorowany, <xref:Microsoft.AspNetCore.Mvc.MvcOptions>chyba że jest ustawiona na true on .</span><span class="sxs-lookup"><span data-stu-id="11791-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="11791-163">Przeglądarki i negocjacje treści</span><span class="sxs-lookup"><span data-stu-id="11791-163">Browsers and content negotiation</span></span>

<span data-ttu-id="11791-164">W przeciwieństwie do typowych `Accept` klientów interfejsu API przeglądarki internetowe dostarczają nagłówki.</span><span class="sxs-lookup"><span data-stu-id="11791-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="11791-165">Przeglądarka internetowa określić wiele formatów, w tym symbole wieloznaczne.</span><span class="sxs-lookup"><span data-stu-id="11791-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="11791-166">Domyślnie, gdy struktura wykryje, że żądanie pochodzi z przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="11791-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="11791-167">Nagłówek `Accept` jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="11791-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="11791-168">Zawartość jest zwracana w JSON, chyba że skonfigurowano inaczej.</span><span class="sxs-lookup"><span data-stu-id="11791-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="11791-169">Zapewnia to bardziej spójne środowisko w przeglądarkach podczas korzystania z interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="11791-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="11791-170">Aby skonfigurować aplikację do honorujskusje nagłówki akceptujące przeglądarki, ustaw na: <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`</span><span class="sxs-lookup"><span data-stu-id="11791-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="11791-171">Konfigurowanie programów formatu</span><span class="sxs-lookup"><span data-stu-id="11791-171">Configure formatters</span></span>

<span data-ttu-id="11791-172">Aplikacje, które muszą obsługiwać dodatkowe formaty można dodać odpowiednie pakiety NuGet i skonfigurować obsługę.</span><span class="sxs-lookup"><span data-stu-id="11791-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="11791-173">Istnieją oddzielne formatery dla wejścia i wyjścia.</span><span class="sxs-lookup"><span data-stu-id="11791-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="11791-174">Formaterów wprowadzania są używane przez [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="11791-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="11791-175">Formaterów danych wyjściowych są używane do formatowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11791-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="11791-176">Aby uzyskać informacje dotyczące tworzenia niestandardowego formatera, zobacz [Programy formatujące niestandardowe](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="11791-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="11791-177">Dodawanie obsługi formatów XML</span><span class="sxs-lookup"><span data-stu-id="11791-177">Add XML format support</span></span>

<span data-ttu-id="11791-178">Formaterów XML zaimplementowanych przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*></span><span class="sxs-lookup"><span data-stu-id="11791-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="11791-179">Poprzedni kod serializuje wyniki `XmlSerializer`za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="11791-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="11791-180">Korzystając z poprzedniego kodu, metody kontrolera zwracają odpowiedni format `Accept` na podstawie nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="11791-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="11791-181">Konfigurowanie programów formaterów opartych na systemie.Text.Json</span><span class="sxs-lookup"><span data-stu-id="11791-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="11791-182">Funkcje `System.Text.Json`programów formaterów opartych `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`na programach można konfigurować za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="11791-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="11791-183">Opcje serializacji wyjściowej, na podstawie akcji, można `JsonResult`skonfigurować za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="11791-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="11791-184">Przykład:</span><span class="sxs-lookup"><span data-stu-id="11791-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="11791-185">Dodaj obsługę formatu JSON opartego na newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="11791-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="11791-186">Przed ASP.NET Core 3.0 domyślnie używane formaterów JSON zaimplementowane przy użyciu `Newtonsoft.Json` pakietu.</span><span class="sxs-lookup"><span data-stu-id="11791-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="11791-187">W ASP.NET Core 3.0 lub nowszym oparte są na `System.Text.Json`domyślnych formaterach JSON.</span><span class="sxs-lookup"><span data-stu-id="11791-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="11791-188">Obsługa `Newtonsoft.Json` opartych formaterów i funkcji jest dostępna po zainstalowaniu pakietu [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet i skonfigurowaniu go w `Startup.ConfigureServices`programie .</span><span class="sxs-lookup"><span data-stu-id="11791-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="11791-189">Niektóre funkcje mogą `System.Text.Json`nie działać dobrze z programami `Newtonsoft.Json`formatującym opartymi na podstawie i wymagają odwołania do programów formatnicznych opartych na tym.</span><span class="sxs-lookup"><span data-stu-id="11791-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="11791-190">Kontynuuj korzystanie `Newtonsoft.Json`z programów formatniczych opartych na aplikacji:</span><span class="sxs-lookup"><span data-stu-id="11791-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="11791-191">Używa `Newtonsoft.Json` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="11791-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="11791-192">Na przykład: `[JsonProperty]` lub `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="11791-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="11791-193">Dostosowuje ustawienia serializacji.</span><span class="sxs-lookup"><span data-stu-id="11791-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="11791-194">Opiera się `Newtonsoft.Json` na funkcjach, które zapewnia.</span><span class="sxs-lookup"><span data-stu-id="11791-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="11791-195">Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="11791-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="11791-196">Przed ASP.NET Core 3.0 akceptuje `JsonResult.SerializerSettings` `JsonSerializerSettings` wystąpienie, które jest `Newtonsoft.Json`specyficzne dla .</span><span class="sxs-lookup"><span data-stu-id="11791-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="11791-197">Generuje dokumentację [OpenAPI.](<xref:tutorials/web-api-help-pages-using-swagger>)</span><span class="sxs-lookup"><span data-stu-id="11791-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="11791-198">Funkcje `Newtonsoft.Json`programów formaterów opartych `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`na programach można konfigurować za pomocą:</span><span class="sxs-lookup"><span data-stu-id="11791-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="11791-199">Opcje serializacji wyjściowej, na podstawie akcji, można `JsonResult`skonfigurować za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="11791-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="11791-200">Przykład:</span><span class="sxs-lookup"><span data-stu-id="11791-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="11791-201">Dodawanie obsługi formatów XML</span><span class="sxs-lookup"><span data-stu-id="11791-201">Add XML format support</span></span>

<span data-ttu-id="11791-202">Formatowanie XML wymaga pakietu [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="11791-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="11791-203">Formaterów XML zaimplementowanych przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*></span><span class="sxs-lookup"><span data-stu-id="11791-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="11791-204">Poprzedni kod serializuje wyniki `XmlSerializer`za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="11791-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="11791-205">Korzystając z poprzedniego kodu, metody kontrolera powinny zwracać odpowiedni `Accept` format na podstawie nagłówka żądania.</span><span class="sxs-lookup"><span data-stu-id="11791-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="11791-206">Określanie formatu</span><span class="sxs-lookup"><span data-stu-id="11791-206">Specify a format</span></span>

<span data-ttu-id="11791-207">Aby ograniczyć formaty odpowiedzi, zastosuj [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr.</span><span class="sxs-lookup"><span data-stu-id="11791-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="11791-208">Podobnie jak większość `[Produces]` [filtrów,](xref:mvc/controllers/filters)można zastosować w zakresie akcji, kontrolera lub zakresu globalnego:</span><span class="sxs-lookup"><span data-stu-id="11791-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="11791-209">Poprzedni [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:</span><span class="sxs-lookup"><span data-stu-id="11791-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="11791-210">Wymusza wszystkie akcje w kontrolerze, aby zwrócić odpowiedzi w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="11791-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="11791-211">Jeśli inne formaterów są skonfigurowane i klient określa inny format, JSON jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="11791-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="11791-212">Aby uzyskać więcej informacji, zobacz [Filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="11791-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="11791-213">Formaterów specjalnych przypadków</span><span class="sxs-lookup"><span data-stu-id="11791-213">Special case formatters</span></span>

<span data-ttu-id="11791-214">Niektóre specjalne przypadki są implementowane przy użyciu wbudowanych formaterów.</span><span class="sxs-lookup"><span data-stu-id="11791-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="11791-215">Domyślnie `string` typy zwracane są formatowane jako *tekst/zwykły* *(tekst/html,* jeśli jest to wymagane za pośrednictwem nagłówka). `Accept`</span><span class="sxs-lookup"><span data-stu-id="11791-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="11791-216">To zachowanie można usunąć, <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>usuwając plik .</span><span class="sxs-lookup"><span data-stu-id="11791-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="11791-217">Formaterów są usuwane w metodzie. `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="11791-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="11791-218">Akcje, które mają typ `204 No Content` zwracany `null`typu obiektu modelu, zwracają po zwróceniu .</span><span class="sxs-lookup"><span data-stu-id="11791-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="11791-219">To zachowanie można usunąć, <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>usuwając plik .</span><span class="sxs-lookup"><span data-stu-id="11791-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="11791-220">Poniższy kod usuwa `StringOutputFormatter` `HttpNoContentOutputFormatter`i .</span><span class="sxs-lookup"><span data-stu-id="11791-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="11791-221">Bez `StringOutputFormatter`, wbudowane formaty formatów `string` JSON zwraca typy.</span><span class="sxs-lookup"><span data-stu-id="11791-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="11791-222">Jeśli wbudowany formater JSON zostanie usunięty i dostępny jest formater XML, `string` formaty formatów XML zwracają typy.</span><span class="sxs-lookup"><span data-stu-id="11791-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="11791-223">W `string` przeciwnym razie `406 Not Acceptable`zwracane są typy zwracane .</span><span class="sxs-lookup"><span data-stu-id="11791-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="11791-224">Bez `HttpNoContentOutputFormatter`, null obiekty są formatowane za pomocą skonfigurowany formater.</span><span class="sxs-lookup"><span data-stu-id="11791-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="11791-225">Przykład:</span><span class="sxs-lookup"><span data-stu-id="11791-225">For example:</span></span>

* <span data-ttu-id="11791-226">Formater JSON zwraca odpowiedź z `null`treścią .</span><span class="sxs-lookup"><span data-stu-id="11791-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="11791-227">Formater XML zwraca pusty element XML `xsi:nil="true"` z zestawem atrybutów.</span><span class="sxs-lookup"><span data-stu-id="11791-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="11791-228">Mapowania adresów URL formatu odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="11791-228">Response format URL mappings</span></span>

<span data-ttu-id="11791-229">Klienci mogą zażądać określonego formatu jako części adresu URL, na przykład:</span><span class="sxs-lookup"><span data-stu-id="11791-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="11791-230">W ciągu zapytania lub części ścieżki.</span><span class="sxs-lookup"><span data-stu-id="11791-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="11791-231">Za pomocą rozszerzenia pliku specyficzne dla formatu, takich jak xml lub .json.</span><span class="sxs-lookup"><span data-stu-id="11791-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="11791-232">Mapowanie ze ścieżki żądania powinny być określone w trasie, którą używa interfejs API.</span><span class="sxs-lookup"><span data-stu-id="11791-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="11791-233">Przykład:</span><span class="sxs-lookup"><span data-stu-id="11791-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="11791-234">Poprzednia trasa umożliwia określony żądany format jako opcjonalne rozszerzenie pliku.</span><span class="sxs-lookup"><span data-stu-id="11791-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="11791-235">Atrybut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) sprawdza istnienie wartości formatu w `RouteData` formacie i mapuje format odpowiedzi do odpowiedniego formatera podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11791-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="11791-236">Trasa</span><span class="sxs-lookup"><span data-stu-id="11791-236">Route</span></span>        |             <span data-ttu-id="11791-237">Program formatujący</span><span class="sxs-lookup"><span data-stu-id="11791-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="11791-238">Domyślny formater danych wyjściowych</span><span class="sxs-lookup"><span data-stu-id="11791-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="11791-239">Formater JSON (jeśli jest skonfigurowany)</span><span class="sxs-lookup"><span data-stu-id="11791-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="11791-240">Formater XML (jeśli jest skonfigurowany)</span><span class="sxs-lookup"><span data-stu-id="11791-240">The XML formatter (if configured)</span></span>  |
