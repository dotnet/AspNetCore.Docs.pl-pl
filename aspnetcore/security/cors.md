---
<span data-ttu-id="ca705-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca705-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca705-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca705-102">'Blazor'</span></span>
- <span data-ttu-id="ca705-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca705-103">'Identity'</span></span>
- <span data-ttu-id="ca705-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca705-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca705-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca705-105">'Razor'</span></span>
- <span data-ttu-id="ca705-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca705-106">'SignalR' uid:</span></span> 

---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="ca705-107">Włącz żądania między źródłami (CORS) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca705-107">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca705-108">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ca705-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="ca705-109">W tym artykule pokazano, jak włączyć funkcję CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca705-109">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="ca705-110">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca705-110">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="ca705-111">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="ca705-111">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="ca705-112">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-112">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="ca705-113">Czasami możesz chcieć zezwolić innym lokacjom na wykonywanie żądań między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-113">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="ca705-114">Aby uzyskać więcej informacji, zobacz [artykuł CORS firmy Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="ca705-114">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="ca705-115">[Współużytkowanie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="ca705-115">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="ca705-116">Jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="ca705-116">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="ca705-117">**Nie** jest funkcją zabezpieczeń, funkcja CORS osłabi zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="ca705-117">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="ca705-118">Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-118">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="ca705-119">Aby uzyskać więcej informacji, zobacz [jak działa mechanizm CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="ca705-119">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="ca705-120">Zezwala serwerowi jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.</span><span class="sxs-lookup"><span data-stu-id="ca705-120">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="ca705-121">Jest bezpieczniejsze i bardziej elastyczne niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="ca705-121">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="ca705-122">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca705-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="ca705-123">To samo źródło</span><span class="sxs-lookup"><span data-stu-id="ca705-123">Same origin</span></span>

<span data-ttu-id="ca705-124">Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="ca705-124">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="ca705-125">Te dwa adresy URL mają te same źródła:</span><span class="sxs-lookup"><span data-stu-id="ca705-125">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="ca705-126">Te adresy URL mają różne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="ca705-126">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="ca705-127">`https://example.net`: Inna domena</span><span class="sxs-lookup"><span data-stu-id="ca705-127">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="ca705-128">`https://www.example.com/foo.html`: Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="ca705-128">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="ca705-129">`http://example.com/foo.html`: Inny schemat</span><span class="sxs-lookup"><span data-stu-id="ca705-129">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="ca705-130">`https://example.com:9000/foo.html`: Inny port</span><span class="sxs-lookup"><span data-stu-id="ca705-130">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="ca705-131">Włączanie mechanizmu CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-131">Enable CORS</span></span>

<span data-ttu-id="ca705-132">Istnieją trzy sposoby włączania mechanizmu CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-132">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="ca705-133">W oprogramowaniu pośredniczącym przy użyciu [nazwanych zasad](#np) lub [zasad domyślnych](#dp).</span><span class="sxs-lookup"><span data-stu-id="ca705-133">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="ca705-134">Korzystanie z [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="ca705-134">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="ca705-135">Z atrybutem [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="ca705-135">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="ca705-136">Użycie atrybutu [[EnableCors]](#attr) z nazwanymi zasadami zapewnia kontrolkę Finest w ograniczaniu punktów końcowych, które obsługują mechanizm CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-136">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="ca705-137">Każde podejście jest szczegółowo opisane w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="ca705-137">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="ca705-138">CORS z nazwanymi zasadami i programem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="ca705-138">CORS with named policy and middleware</span></span>

<span data-ttu-id="ca705-139">Oprogramowanie pośredniczące CORS obsługuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-139">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="ca705-140">Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji z określonymi źródłami:</span><span class="sxs-lookup"><span data-stu-id="ca705-140">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="ca705-141">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ca705-141">The preceding code:</span></span>

* <span data-ttu-id="ca705-142">Ustawia nazwę zasad na `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="ca705-142">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="ca705-143">Nazwa zasad jest dowolną.</span><span class="sxs-lookup"><span data-stu-id="ca705-143">The policy name is arbitrary.</span></span>
* <span data-ttu-id="ca705-144">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzającą i określa `_myAllowSpecificOrigins` zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-144">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="ca705-145">`UseCors`dodaje oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-145">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="ca705-146">Wywołanie `UseCors` musi być umieszczone po `UseRouting` , ale wcześniej `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="ca705-146">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="ca705-147">Aby uzyskać więcej informacji, zobacz [kolejność oprogramowania pośredniczącego](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="ca705-147">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="ca705-148">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="ca705-148">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="ca705-149">Wyrażenie lambda przyjmuje <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="ca705-149">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="ca705-150">[Opcje konfiguracji](#cors-policy-options), takie jak `WithOrigins` , zostały opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="ca705-150">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="ca705-151">Włącza `_myAllowSpecificOrigins` zasady CORS dla wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ca705-151">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="ca705-152">Zobacz [Routing punktów końcowych](#ecors) , aby zastosować zasady CORS do określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="ca705-152">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="ca705-153">W przypadku routingu punktu końcowego oprogramowanie do obsługi mechanizmu CORS **musi** być skonfigurowane do wykonywania między wywołaniami `UseRouting` i `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="ca705-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="ca705-154">Zobacz temat [CORS testów](#testc) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="ca705-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="ca705-155"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca705-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="ca705-156">Aby uzyskać więcej informacji, zobacz [Opcje zasad CORS](#cpo) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="ca705-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="ca705-157"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metody mogą być łańcucha, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ca705-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="ca705-158">Uwaga: określony adres URL **nie** może zawierać końcowego ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="ca705-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="ca705-159">Jeśli adres URL kończy się na `/` , porównywanie zwraca `false` i nie jest zwracany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="ca705-160">Mechanizm CORS z domyślnymi zasadami i oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="ca705-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="ca705-161">Następujący wyróżniony kod włącza domyślne zasady CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="ca705-162">Poprzedni kod stosuje domyślne zasady CORS do wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ca705-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="ca705-163">Włączanie mechanizmu CORS przy użyciu routingu punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="ca705-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="ca705-164">Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy użyciu `RequireCors` obecnie **nie** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="ca705-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="ca705-165">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/20709) w [usłudze GitHub i testowanie mechanizmu CORS przy użyciu routingu punktów końcowych i [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="ca705-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="ca705-166">Dzięki kierowaniu punktów końcowych można włączyć funkcję CORS dla poszczególnych punktów końcowych przy użyciu <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> zestawu metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="ca705-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="ca705-167">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ca705-167">In the preceding code:</span></span>

* <span data-ttu-id="ca705-168">`app.UseCors`włącza oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="ca705-169">Ponieważ zasady domyślne nie zostały skonfigurowane, `app.UseCors()` sama nie włącza mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="ca705-170">`/echo`Punkty końcowe i umożliwiają Zezwalanie na żądania między źródłami przy użyciu określonych zasad.</span><span class="sxs-lookup"><span data-stu-id="ca705-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="ca705-171">`/echo2` Razor Punkty końcowe stron i **nie** zezwalają na żądania między źródłami, ponieważ nie określono żadnych zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="ca705-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="ca705-172">Atrybut [[DisableCors]](#dc) **nie wyłącza mechanizmu** CORS, który został włączony przez Routing punktów końcowych za pomocą `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="ca705-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="ca705-173">Aby uzyskać instrukcje dotyczące testowania kodu podobnego do powyższego, zobacz artykuł [CORS testów z routingiem punktów końcowych i [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="ca705-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="ca705-174">Włączanie mechanizmu CORS z atrybutami</span><span class="sxs-lookup"><span data-stu-id="ca705-174">Enable CORS with attributes</span></span>

<span data-ttu-id="ca705-175">Włączanie mechanizmu CORS z atrybutem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) i stosowanie nazwanych zasad tylko do tych punktów końcowych, które wymagają mechanizmu CORS, udostępnia formant Finest.</span><span class="sxs-lookup"><span data-stu-id="ca705-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="ca705-176">Atrybut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) zapewnia alternatywę do stosowania mechanizmu CORS globalnie.</span><span class="sxs-lookup"><span data-stu-id="ca705-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="ca705-177">Ten `[EnableCors]` atrybut włącza funkcję CORS dla wybranych punktów końcowych, a nie wszystkie punkty końcowe:</span><span class="sxs-lookup"><span data-stu-id="ca705-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="ca705-178">`[EnableCors]`Określa zasady domyślne.</span><span class="sxs-lookup"><span data-stu-id="ca705-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="ca705-179">`[EnableCors("{Policy String}")]`Określa nazwane zasady.</span><span class="sxs-lookup"><span data-stu-id="ca705-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="ca705-180">Ten `[EnableCors]` atrybut może być stosowany do:</span><span class="sxs-lookup"><span data-stu-id="ca705-180">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="ca705-181">Stronic`PageModel`</span><span class="sxs-lookup"><span data-stu-id="ca705-181"> Page `PageModel`</span></span>
* <span data-ttu-id="ca705-182">Kontroler</span><span class="sxs-lookup"><span data-stu-id="ca705-182">Controller</span></span>
* <span data-ttu-id="ca705-183">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="ca705-183">Controller action method</span></span>

<span data-ttu-id="ca705-184">Różne zasady można stosować do kontrolerów, modeli stron lub metod akcji z `[EnableCors]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="ca705-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="ca705-185">Gdy `[EnableCors]` atrybut jest stosowany do kontrolera, modelu strony lub metody akcji, a funkcja CORS jest włączona w oprogramowaniu pośredniczącym, **obie** zasady są stosowane.</span><span class="sxs-lookup"><span data-stu-id="ca705-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="ca705-186">**Zalecamy łączenie zasad. Użyj** `[EnableCors]` **atrybutu lub oprogramowania pośredniczącego, a nie obu w tej samej aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="ca705-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="ca705-187">Poniższy kod stosuje różne zasady do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="ca705-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="ca705-188">Poniższy kod tworzy dwie zasady CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="ca705-189">W przypadku kontroli Finest ograniczającej żądania CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="ca705-190">Używany `[EnableCors("MyPolicy")]` z nazwanymi zasadami.</span><span class="sxs-lookup"><span data-stu-id="ca705-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="ca705-191">Nie Definiuj zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="ca705-191">Don't define a default policy.</span></span>
* <span data-ttu-id="ca705-192">Nie używaj [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="ca705-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="ca705-193">Kod w następnej sekcji jest zgodny z poprzednią listą.</span><span class="sxs-lookup"><span data-stu-id="ca705-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="ca705-194">Zobacz temat [CORS testów](#testc) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="ca705-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="ca705-195">Wyłącz funkcję CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-195">Disable CORS</span></span>

<span data-ttu-id="ca705-196">Atrybut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **nie wyłącza mechanizmu** CORS, który został włączony przez [Routing punktu końcowego](#ecors).</span><span class="sxs-lookup"><span data-stu-id="ca705-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="ca705-197">Poniższy kod definiuje zasady CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="ca705-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="ca705-198">Poniższy kod wyłącza funkcję CORS dla `GetValues2` akcji:</span><span class="sxs-lookup"><span data-stu-id="ca705-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="ca705-199">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ca705-199">The preceding code:</span></span>

* <span data-ttu-id="ca705-200">Nie włącza mechanizmu CORS z [routingiem punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="ca705-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="ca705-201">Nie definiuje [domyślnych zasad CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="ca705-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="ca705-202">Używa elementu [[EnableCors ("Moja Policy")]](#attr) w celu włączenia `"MyPolicy"` zasad CORS dla kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ca705-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="ca705-203">Wyłącza funkcję CORS dla `GetValues2` metody.</span><span class="sxs-lookup"><span data-stu-id="ca705-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="ca705-204">Aby uzyskać instrukcje dotyczące testowania poprzedniego kodu, zobacz temat [CORS testów](#testc) .</span><span class="sxs-lookup"><span data-stu-id="ca705-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="ca705-205">Opcje zasad CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-205">CORS policy options</span></span>

<span data-ttu-id="ca705-206">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="ca705-207">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="ca705-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="ca705-208">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="ca705-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="ca705-209">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="ca705-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="ca705-210">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="ca705-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="ca705-211">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="ca705-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="ca705-212">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="ca705-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="ca705-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ca705-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ca705-214">W przypadku niektórych opcji warto przeczytać najpierw sekcję [jak działa mechanizm CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="ca705-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="ca705-215">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="ca705-215">Set the allowed origins</span></span>

<span data-ttu-id="ca705-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Umożliwia żądania CORS ze wszystkich źródeł z dowolnym schematem ( `http` lub `https` ).</span><span class="sxs-lookup"><span data-stu-id="ca705-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="ca705-217">`AllowAnyOrigin`jest niezabezpieczony, ponieważ *Każda witryna sieci Web* może wprowadzać żądania między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="ca705-218">Określenie `AllowAnyOrigin` i `AllowCredentials` jest niebezpieczną konfiguracją i może skutkować fałszerstwem żądania między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="ca705-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="ca705-219">Usługa CORS zwraca nieprawidłową odpowiedź CORS, gdy aplikacja jest skonfigurowana przy użyciu obu metod.</span><span class="sxs-lookup"><span data-stu-id="ca705-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="ca705-220">`AllowAnyOrigin`ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Origin` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="ca705-221">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="ca705-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="ca705-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Właściwość zasad jako funkcję, która umożliwia pochodzenie w celu dopasowania do skonfigurowanej domeny z symbolami wieloznacznymi podczas oceniania, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="ca705-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="ca705-223">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="ca705-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="ca705-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="ca705-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="ca705-225">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="ca705-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="ca705-226">Ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Methods` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="ca705-227">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="ca705-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="ca705-228">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="ca705-228">Set the allowed request headers</span></span>

<span data-ttu-id="ca705-229">Aby zezwolić na wysyłanie określonych nagłówków w żądaniu CORS, nazywanymi [nagłówkami żądań autora](https://xhr.spec.whatwg.org/#request), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="ca705-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="ca705-230">Aby zezwolić na wszystkie [nagłówki żądań autora](https://www.w3.org/TR/cors/#author-request-headers), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="ca705-231">`AllowAnyHeader`ma wpływ na żądania inspekcji wstępnej oraz nagłówek [Access-Control-Request-Heads](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="ca705-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="ca705-232">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="ca705-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="ca705-233">Zasady oprogramowania CORS są zgodne z określonymi nagłówkami określonymi przez `WithHeaders` jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie pasują do nagłówków określonych w `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="ca705-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="ca705-234">Na przykład rozważ zastosowanie skonfigurowanej aplikacji w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="ca705-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="ca705-235">Oprogramowanie pośredniczące CORS odrzuca żądanie wstępne z następującym nagłówkiem żądania, ponieważ `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nie ma na liście `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="ca705-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="ca705-236">Aplikacja zwraca odpowiedź *200 OK* , ale nie wysyła nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="ca705-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="ca705-237">W związku z tym przeglądarka nie próbuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="ca705-238">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="ca705-238">Set the exposed response headers</span></span>

<span data-ttu-id="ca705-239">Domyślnie przeglądarka nie ujawnia wszystkich nagłówków odpowiedzi dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="ca705-240">Aby uzyskać więcej informacji, zobacz temat [udostępnianie zasobów między źródłami W3C (terminologia): prosty nagłówek odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="ca705-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="ca705-241">Domyślnie dostępne są nagłówki odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="ca705-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="ca705-242">Specyfikacja CORS wywołuje te nagłówki *proste odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="ca705-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="ca705-243">Aby udostępnić inne nagłówki aplikacji, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="ca705-244">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="ca705-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="ca705-245">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="ca705-246">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="ca705-247">Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP.</span><span class="sxs-lookup"><span data-stu-id="ca705-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="ca705-248">Aby wysłać poświadczenia z żądaniem między źródłami, klient musi mieć ustawioną `XMLHttpRequest.withCredentials` wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="ca705-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="ca705-249">`XMLHttpRequest`Bezpośrednie używanie:</span><span class="sxs-lookup"><span data-stu-id="ca705-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="ca705-250">Za pomocą jQuery:</span><span class="sxs-lookup"><span data-stu-id="ca705-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="ca705-251">Za pomocą [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="ca705-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="ca705-252">Serwer musi zezwalać na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="ca705-252">The server must allow the credentials.</span></span> <span data-ttu-id="ca705-253">Aby zezwolić na poświadczenia między źródłami, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="ca705-254">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="ca705-255">Jeśli przeglądarka wyśle poświadczenia, ale odpowiedź nie zawiera prawidłowego `Access-Control-Allow-Credentials` nagłówka, przeglądarka nie ujawnia odpowiedzi aplikacji, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ca705-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="ca705-256">Zezwalanie na poświadczenia między źródłami stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="ca705-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="ca705-257">Witryna sieci Web w innej domenie może wysyłać poświadczenia zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca705-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="ca705-258">Specyfikacja mechanizmu CORS określa również, że ustawienia pochodzenia `"*"` (wszystkie źródła) są nieprawidłowe, jeśli `Access-Control-Allow-Credentials` nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="ca705-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="ca705-259">Żądania wstępnego lotu</span><span class="sxs-lookup"><span data-stu-id="ca705-259">Preflight requests</span></span>

<span data-ttu-id="ca705-260">W przypadku niektórych żądań CORS przeglądarka wysyła żądanie dodatkowych [opcji](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) przed wykonaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="ca705-261">To żądanie jest nazywane [żądaniem wstępnym](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="ca705-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="ca705-262">Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są **wszystkie** następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="ca705-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="ca705-263">Metoda żądania ma wartość GET, główna lub OPUBLIKOWANa.</span><span class="sxs-lookup"><span data-stu-id="ca705-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="ca705-264">Aplikacja nie ustawia nagłówków żądań innych niż `Accept` , `Accept-Language` ,, `Content-Language` , `Content-Type` lub `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="ca705-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="ca705-265">`Content-Type`Nagłówek, jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="ca705-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="ca705-266">Reguła dotycząca nagłówków żądań ustawiona dla żądania klienta dotyczy nagłówków, które są ustawiane przez aplikację przez wywołanie `setRequestHeader` na `XMLHttpRequest` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="ca705-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="ca705-267">Specyfikacja CORS wywołuje [nagłówki żądania autora](https://www.w3.org/TR/cors/#author-request-headers)tych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="ca705-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="ca705-268">Reguła nie ma zastosowania do nagłówków, które można ustawić w przeglądarce, na przykład `User-Agent` , `Host` lub `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="ca705-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="ca705-269">Poniżej znajduje się Przykładowa odpowiedź podobna do żądania inspekcji wstępnej wykonanego z przycisku **[Put Test]** w sekcji [test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="ca705-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="ca705-270">W ramach żądania wstępnego jest stosowana Metoda [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="ca705-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="ca705-271">Może zawierać następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="ca705-271">It may include the following headers:</span></span>

* <span data-ttu-id="ca705-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda http, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="ca705-273">[Access-Control-Request-Heads](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): lista nagłówków żądań, które aplikacja ustawia na rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="ca705-274">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które są ustawiane przez przeglądarkę, takich jak `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="ca705-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="ca705-275">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="ca705-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="ca705-276">Jeśli żądanie wstępne nie zostanie odrzucone, aplikacja zwróci `200 OK` odpowiedź, ale nie ustawi nagłówków CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="ca705-277">W związku z tym przeglądarka nie próbuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="ca705-278">Przykład odmowy żądania inspekcji wstępnej znajduje się w sekcji [test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="ca705-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="ca705-279">Korzystając z narzędzi F12, aplikacja konsoli pokazuje błąd podobny do jednego z następujących, w zależności od przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="ca705-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="ca705-280">Firefox: Zablokowano żądanie między źródłami: te same zasady pochodzenia nie zezwalają na odczytywanie zasobu zdalnego pod adresem `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="ca705-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="ca705-281">(Przyczyna: żądanie CORS nie powiodło się).</span><span class="sxs-lookup"><span data-stu-id="ca705-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="ca705-282">Dowiedz się więcej</span><span class="sxs-lookup"><span data-stu-id="ca705-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="ca705-283">Oparte na chromie: dostęp do pobierania w " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " z pochodzenia " https://cors3.azurewebsites.net " został zablokowany przez zasady CORS: odpowiedź na żądanie wstępnej inspekcji nie przeszedł sprawdzenia kontroli dostępu: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.</span><span class="sxs-lookup"><span data-stu-id="ca705-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="ca705-284">Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="ca705-285">Aby zezwolić na określone nagłówki, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="ca705-286">Aby zezwolić na wszystkie [nagłówki żądań autora](https://www.w3.org/TR/cors/#author-request-headers), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="ca705-287">Przeglądarki nie są spójne w sposób ich ustawiania `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="ca705-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="ca705-288">Jeśli:</span><span class="sxs-lookup"><span data-stu-id="ca705-288">If either:</span></span>

* <span data-ttu-id="ca705-289">Nagłówki są ustawione na coś innego niż`"*"`</span><span class="sxs-lookup"><span data-stu-id="ca705-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="ca705-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jest wywoływana: Uwzględnij co najmniej `Accept` , `Content-Type` , i oraz `Origin` wszystkie niestandardowe nagłówki, które mają być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ca705-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="ca705-291">Kod automatycznego żądania wstępnego</span><span class="sxs-lookup"><span data-stu-id="ca705-291">Automatic preflight request code</span></span>

<span data-ttu-id="ca705-292">Po zastosowaniu zasad CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="ca705-293">Globalnie przez wywołanie `app.UseCors` w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ca705-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="ca705-294">Przy użyciu `[EnableCors]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="ca705-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="ca705-295">ASP.NET Core odpowiada na żądanie opcji inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="ca705-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="ca705-296">Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy użyciu `RequireCors` obecnie **nie** obsługuje automatycznych żądań inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="ca705-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="ca705-297">Sekcja [CORS testów](#testc) tego dokumentu przedstawia takie zachowanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="ca705-298">[HttpOptions] atrybut dla żądań inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="ca705-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="ca705-299">Gdy mechanizm CORS jest włączony przy użyciu odpowiednich zasad, ASP.NET Core zazwyczaj automatycznie reaguje na żądania funkcji CORS przed inspekcją.</span><span class="sxs-lookup"><span data-stu-id="ca705-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="ca705-300">W niektórych scenariuszach taka sytuacja może nie być taka sama.</span><span class="sxs-lookup"><span data-stu-id="ca705-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="ca705-301">Na przykład przy użyciu mechanizmu [CORS z routingiem punktu końcowego](#ecors).</span><span class="sxs-lookup"><span data-stu-id="ca705-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="ca705-302">Poniższy kod używa atrybutu [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) do tworzenia punktów końcowych dla żądań opcji:</span><span class="sxs-lookup"><span data-stu-id="ca705-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="ca705-303">Aby uzyskać instrukcje dotyczące testowania poprzedniego kodu, zobacz temat [CORS testów z routingiem punktów końcowych i [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="ca705-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="ca705-304">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="ca705-304">Set the preflight expiration time</span></span>

<span data-ttu-id="ca705-305">`Access-Control-Max-Age`Nagłówek określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="ca705-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="ca705-306">Aby ustawić ten nagłówek, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="ca705-307">Jak działa mechanizm CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-307">How CORS works</span></span>

<span data-ttu-id="ca705-308">W tej sekcji opisano, co się dzieje w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie komunikatów http.</span><span class="sxs-lookup"><span data-stu-id="ca705-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="ca705-309">Mechanizm CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="ca705-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="ca705-310">CORS jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="ca705-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="ca705-311">Na przykład złośliwy aktor może użyć [skryptów między lokacjami (XSS)](xref:security/cross-site-scripting) względem witryny i wykonać żądanie między lokacjami w celu wykraść informacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="ca705-312">Interfejs API nie jest bezpieczniejszy dzięki umożliwieniu mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="ca705-313">Aby wymusić mechanizm CORS, należy do klienta (przeglądarki).</span><span class="sxs-lookup"><span data-stu-id="ca705-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="ca705-314">Serwer wykonuje żądanie i zwraca odpowiedź, jest to klient, który zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="ca705-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="ca705-315">Na przykład w dowolnym z poniższych narzędzi zostanie wyświetlona odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="ca705-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="ca705-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="ca705-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="ca705-317">Postman</span><span class="sxs-lookup"><span data-stu-id="ca705-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="ca705-318">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="ca705-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="ca705-319">Przeglądarka sieci Web, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="ca705-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="ca705-320">Jest to sposób, aby serwer zezwalał przeglądarce na wykonywanie żądania [interfejsu API](https://developer.mozilla.org/docs/Web/API/Fetch_API) [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub pobierania między źródłami, które w przeciwnym razie byłoby zabronione.</span><span class="sxs-lookup"><span data-stu-id="ca705-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="ca705-321">Przeglądarki bez funkcji CORS nie mogą wykonywać żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="ca705-322">Przed zastosowaniem mechanizmu CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) został użyty do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="ca705-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="ca705-323">JSONP nie używa XHR, używa `<script>` znacznika do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="ca705-324">Skrypty mogą być ładowane między różnymi źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="ca705-325">[Specyfikacja CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="ca705-326">Jeśli przeglądarka obsługuje mechanizm CORS, ustawia te nagłówki automatycznie dla żądań cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="ca705-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="ca705-327">Niestandardowy kod JavaScript nie jest wymagany do włączenia mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="ca705-328">[Przycisk Put test](https://cors3.azurewebsites.net/test) na wdrożonym [przykładzie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="ca705-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="ca705-329">Poniżej znajduje się przykład żądania między źródłami z przycisku Testuj [wartości](https://cors3.azurewebsites.net/) do `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="ca705-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="ca705-330">`Origin`Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="ca705-330">The `Origin` header:</span></span>

* <span data-ttu-id="ca705-331">Zapewnia domenę lokacji, która żąda żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="ca705-332">Jest wymagany i musi się różnić od hosta.</span><span class="sxs-lookup"><span data-stu-id="ca705-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="ca705-333">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="ca705-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="ca705-334">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="ca705-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="ca705-335">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="ca705-335">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="ca705-336">W obszarze `OPTIONS` żądania serwer ustawia nagłówek **odpowiedzi** `Access-Control-Allow-Origin: {allowed origin}` w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="ca705-337">Przykładowo wdrożone [przykładowe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)żądanie [usunięcia [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` zawiera następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="ca705-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="ca705-338">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="ca705-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="ca705-339">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="ca705-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="ca705-340">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="ca705-340">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="ca705-341">W poprzednich **nagłówkach odpowiedzi**serwer ustawia nagłówek [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="ca705-342">`https://cors1.azurewebsites.net`Wartość tego nagłówka pasuje do `Origin` nagłówka z żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="ca705-343">Jeśli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> jest wywoływana, `Access-Control-Allow-Origin: *` jest zwracana wartość symbol wieloznaczny.</span><span class="sxs-lookup"><span data-stu-id="ca705-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="ca705-344">`AllowAnyOrigin`zezwala na dowolne źródło.</span><span class="sxs-lookup"><span data-stu-id="ca705-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="ca705-345">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ca705-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="ca705-346">W programie przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="ca705-347">Nawet jeśli serwer zwróci pomyślną odpowiedź, przeglądarka nie udostępni odpowiedzi dla aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="ca705-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="ca705-348">Wyświetl żądania opcji</span><span class="sxs-lookup"><span data-stu-id="ca705-348">Display OPTIONS requests</span></span>

<span data-ttu-id="ca705-349">Domyślnie przeglądarki Chrome i Edge nie wyświetlają żądań OPTIONS na karcie Sieć narzędzi F12.</span><span class="sxs-lookup"><span data-stu-id="ca705-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="ca705-350">Aby wyświetlić żądania opcji w następujących przeglądarkach:</span><span class="sxs-lookup"><span data-stu-id="ca705-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="ca705-351">`chrome://flags/#out-of-blink-cors` lub `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="ca705-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="ca705-352">Wyłącz flagę.</span><span class="sxs-lookup"><span data-stu-id="ca705-352">disable the flag.</span></span>
* <span data-ttu-id="ca705-353">uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="ca705-353">restart.</span></span>

<span data-ttu-id="ca705-354">W przeglądarce Firefox domyślnie są wyświetlane żądania dotyczące opcji.</span><span class="sxs-lookup"><span data-stu-id="ca705-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="ca705-355">Mechanizm CORS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca705-355">CORS in IIS</span></span>

<span data-ttu-id="ca705-356">W przypadku wdrażania w programie IIS należy uruchomić funkcję CORS przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="ca705-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="ca705-357">Aby zapewnić obsługę tego scenariusza, należy zainstalować i skonfigurować [moduł CORS usług IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="ca705-358">Testowanie CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-358">Test CORS</span></span>

<span data-ttu-id="ca705-359">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ma kod do testowania mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="ca705-360">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ca705-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="ca705-361">Przykładem jest projekt interfejsu API ze Razor stronami dodanymi:</span><span class="sxs-lookup"><span data-stu-id="ca705-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="ca705-362">`WithOrigins("https://localhost:<port>");`powinien być używany tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="ca705-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="ca705-363">Poniżej `ValuesController` podano punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="ca705-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="ca705-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) jest dostarczany przez pakiet NuGet [Rick. docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) i wyświetla informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="ca705-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="ca705-365">Przetestuj poprzedni przykładowy kod przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca705-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="ca705-366">Użyj wdrożonej przykładowej aplikacji pod adresem [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="ca705-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="ca705-367">Nie ma potrzeby pobierania przykładu.</span><span class="sxs-lookup"><span data-stu-id="ca705-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="ca705-368">Uruchom przykład przy `dotnet run` użyciu domyślnego adresu URL `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="ca705-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="ca705-369">Uruchom przykład z programu Visual Studio z portem ustawionym na 44398 dla adresu URL `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="ca705-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="ca705-370">Używanie przeglądarki z narzędziami F12:</span><span class="sxs-lookup"><span data-stu-id="ca705-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="ca705-371">Wybierz przycisk **wartości** i Przejrzyj nagłówki na karcie **Sieć** .</span><span class="sxs-lookup"><span data-stu-id="ca705-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="ca705-372">Wybierz przycisk **Put test** .</span><span class="sxs-lookup"><span data-stu-id="ca705-372">Select the **PUT test** button.</span></span> <span data-ttu-id="ca705-373">Zobacz temat [Opcje wyświetlania żądania](#options) , aby uzyskać instrukcje dotyczące wyświetlania żądania opcji.</span><span class="sxs-lookup"><span data-stu-id="ca705-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="ca705-374">**Test Put** tworzy dwa żądania, żądanie wstępnej kontroli i żądanie Put.</span><span class="sxs-lookup"><span data-stu-id="ca705-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="ca705-375">Wybierz **`GetValues2 [DisableCors]`** przycisk, aby wyzwolić nieudane żądanie CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="ca705-376">Jak wspomniano w dokumencie, odpowiedź zwraca 200 sukcesu, ale żądanie CORS nie zostało wykonane.</span><span class="sxs-lookup"><span data-stu-id="ca705-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="ca705-377">Wybierz kartę **konsola** , aby wyświetlić błąd CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="ca705-378">W zależności od przeglądarki wyświetlane są błędy podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="ca705-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="ca705-379">Dostęp do pobierania z lokalizacji źródłowej został `'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` zablokowany przez zasady CORS: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.</span><span class="sxs-lookup"><span data-stu-id="ca705-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="ca705-380">Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="ca705-381">Punkty końcowe z obsługą mechanizmu CORS można testować za pomocą narzędzia, takiego jak [zwinięcie](https://curl.haxx.se/), [programu Fiddler](https://www.telerik.com/fiddler)lub [Poster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="ca705-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="ca705-382">W przypadku korzystania z narzędzia, Źródło żądania określone przez `Origin` nagłówek musi różnić się od hosta przyjmującego żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="ca705-383">Jeśli żądanie nie jest *źródłem krzyżowe* na podstawie wartości `Origin` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="ca705-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="ca705-384">Nie ma potrzeby przetwarzania żądania przez oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="ca705-385">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="ca705-386">Następujące polecenie używa polecenia `curl` , aby wydać żądanie Options z informacjami:</span><span class="sxs-lookup"><span data-stu-id="ca705-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="ca705-387">Testowanie mechanizmu CORS przy użyciu routingu punktów końcowych i [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="ca705-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="ca705-388">Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy użyciu `RequireCors` obecnie **nie** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="ca705-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="ca705-389">Rozważmy następujący kod, który używa [routingu punktu końcowego w celu włączenia mechanizmu CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="ca705-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="ca705-390">Poniżej `TodoItems1Controller` przedstawiono punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="ca705-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="ca705-391">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=1) wdrożonej [próbki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="ca705-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="ca705-392">Przyciski **delete [EnableCors]** i **Get [EnableCors]** zostały wykonane pomyślnie, ponieważ punkty końcowe mają `[EnableCors]` i odpowiadają na żądania inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="ca705-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="ca705-393">Inne punkty końcowe zakończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="ca705-393">The other endpoints fails.</span></span> <span data-ttu-id="ca705-394">Przycisk **Get** kończy się niepowodzeniem, ponieważ [kod JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) wysyła:</span><span class="sxs-lookup"><span data-stu-id="ca705-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="ca705-395">Poniżej `TodoItems2Controller` znajdują się podobne punkty końcowe, ale zawiera jawny kod odpowiadający na żądania opcji:</span><span class="sxs-lookup"><span data-stu-id="ca705-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="ca705-396">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=2) wdrożonej próbki.</span><span class="sxs-lookup"><span data-stu-id="ca705-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="ca705-397">Z listy rozwijanej **kontroler** wybierz kolejno pozycje **Inspekcja wstępna** , a następnie **Ustaw kontroler**.</span><span class="sxs-lookup"><span data-stu-id="ca705-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="ca705-398">Wszystkie wywołania mechanizmu CORS do `TodoItems2Controller` punktów końcowych powiodły się.</span><span class="sxs-lookup"><span data-stu-id="ca705-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca705-399">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ca705-399">Additional resources</span></span>

* [<span data-ttu-id="ca705-400">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="ca705-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="ca705-401">Wprowadzenie do modułu CORS usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca705-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca705-402">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca705-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ca705-403">W tym artykule pokazano, jak włączyć funkcję CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca705-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="ca705-404">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca705-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="ca705-405">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="ca705-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="ca705-406">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="ca705-407">Czasami możesz chcieć zezwolić innym lokacjom na wykonywanie żądań między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="ca705-408">Aby uzyskać więcej informacji, zobacz [artykuł CORS firmy Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="ca705-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="ca705-409">[Współużytkowanie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="ca705-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="ca705-410">Jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="ca705-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="ca705-411">**Nie** jest funkcją zabezpieczeń, funkcja CORS osłabi zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="ca705-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="ca705-412">Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="ca705-413">Aby uzyskać więcej informacji, zobacz [jak działa mechanizm CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="ca705-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="ca705-414">Zezwala serwerowi jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.</span><span class="sxs-lookup"><span data-stu-id="ca705-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="ca705-415">Jest bezpieczniejsze i bardziej elastyczne niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="ca705-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="ca705-416">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca705-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="ca705-417">To samo źródło</span><span class="sxs-lookup"><span data-stu-id="ca705-417">Same origin</span></span>

<span data-ttu-id="ca705-418">Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="ca705-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="ca705-419">Te dwa adresy URL mają te same źródła:</span><span class="sxs-lookup"><span data-stu-id="ca705-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="ca705-420">Te adresy URL mają różne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="ca705-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="ca705-421">`https://example.net`: Inna domena</span><span class="sxs-lookup"><span data-stu-id="ca705-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="ca705-422">`https://www.example.com/foo.html`: Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="ca705-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="ca705-423">`http://example.com/foo.html`: Inny schemat</span><span class="sxs-lookup"><span data-stu-id="ca705-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="ca705-424">`https://example.com:9000/foo.html`: Inny port</span><span class="sxs-lookup"><span data-stu-id="ca705-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="ca705-425">Program Internet Explorer nie traktuje portu podczas porównywania źródeł.</span><span class="sxs-lookup"><span data-stu-id="ca705-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="ca705-426">CORS z nazwanymi zasadami i programem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="ca705-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="ca705-427">Oprogramowanie pośredniczące CORS obsługuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="ca705-428">Poniższy kod włącza mechanizm CORS dla całej aplikacji z określonym źródłem:</span><span class="sxs-lookup"><span data-stu-id="ca705-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="ca705-429">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ca705-429">The preceding code:</span></span>

* <span data-ttu-id="ca705-430">Ustawia nazwę zasad na " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="ca705-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="ca705-431">Nazwa zasad jest dowolną.</span><span class="sxs-lookup"><span data-stu-id="ca705-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="ca705-432">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia, która umożliwia mechanizm CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="ca705-433">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="ca705-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="ca705-434">Wyrażenie lambda przyjmuje <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="ca705-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="ca705-435">[Opcje konfiguracji](#cors-policy-options), takie jak `WithOrigins` , zostały opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="ca705-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="ca705-436"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca705-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="ca705-437">Aby uzyskać więcej informacji, zobacz [Opcje zasad CORS](#cpo) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="ca705-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="ca705-438"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metoda może łączyć metody łańcucha, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ca705-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="ca705-439">Uwaga: adres URL **nie** może zawierać końcowego ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="ca705-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="ca705-440">Jeśli adres URL kończy się na `/` , porównywanie zwraca `false` i nie jest zwracany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="ca705-441">Poniższy kod dotyczy zasad CORS dla wszystkich punktów końcowych aplikacji za pośrednictwem oprogramowania do obsługi mechanizmu CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="ca705-442">Uwaga: `UseCors` należy wywołać przed `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="ca705-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="ca705-443">Zobacz [Włączanie mechanizmu CORS na Razor stronach, kontrolerach i metodach akcji](#ecors) , aby zastosować zasady CORS na poziomie strony/kontrolera/akcji.</span><span class="sxs-lookup"><span data-stu-id="ca705-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="ca705-444">Zobacz temat [CORS testów](#test) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="ca705-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="ca705-445">Włączanie mechanizmu CORS z atrybutami</span><span class="sxs-lookup"><span data-stu-id="ca705-445">Enable CORS with attributes</span></span>

<span data-ttu-id="ca705-446">Atrybut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) stanowi alternatywę dla zastosowania mechanizmu CORS globalnie.</span><span class="sxs-lookup"><span data-stu-id="ca705-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="ca705-447">Ten `[EnableCors]` atrybut włącza funkcję CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="ca705-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="ca705-448">Użyj, `[EnableCors]` Aby określić zasady domyślne i `[EnableCors("{Policy String}")]` określić zasady.</span><span class="sxs-lookup"><span data-stu-id="ca705-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="ca705-449">Ten `[EnableCors]` atrybut może być stosowany do:</span><span class="sxs-lookup"><span data-stu-id="ca705-449">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="ca705-450">Stronic`PageModel`</span><span class="sxs-lookup"><span data-stu-id="ca705-450"> Page `PageModel`</span></span>
* <span data-ttu-id="ca705-451">Kontroler</span><span class="sxs-lookup"><span data-stu-id="ca705-451">Controller</span></span>
* <span data-ttu-id="ca705-452">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="ca705-452">Controller action method</span></span>

<span data-ttu-id="ca705-453">Możesz zastosować różne zasady do kontrolera/strony-model/akcja z `[EnableCors]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="ca705-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="ca705-454">Gdy `[EnableCors]` atrybut jest stosowany do metody Controller/model/akcja, a funkcja CORS jest włączona w oprogramowaniu pośredniczącym, stosowane są **obie** zasady.</span><span class="sxs-lookup"><span data-stu-id="ca705-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="ca705-455">**Nie** zaleca się łączenia zasad.</span><span class="sxs-lookup"><span data-stu-id="ca705-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="ca705-456">Użyj `[EnableCors]` atrybutu lub oprogramowania pośredniczącego, a **nie obu**.</span><span class="sxs-lookup"><span data-stu-id="ca705-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="ca705-457">W przypadku korzystania z programu nie `[EnableCors]` należy definiować zasad domyślnych. **not**</span><span class="sxs-lookup"><span data-stu-id="ca705-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="ca705-458">Poniższy kod stosuje różne zasady do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="ca705-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="ca705-459">Poniższy kod tworzy domyślne zasady CORS i zasady o nazwie `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="ca705-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="ca705-460">Wyłącz funkcję CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-460">Disable CORS</span></span>

<span data-ttu-id="ca705-461">Atrybut [ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) wyłącza funkcję CORS dla kontrolera/strony-modelu/akcji.</span><span class="sxs-lookup"><span data-stu-id="ca705-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="ca705-462">Opcje zasad CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-462">CORS policy options</span></span>

<span data-ttu-id="ca705-463">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="ca705-464">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="ca705-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="ca705-465">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="ca705-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="ca705-466">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="ca705-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="ca705-467">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="ca705-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="ca705-468">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="ca705-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="ca705-469">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="ca705-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="ca705-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ca705-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ca705-471">W przypadku niektórych opcji warto przeczytać najpierw sekcję [jak działa mechanizm CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="ca705-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="ca705-472">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="ca705-472">Set the allowed origins</span></span>

<span data-ttu-id="ca705-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Umożliwia żądania CORS ze wszystkich źródeł z dowolnym schematem ( `http` lub `https` ).</span><span class="sxs-lookup"><span data-stu-id="ca705-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="ca705-474">`AllowAnyOrigin`jest niezabezpieczony, ponieważ *Każda witryna sieci Web* może wprowadzać żądania między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="ca705-475">Określenie `AllowAnyOrigin` i `AllowCredentials` jest niebezpieczną konfiguracją i może skutkować fałszerstwem żądania między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="ca705-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="ca705-476">W przypadku bezpiecznej aplikacji należy określić dokładną listę źródeł, jeśli klient musi autoryzować sam do uzyskiwania dostępu do zasobów serwera.</span><span class="sxs-lookup"><span data-stu-id="ca705-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="ca705-477">`AllowAnyOrigin`ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Origin` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="ca705-478">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="ca705-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="ca705-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Właściwość zasad jako funkcję, która umożliwia pochodzenie w celu dopasowania do skonfigurowanej domeny z symbolami wieloznacznymi podczas oceniania, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="ca705-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="ca705-480">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="ca705-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="ca705-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="ca705-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="ca705-482">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="ca705-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="ca705-483">Ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Methods` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="ca705-484">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="ca705-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="ca705-485">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="ca705-485">Set the allowed request headers</span></span>

<span data-ttu-id="ca705-486">Aby zezwolić na wysyłanie określonych nagłówków w żądaniu CORS, nazywanymi *nagłówkami żądań autora*, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="ca705-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="ca705-487">Aby zezwolić na wszystkie nagłówki żądań autora, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="ca705-488">To ustawienie ma wpływ na żądania inspekcji wstępnej i `Access-Control-Request-Headers` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="ca705-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="ca705-489">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="ca705-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="ca705-490">Oprogramowanie pośredniczące CORS zawsze umożliwia wysyłanie czterech nagłówków w `Access-Control-Request-Headers` celu, niezależnie od wartości skonfigurowanych w CorsPolicy. Heads.</span><span class="sxs-lookup"><span data-stu-id="ca705-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="ca705-491">Ta lista nagłówków obejmuje:</span><span class="sxs-lookup"><span data-stu-id="ca705-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="ca705-492">Na przykład rozważ zastosowanie skonfigurowanej aplikacji w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="ca705-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="ca705-493">Oprogramowanie pośredniczące CORS pomyślnie reaguje na żądanie inspekcji wstępnej z następującym nagłówkiem żądania, ponieważ `Content-Language` zawsze jest listy dozwolonych:</span><span class="sxs-lookup"><span data-stu-id="ca705-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="ca705-494">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="ca705-494">Set the exposed response headers</span></span>

<span data-ttu-id="ca705-495">Domyślnie przeglądarka nie ujawnia wszystkich nagłówków odpowiedzi dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="ca705-496">Aby uzyskać więcej informacji, zobacz temat [udostępnianie zasobów między źródłami W3C (terminologia): prosty nagłówek odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="ca705-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="ca705-497">Domyślnie dostępne są nagłówki odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="ca705-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="ca705-498">Specyfikacja CORS wywołuje te nagłówki *proste odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="ca705-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="ca705-499">Aby udostępnić inne nagłówki aplikacji, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="ca705-500">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="ca705-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="ca705-501">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="ca705-502">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="ca705-503">Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP.</span><span class="sxs-lookup"><span data-stu-id="ca705-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="ca705-504">Aby wysłać poświadczenia z żądaniem między źródłami, klient musi mieć ustawioną `XMLHttpRequest.withCredentials` wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="ca705-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="ca705-505">`XMLHttpRequest`Bezpośrednie używanie:</span><span class="sxs-lookup"><span data-stu-id="ca705-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="ca705-506">Za pomocą jQuery:</span><span class="sxs-lookup"><span data-stu-id="ca705-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="ca705-507">Za pomocą [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="ca705-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="ca705-508">Serwer musi zezwalać na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="ca705-508">The server must allow the credentials.</span></span> <span data-ttu-id="ca705-509">Aby zezwolić na poświadczenia między źródłami, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="ca705-510">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="ca705-511">Jeśli przeglądarka wyśle poświadczenia, ale odpowiedź nie zawiera prawidłowego `Access-Control-Allow-Credentials` nagłówka, przeglądarka nie ujawnia odpowiedzi aplikacji, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ca705-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="ca705-512">Zezwalanie na poświadczenia między źródłami stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="ca705-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="ca705-513">Witryna sieci Web w innej domenie może wysyłać poświadczenia zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca705-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="ca705-514">Specyfikacja mechanizmu CORS określa również, że ustawienia pochodzenia `"*"` (wszystkie źródła) są nieprawidłowe, jeśli `Access-Control-Allow-Credentials` nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="ca705-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="ca705-515">Żądania wstępnego lotu</span><span class="sxs-lookup"><span data-stu-id="ca705-515">Preflight requests</span></span>

<span data-ttu-id="ca705-516">W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie przed wykonaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="ca705-517">To żądanie jest nazywane *żądaniem wstępnym*.</span><span class="sxs-lookup"><span data-stu-id="ca705-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="ca705-518">Jeśli spełnione są następujące warunki, przeglądarka może pominąć żądanie wstępne:</span><span class="sxs-lookup"><span data-stu-id="ca705-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="ca705-519">Metoda żądania ma wartość GET, główna lub OPUBLIKOWANa.</span><span class="sxs-lookup"><span data-stu-id="ca705-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="ca705-520">Aplikacja nie ustawia nagłówków żądań innych niż `Accept` , `Accept-Language` ,, `Content-Language` , `Content-Type` lub `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="ca705-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="ca705-521">`Content-Type`Nagłówek, jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="ca705-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="ca705-522">Reguła dotycząca nagłówków żądań ustawiona dla żądania klienta dotyczy nagłówków, które są ustawiane przez aplikację przez wywołanie `setRequestHeader` na `XMLHttpRequest` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="ca705-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="ca705-523">Specyfikacja CORS wywołuje *nagłówki żądania autora*tych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="ca705-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="ca705-524">Reguła nie ma zastosowania do nagłówków, które można ustawić w przeglądarce, na przykład `User-Agent` , `Host` lub `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="ca705-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="ca705-525">Poniżej znajduje się przykład żądania wstępnego:</span><span class="sxs-lookup"><span data-stu-id="ca705-525">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="ca705-526">Żądanie przed inspekcją używa metody HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="ca705-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="ca705-527">Zawiera dwa specjalne nagłówki:</span><span class="sxs-lookup"><span data-stu-id="ca705-527">It includes two special headers:</span></span>

* <span data-ttu-id="ca705-528">`Access-Control-Request-Method`: Metoda HTTP, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="ca705-529">`Access-Control-Request-Headers`: Lista nagłówków żądań, które aplikacja ustawia na rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="ca705-530">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które są ustawiane przez przeglądarkę, takich jak `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="ca705-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="ca705-531">Gdy mechanizm CORS jest włączony przy użyciu odpowiednich zasad, ASP.NET Core zwykle automatycznie reaguje na żądania inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="ca705-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="ca705-532">Zobacz [[HttpOptions] atrybutu dla żądań inspekcji wstępnej](#pro).</span><span class="sxs-lookup"><span data-stu-id="ca705-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="ca705-533">Żądanie inspekcji wstępnej CORS może zawierać `Access-Control-Request-Headers` nagłówek, który wskazuje serwerowi nagłówki wysyłane z rzeczywistym żądaniem.</span><span class="sxs-lookup"><span data-stu-id="ca705-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="ca705-534">Aby zezwolić na określone nagłówki, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="ca705-535">Aby zezwolić na wszystkie nagłówki żądań autora, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="ca705-536">Przeglądarki nie są w pełni spójne w sposób ich ustawiania `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="ca705-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="ca705-537">Jeśli ustawisz nagłówki jako elementy inne niż `"*"` (lub użycie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), należy uwzględnić co najmniej `Accept` , `Content-Type` i i `Origin` wszystkie niestandardowe nagłówki, które mają być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ca705-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="ca705-538">Poniżej znajduje się Przykładowa odpowiedź na żądanie inspekcji wstępnej (przy założeniu, że serwer zezwala na żądanie):</span><span class="sxs-lookup"><span data-stu-id="ca705-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="ca705-539">Odpowiedź zawiera nagłówek, `Access-Control-Allow-Methods` który zawiera listę dozwolonych metod i opcjonalnie `Access-Control-Allow-Headers` nagłówek, który zawiera listę dozwolonych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="ca705-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="ca705-540">W przypadku pomyślnego przeprowadzenia żądania wstępnego przeglądarka wyśle rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="ca705-541">Jeśli żądanie wstępne nie zostanie odrzucone, aplikacja zwróci odpowiedź *200 OK* , ale nie wyśle nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="ca705-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="ca705-542">W związku z tym przeglądarka nie próbuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="ca705-543">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="ca705-543">Set the preflight expiration time</span></span>

<span data-ttu-id="ca705-544">`Access-Control-Max-Age`Nagłówek określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="ca705-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="ca705-545">Aby ustawić ten nagłówek, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="ca705-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="ca705-546">Jak działa mechanizm CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-546">How CORS works</span></span>

<span data-ttu-id="ca705-547">W tej sekcji opisano, co się dzieje w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie komunikatów http.</span><span class="sxs-lookup"><span data-stu-id="ca705-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="ca705-548">Mechanizm CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="ca705-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="ca705-549">CORS jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="ca705-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="ca705-550">Na przykład złośliwy aktor może użyć [zapobiegania skryptom między lokacjami (XSS)](xref:security/cross-site-scripting) względem witryny i wykonać żądanie między lokacjami w celu wykraść informacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="ca705-551">Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="ca705-552">Aby wymusić mechanizm CORS, należy do klienta (przeglądarki).</span><span class="sxs-lookup"><span data-stu-id="ca705-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="ca705-553">Serwer wykonuje żądanie i zwraca odpowiedź, jest to klient, który zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="ca705-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="ca705-554">Na przykład w dowolnym z poniższych narzędzi zostanie wyświetlona odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="ca705-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="ca705-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="ca705-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="ca705-556">Postman</span><span class="sxs-lookup"><span data-stu-id="ca705-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="ca705-557">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="ca705-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="ca705-558">Przeglądarka sieci Web, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="ca705-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="ca705-559">Jest to sposób, aby serwer zezwalał przeglądarce na wykonywanie żądania [interfejsu API](https://developer.mozilla.org/docs/Web/API/Fetch_API) [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub pobierania między źródłami, które w przeciwnym razie byłoby zabronione.</span><span class="sxs-lookup"><span data-stu-id="ca705-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="ca705-560">Przeglądarki (bez CORS) nie mogą wykonywać żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="ca705-561">Przed zastosowaniem mechanizmu CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) został użyty do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="ca705-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="ca705-562">JSONP nie używa XHR, używa `<script>` znacznika do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="ca705-563">Skrypty mogą być ładowane między różnymi źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="ca705-564">[Specyfikacja CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="ca705-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="ca705-565">Jeśli przeglądarka obsługuje mechanizm CORS, ustawia te nagłówki automatycznie dla żądań cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="ca705-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="ca705-566">Niestandardowy kod JavaScript nie jest wymagany do włączenia mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="ca705-567">Poniżej przedstawiono przykład żądania między źródłami danych.</span><span class="sxs-lookup"><span data-stu-id="ca705-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="ca705-568">`Origin`Nagłówek zawiera domenę lokacji, która żąda żądania.</span><span class="sxs-lookup"><span data-stu-id="ca705-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="ca705-569">`Origin`Nagłówek jest wymagany i musi się różnić od hosta.</span><span class="sxs-lookup"><span data-stu-id="ca705-569">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="ca705-570">Jeśli serwer zezwala na żądanie, ustawia `Access-Control-Allow-Origin` nagłówek w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="ca705-571">Wartość tego nagłówka jest zgodna z `Origin` nagłówkiem z żądania lub jest wartością symbolu wieloznacznego `"*"` , co oznacza, że wszystkie źródła są dozwolone:</span><span class="sxs-lookup"><span data-stu-id="ca705-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="ca705-572">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ca705-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="ca705-573">W programie przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="ca705-574">Nawet jeśli serwer zwróci pomyślną odpowiedź, przeglądarka nie udostępni odpowiedzi dla aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="ca705-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="ca705-575">Testowanie CORS</span><span class="sxs-lookup"><span data-stu-id="ca705-575">Test CORS</span></span>

<span data-ttu-id="ca705-576">Aby przetestować CORS:</span><span class="sxs-lookup"><span data-stu-id="ca705-576">To test CORS:</span></span>

1. <span data-ttu-id="ca705-577">[Utwórz projekt interfejsu API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="ca705-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="ca705-578">Możesz również [pobrać przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="ca705-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="ca705-579">Włącz funkcję CORS przy użyciu jednego z metod w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="ca705-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="ca705-580">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ca705-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="ca705-581">`WithOrigins("https://localhost:<port>");`powinien być używany tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="ca705-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="ca705-582">Utwórz projekt aplikacji sieci Web ( Razor strony lub MVC).</span><span class="sxs-lookup"><span data-stu-id="ca705-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="ca705-583">Przykład używa Razor stron.</span><span class="sxs-lookup"><span data-stu-id="ca705-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="ca705-584">Aplikację sieci Web można utworzyć w tym samym rozwiązaniu co projekt interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="ca705-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="ca705-585">Dodaj następujący wyróżniony kod do pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ca705-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="ca705-586">W poprzednim kodzie Zastąp ciąg `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresem URL wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="ca705-587">Wdróż projekt interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="ca705-587">Deploy the API project.</span></span> <span data-ttu-id="ca705-588">Na przykład [Wdróż na platformie Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="ca705-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="ca705-589">Uruchom Razor strony lub aplikację MVC na pulpicie, a następnie kliknij przycisk **Testuj** .</span><span class="sxs-lookup"><span data-stu-id="ca705-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="ca705-590">Użyj narzędzi F12, aby przejrzeć komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="ca705-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="ca705-591">Usuń pochodzenie hosta lokalnego z `WithOrigins` i Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="ca705-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="ca705-592">Alternatywnie Uruchom aplikację kliencką z innym portem.</span><span class="sxs-lookup"><span data-stu-id="ca705-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="ca705-593">Na przykład uruchom polecenie z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ca705-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="ca705-594">Przetestuj za pomocą aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="ca705-594">Test with the client app.</span></span> <span data-ttu-id="ca705-595">Błędy funkcji CORS zwracają błąd, ale komunikat o błędzie nie jest dostępny dla języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ca705-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="ca705-596">Aby wyświetlić błąd, Użyj karty konsola w narzędziach F12.</span><span class="sxs-lookup"><span data-stu-id="ca705-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="ca705-597">W zależności od przeglądarki pojawia się błąd (w konsoli narzędzia F12) podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="ca705-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="ca705-598">Korzystanie z przeglądarki Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="ca705-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="ca705-599">**SEC7120: [CORS] `https://localhost:44375` nie znaleziono źródła `https://localhost:44375` w nagłówku odpowiedzi "Access-Control-Allow-Origin" dla zasobu Cross-Origin w`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="ca705-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="ca705-600">Korzystanie z programu Chrome:</span><span class="sxs-lookup"><span data-stu-id="ca705-600">Using Chrome:</span></span>

     <span data-ttu-id="ca705-601">**Dostęp do elementu XMLHttpRequest w lokalizacji `https://webapi.azurewebsites.net/api/values/1` z punktu początkowego został `https://localhost:44375` zablokowany przez zasady CORS: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.**</span><span class="sxs-lookup"><span data-stu-id="ca705-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="ca705-602">Punkty końcowe z obsługą mechanizmu CORS można testować za pomocą narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="ca705-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="ca705-603">W przypadku korzystania z narzędzia, Źródło żądania określone przez `Origin` nagłówek musi różnić się od hosta przyjmującego żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca705-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="ca705-604">Jeśli żądanie nie jest *źródłem krzyżowe* na podstawie wartości `Origin` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="ca705-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="ca705-605">Nie ma potrzeby przetwarzania żądania przez oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="ca705-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="ca705-606">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ca705-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="ca705-607">Mechanizm CORS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca705-607">CORS in IIS</span></span>

<span data-ttu-id="ca705-608">W przypadku wdrażania w programie IIS należy uruchomić funkcję CORS przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="ca705-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="ca705-609">Aby zapewnić obsługę tego scenariusza, należy zainstalować i skonfigurować [moduł CORS usług IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca705-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca705-610">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ca705-610">Additional resources</span></span>

* [<span data-ttu-id="ca705-611">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="ca705-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="ca705-612">Wprowadzenie do modułu CORS usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca705-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
