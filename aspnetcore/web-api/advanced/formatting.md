---
<span data-ttu-id="2e33e-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-102">'Blazor'</span></span>
- <span data-ttu-id="2e33e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-103">'Identity'</span></span>
- <span data-ttu-id="2e33e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-105">'Razor'</span></span>
- <span data-ttu-id="2e33e-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="2e33e-107">Formatowanie danych odpowiedzi w ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="2e33e-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="2e33e-108">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2e33e-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2e33e-109">ASP.NET Core MVC obsługuje formatowanie danych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2e33e-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="2e33e-110">Dane odpowiedzi można sformatować przy użyciu określonych formatów lub w odpowiedzi na żądany format klienta.</span><span class="sxs-lookup"><span data-stu-id="2e33e-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="2e33e-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2e33e-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="2e33e-112">Wyniki akcji specyficzne dla formatu</span><span class="sxs-lookup"><span data-stu-id="2e33e-112">Format-specific Action Results</span></span>

<span data-ttu-id="2e33e-113">Niektóre typy wyników akcji są specyficzne dla określonego formatu, takiego jak <xref:Microsoft.AspNetCore.Mvc.JsonResult> i <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="2e33e-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="2e33e-114">Akcje mogą zwracać wyniki sformatowane w określonym formacie, niezależnie od preferencji klienta.</span><span class="sxs-lookup"><span data-stu-id="2e33e-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="2e33e-115">Na przykład zwracanie `JsonResult` zwraca dane w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="2e33e-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="2e33e-116">Zwracanie `ContentResult` lub ciąg zwraca dane ciągu w formacie zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="2e33e-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="2e33e-117">Akcja nie jest wymagana do zwrócenia żadnego określonego typu.</span><span class="sxs-lookup"><span data-stu-id="2e33e-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="2e33e-118">ASP.NET Core obsługuje dowolną wartość zwracaną przez obiekt.</span><span class="sxs-lookup"><span data-stu-id="2e33e-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="2e33e-119">Wyniki akcji, które zwracają obiekty, które nie są <xref:Microsoft.AspNetCore.Mvc.IActionResult> typami, są serializowane przy użyciu odpowiedniej <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementacji.</span><span class="sxs-lookup"><span data-stu-id="2e33e-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="2e33e-120">Aby uzyskać więcej informacji, zobacz <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="2e33e-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="2e33e-121">Wbudowana metoda pomocnika <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> zwraca dane sformatowane w formacie JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="2e33e-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="2e33e-122">Pobieranie próbek zwraca listę autorów.</span><span class="sxs-lookup"><span data-stu-id="2e33e-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="2e33e-123">Za pomocą narzędzi deweloperskich przeglądarki F12 lub [po](https://www.getpostman.com/tools) powyższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="2e33e-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="2e33e-124">Zostanie wyświetlony nagłówek odpowiedzi zawierający **Typ zawartości:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="2e33e-125">Wyświetlane są nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="2e33e-125">The request headers are displayed.</span></span> <span data-ttu-id="2e33e-126">Na przykład `Accept` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="2e33e-126">For example, the `Accept` header.</span></span> <span data-ttu-id="2e33e-127">`Accept`Nagłówek jest ignorowany przez poprzedni kod.</span><span class="sxs-lookup"><span data-stu-id="2e33e-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="2e33e-128">Aby zwrócić dane w formacie zwykłego tekstu, użyj <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> i <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocnika:</span><span class="sxs-lookup"><span data-stu-id="2e33e-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="2e33e-129">W powyższym kodzie `Content-Type` zwraca wartość `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="2e33e-130">Zwracanie ciągu `Content-Type` z `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="2e33e-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="2e33e-131">W przypadku akcji z wieloma zwracanymi typami zwracamy `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="2e33e-132">Na przykład zwrócenie różnych kodów stanu HTTP w oparciu o wynik wykonanych operacji.</span><span class="sxs-lookup"><span data-stu-id="2e33e-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="2e33e-133">Negocjowanie zawartości</span><span class="sxs-lookup"><span data-stu-id="2e33e-133">Content negotiation</span></span>

<span data-ttu-id="2e33e-134">Negocjowanie zawartości odbywa się, gdy klient określi [nagłówek Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="2e33e-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="2e33e-135">Domyślny format używany przez ASP.NET Core to [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="2e33e-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="2e33e-136">Negocjowanie zawartości:</span><span class="sxs-lookup"><span data-stu-id="2e33e-136">Content negotiation is:</span></span>

* <span data-ttu-id="2e33e-137">Zaimplementowane przez <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="2e33e-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="2e33e-138">Wbudowane wyniki akcji specyficzne dla kodu stanu zwracane z metod pomocnika.</span><span class="sxs-lookup"><span data-stu-id="2e33e-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="2e33e-139">Metody pomocnika wyników akcji są oparte na `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="2e33e-140">Po zwróceniu typu modelu zwracanym typem jest `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="2e33e-141">W poniższej metodzie działania są `Ok` stosowane `NotFound` metody pomocnika i:</span><span class="sxs-lookup"><span data-stu-id="2e33e-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="2e33e-142">Domyślnie ASP.NET Core obsługuje `application/json` `text/json` `text/plain` typy nośników, i.</span><span class="sxs-lookup"><span data-stu-id="2e33e-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="2e33e-143">Narzędzia takie jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/tools) mogą ustawić `Accept` nagłówek żądania w celu określenia formatu zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="2e33e-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="2e33e-144">Gdy `Accept` Nagłówek zawiera typ obsługiwany przez serwer, zwracany jest ten typ.</span><span class="sxs-lookup"><span data-stu-id="2e33e-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="2e33e-145">W następnej sekcji pokazano, jak dodać dodatkowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="2e33e-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="2e33e-146">Akcje kontrolera mogą zwracać POCOs (zwykłe stare obiekty CLR).</span><span class="sxs-lookup"><span data-stu-id="2e33e-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="2e33e-147">Gdy zostanie zwrócona wartość POCO, środowisko uruchomieniowe automatycznie tworzy `ObjectResult` , które zawija obiekt.</span><span class="sxs-lookup"><span data-stu-id="2e33e-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="2e33e-148">Klient pobiera sformatowany obiekt Zserializowany.</span><span class="sxs-lookup"><span data-stu-id="2e33e-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="2e33e-149">Jeśli zwracany obiekt jest `null` `204 No Content` zwracany, zwracana jest odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="2e33e-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="2e33e-150">Zwracanie typu obiektu:</span><span class="sxs-lookup"><span data-stu-id="2e33e-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="2e33e-151">W poprzednim kodzie żądanie poprawnego aliasu autora zwraca `200 OK` odpowiedź z danymi autora.</span><span class="sxs-lookup"><span data-stu-id="2e33e-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="2e33e-152">Żądanie dotyczące nieprawidłowego aliasu zwraca `204 No Content` odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="2e33e-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="2e33e-153">Nagłówek Accept</span><span class="sxs-lookup"><span data-stu-id="2e33e-153">The Accept header</span></span>

<span data-ttu-id="2e33e-154">*Negocjowanie* zawartości odbywa się po `Accept` pojawieniu się nagłówka w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="2e33e-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="2e33e-155">Gdy żądanie zawiera nagłówek Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2e33e-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="2e33e-156">Wylicza typy nośników w nagłówku Accept w kolejności preferencji.</span><span class="sxs-lookup"><span data-stu-id="2e33e-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="2e33e-157">Próbuje znaleźć program formatujący, który może wygenerować odpowiedź w jednym z określonych formatów.</span><span class="sxs-lookup"><span data-stu-id="2e33e-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="2e33e-158">Jeśli nie zostanie znaleziony żaden program formatujący, który może spełnić żądanie klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2e33e-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="2e33e-159">Zwraca `406 Not Acceptable` wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions> , jeśli została ustawiona, lub-</span><span class="sxs-lookup"><span data-stu-id="2e33e-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="2e33e-160">Próbuje znaleźć pierwszy program formatujący, który może wygenerować odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="2e33e-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="2e33e-161">Jeśli nie skonfigurowano programu formatującego dla żądanego formatu, jest używany pierwszy program formatujący, który może formatować obiekt.</span><span class="sxs-lookup"><span data-stu-id="2e33e-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="2e33e-162">Jeśli `Accept` w żądaniu nie zostanie wyświetlony nagłówek:</span><span class="sxs-lookup"><span data-stu-id="2e33e-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="2e33e-163">Pierwszy program formatujący, który może obsłużyć obiekt, jest używany do serializacji odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2e33e-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="2e33e-164">Nie ma żadnej negocjacji.</span><span class="sxs-lookup"><span data-stu-id="2e33e-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="2e33e-165">Serwer określa format do zwrócenia.</span><span class="sxs-lookup"><span data-stu-id="2e33e-165">The server is determining what format to return.</span></span>

<span data-ttu-id="2e33e-166">Jeśli nagłówek Accept zawiera `*/*` , nagłówek jest ignorowany, chyba że `RespectBrowserAcceptHeader` jest ustawiona na wartość true (prawda) <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="2e33e-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="2e33e-167">Przeglądarki i negocjacje zawartości</span><span class="sxs-lookup"><span data-stu-id="2e33e-167">Browsers and content negotiation</span></span>

<span data-ttu-id="2e33e-168">W przeciwieństwie do typowych klientów interfejsu API, przeglądarki sieci Web dostarczają `Accept` nagłówki.</span><span class="sxs-lookup"><span data-stu-id="2e33e-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="2e33e-169">Przeglądarka sieci Web określa wiele formatów, w tym symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="2e33e-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="2e33e-170">Domyślnie, gdy struktura wykryje, że żądanie pochodzi z przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="2e33e-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="2e33e-171">`Accept`Nagłówek jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="2e33e-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="2e33e-172">Zawartość jest zwracana w formacie JSON, o ile nie została skonfigurowana inaczej.</span><span class="sxs-lookup"><span data-stu-id="2e33e-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="2e33e-173">Zapewnia to bardziej spójne środowisko w przeglądarkach podczas używania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="2e33e-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="2e33e-174">Aby skonfigurować aplikację do honorowania nagłówków akceptowanych przez przeglądarkę, ustaw wartość <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true` :</span><span class="sxs-lookup"><span data-stu-id="2e33e-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="2e33e-175">Konfigurowanie elementów formatujących</span><span class="sxs-lookup"><span data-stu-id="2e33e-175">Configure formatters</span></span>

<span data-ttu-id="2e33e-176">Aplikacje, które muszą obsługiwać dodatkowe formaty, mogą dodać odpowiednie pakiety NuGet i skonfigurować obsługę.</span><span class="sxs-lookup"><span data-stu-id="2e33e-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="2e33e-177">Istnieją osobne elementy formatujące dla danych wejściowych i wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="2e33e-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="2e33e-178">Wejściowe elementy formatującego są używane przez [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="2e33e-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="2e33e-179">Wyjściowe elementy formatujące są używane do formatowania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2e33e-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="2e33e-180">Aby uzyskać informacje na temat tworzenia niestandardowego programu formatującego, zobacz [niestandardowe elementy formatujące](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="2e33e-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="2e33e-181">Dodawanie obsługi formatu XML</span><span class="sxs-lookup"><span data-stu-id="2e33e-181">Add XML format support</span></span>

<span data-ttu-id="2e33e-182">Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="2e33e-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="2e33e-183">Poprzedni kod serializacji wyników przy użyciu `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="2e33e-184">W przypadku korzystania z powyższego kodu metody kontrolera zwracają odpowiedni format na podstawie nagłówka żądania `Accept` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="2e33e-185">Skonfiguruj elementy formatujące system. Text. JSON w oparciu o</span><span class="sxs-lookup"><span data-stu-id="2e33e-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="2e33e-186">Funkcje dla elementów `System.Text.Json` formatujących opartych na programie można skonfigurować przy użyciu polecenia `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="2e33e-187">Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu polecenia `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="2e33e-188">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2e33e-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="2e33e-189">Dodawanie obsługi formatu JSON opartego na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="2e33e-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="2e33e-190">Przed ASP.NET Core 3,0 stosowane są domyślne elementy formatujące JSON zaimplementowane przy użyciu `Newtonsoft.Json` pakietu.</span><span class="sxs-lookup"><span data-stu-id="2e33e-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="2e33e-191">W ASP.NET Core 3,0 lub nowszych domyślne elementy formatujące JSON są oparte na `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="2e33e-192">Obsługa `Newtonsoft.Json` opartych na programie formatującego i funkcji jest dostępna przez zainstalowanie [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pakietu NuGet i skonfigurowanie go w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="2e33e-193">Niektóre funkcje mogą nie współdziałać z modułami `System.Text.Json` formatującego opartymi na języku i wymagają odwołania do elementów `Newtonsoft.Json` formatujących opartych na bazie.</span><span class="sxs-lookup"><span data-stu-id="2e33e-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="2e33e-194">Kontynuuj korzystanie z programu `Newtonsoft.Json` formatującego w oparciu o aplikacje:</span><span class="sxs-lookup"><span data-stu-id="2e33e-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="2e33e-195">Używa `Newtonsoft.Json` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2e33e-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="2e33e-196">Na przykład: `[JsonProperty]` lub `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="2e33e-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="2e33e-197">Dostosowuje ustawienia serializacji.</span><span class="sxs-lookup"><span data-stu-id="2e33e-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="2e33e-198">Opiera się na `Newtonsoft.Json` udostępnianych funkcjach.</span><span class="sxs-lookup"><span data-stu-id="2e33e-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="2e33e-199">Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="2e33e-200">Przed ASP.NET Core 3,0, `JsonResult.SerializerSettings` akceptuje wystąpienie `JsonSerializerSettings` , które jest specyficzne dla `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="2e33e-201">Generuje dokumentację [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="2e33e-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="2e33e-202">Funkcje dla elementów `Newtonsoft.Json` formatujących opartych na programie można skonfigurować przy użyciu `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="2e33e-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="2e33e-203">Opcje serializacji danych wyjściowych dla poszczególnych akcji można skonfigurować przy użyciu polecenia `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="2e33e-204">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2e33e-204">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="2e33e-205">Dodawanie obsługi formatu XML</span><span class="sxs-lookup"><span data-stu-id="2e33e-205">Add XML format support</span></span>

<span data-ttu-id="2e33e-206">Formatowanie XML wymaga pakietu NuGet [Microsoft. AspNetCore. MVC. formatującegos. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="2e33e-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="2e33e-207">Elementy formatujące XML zaimplementowane przy użyciu <xref:System.Xml.Serialization.XmlSerializer> są konfigurowane przez wywołanie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="2e33e-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="2e33e-208">Poprzedni kod serializacji wyników przy użyciu `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="2e33e-209">W przypadku korzystania z powyższego kodu metody kontrolera powinny zwrócić odpowiedni format na podstawie nagłówka żądania `Accept` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="2e33e-210">Określ format</span><span class="sxs-lookup"><span data-stu-id="2e33e-210">Specify a format</span></span>

<span data-ttu-id="2e33e-211">Aby ograniczyć formaty odpowiedzi, Zastosuj [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr.</span><span class="sxs-lookup"><span data-stu-id="2e33e-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="2e33e-212">Podobnie jak większość [filtrów](xref:mvc/controllers/filters), `[Produces]` można zastosować do akcji, kontrolera lub zakresu globalnego:</span><span class="sxs-lookup"><span data-stu-id="2e33e-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="2e33e-213">Poprzedni [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr:</span><span class="sxs-lookup"><span data-stu-id="2e33e-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="2e33e-214">Wymusza, aby wszystkie akcje w ramach kontrolera zwracały odpowiedzi w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="2e33e-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="2e33e-215">Jeśli inne elementy formatujące są skonfigurowane, a klient określi inny format, zwracany jest kod JSON.</span><span class="sxs-lookup"><span data-stu-id="2e33e-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="2e33e-216">Aby uzyskać więcej informacji, zobacz [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="2e33e-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="2e33e-217">Specjalne elementy formatujące Case</span><span class="sxs-lookup"><span data-stu-id="2e33e-217">Special case formatters</span></span>

<span data-ttu-id="2e33e-218">Niektóre specjalne przypadki są implementowane przy użyciu wbudowanych elementów formatujących.</span><span class="sxs-lookup"><span data-stu-id="2e33e-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="2e33e-219">Domyślnie `string` typy zwracane są formatowane jako *tekst/zwykły* (*text/html* , jeśli żąda się za pośrednictwem `Accept` nagłówka).</span><span class="sxs-lookup"><span data-stu-id="2e33e-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="2e33e-220">Takie zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="2e33e-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="2e33e-221">Elementy formatujące są usuwane w `ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="2e33e-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="2e33e-222">Akcje, które mają typ zwracany obiektu modelu zwracają, `204 No Content` gdy zwracają `null` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="2e33e-223">Takie zachowanie można usunąć, usuwając <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="2e33e-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="2e33e-224">Poniższy kod usuwa `StringOutputFormatter` i `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="2e33e-225">Bez `StringOutputFormatter` , wbudowany typ programu FORMATUJĄCEGO JSON formatuje `string` typy zwracane.</span><span class="sxs-lookup"><span data-stu-id="2e33e-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="2e33e-226">Jeśli wbudowany program formatujący JSON jest usuwany, a element formatujący XML jest dostępny, format XML programu formatującego jest `string` typem zwracanym.</span><span class="sxs-lookup"><span data-stu-id="2e33e-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="2e33e-227">W przeciwnym razie zwracane `string` typy zwracają `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="2e33e-228">Bez `HttpNoContentOutputFormatter` obiektów o wartości null są formatowane przy użyciu skonfigurowanego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="2e33e-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="2e33e-229">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2e33e-229">For example:</span></span>

* <span data-ttu-id="2e33e-230">Program formatujący JSON zwraca odpowiedź z treścią `null` .</span><span class="sxs-lookup"><span data-stu-id="2e33e-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="2e33e-231">Program formatujący XML zwraca pusty element XML z `xsi:nil="true"` zestawem atrybutów.</span><span class="sxs-lookup"><span data-stu-id="2e33e-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="2e33e-232">Mapowania adresów URL w formacie odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="2e33e-232">Response format URL mappings</span></span>

<span data-ttu-id="2e33e-233">Klienci mogą zażądać określonego formatu w ramach adresu URL, na przykład:</span><span class="sxs-lookup"><span data-stu-id="2e33e-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="2e33e-234">W ciągu zapytania lub części ścieżki.</span><span class="sxs-lookup"><span data-stu-id="2e33e-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="2e33e-235">Przy użyciu rozszerzenia pliku specyficznego dla formatu, takiego jak. XML lub. JSON.</span><span class="sxs-lookup"><span data-stu-id="2e33e-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="2e33e-236">Mapowanie ze ścieżki żądania należy określić w marszrucie używanej przez interfejs API.</span><span class="sxs-lookup"><span data-stu-id="2e33e-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="2e33e-237">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2e33e-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="2e33e-238">Poprzednia trasa pozwala określić żądany format jako opcjonalne rozszerzenie pliku.</span><span class="sxs-lookup"><span data-stu-id="2e33e-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="2e33e-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Atrybut sprawdza obecność wartości format w `RouteData` i mapuje format odpowiedzi do odpowiedniego programu formatującego podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="2e33e-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="2e33e-240">Trasa</span><span class="sxs-lookup"><span data-stu-id="2e33e-240">Route</span></span>        |             <span data-ttu-id="2e33e-241">EQ</span><span class="sxs-lookup"><span data-stu-id="2e33e-241">Formatter</span></span>              |
|---
<span data-ttu-id="2e33e-242">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-243">'Blazor'</span></span>
- <span data-ttu-id="2e33e-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-244">'Identity'</span></span>
- <span data-ttu-id="2e33e-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-246">'Razor'</span></span>
- <span data-ttu-id="2e33e-247">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-248">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-249">'Blazor'</span></span>
- <span data-ttu-id="2e33e-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-250">'Identity'</span></span>
- <span data-ttu-id="2e33e-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-252">'Razor'</span></span>
- <span data-ttu-id="2e33e-253">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-254">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-255">'Blazor'</span></span>
- <span data-ttu-id="2e33e-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-256">'Identity'</span></span>
- <span data-ttu-id="2e33e-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-258">'Razor'</span></span>
- <span data-ttu-id="2e33e-259">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-260">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-261">'Blazor'</span></span>
- <span data-ttu-id="2e33e-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-262">'Identity'</span></span>
- <span data-ttu-id="2e33e-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-264">'Razor'</span></span>
- <span data-ttu-id="2e33e-265">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-266">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-267">'Blazor'</span></span>
- <span data-ttu-id="2e33e-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-268">'Identity'</span></span>
- <span data-ttu-id="2e33e-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-270">'Razor'</span></span>
- <span data-ttu-id="2e33e-271">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-272">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-273">'Blazor'</span></span>
- <span data-ttu-id="2e33e-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-274">'Identity'</span></span>
- <span data-ttu-id="2e33e-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-276">'Razor'</span></span>
- <span data-ttu-id="2e33e-277">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-278">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-279">'Blazor'</span></span>
- <span data-ttu-id="2e33e-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-280">'Identity'</span></span>
- <span data-ttu-id="2e33e-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-282">'Razor'</span></span>
- <span data-ttu-id="2e33e-283">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-284">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-285">'Blazor'</span></span>
- <span data-ttu-id="2e33e-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-286">'Identity'</span></span>
- <span data-ttu-id="2e33e-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-288">'Razor'</span></span>
- <span data-ttu-id="2e33e-289">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-290">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-291">'Blazor'</span></span>
- <span data-ttu-id="2e33e-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-292">'Identity'</span></span>
- <span data-ttu-id="2e33e-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-294">'Razor'</span></span>
- <span data-ttu-id="2e33e-295">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-296">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-297">'Blazor'</span></span>
- <span data-ttu-id="2e33e-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-298">'Identity'</span></span>
- <span data-ttu-id="2e33e-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-300">'Razor'</span></span>
- <span data-ttu-id="2e33e-301">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-301">'SignalR' uid:</span></span> 

<span data-ttu-id="2e33e-302">------------|---Tytuł: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-303">'Blazor'</span></span>
- <span data-ttu-id="2e33e-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-304">'Identity'</span></span>
- <span data-ttu-id="2e33e-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-306">'Razor'</span></span>
- <span data-ttu-id="2e33e-307">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-308">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-309">'Blazor'</span></span>
- <span data-ttu-id="2e33e-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-310">'Identity'</span></span>
- <span data-ttu-id="2e33e-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-312">'Razor'</span></span>
- <span data-ttu-id="2e33e-313">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-314">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-315">'Blazor'</span></span>
- <span data-ttu-id="2e33e-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-316">'Identity'</span></span>
- <span data-ttu-id="2e33e-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-318">'Razor'</span></span>
- <span data-ttu-id="2e33e-319">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-320">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-321">'Blazor'</span></span>
- <span data-ttu-id="2e33e-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-322">'Identity'</span></span>
- <span data-ttu-id="2e33e-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-324">'Razor'</span></span>
- <span data-ttu-id="2e33e-325">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-326">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-327">'Blazor'</span></span>
- <span data-ttu-id="2e33e-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-328">'Identity'</span></span>
- <span data-ttu-id="2e33e-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-330">'Razor'</span></span>
- <span data-ttu-id="2e33e-331">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-332">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-333">'Blazor'</span></span>
- <span data-ttu-id="2e33e-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-334">'Identity'</span></span>
- <span data-ttu-id="2e33e-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-336">'Razor'</span></span>
- <span data-ttu-id="2e33e-337">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-338">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-339">'Blazor'</span></span>
- <span data-ttu-id="2e33e-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-340">'Identity'</span></span>
- <span data-ttu-id="2e33e-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-342">'Razor'</span></span>
- <span data-ttu-id="2e33e-343">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-344">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-345">'Blazor'</span></span>
- <span data-ttu-id="2e33e-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-346">'Identity'</span></span>
- <span data-ttu-id="2e33e-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-348">'Razor'</span></span>
- <span data-ttu-id="2e33e-349">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-350">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-351">'Blazor'</span></span>
- <span data-ttu-id="2e33e-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-352">'Identity'</span></span>
- <span data-ttu-id="2e33e-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-354">'Razor'</span></span>
- <span data-ttu-id="2e33e-355">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-356">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-357">'Blazor'</span></span>
- <span data-ttu-id="2e33e-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-358">'Identity'</span></span>
- <span data-ttu-id="2e33e-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-360">'Razor'</span></span>
- <span data-ttu-id="2e33e-361">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-362">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-363">'Blazor'</span></span>
- <span data-ttu-id="2e33e-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-364">'Identity'</span></span>
- <span data-ttu-id="2e33e-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-366">'Razor'</span></span>
- <span data-ttu-id="2e33e-367">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-368">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-369">'Blazor'</span></span>
- <span data-ttu-id="2e33e-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-370">'Identity'</span></span>
- <span data-ttu-id="2e33e-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-372">'Razor'</span></span>
- <span data-ttu-id="2e33e-373">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-374">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-375">'Blazor'</span></span>
- <span data-ttu-id="2e33e-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-376">'Identity'</span></span>
- <span data-ttu-id="2e33e-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-378">'Razor'</span></span>
- <span data-ttu-id="2e33e-379">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-380">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-381">'Blazor'</span></span>
- <span data-ttu-id="2e33e-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-382">'Identity'</span></span>
- <span data-ttu-id="2e33e-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-384">'Razor'</span></span>
- <span data-ttu-id="2e33e-385">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-386">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-387">'Blazor'</span></span>
- <span data-ttu-id="2e33e-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-388">'Identity'</span></span>
- <span data-ttu-id="2e33e-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-390">'Razor'</span></span>
- <span data-ttu-id="2e33e-391">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2e33e-392">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2e33e-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e33e-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-393">'Blazor'</span></span>
- <span data-ttu-id="2e33e-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e33e-394">'Identity'</span></span>
- <span data-ttu-id="2e33e-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e33e-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e33e-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e33e-396">'Razor'</span></span>
- <span data-ttu-id="2e33e-397">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="2e33e-397">'SignalR' uid:</span></span> 

<span data-ttu-id="2e33e-398">------------------| |   `/api/products/5`    |    Domyślny program formatujący dane wyjściowe | | `/api/products/5.json` | Element formatujący JSON (jeśli jest skonfigurowany) | | `/api/products/5.xml`  | Element formatujący XML (jeśli jest skonfigurowany) |</span><span class="sxs-lookup"><span data-stu-id="2e33e-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
