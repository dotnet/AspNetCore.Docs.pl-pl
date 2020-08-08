---
title: Włącz żądania między źródłami (CORS) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, w jaki sposób mechanizm CORS jest standardem umożliwiającym lub odrzucanie żądań między źródłami w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: ee640ded37f40175e3e150f713fa970e9705b62c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021110"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="aed16-103">Włącz żądania między źródłami (CORS) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aed16-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aed16-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="aed16-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="aed16-105">W tym artykule pokazano, jak włączyć funkcję CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aed16-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="aed16-106">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="aed16-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="aed16-107">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="aed16-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="aed16-108">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="aed16-109">Czasami możesz chcieć zezwolić innym lokacjom na wykonywanie żądań między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="aed16-110">Aby uzyskać więcej informacji, zobacz [artykuł CORS firmy Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="aed16-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="aed16-111">[Współużytkowanie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="aed16-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="aed16-112">Jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="aed16-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="aed16-113">**Nie** jest funkcją zabezpieczeń, funkcja CORS osłabi zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="aed16-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="aed16-114">Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="aed16-115">Aby uzyskać więcej informacji, zobacz [jak działa mechanizm CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="aed16-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="aed16-116">Zezwala serwerowi jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.</span><span class="sxs-lookup"><span data-stu-id="aed16-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="aed16-117">Jest bezpieczniejsze i bardziej elastyczne niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="aed16-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="aed16-118">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aed16-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="aed16-119">To samo źródło</span><span class="sxs-lookup"><span data-stu-id="aed16-119">Same origin</span></span>

<span data-ttu-id="aed16-120">Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="aed16-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="aed16-121">Te dwa adresy URL mają te same źródła:</span><span class="sxs-lookup"><span data-stu-id="aed16-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="aed16-122">Te adresy URL mają różne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="aed16-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="aed16-123">`https://example.net`: Inna domena</span><span class="sxs-lookup"><span data-stu-id="aed16-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="aed16-124">`https://www.example.com/foo.html`: Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="aed16-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="aed16-125">`http://example.com/foo.html`: Inny schemat</span><span class="sxs-lookup"><span data-stu-id="aed16-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="aed16-126">`https://example.com:9000/foo.html`: Inny port</span><span class="sxs-lookup"><span data-stu-id="aed16-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="aed16-127">Włączanie mechanizmu CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-127">Enable CORS</span></span>

<span data-ttu-id="aed16-128">Istnieją trzy sposoby włączania mechanizmu CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="aed16-129">W oprogramowaniu pośredniczącym przy użyciu [nazwanych zasad](#np) lub [zasad domyślnych](#dp).</span><span class="sxs-lookup"><span data-stu-id="aed16-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="aed16-130">Korzystanie z [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="aed16-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="aed16-131">Z atrybutem [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="aed16-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="aed16-132">Użycie atrybutu [[EnableCors]](#attr) z nazwanymi zasadami zapewnia kontrolkę Finest w ograniczaniu punktów końcowych, które obsługują mechanizm CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="aed16-133"><xref:Owin.CorsExtensions.UseCors%2A>musi być wywoływana przed <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> użyciem `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="aed16-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="aed16-134">Każde podejście jest szczegółowo opisane w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="aed16-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="aed16-135">CORS z nazwanymi zasadami i programem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="aed16-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="aed16-136">Oprogramowanie pośredniczące CORS obsługuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="aed16-137">Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji z określonymi źródłami:</span><span class="sxs-lookup"><span data-stu-id="aed16-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="aed16-138">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="aed16-138">The preceding code:</span></span>

* <span data-ttu-id="aed16-139">Ustawia nazwę zasad na `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="aed16-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="aed16-140">Nazwa zasad jest dowolną.</span><span class="sxs-lookup"><span data-stu-id="aed16-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="aed16-141">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzającą i określa `_myAllowSpecificOrigins` zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="aed16-142">`UseCors`dodaje oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="aed16-143">Wywołanie `UseCors` musi być umieszczone po `UseRouting` , ale wcześniej `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="aed16-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="aed16-144">Aby uzyskać więcej informacji, zobacz [kolejność oprogramowania pośredniczącego](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="aed16-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="aed16-145">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="aed16-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="aed16-146">Wyrażenie lambda przyjmuje <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="aed16-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="aed16-147">[Opcje konfiguracji](#cors-policy-options), takie jak `WithOrigins` , zostały opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="aed16-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="aed16-148">Włącza `_myAllowSpecificOrigins` zasady CORS dla wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="aed16-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="aed16-149">Zobacz [Routing punktów końcowych](#ecors) , aby zastosować zasady CORS do określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="aed16-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="aed16-150">W przypadku korzystania z [oprogramowania pośredniczącego buforowania odpowiedzi](xref:performance/caching/middleware)Wywołaj <xref:Owin.CorsExtensions.UseCors%2A> przed <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="aed16-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="aed16-151">W przypadku routingu punktu końcowego oprogramowanie do obsługi mechanizmu CORS **musi** być skonfigurowane do wykonywania między wywołaniami `UseRouting` i `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="aed16-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="aed16-152">Zobacz temat [CORS testów](#testc) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="aed16-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="aed16-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="aed16-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="aed16-154">Aby uzyskać więcej informacji, zobacz [Opcje zasad CORS](#cpo) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="aed16-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="aed16-155"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metody mogą być łańcucha, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="aed16-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="aed16-156">Uwaga: określony adres URL **nie** może zawierać końcowego ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="aed16-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="aed16-157">Jeśli adres URL kończy się na `/` , porównywanie zwraca `false` i nie jest zwracany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="aed16-158">Mechanizm CORS z domyślnymi zasadami i oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="aed16-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="aed16-159">Następujący wyróżniony kod włącza domyślne zasady CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="aed16-160">Poprzedni kod stosuje domyślne zasady CORS do wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="aed16-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="aed16-161">Włączanie mechanizmu CORS przy użyciu routingu punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="aed16-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="aed16-162">Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy użyciu `RequireCors` obecnie **nie** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="aed16-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="aed16-163">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/20709) w [usłudze GitHub i testowanie mechanizmu CORS przy użyciu routingu punktów końcowych i [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="aed16-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="aed16-164">Dzięki kierowaniu punktów końcowych można włączyć funkcję CORS dla poszczególnych punktów końcowych przy użyciu <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> zestawu metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="aed16-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="aed16-165">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="aed16-165">In the preceding code:</span></span>

* <span data-ttu-id="aed16-166">`app.UseCors`włącza oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="aed16-167">Ponieważ zasady domyślne nie zostały skonfigurowane, `app.UseCors()` sama nie włącza mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="aed16-168">`/echo`Punkty końcowe i umożliwiają Zezwalanie na żądania między źródłami przy użyciu określonych zasad.</span><span class="sxs-lookup"><span data-stu-id="aed16-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="aed16-169">`/echo2` Razor Punkty końcowe stron i **nie** zezwalają na żądania między źródłami, ponieważ nie określono żadnych zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="aed16-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="aed16-170">Atrybut [[DisableCors]](#dc) **nie wyłącza mechanizmu** CORS, który został włączony przez Routing punktów końcowych za pomocą `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="aed16-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="aed16-171">Aby uzyskać instrukcje dotyczące testowania kodu podobnego do powyższego, zobacz artykuł [CORS testów z routingiem punktów końcowych i [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="aed16-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="aed16-172">Włączanie mechanizmu CORS z atrybutami</span><span class="sxs-lookup"><span data-stu-id="aed16-172">Enable CORS with attributes</span></span>

<span data-ttu-id="aed16-173">Włączanie mechanizmu CORS z atrybutem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) i stosowanie nazwanych zasad tylko do tych punktów końcowych, które wymagają mechanizmu CORS, udostępnia formant Finest.</span><span class="sxs-lookup"><span data-stu-id="aed16-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="aed16-174">Atrybut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) zapewnia alternatywę do stosowania mechanizmu CORS globalnie.</span><span class="sxs-lookup"><span data-stu-id="aed16-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="aed16-175">Ten `[EnableCors]` atrybut włącza funkcję CORS dla wybranych punktów końcowych, a nie wszystkie punkty końcowe:</span><span class="sxs-lookup"><span data-stu-id="aed16-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="aed16-176">`[EnableCors]`Określa zasady domyślne.</span><span class="sxs-lookup"><span data-stu-id="aed16-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="aed16-177">`[EnableCors("{Policy String}")]`Określa nazwane zasady.</span><span class="sxs-lookup"><span data-stu-id="aed16-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="aed16-178">Ten `[EnableCors]` atrybut może być stosowany do:</span><span class="sxs-lookup"><span data-stu-id="aed16-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="aed16-179">RazorStronic`PageModel`</span><span class="sxs-lookup"><span data-stu-id="aed16-179">Razor Page `PageModel`</span></span>
* <span data-ttu-id="aed16-180">Kontroler</span><span class="sxs-lookup"><span data-stu-id="aed16-180">Controller</span></span>
* <span data-ttu-id="aed16-181">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="aed16-181">Controller action method</span></span>

<span data-ttu-id="aed16-182">Różne zasady można stosować do kontrolerów, modeli stron lub metod akcji z `[EnableCors]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="aed16-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="aed16-183">Gdy `[EnableCors]` atrybut jest stosowany do kontrolera, modelu strony lub metody akcji, a funkcja CORS jest włączona w oprogramowaniu pośredniczącym, **obie** zasady są stosowane.</span><span class="sxs-lookup"><span data-stu-id="aed16-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="aed16-184">**Zalecamy łączenie zasad. Użyj** `[EnableCors]` **atrybutu lub oprogramowania pośredniczącego, a nie obu w tej samej aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="aed16-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="aed16-185">Poniższy kod stosuje różne zasady do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="aed16-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="aed16-186">Poniższy kod tworzy dwie zasady CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="aed16-187">W przypadku kontroli Finest ograniczającej żądania CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="aed16-188">Używany `[EnableCors("MyPolicy")]` z nazwanymi zasadami.</span><span class="sxs-lookup"><span data-stu-id="aed16-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="aed16-189">Nie Definiuj zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="aed16-189">Don't define a default policy.</span></span>
* <span data-ttu-id="aed16-190">Nie używaj [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="aed16-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="aed16-191">Kod w następnej sekcji jest zgodny z poprzednią listą.</span><span class="sxs-lookup"><span data-stu-id="aed16-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="aed16-192">Zobacz temat [CORS testów](#testc) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="aed16-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="aed16-193">Wyłącz funkcję CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-193">Disable CORS</span></span>

<span data-ttu-id="aed16-194">Atrybut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **nie wyłącza mechanizmu** CORS, który został włączony przez [Routing punktu końcowego](#ecors).</span><span class="sxs-lookup"><span data-stu-id="aed16-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="aed16-195">Poniższy kod definiuje zasady CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="aed16-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="aed16-196">Poniższy kod wyłącza funkcję CORS dla `GetValues2` akcji:</span><span class="sxs-lookup"><span data-stu-id="aed16-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="aed16-197">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="aed16-197">The preceding code:</span></span>

* <span data-ttu-id="aed16-198">Nie włącza mechanizmu CORS z [routingiem punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="aed16-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="aed16-199">Nie definiuje [domyślnych zasad CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="aed16-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="aed16-200">Używa elementu [[EnableCors ("Moja Policy")]](#attr) w celu włączenia `"MyPolicy"` zasad CORS dla kontrolera.</span><span class="sxs-lookup"><span data-stu-id="aed16-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="aed16-201">Wyłącza funkcję CORS dla `GetValues2` metody.</span><span class="sxs-lookup"><span data-stu-id="aed16-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="aed16-202">Aby uzyskać instrukcje dotyczące testowania poprzedniego kodu, zobacz temat [CORS testów](#testc) .</span><span class="sxs-lookup"><span data-stu-id="aed16-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="aed16-203">Opcje zasad CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-203">CORS policy options</span></span>

<span data-ttu-id="aed16-204">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="aed16-205">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="aed16-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="aed16-206">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="aed16-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="aed16-207">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="aed16-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="aed16-208">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="aed16-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="aed16-209">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="aed16-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="aed16-210">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="aed16-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="aed16-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="aed16-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aed16-212">W przypadku niektórych opcji warto przeczytać najpierw sekcję [jak działa mechanizm CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="aed16-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="aed16-213">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="aed16-213">Set the allowed origins</span></span>

<span data-ttu-id="aed16-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Umożliwia żądania CORS ze wszystkich źródeł z dowolnym schematem ( `http` lub `https` ).</span><span class="sxs-lookup"><span data-stu-id="aed16-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="aed16-215">`AllowAnyOrigin`jest niezabezpieczony, ponieważ *Każda witryna sieci Web* może wprowadzać żądania między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="aed16-216">Określenie `AllowAnyOrigin` i `AllowCredentials` jest niebezpieczną konfiguracją i może skutkować fałszerstwem żądania między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="aed16-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="aed16-217">Usługa CORS zwraca nieprawidłową odpowiedź CORS, gdy aplikacja jest skonfigurowana przy użyciu obu metod.</span><span class="sxs-lookup"><span data-stu-id="aed16-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="aed16-218">`AllowAnyOrigin`ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Origin` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="aed16-219">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="aed16-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="aed16-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Właściwość zasad jako funkcję, która umożliwia pochodzenie w celu dopasowania do skonfigurowanej domeny z symbolami wieloznacznymi podczas oceniania, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="aed16-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="aed16-221">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="aed16-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="aed16-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="aed16-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="aed16-223">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="aed16-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="aed16-224">Ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Methods` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="aed16-225">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="aed16-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="aed16-226">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="aed16-226">Set the allowed request headers</span></span>

<span data-ttu-id="aed16-227">Aby zezwolić na wysyłanie określonych nagłówków w żądaniu CORS, nazywanymi [nagłówkami żądań autora](https://xhr.spec.whatwg.org/#request), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="aed16-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="aed16-228">Aby zezwolić na wszystkie [nagłówki żądań autora](https://www.w3.org/TR/cors/#author-request-headers), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="aed16-229">`AllowAnyHeader`ma wpływ na żądania inspekcji wstępnej oraz nagłówek [Access-Control-Request-Heads](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="aed16-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="aed16-230">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="aed16-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="aed16-231">Zasady oprogramowania CORS są zgodne z określonymi nagłówkami określonymi przez `WithHeaders` jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie pasują do nagłówków określonych w `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="aed16-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="aed16-232">Na przykład rozważ zastosowanie skonfigurowanej aplikacji w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="aed16-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="aed16-233">Oprogramowanie pośredniczące CORS odrzuca żądanie wstępne z następującym nagłówkiem żądania, ponieważ `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nie ma na liście `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="aed16-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="aed16-234">Aplikacja zwraca odpowiedź *200 OK* , ale nie wysyła nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="aed16-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="aed16-235">W związku z tym przeglądarka nie próbuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="aed16-236">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="aed16-236">Set the exposed response headers</span></span>

<span data-ttu-id="aed16-237">Domyślnie przeglądarka nie ujawnia wszystkich nagłówków odpowiedzi dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="aed16-238">Aby uzyskać więcej informacji, zobacz temat [udostępnianie zasobów między źródłami W3C (terminologia): prosty nagłówek odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="aed16-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="aed16-239">Domyślnie dostępne są nagłówki odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="aed16-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="aed16-240">Specyfikacja CORS wywołuje te nagłówki *proste odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="aed16-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="aed16-241">Aby udostępnić inne nagłówki aplikacji, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="aed16-242">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="aed16-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="aed16-243">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="aed16-244">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="aed16-245">Poświadczenia obejmują cookie schematy uwierzytelniania s i http.</span><span class="sxs-lookup"><span data-stu-id="aed16-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="aed16-246">Aby wysłać poświadczenia z żądaniem między źródłami, klient musi mieć ustawioną `XMLHttpRequest.withCredentials` wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="aed16-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="aed16-247">`XMLHttpRequest`Bezpośrednie używanie:</span><span class="sxs-lookup"><span data-stu-id="aed16-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="aed16-248">Za pomocą jQuery:</span><span class="sxs-lookup"><span data-stu-id="aed16-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="aed16-249">Za pomocą [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="aed16-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="aed16-250">Serwer musi zezwalać na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="aed16-250">The server must allow the credentials.</span></span> <span data-ttu-id="aed16-251">Aby zezwolić na poświadczenia między źródłami, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="aed16-252">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="aed16-253">Jeśli przeglądarka wyśle poświadczenia, ale odpowiedź nie zawiera prawidłowego `Access-Control-Allow-Credentials` nagłówka, przeglądarka nie ujawnia odpowiedzi aplikacji, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="aed16-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="aed16-254">Zezwalanie na poświadczenia między źródłami stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="aed16-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="aed16-255">Witryna sieci Web w innej domenie może wysyłać poświadczenia zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed16-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="aed16-256">Specyfikacja mechanizmu CORS określa również, że ustawienia pochodzenia `"*"` (wszystkie źródła) są nieprawidłowe, jeśli `Access-Control-Allow-Credentials` nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="aed16-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="aed16-257">Żądania wstępnego lotu</span><span class="sxs-lookup"><span data-stu-id="aed16-257">Preflight requests</span></span>

<span data-ttu-id="aed16-258">W przypadku niektórych żądań CORS przeglądarka wysyła żądanie dodatkowych [opcji](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) przed wykonaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="aed16-259">To żądanie jest nazywane [żądaniem wstępnym](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="aed16-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="aed16-260">Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są **wszystkie** następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="aed16-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="aed16-261">Metoda żądania ma wartość GET, główna lub OPUBLIKOWANa.</span><span class="sxs-lookup"><span data-stu-id="aed16-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="aed16-262">Aplikacja nie ustawia nagłówków żądań innych niż `Accept` , `Accept-Language` ,, `Content-Language` , `Content-Type` lub `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="aed16-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="aed16-263">`Content-Type`Nagłówek, jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="aed16-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="aed16-264">Reguła dotycząca nagłówków żądań ustawiona dla żądania klienta dotyczy nagłówków, które są ustawiane przez aplikację przez wywołanie `setRequestHeader` na `XMLHttpRequest` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="aed16-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="aed16-265">Specyfikacja CORS wywołuje [nagłówki żądania autora](https://www.w3.org/TR/cors/#author-request-headers)tych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="aed16-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="aed16-266">Reguła nie ma zastosowania do nagłówków, które można ustawić w przeglądarce, na przykład `User-Agent` , `Host` lub `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="aed16-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="aed16-267">Poniżej znajduje się Przykładowa odpowiedź podobna do żądania inspekcji wstępnej wykonanego z przycisku **[Put Test]** w sekcji [test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="aed16-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="aed16-268">W ramach żądania wstępnego jest stosowana Metoda [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="aed16-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="aed16-269">Może zawierać następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="aed16-269">It may include the following headers:</span></span>

* <span data-ttu-id="aed16-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda http, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="aed16-271">[Access-Control-Request-Heads](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): lista nagłówków żądań, które aplikacja ustawia na rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="aed16-272">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które są ustawiane przez przeglądarkę, takich jak `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="aed16-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="aed16-273">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="aed16-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="aed16-274">Jeśli żądanie wstępne nie zostanie odrzucone, aplikacja zwróci `200 OK` odpowiedź, ale nie ustawi nagłówków CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="aed16-275">W związku z tym przeglądarka nie próbuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="aed16-276">Przykład odmowy żądania inspekcji wstępnej znajduje się w sekcji [test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="aed16-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="aed16-277">Korzystając z narzędzi F12, aplikacja konsoli pokazuje błąd podobny do jednego z następujących, w zależności od przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="aed16-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="aed16-278">Firefox: Zablokowano żądanie między źródłami: te same zasady pochodzenia nie zezwalają na odczytywanie zasobu zdalnego pod adresem `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="aed16-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="aed16-279">(Przyczyna: żądanie CORS nie powiodło się).</span><span class="sxs-lookup"><span data-stu-id="aed16-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="aed16-280">Więcej informacji</span><span class="sxs-lookup"><span data-stu-id="aed16-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="aed16-281">Oparte na chromie: dostęp do pobierania w " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " z pochodzenia " https://cors3.azurewebsites.net " został zablokowany przez zasady CORS: odpowiedź na żądanie wstępnej inspekcji nie przeszedł sprawdzenia kontroli dostępu: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.</span><span class="sxs-lookup"><span data-stu-id="aed16-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="aed16-282">Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="aed16-283">Aby zezwolić na określone nagłówki, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="aed16-284">Aby zezwolić na wszystkie [nagłówki żądań autora](https://www.w3.org/TR/cors/#author-request-headers), wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="aed16-285">Przeglądarki nie są spójne w sposób ich ustawiania `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="aed16-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="aed16-286">Jeśli:</span><span class="sxs-lookup"><span data-stu-id="aed16-286">If either:</span></span>

* <span data-ttu-id="aed16-287">Nagłówki są ustawione na coś innego niż`"*"`</span><span class="sxs-lookup"><span data-stu-id="aed16-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="aed16-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jest wywoływana: Uwzględnij co najmniej `Accept` , `Content-Type` , i oraz `Origin` wszystkie niestandardowe nagłówki, które mają być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="aed16-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="aed16-289">Kod automatycznego żądania wstępnego</span><span class="sxs-lookup"><span data-stu-id="aed16-289">Automatic preflight request code</span></span>

<span data-ttu-id="aed16-290">Po zastosowaniu zasad CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="aed16-291">Globalnie przez wywołanie `app.UseCors` w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="aed16-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="aed16-292">Przy użyciu `[EnableCors]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="aed16-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="aed16-293">ASP.NET Core odpowiada na żądanie opcji inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="aed16-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="aed16-294">Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy użyciu `RequireCors` obecnie **nie** obsługuje automatycznych żądań inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="aed16-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="aed16-295">Sekcja [CORS testów](#testc) tego dokumentu przedstawia takie zachowanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="aed16-296">[HttpOptions] atrybut dla żądań inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="aed16-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="aed16-297">Gdy mechanizm CORS jest włączony przy użyciu odpowiednich zasad, ASP.NET Core zazwyczaj automatycznie reaguje na żądania funkcji CORS przed inspekcją.</span><span class="sxs-lookup"><span data-stu-id="aed16-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="aed16-298">W niektórych scenariuszach taka sytuacja może nie być taka sama.</span><span class="sxs-lookup"><span data-stu-id="aed16-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="aed16-299">Na przykład przy użyciu mechanizmu [CORS z routingiem punktu końcowego](#ecors).</span><span class="sxs-lookup"><span data-stu-id="aed16-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="aed16-300">Poniższy kod używa atrybutu [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) do tworzenia punktów końcowych dla żądań opcji:</span><span class="sxs-lookup"><span data-stu-id="aed16-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="aed16-301">Aby uzyskać instrukcje dotyczące testowania poprzedniego kodu, zobacz temat [CORS testów z routingiem punktów końcowych i [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="aed16-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="aed16-302">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="aed16-302">Set the preflight expiration time</span></span>

<span data-ttu-id="aed16-303">`Access-Control-Max-Age`Nagłówek określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="aed16-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="aed16-304">Aby ustawić ten nagłówek, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="aed16-305">Jak działa mechanizm CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-305">How CORS works</span></span>

<span data-ttu-id="aed16-306">W tej sekcji opisano, co się dzieje w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie komunikatów http.</span><span class="sxs-lookup"><span data-stu-id="aed16-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="aed16-307">Mechanizm CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="aed16-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="aed16-308">CORS jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="aed16-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="aed16-309">Na przykład złośliwy aktor może użyć [skryptów między lokacjami (XSS)](xref:security/cross-site-scripting) względem witryny i wykonać żądanie między lokacjami w celu wykraść informacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="aed16-310">Interfejs API nie jest bezpieczniejszy dzięki umożliwieniu mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="aed16-311">Aby wymusić mechanizm CORS, należy do klienta (przeglądarki).</span><span class="sxs-lookup"><span data-stu-id="aed16-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="aed16-312">Serwer wykonuje żądanie i zwraca odpowiedź, jest to klient, który zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="aed16-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="aed16-313">Na przykład w dowolnym z poniższych narzędzi zostanie wyświetlona odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="aed16-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="aed16-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="aed16-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="aed16-315">Postman</span><span class="sxs-lookup"><span data-stu-id="aed16-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="aed16-316">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="aed16-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="aed16-317">Przeglądarka sieci Web, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="aed16-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="aed16-318">Jest to sposób, aby serwer zezwalał przeglądarce na wykonywanie żądania [interfejsu API](https://developer.mozilla.org/docs/Web/API/Fetch_API) [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub pobierania między źródłami, które w przeciwnym razie byłoby zabronione.</span><span class="sxs-lookup"><span data-stu-id="aed16-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="aed16-319">Przeglądarki bez funkcji CORS nie mogą wykonywać żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="aed16-320">Przed zastosowaniem mechanizmu CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) został użyty do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="aed16-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="aed16-321">JSONP nie używa XHR, używa `<script>` znacznika do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="aed16-322">Skrypty mogą być ładowane między różnymi źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="aed16-323">[Specyfikacja CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="aed16-324">Jeśli przeglądarka obsługuje mechanizm CORS, ustawia te nagłówki automatycznie dla żądań cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="aed16-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="aed16-325">Niestandardowy kod JavaScript nie jest wymagany do włączenia mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="aed16-326">[Przycisk Put test](https://cors3.azurewebsites.net/test) na wdrożonym [przykładzie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="aed16-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="aed16-327">Poniżej znajduje się przykład żądania między źródłami z przycisku Testuj [wartości](https://cors3.azurewebsites.net/) do `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="aed16-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="aed16-328">`Origin`Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="aed16-328">The `Origin` header:</span></span>

* <span data-ttu-id="aed16-329">Zapewnia domenę lokacji, która żąda żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="aed16-330">Jest wymagany i musi się różnić od hosta.</span><span class="sxs-lookup"><span data-stu-id="aed16-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="aed16-331">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="aed16-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="aed16-332">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="aed16-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="aed16-333">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="aed16-333">**Request headers**</span></span>

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

<span data-ttu-id="aed16-334">W obszarze `OPTIONS` żądania serwer ustawia nagłówek **odpowiedzi** `Access-Control-Allow-Origin: {allowed origin}` w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="aed16-335">Przykładowo wdrożone [przykładowe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)żądanie [usunięcia [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` zawiera następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="aed16-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="aed16-336">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="aed16-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="aed16-337">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="aed16-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="aed16-338">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="aed16-338">**Request headers**</span></span>

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

<span data-ttu-id="aed16-339">W poprzednich **nagłówkach odpowiedzi**serwer ustawia nagłówek [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="aed16-340">`https://cors1.azurewebsites.net`Wartość tego nagłówka pasuje do `Origin` nagłówka z żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="aed16-341">Jeśli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> jest wywoływana, `Access-Control-Allow-Origin: *` jest zwracana wartość symbol wieloznaczny.</span><span class="sxs-lookup"><span data-stu-id="aed16-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="aed16-342">`AllowAnyOrigin`zezwala na dowolne źródło.</span><span class="sxs-lookup"><span data-stu-id="aed16-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="aed16-343">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="aed16-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="aed16-344">W programie przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="aed16-345">Nawet jeśli serwer zwróci pomyślną odpowiedź, przeglądarka nie udostępni odpowiedzi dla aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="aed16-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="aed16-346">Wyświetl żądania opcji</span><span class="sxs-lookup"><span data-stu-id="aed16-346">Display OPTIONS requests</span></span>

<span data-ttu-id="aed16-347">Domyślnie przeglądarki Chrome i Edge nie wyświetlają żądań OPTIONS na karcie Sieć narzędzi F12.</span><span class="sxs-lookup"><span data-stu-id="aed16-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="aed16-348">Aby wyświetlić żądania opcji w następujących przeglądarkach:</span><span class="sxs-lookup"><span data-stu-id="aed16-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="aed16-349">`chrome://flags/#out-of-blink-cors` lub `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="aed16-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="aed16-350">Wyłącz flagę.</span><span class="sxs-lookup"><span data-stu-id="aed16-350">disable the flag.</span></span>
* <span data-ttu-id="aed16-351">uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="aed16-351">restart.</span></span>

<span data-ttu-id="aed16-352">W przeglądarce Firefox domyślnie są wyświetlane żądania dotyczące opcji.</span><span class="sxs-lookup"><span data-stu-id="aed16-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="aed16-353">Mechanizm CORS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="aed16-353">CORS in IIS</span></span>

<span data-ttu-id="aed16-354">W przypadku wdrażania w programie IIS należy uruchomić funkcję CORS przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="aed16-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="aed16-355">Aby zapewnić obsługę tego scenariusza, należy zainstalować i skonfigurować [moduł CORS usług IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="aed16-356">Testowanie CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-356">Test CORS</span></span>

<span data-ttu-id="aed16-357">[Pobieranie próbek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ma kod do testowania mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="aed16-358">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="aed16-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="aed16-359">Przykładem jest projekt interfejsu API ze Razor stronami dodanymi:</span><span class="sxs-lookup"><span data-stu-id="aed16-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="aed16-360">`WithOrigins("https://localhost:<port>");`powinien być używany tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="aed16-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="aed16-361">Poniżej `ValuesController` podano punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="aed16-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="aed16-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) jest dostarczany przez pakiet NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) i wyświetla informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="aed16-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="aed16-363">Przetestuj poprzedni przykładowy kod przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="aed16-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="aed16-364">Użyj wdrożonej przykładowej aplikacji pod adresem [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="aed16-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="aed16-365">Nie ma potrzeby pobierania przykładu.</span><span class="sxs-lookup"><span data-stu-id="aed16-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="aed16-366">Uruchom przykład przy `dotnet run` użyciu domyślnego adresu URL `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="aed16-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="aed16-367">Uruchom przykład z programu Visual Studio z portem ustawionym na 44398 dla adresu URL `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="aed16-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="aed16-368">Używanie przeglądarki z narzędziami F12:</span><span class="sxs-lookup"><span data-stu-id="aed16-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="aed16-369">Wybierz przycisk **wartości** i Przejrzyj nagłówki na karcie **Sieć** .</span><span class="sxs-lookup"><span data-stu-id="aed16-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="aed16-370">Wybierz przycisk **Put test** .</span><span class="sxs-lookup"><span data-stu-id="aed16-370">Select the **PUT test** button.</span></span> <span data-ttu-id="aed16-371">Zobacz temat [Opcje wyświetlania żądania](#options) , aby uzyskać instrukcje dotyczące wyświetlania żądania opcji.</span><span class="sxs-lookup"><span data-stu-id="aed16-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="aed16-372">**Test Put** tworzy dwa żądania, żądanie wstępnej kontroli i żądanie Put.</span><span class="sxs-lookup"><span data-stu-id="aed16-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="aed16-373">Wybierz **`GetValues2 [DisableCors]`** przycisk, aby wyzwolić nieudane żądanie CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="aed16-374">Jak wspomniano w dokumencie, odpowiedź zwraca 200 sukcesu, ale żądanie CORS nie zostało wykonane.</span><span class="sxs-lookup"><span data-stu-id="aed16-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="aed16-375">Wybierz kartę **konsola** , aby wyświetlić błąd CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="aed16-376">W zależności od przeglądarki wyświetlane są błędy podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="aed16-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="aed16-377">Dostęp do pobierania z lokalizacji źródłowej został `'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` zablokowany przez zasady CORS: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.</span><span class="sxs-lookup"><span data-stu-id="aed16-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="aed16-378">Jeśli nieprzezroczysta odpowiedź spełnia Twoje wymagania, ustaw tryb żądania na wartość "No-CORS", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="aed16-379">Punkty końcowe z obsługą mechanizmu CORS można testować za pomocą narzędzia, takiego jak [zwinięcie](https://curl.haxx.se/), [programu Fiddler](https://www.telerik.com/fiddler)lub [Poster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="aed16-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="aed16-380">W przypadku korzystania z narzędzia, Źródło żądania określone przez `Origin` nagłówek musi różnić się od hosta przyjmującego żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="aed16-381">Jeśli żądanie nie jest *źródłem krzyżowe* na podstawie wartości `Origin` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="aed16-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="aed16-382">Nie ma potrzeby przetwarzania żądania przez oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="aed16-383">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="aed16-384">Następujące polecenie używa polecenia `curl` , aby wydać żądanie Options z informacjami:</span><span class="sxs-lookup"><span data-stu-id="aed16-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="aed16-385">Testowanie mechanizmu CORS przy użyciu routingu punktów końcowych i [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="aed16-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="aed16-386">Włączenie mechanizmu CORS dla poszczególnych punktów końcowych przy użyciu `RequireCors` obecnie **nie** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="aed16-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="aed16-387">Rozważmy następujący kod, który używa [routingu punktu końcowego w celu włączenia mechanizmu CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="aed16-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="aed16-388">Poniżej `TodoItems1Controller` przedstawiono punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="aed16-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="aed16-389">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=1) wdrożonej [próbki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="aed16-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="aed16-390">Przyciski **delete [EnableCors]** i **Get [EnableCors]** zostały wykonane pomyślnie, ponieważ punkty końcowe mają `[EnableCors]` i odpowiadają na żądania inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="aed16-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="aed16-391">Inne punkty końcowe zakończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="aed16-391">The other endpoints fails.</span></span> <span data-ttu-id="aed16-392">Przycisk **Get** kończy się niepowodzeniem, ponieważ [kod JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) wysyła:</span><span class="sxs-lookup"><span data-stu-id="aed16-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="aed16-393">Poniżej `TodoItems2Controller` znajdują się podobne punkty końcowe, ale zawiera jawny kod odpowiadający na żądania opcji:</span><span class="sxs-lookup"><span data-stu-id="aed16-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="aed16-394">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=2) wdrożonej próbki.</span><span class="sxs-lookup"><span data-stu-id="aed16-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="aed16-395">Z listy rozwijanej **kontroler** wybierz kolejno pozycje **Inspekcja wstępna** , a następnie **Ustaw kontroler**.</span><span class="sxs-lookup"><span data-stu-id="aed16-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="aed16-396">Wszystkie wywołania mechanizmu CORS do `TodoItems2Controller` punktów końcowych powiodły się.</span><span class="sxs-lookup"><span data-stu-id="aed16-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aed16-397">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="aed16-397">Additional resources</span></span>

* [<span data-ttu-id="aed16-398">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="aed16-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="aed16-399">Wprowadzenie do modułu CORS usług IIS</span><span class="sxs-lookup"><span data-stu-id="aed16-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aed16-400">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aed16-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aed16-401">W tym artykule pokazano, jak włączyć funkcję CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aed16-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="aed16-402">Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="aed16-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="aed16-403">To ograniczenie jest nazywane *zasadami tego samego źródła*.</span><span class="sxs-lookup"><span data-stu-id="aed16-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="aed16-404">Zasady tego samego źródła uniemożliwiają złośliwej lokacji odczytywanie poufnych danych z innej lokacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="aed16-405">Czasami możesz chcieć zezwolić innym lokacjom na wykonywanie żądań między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="aed16-406">Aby uzyskać więcej informacji, zobacz [artykuł CORS firmy Mozilla](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="aed16-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="aed16-407">[Współużytkowanie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="aed16-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="aed16-408">Jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="aed16-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="aed16-409">**Nie** jest funkcją zabezpieczeń, funkcja CORS osłabi zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="aed16-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="aed16-410">Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="aed16-411">Aby uzyskać więcej informacji, zobacz [jak działa mechanizm CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="aed16-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="aed16-412">Zezwala serwerowi jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.</span><span class="sxs-lookup"><span data-stu-id="aed16-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="aed16-413">Jest bezpieczniejsze i bardziej elastyczne niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="aed16-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="aed16-414">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aed16-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="aed16-415">To samo źródło</span><span class="sxs-lookup"><span data-stu-id="aed16-415">Same origin</span></span>

<span data-ttu-id="aed16-416">Dwa adresy URL mają te same źródła, jeśli mają identyczne schematy, hosty i porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="aed16-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="aed16-417">Te dwa adresy URL mają te same źródła:</span><span class="sxs-lookup"><span data-stu-id="aed16-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="aed16-418">Te adresy URL mają różne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="aed16-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="aed16-419">`https://example.net`: Inna domena</span><span class="sxs-lookup"><span data-stu-id="aed16-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="aed16-420">`https://www.example.com/foo.html`: Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="aed16-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="aed16-421">`http://example.com/foo.html`: Inny schemat</span><span class="sxs-lookup"><span data-stu-id="aed16-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="aed16-422">`https://example.com:9000/foo.html`: Inny port</span><span class="sxs-lookup"><span data-stu-id="aed16-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="aed16-423">Program Internet Explorer nie traktuje portu podczas porównywania źródeł.</span><span class="sxs-lookup"><span data-stu-id="aed16-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="aed16-424">CORS z nazwanymi zasadami i programem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="aed16-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="aed16-425">Oprogramowanie pośredniczące CORS obsługuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="aed16-426">Poniższy kod włącza mechanizm CORS dla całej aplikacji z określonym źródłem:</span><span class="sxs-lookup"><span data-stu-id="aed16-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="aed16-427">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="aed16-427">The preceding code:</span></span>

* <span data-ttu-id="aed16-428">Ustawia nazwę zasad na " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="aed16-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="aed16-429">Nazwa zasad jest dowolną.</span><span class="sxs-lookup"><span data-stu-id="aed16-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="aed16-430">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia, która umożliwia mechanizm CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="aed16-431">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="aed16-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="aed16-432">Wyrażenie lambda przyjmuje <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="aed16-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="aed16-433">[Opcje konfiguracji](#cors-policy-options), takie jak `WithOrigins` , zostały opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="aed16-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="aed16-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="aed16-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="aed16-435">Aby uzyskać więcej informacji, zobacz [Opcje zasad CORS](#cpo) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="aed16-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="aed16-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metoda może łączyć metody łańcucha, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="aed16-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="aed16-437">Uwaga: adres URL **nie** może zawierać końcowego ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="aed16-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="aed16-438">Jeśli adres URL kończy się na `/` , porównywanie zwraca `false` i nie jest zwracany nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="aed16-439">Poniższy kod dotyczy zasad CORS dla wszystkich punktów końcowych aplikacji za pośrednictwem oprogramowania do obsługi mechanizmu CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="aed16-440">Uwaga: `UseCors` należy wywołać przed `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="aed16-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="aed16-441">Zobacz [Włączanie mechanizmu CORS na Razor stronach, kontrolerach i metodach akcji](#ecors) , aby zastosować zasady CORS na poziomie strony/kontrolera/akcji.</span><span class="sxs-lookup"><span data-stu-id="aed16-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="aed16-442">Zobacz temat [CORS testów](#test) , aby uzyskać instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="aed16-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="aed16-443">Włączanie mechanizmu CORS z atrybutami</span><span class="sxs-lookup"><span data-stu-id="aed16-443">Enable CORS with attributes</span></span>

<span data-ttu-id="aed16-444">Atrybut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) stanowi alternatywę dla zastosowania mechanizmu CORS globalnie.</span><span class="sxs-lookup"><span data-stu-id="aed16-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="aed16-445">Ten `[EnableCors]` atrybut włącza funkcję CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="aed16-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="aed16-446">Użyj, `[EnableCors]` Aby określić zasady domyślne i `[EnableCors("{Policy String}")]` określić zasady.</span><span class="sxs-lookup"><span data-stu-id="aed16-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="aed16-447">Ten `[EnableCors]` atrybut może być stosowany do:</span><span class="sxs-lookup"><span data-stu-id="aed16-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="aed16-448">RazorStronic`PageModel`</span><span class="sxs-lookup"><span data-stu-id="aed16-448">Razor Page `PageModel`</span></span>
* <span data-ttu-id="aed16-449">Kontroler</span><span class="sxs-lookup"><span data-stu-id="aed16-449">Controller</span></span>
* <span data-ttu-id="aed16-450">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="aed16-450">Controller action method</span></span>

<span data-ttu-id="aed16-451">Możesz zastosować różne zasady do kontrolera/strony-model/akcja z `[EnableCors]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="aed16-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="aed16-452">Gdy `[EnableCors]` atrybut jest stosowany do metody Controller/model/akcja, a funkcja CORS jest włączona w oprogramowaniu pośredniczącym, stosowane są **obie** zasady.</span><span class="sxs-lookup"><span data-stu-id="aed16-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="aed16-453">**Nie** zaleca się łączenia zasad.</span><span class="sxs-lookup"><span data-stu-id="aed16-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="aed16-454">Użyj `[EnableCors]` atrybutu lub oprogramowania pośredniczącego, a **nie obu**.</span><span class="sxs-lookup"><span data-stu-id="aed16-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="aed16-455">W przypadku korzystania z programu nie `[EnableCors]` należy definiować zasad domyślnych. **not**</span><span class="sxs-lookup"><span data-stu-id="aed16-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="aed16-456">Poniższy kod stosuje różne zasady do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="aed16-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="aed16-457">Poniższy kod tworzy domyślne zasady CORS i zasady o nazwie `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="aed16-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="aed16-458">Wyłącz funkcję CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-458">Disable CORS</span></span>

<span data-ttu-id="aed16-459">Atrybut [ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) wyłącza funkcję CORS dla kontrolera/strony-modelu/akcji.</span><span class="sxs-lookup"><span data-stu-id="aed16-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="aed16-460">Opcje zasad CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-460">CORS policy options</span></span>

<span data-ttu-id="aed16-461">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="aed16-462">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="aed16-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="aed16-463">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="aed16-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="aed16-464">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="aed16-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="aed16-465">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="aed16-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="aed16-466">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="aed16-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="aed16-467">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="aed16-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="aed16-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="aed16-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aed16-469">W przypadku niektórych opcji warto przeczytać najpierw sekcję [jak działa mechanizm CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="aed16-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="aed16-470">Ustaw dozwolone źródła</span><span class="sxs-lookup"><span data-stu-id="aed16-470">Set the allowed origins</span></span>

<span data-ttu-id="aed16-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Umożliwia żądania CORS ze wszystkich źródeł z dowolnym schematem ( `http` lub `https` ).</span><span class="sxs-lookup"><span data-stu-id="aed16-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="aed16-472">`AllowAnyOrigin`jest niezabezpieczony, ponieważ *Każda witryna sieci Web* może wprowadzać żądania między źródłami do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="aed16-473">Określenie `AllowAnyOrigin` i `AllowCredentials` jest niebezpieczną konfiguracją i może skutkować fałszerstwem żądania między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="aed16-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="aed16-474">W przypadku bezpiecznej aplikacji należy określić dokładną listę źródeł, jeśli klient musi autoryzować sam do uzyskiwania dostępu do zasobów serwera.</span><span class="sxs-lookup"><span data-stu-id="aed16-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="aed16-475">`AllowAnyOrigin`ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Origin` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="aed16-476">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="aed16-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="aed16-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Właściwość zasad jako funkcję, która umożliwia pochodzenie w celu dopasowania do skonfigurowanej domeny z symbolami wieloznacznymi podczas oceniania, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="aed16-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="aed16-478">Ustaw dozwolone metody HTTP</span><span class="sxs-lookup"><span data-stu-id="aed16-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="aed16-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="aed16-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="aed16-480">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="aed16-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="aed16-481">Ma wpływ na żądania inspekcji wstępnej i `Access-Control-Allow-Methods` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="aed16-482">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="aed16-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="aed16-483">Ustaw nagłówki dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="aed16-483">Set the allowed request headers</span></span>

<span data-ttu-id="aed16-484">Aby zezwolić na wysyłanie określonych nagłówków w żądaniu CORS, nazywanymi *nagłówkami żądań autora*, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="aed16-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="aed16-485">Aby zezwolić na wszystkie nagłówki żądań autora, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="aed16-486">To ustawienie ma wpływ na żądania inspekcji wstępnej i `Access-Control-Request-Headers` nagłówek.</span><span class="sxs-lookup"><span data-stu-id="aed16-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="aed16-487">Aby uzyskać więcej informacji, zobacz sekcję [żądania dotyczące inspekcji wstępnej](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="aed16-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="aed16-488">Oprogramowanie pośredniczące CORS zawsze umożliwia wysyłanie czterech nagłówków w `Access-Control-Request-Headers` celu, niezależnie od wartości skonfigurowanych w CorsPolicy. Heads.</span><span class="sxs-lookup"><span data-stu-id="aed16-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="aed16-489">Ta lista nagłówków obejmuje:</span><span class="sxs-lookup"><span data-stu-id="aed16-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="aed16-490">Na przykład rozważ zastosowanie skonfigurowanej aplikacji w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="aed16-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="aed16-491">Oprogramowanie pośredniczące CORS pomyślnie reaguje na żądanie inspekcji wstępnej z następującym nagłówkiem żądania, ponieważ `Content-Language` jest zawsze dozwolone:</span><span class="sxs-lookup"><span data-stu-id="aed16-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="aed16-492">Ustawianie nagłówków uwidocznionych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="aed16-492">Set the exposed response headers</span></span>

<span data-ttu-id="aed16-493">Domyślnie przeglądarka nie ujawnia wszystkich nagłówków odpowiedzi dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="aed16-494">Aby uzyskać więcej informacji, zobacz temat [udostępnianie zasobów między źródłami W3C (terminologia): prosty nagłówek odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="aed16-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="aed16-495">Domyślnie dostępne są nagłówki odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="aed16-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="aed16-496">Specyfikacja CORS wywołuje te nagłówki *proste odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="aed16-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="aed16-497">Aby udostępnić inne nagłówki aplikacji, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="aed16-498">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="aed16-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="aed16-499">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="aed16-500">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="aed16-501">Poświadczenia obejmują cookie schematy uwierzytelniania s i http.</span><span class="sxs-lookup"><span data-stu-id="aed16-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="aed16-502">Aby wysłać poświadczenia z żądaniem między źródłami, klient musi mieć ustawioną `XMLHttpRequest.withCredentials` wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="aed16-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="aed16-503">`XMLHttpRequest`Bezpośrednie używanie:</span><span class="sxs-lookup"><span data-stu-id="aed16-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="aed16-504">Za pomocą jQuery:</span><span class="sxs-lookup"><span data-stu-id="aed16-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="aed16-505">Za pomocą [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="aed16-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="aed16-506">Serwer musi zezwalać na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="aed16-506">The server must allow the credentials.</span></span> <span data-ttu-id="aed16-507">Aby zezwolić na poświadczenia między źródłami, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="aed16-508">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="aed16-509">Jeśli przeglądarka wyśle poświadczenia, ale odpowiedź nie zawiera prawidłowego `Access-Control-Allow-Credentials` nagłówka, przeglądarka nie ujawnia odpowiedzi aplikacji, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="aed16-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="aed16-510">Zezwalanie na poświadczenia między źródłami stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="aed16-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="aed16-511">Witryna sieci Web w innej domenie może wysyłać poświadczenia zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed16-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="aed16-512">Specyfikacja mechanizmu CORS określa również, że ustawienia pochodzenia `"*"` (wszystkie źródła) są nieprawidłowe, jeśli `Access-Control-Allow-Credentials` nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="aed16-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="aed16-513">Żądania wstępnego lotu</span><span class="sxs-lookup"><span data-stu-id="aed16-513">Preflight requests</span></span>

<span data-ttu-id="aed16-514">W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie przed wykonaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="aed16-515">To żądanie jest nazywane *żądaniem wstępnym*.</span><span class="sxs-lookup"><span data-stu-id="aed16-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="aed16-516">Jeśli spełnione są następujące warunki, przeglądarka może pominąć żądanie wstępne:</span><span class="sxs-lookup"><span data-stu-id="aed16-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="aed16-517">Metoda żądania ma wartość GET, główna lub OPUBLIKOWANa.</span><span class="sxs-lookup"><span data-stu-id="aed16-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="aed16-518">Aplikacja nie ustawia nagłówków żądań innych niż `Accept` , `Accept-Language` ,, `Content-Language` , `Content-Type` lub `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="aed16-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="aed16-519">`Content-Type`Nagłówek, jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="aed16-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="aed16-520">Reguła dotycząca nagłówków żądań ustawiona dla żądania klienta dotyczy nagłówków, które są ustawiane przez aplikację przez wywołanie `setRequestHeader` na `XMLHttpRequest` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="aed16-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="aed16-521">Specyfikacja CORS wywołuje *nagłówki żądania autora*tych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="aed16-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="aed16-522">Reguła nie ma zastosowania do nagłówków, które można ustawić w przeglądarce, na przykład `User-Agent` , `Host` lub `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="aed16-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="aed16-523">Poniżej znajduje się przykład żądania wstępnego:</span><span class="sxs-lookup"><span data-stu-id="aed16-523">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="aed16-524">Żądanie przed inspekcją używa metody HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="aed16-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="aed16-525">Zawiera dwa specjalne nagłówki:</span><span class="sxs-lookup"><span data-stu-id="aed16-525">It includes two special headers:</span></span>

* <span data-ttu-id="aed16-526">`Access-Control-Request-Method`: Metoda HTTP, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="aed16-527">`Access-Control-Request-Headers`: Lista nagłówków żądań, które aplikacja ustawia na rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="aed16-528">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które są ustawiane przez przeglądarkę, takich jak `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="aed16-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="aed16-529">Gdy mechanizm CORS jest włączony przy użyciu odpowiednich zasad, ASP.NET Core zwykle automatycznie reaguje na żądania inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="aed16-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="aed16-530">Zobacz [[HttpOptions] atrybutu dla żądań inspekcji wstępnej](#pro).</span><span class="sxs-lookup"><span data-stu-id="aed16-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="aed16-531">Żądanie inspekcji wstępnej CORS może zawierać `Access-Control-Request-Headers` nagłówek, który wskazuje serwerowi nagłówki wysyłane z rzeczywistym żądaniem.</span><span class="sxs-lookup"><span data-stu-id="aed16-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="aed16-532">Aby zezwolić na określone nagłówki, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="aed16-533">Aby zezwolić na wszystkie nagłówki żądań autora, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="aed16-534">Przeglądarki nie są w pełni spójne w sposób ich ustawiania `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="aed16-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="aed16-535">Jeśli ustawisz nagłówki jako elementy inne niż `"*"` (lub użycie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), należy uwzględnić co najmniej `Accept` , `Content-Type` i i `Origin` wszystkie niestandardowe nagłówki, które mają być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="aed16-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="aed16-536">Poniżej znajduje się Przykładowa odpowiedź na żądanie inspekcji wstępnej (przy założeniu, że serwer zezwala na żądanie):</span><span class="sxs-lookup"><span data-stu-id="aed16-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="aed16-537">Odpowiedź zawiera nagłówek, `Access-Control-Allow-Methods` który zawiera listę dozwolonych metod i opcjonalnie `Access-Control-Allow-Headers` nagłówek, który zawiera listę dozwolonych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="aed16-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="aed16-538">W przypadku pomyślnego przeprowadzenia żądania wstępnego przeglądarka wyśle rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="aed16-539">Jeśli żądanie wstępne nie zostanie odrzucone, aplikacja zwróci odpowiedź *200 OK* , ale nie wyśle nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="aed16-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="aed16-540">W związku z tym przeglądarka nie próbuje żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="aed16-541">Ustaw czas wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="aed16-541">Set the preflight expiration time</span></span>

<span data-ttu-id="aed16-542">`Access-Control-Max-Age`Nagłówek określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="aed16-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="aed16-543">Aby ustawić ten nagłówek, wywołaj <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="aed16-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="aed16-544">Jak działa mechanizm CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-544">How CORS works</span></span>

<span data-ttu-id="aed16-545">W tej sekcji opisano, co się dzieje w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie komunikatów http.</span><span class="sxs-lookup"><span data-stu-id="aed16-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="aed16-546">Mechanizm CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="aed16-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="aed16-547">CORS jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="aed16-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="aed16-548">Na przykład złośliwy aktor może użyć [zapobiegania skryptom między lokacjami (XSS)](xref:security/cross-site-scripting) względem witryny i wykonać żądanie między lokacjami w celu wykraść informacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="aed16-549">Interfejs API nie jest bezpieczniejszy przez umożliwienie mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="aed16-550">Aby wymusić mechanizm CORS, należy do klienta (przeglądarki).</span><span class="sxs-lookup"><span data-stu-id="aed16-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="aed16-551">Serwer wykonuje żądanie i zwraca odpowiedź, jest to klient, który zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="aed16-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="aed16-552">Na przykład w dowolnym z poniższych narzędzi zostanie wyświetlona odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="aed16-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="aed16-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="aed16-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="aed16-554">Postman</span><span class="sxs-lookup"><span data-stu-id="aed16-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="aed16-555">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="aed16-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="aed16-556">Przeglądarka sieci Web, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="aed16-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="aed16-557">Jest to sposób, aby serwer zezwalał przeglądarce na wykonywanie żądania [interfejsu API](https://developer.mozilla.org/docs/Web/API/Fetch_API) [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub pobierania między źródłami, które w przeciwnym razie byłoby zabronione.</span><span class="sxs-lookup"><span data-stu-id="aed16-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="aed16-558">Przeglądarki (bez CORS) nie mogą wykonywać żądań między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="aed16-559">Przed zastosowaniem mechanizmu CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) został użyty do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="aed16-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="aed16-560">JSONP nie używa XHR, używa `<script>` znacznika do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="aed16-561">Skrypty mogą być ładowane między różnymi źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="aed16-562">[Specyfikacja CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="aed16-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="aed16-563">Jeśli przeglądarka obsługuje mechanizm CORS, ustawia te nagłówki automatycznie dla żądań cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="aed16-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="aed16-564">Niestandardowy kod JavaScript nie jest wymagany do włączenia mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="aed16-565">Poniżej przedstawiono przykład żądania między źródłami danych.</span><span class="sxs-lookup"><span data-stu-id="aed16-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="aed16-566">`Origin`Nagłówek zawiera domenę lokacji, która żąda żądania.</span><span class="sxs-lookup"><span data-stu-id="aed16-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="aed16-567">`Origin`Nagłówek jest wymagany i musi się różnić od hosta.</span><span class="sxs-lookup"><span data-stu-id="aed16-567">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="aed16-568">Jeśli serwer zezwala na żądanie, ustawia `Access-Control-Allow-Origin` nagłówek w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="aed16-569">Wartość tego nagłówka jest zgodna z `Origin` nagłówkiem z żądania lub jest wartością symbolu wieloznacznego `"*"` , co oznacza, że wszystkie źródła są dozwolone:</span><span class="sxs-lookup"><span data-stu-id="aed16-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="aed16-570">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="aed16-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="aed16-571">W programie przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="aed16-572">Nawet jeśli serwer zwróci pomyślną odpowiedź, przeglądarka nie udostępni odpowiedzi dla aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="aed16-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="aed16-573">Testowanie CORS</span><span class="sxs-lookup"><span data-stu-id="aed16-573">Test CORS</span></span>

<span data-ttu-id="aed16-574">Aby przetestować CORS:</span><span class="sxs-lookup"><span data-stu-id="aed16-574">To test CORS:</span></span>

1. <span data-ttu-id="aed16-575">[Utwórz projekt interfejsu API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="aed16-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="aed16-576">Możesz również [pobrać przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="aed16-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="aed16-577">Włącz funkcję CORS przy użyciu jednego z metod w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="aed16-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="aed16-578">Przykład:</span><span class="sxs-lookup"><span data-stu-id="aed16-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="aed16-579">`WithOrigins("https://localhost:<port>");`powinien być używany tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="aed16-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="aed16-580">Utwórz projekt aplikacji sieci Web ( Razor strony lub MVC).</span><span class="sxs-lookup"><span data-stu-id="aed16-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="aed16-581">Przykład używa Razor stron.</span><span class="sxs-lookup"><span data-stu-id="aed16-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="aed16-582">Aplikację sieci Web można utworzyć w tym samym rozwiązaniu co projekt interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="aed16-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="aed16-583">Dodaj następujący wyróżniony kod do pliku *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aed16-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="aed16-584">W poprzednim kodzie Zastąp ciąg `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresem URL wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="aed16-585">Wdróż projekt interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="aed16-585">Deploy the API project.</span></span> <span data-ttu-id="aed16-586">Na przykład [Wdróż na platformie Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="aed16-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="aed16-587">Uruchom Razor strony lub aplikację MVC na pulpicie, a następnie kliknij przycisk **Testuj** .</span><span class="sxs-lookup"><span data-stu-id="aed16-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="aed16-588">Użyj narzędzi F12, aby przejrzeć komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="aed16-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="aed16-589">Usuń pochodzenie hosta lokalnego z `WithOrigins` i Wdróż aplikację.</span><span class="sxs-lookup"><span data-stu-id="aed16-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="aed16-590">Alternatywnie Uruchom aplikację kliencką z innym portem.</span><span class="sxs-lookup"><span data-stu-id="aed16-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="aed16-591">Na przykład uruchom polecenie z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aed16-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="aed16-592">Przetestuj za pomocą aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="aed16-592">Test with the client app.</span></span> <span data-ttu-id="aed16-593">Błędy funkcji CORS zwracają błąd, ale komunikat o błędzie nie jest dostępny dla języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="aed16-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="aed16-594">Aby wyświetlić błąd, Użyj karty konsola w narzędziach F12.</span><span class="sxs-lookup"><span data-stu-id="aed16-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="aed16-595">W zależności od przeglądarki pojawia się błąd (w konsoli narzędzia F12) podobny do poniższego:</span><span class="sxs-lookup"><span data-stu-id="aed16-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="aed16-596">Korzystanie z przeglądarki Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="aed16-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="aed16-597">**SEC7120: [CORS] `https://localhost:44375` nie znaleziono źródła `https://localhost:44375` w nagłówku odpowiedzi "Access-Control-Allow-Origin" dla zasobu Cross-Origin w`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="aed16-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="aed16-598">Korzystanie z programu Chrome:</span><span class="sxs-lookup"><span data-stu-id="aed16-598">Using Chrome:</span></span>

     <span data-ttu-id="aed16-599">**Dostęp do elementu XMLHttpRequest w lokalizacji `https://webapi.azurewebsites.net/api/values/1` z punktu początkowego został `https://localhost:44375` zablokowany przez zasady CORS: brak nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.**</span><span class="sxs-lookup"><span data-stu-id="aed16-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="aed16-600">Punkty końcowe z obsługą mechanizmu CORS można testować za pomocą narzędzia, takiego jak [programu Fiddler](https://www.telerik.com/fiddler) lub [Poster](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="aed16-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="aed16-601">W przypadku korzystania z narzędzia, Źródło żądania określone przez `Origin` nagłówek musi różnić się od hosta przyjmującego żądanie.</span><span class="sxs-lookup"><span data-stu-id="aed16-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="aed16-602">Jeśli żądanie nie jest *źródłem krzyżowe* na podstawie wartości `Origin` nagłówka:</span><span class="sxs-lookup"><span data-stu-id="aed16-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="aed16-603">Nie ma potrzeby przetwarzania żądania przez oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="aed16-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="aed16-604">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="aed16-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="aed16-605">Mechanizm CORS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="aed16-605">CORS in IIS</span></span>

<span data-ttu-id="aed16-606">W przypadku wdrażania w programie IIS należy uruchomić funkcję CORS przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="aed16-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="aed16-607">Aby zapewnić obsługę tego scenariusza, należy zainstalować i skonfigurować [moduł CORS usług IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed16-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aed16-608">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="aed16-608">Additional resources</span></span>

* [<span data-ttu-id="aed16-609">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="aed16-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="aed16-610">Wprowadzenie do modułu CORS usług IIS</span><span class="sxs-lookup"><span data-stu-id="aed16-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
