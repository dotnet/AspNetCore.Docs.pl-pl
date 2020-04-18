---
title: Włączanie żądań między źródłami (CORS) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak cors jako standard zezwalania lub odrzucania żądań cross-origin w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642698"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="48ba0-103">Włączanie żądań między źródłami (CORS) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48ba0-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48ba0-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="48ba0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="48ba0-105">W tym artykule pokazano, jak włączyć usługę CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="48ba0-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="48ba0-106">Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="48ba0-107">To ograniczenie jest nazywane *zasadami tego samego pochodzenia*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="48ba0-108">Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny.</span><span class="sxs-lookup"><span data-stu-id="48ba0-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="48ba0-109">Czasami możesz zezwolić innym witrynom na składanie żądań cross-origin do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="48ba0-110">Aby uzyskać więcej informacji, zobacz [artykuł Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="48ba0-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="48ba0-111">[Udostępnianie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="48ba0-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="48ba0-112">Jest standardem W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="48ba0-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="48ba0-113">**Nie** jest to funkcja zabezpieczeń, CORS rozluźnia zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="48ba0-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="48ba0-114">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="48ba0-115">Aby uzyskać więcej informacji, zobacz [Jak działa CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="48ba0-116">Umożliwia serwerowi jawne zezwalanie na niektóre żądania między źródłami, jednocześnie odrzucając inne.</span><span class="sxs-lookup"><span data-stu-id="48ba0-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="48ba0-117">Jest bezpieczniejsza i bardziej elastyczna niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="48ba0-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="48ba0-118">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48ba0-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="48ba0-119">To samo pochodzenie</span><span class="sxs-lookup"><span data-stu-id="48ba0-119">Same origin</span></span>

<span data-ttu-id="48ba0-120">Dwa adresy URL mają to samo pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454).](https://tools.ietf.org/html/rfc6454)</span><span class="sxs-lookup"><span data-stu-id="48ba0-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="48ba0-121">Te dwa adresy URL mają to samo źródło:</span><span class="sxs-lookup"><span data-stu-id="48ba0-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="48ba0-122">Te adresy URL mają inne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="48ba0-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="48ba0-123">`https://example.net`&ndash; Inna domena</span><span class="sxs-lookup"><span data-stu-id="48ba0-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="48ba0-124">`https://www.example.com/foo.html`&ndash; Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="48ba0-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="48ba0-125">`http://example.com/foo.html`&ndash; Inny schemat</span><span class="sxs-lookup"><span data-stu-id="48ba0-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="48ba0-126">`https://example.com:9000/foo.html`&ndash; Inny port</span><span class="sxs-lookup"><span data-stu-id="48ba0-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="48ba0-127">Włączanie mechanizmu CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-127">Enable CORS</span></span>

<span data-ttu-id="48ba0-128">Istnieją trzy sposoby włączenia cors:</span><span class="sxs-lookup"><span data-stu-id="48ba0-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="48ba0-129">W oprogramowaniu pośredniczącym przy użyciu [nazwanych zasad](#np) lub [zasad domyślnych](#dp).</span><span class="sxs-lookup"><span data-stu-id="48ba0-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="48ba0-130">Korzystanie z [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="48ba0-131">Z atrybutem [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="48ba0-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="48ba0-132">Za pomocą [atrybutu [EnableCors]](#attr) z nazwanych zasad zapewnia najwyższą kontrolę w ograniczaniu punktów końcowych, które obsługują CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="48ba0-133">Każde podejście jest szczegółowo opisane w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="48ba0-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="48ba0-134">CORS z nazwanymi zasadami i oprogramowaniem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="48ba0-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="48ba0-135">Oprogramowanie pośredniczące CORS obsługuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="48ba0-136">Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji o określonych źródłach:</span><span class="sxs-lookup"><span data-stu-id="48ba0-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="48ba0-137">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-137">The preceding code:</span></span>

* <span data-ttu-id="48ba0-138">Ustawia nazwę zasady `_myAllowSpecificOrigins`na .</span><span class="sxs-lookup"><span data-stu-id="48ba0-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="48ba0-139">Nazwa zasad jest dowolna.</span><span class="sxs-lookup"><span data-stu-id="48ba0-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="48ba0-140">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia i `_myAllowSpecificOrigins` określa zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="48ba0-141">`UseCors`dodaje oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="48ba0-142">Wezwanie do `UseCors` musi być `UseRouting`umieszczone `UseAuthorization`po , ale przed .</span><span class="sxs-lookup"><span data-stu-id="48ba0-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="48ba0-143">Aby uzyskać więcej informacji, zobacz [Kolejność oprogramowania pośredniczącego](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="48ba0-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="48ba0-144">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="48ba0-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="48ba0-145">Lambda bierze <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="48ba0-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="48ba0-146">[Opcje konfiguracji](#cors-policy-options), `WithOrigins`takie jak , są opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="48ba0-147">Włącza `_myAllowSpecificOrigins` zasady CORS dla wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="48ba0-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="48ba0-148">Zobacz [routing punktu końcowego,](#ecors) aby zastosować zasady CORS do określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="48ba0-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="48ba0-149">W obszarze routingu punktu końcowego oprogramowanie pośredniczące CORS `UseRouting` `UseEndpoints` ***musi*** być skonfigurowane do wykonywania między wywołaniami do i .</span><span class="sxs-lookup"><span data-stu-id="48ba0-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="48ba0-150">Zobacz [Test CORS](#testc) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="48ba0-151"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="48ba0-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="48ba0-152">Aby uzyskać więcej informacji, zobacz [opcje zasad CORS](#cpo) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="48ba0-153">Metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> mogą być połączone łańcuchem, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="48ba0-154">Uwaga: Określony adres URL **nie** może`/`zawierać końcowego ukośnika ( ).</span><span class="sxs-lookup"><span data-stu-id="48ba0-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="48ba0-155">Jeśli adres URL `/`zostanie zakończony `false` na , porównanie zwraca się i nie zwracany jest żaden nagłówek.</span><span class="sxs-lookup"><span data-stu-id="48ba0-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="48ba0-156">CORS z domyślną zasadą i oprogramowaniem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="48ba0-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="48ba0-157">Poniższy wyróżniony kod umożliwia domyślną zasadę CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="48ba0-158">Powyższy kod stosuje domyślne zasady CORS do wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="48ba0-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="48ba0-159">Włącz cors z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="48ba0-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="48ba0-160">Włączenie mechanizmu CORS dla punktu `RequireCors` końcowego przy użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="48ba0-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="48ba0-161">Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/aspnetcore/issues/20709) i [test cors z routingiem punktu końcowego i [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="48ba0-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="48ba0-162">Za pomocą routingu punktu końcowego cors można włączyć na <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> podstawie punktu końcowego przy użyciu zestawu metod rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="48ba0-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="48ba0-163">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-163">In the preceding code:</span></span>

* <span data-ttu-id="48ba0-164">`app.UseCors`włącza oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="48ba0-165">Ponieważ domyślna zasada nie została `app.UseCors()` skonfigurowana, sama nie włącza mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="48ba0-166">Punkty `/echo` końcowe i kontrolera zezwalają na żądania cross-origin przy użyciu określonych zasad.</span><span class="sxs-lookup"><span data-stu-id="48ba0-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="48ba0-167">Punkty `/echo2` końcowe stron i Razor ***pages nie*** zezwalają na żądania między źródłami, ponieważ nie określono zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="48ba0-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="48ba0-168">Atrybut [[DisableCors]](#dc) ***nie*** wyłącza mechanizmu CORS, który został `RequireCors`włączony przez routing punktu końcowego za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="48ba0-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="48ba0-169">Zobacz [Test CORS z routingu punktu końcowego i [HttpOptions]](#tcer) instrukcje dotyczące testowania kodu podobnego do poprzedniego.</span><span class="sxs-lookup"><span data-stu-id="48ba0-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="48ba0-170">Włącz cors z atrybutami</span><span class="sxs-lookup"><span data-stu-id="48ba0-170">Enable CORS with attributes</span></span>

<span data-ttu-id="48ba0-171">Włączenie cors z [atrybutem [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) i stosowanie nazwanych zasad tylko do tych punktów końcowych, które wymagają CORS zapewnia najwyższą kontrolę.</span><span class="sxs-lookup"><span data-stu-id="48ba0-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="48ba0-172">Atrybut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) stanowi alternatywę dla globalnego stosowania usługi CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="48ba0-173">Atrybut `[EnableCors]` umożliwia CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych:</span><span class="sxs-lookup"><span data-stu-id="48ba0-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="48ba0-174">`[EnableCors]`określa domyślną zasadę.</span><span class="sxs-lookup"><span data-stu-id="48ba0-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="48ba0-175">`[EnableCors("{Policy String}")]`określa nazwaną zasadę.</span><span class="sxs-lookup"><span data-stu-id="48ba0-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="48ba0-176">Atrybut `[EnableCors]` można zastosować do:</span><span class="sxs-lookup"><span data-stu-id="48ba0-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="48ba0-177">Strona Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="48ba0-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="48ba0-178">Kontrolera</span><span class="sxs-lookup"><span data-stu-id="48ba0-178">Controller</span></span>
* <span data-ttu-id="48ba0-179">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="48ba0-179">Controller action method</span></span>

<span data-ttu-id="48ba0-180">Różne zasady mogą być stosowane do kontrolerów, modeli `[EnableCors]` stron lub metod akcji z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="48ba0-181">Gdy `[EnableCors]` atrybut jest stosowany do kontrolera, modelu strony lub metody akcji, a cors jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane.</span><span class="sxs-lookup"><span data-stu-id="48ba0-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="48ba0-182">***Zalecamy, aby nie łączyć zasad. Użyj atrybutu*** `[EnableCors]` ***lub oprogramowania pośredniczącego, a nie w tej samej aplikacji.***</span><span class="sxs-lookup"><span data-stu-id="48ba0-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="48ba0-183">Poniższy kod stosuje inną zasadę do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="48ba0-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="48ba0-184">Poniższy kod tworzy dwie zasady CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="48ba0-185">Aby uzyskać najlepszą kontrolę ograniczania żądań CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="48ba0-186">Użyj `[EnableCors("MyPolicy")]` z nazwanymi zasadami.</span><span class="sxs-lookup"><span data-stu-id="48ba0-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="48ba0-187">Nie definiuj zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="48ba0-187">Don't define a default policy.</span></span>
* <span data-ttu-id="48ba0-188">Nie używaj [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="48ba0-189">Kod w następnej sekcji spełnia poprzednią listę.</span><span class="sxs-lookup"><span data-stu-id="48ba0-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="48ba0-190">Zobacz [Test CORS](#testc) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="48ba0-191">Wyłącz cors</span><span class="sxs-lookup"><span data-stu-id="48ba0-191">Disable CORS</span></span>

<span data-ttu-id="48ba0-192">Atrybut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***nie*** wyłącza mechanizmu CORS, który został włączony przez [routing punktu końcowego](#ecors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="48ba0-193">Poniższy kod definiuje zasady `"MyPolicy"`CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="48ba0-194">Następujący kod wyłącza cors `GetValues2` dla akcji:</span><span class="sxs-lookup"><span data-stu-id="48ba0-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="48ba0-195">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-195">The preceding code:</span></span>

* <span data-ttu-id="48ba0-196">Nie włącza mechanizmu CORS z [routingiem punktów końcowych.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="48ba0-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="48ba0-197">Nie definiuje [domyślnej zasady CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="48ba0-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="48ba0-198">Używa [funkcji [EnableCors("MyPolicy")],](#attr) `"MyPolicy"` aby włączyć zasady CORS dla kontrolera.</span><span class="sxs-lookup"><span data-stu-id="48ba0-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="48ba0-199">Wyłącza CORS `GetValues2` dla metody.</span><span class="sxs-lookup"><span data-stu-id="48ba0-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="48ba0-200">Zobacz [Test CORS](#testc) instrukcje dotyczące testowania poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="48ba0-201">Warianty polityki CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-201">CORS policy options</span></span>

<span data-ttu-id="48ba0-202">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="48ba0-203">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="48ba0-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="48ba0-204">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="48ba0-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="48ba0-205">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="48ba0-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="48ba0-206">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="48ba0-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="48ba0-207">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="48ba0-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="48ba0-208">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="48ba0-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48ba0-210">W przypadku niektórych opcji warto najpierw przeczytać sekcję [Jak działa CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="48ba0-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="48ba0-211">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="48ba0-211">Set the allowed origins</span></span>

<span data-ttu-id="48ba0-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umożliwia żądania CORS ze wszystkich źródeł`http` `https`z dowolnym schematem ( lub ).</span><span class="sxs-lookup"><span data-stu-id="48ba0-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="48ba0-213">`AllowAnyOrigin`jest niezabezpieczona, ponieważ *każda witryna sieci Web* może składać żądania cross-origin do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="48ba0-214">Określanie `AllowAnyOrigin` `AllowCredentials` i jest niezabezpieczona konfiguracja i może spowodować fałszerstwa żądań między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="48ba0-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="48ba0-215">Usługa CORS zwraca nieprawidłową odpowiedź CORS, gdy aplikacja jest skonfigurowana przy obu metodach.</span><span class="sxs-lookup"><span data-stu-id="48ba0-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="48ba0-216">`AllowAnyOrigin`wpływa na żądania inspekcji `Access-Control-Allow-Origin` wstępnej i nagłówek.</span><span class="sxs-lookup"><span data-stu-id="48ba0-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="48ba0-217">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="48ba0-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="48ba0-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> właściwość zasad jako funkcję, która umożliwia początek, aby dopasować skonfigurowaną domenę symboli wieloznacznych podczas oceny, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="48ba0-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="48ba0-219">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="48ba0-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="48ba0-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="48ba0-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="48ba0-221">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="48ba0-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="48ba0-222">Wpływa na żądania inspekcji `Access-Control-Allow-Methods` wstępnej i nagłówka.</span><span class="sxs-lookup"><span data-stu-id="48ba0-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="48ba0-223">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="48ba0-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="48ba0-224">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="48ba0-224">Set the allowed request headers</span></span>

<span data-ttu-id="48ba0-225">Aby zezwolić na wysyłane określone nagłówki w żądaniu CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> nazywane [nagłówkami żądań autora](https://xhr.spec.whatwg.org/#request), zadzwoń i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="48ba0-226">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [nagłówki żądań autora,](https://www.w3.org/TR/cors/#author-request-headers)zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="48ba0-227">`AllowAnyHeader`wpływa na żądania inspekcji wstępnej i nagłówka [nagłówków żądania kontroli dostępu.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="48ba0-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="48ba0-228">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="48ba0-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="48ba0-229">Zasady oprogramowania pośredniczącego CORS są `WithHeaders` zgodne z określonymi nagłówkami określonymi przez jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie odpowiadają nagłówkom podanym w pliku `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="48ba0-230">Rozważmy na przykład aplikację skonfigurowaną w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="48ba0-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="48ba0-231">Oprogramowanie pośredniczące cors odrzuca żądanie inspekcji wstępnej `Content-Language` z następującym nagłówkiem żądania, ponieważ ( `WithHeaders`[HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nie jest wymieniony w :</span><span class="sxs-lookup"><span data-stu-id="48ba0-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="48ba0-232">Aplikacja zwraca odpowiedź *200 OK,* ale nie wysyła nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="48ba0-233">W związku z tym przeglądarka nie próbuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="48ba0-234">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="48ba0-234">Set the exposed response headers</span></span>

<span data-ttu-id="48ba0-235">Domyślnie przeglądarka nie udostępnia wszystkie nagłówki odpowiedzi do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="48ba0-236">Aby uzyskać więcej informacji, zobacz [Udostępnianie zasobów W3C Cross-Origin (Terminologia): Nagłówek prostej odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="48ba0-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="48ba0-237">Nagłówki odpowiedzi, które są domyślnie dostępne, to:</span><span class="sxs-lookup"><span data-stu-id="48ba0-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="48ba0-238">Specyfikacja CORS wywołuje te nagłówki *proste nagłówki odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="48ba0-239">Aby udostępnić aplikacji inne nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>zadzwoń do:</span><span class="sxs-lookup"><span data-stu-id="48ba0-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="48ba0-240">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="48ba0-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="48ba0-241">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="48ba0-242">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="48ba0-243">Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP.</span><span class="sxs-lookup"><span data-stu-id="48ba0-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="48ba0-244">Aby wysłać poświadczenia z żądaniem cross-origin, klient musi ustawić `XMLHttpRequest.withCredentials` na `true`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="48ba0-245">Korzystanie `XMLHttpRequest` bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="48ba0-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="48ba0-246">Korzystanie z jQuery:</span><span class="sxs-lookup"><span data-stu-id="48ba0-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="48ba0-247">Korzystanie z [interfejsu API pobierania:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="48ba0-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="48ba0-248">Serwer musi zezwolić na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="48ba0-248">The server must allow the credentials.</span></span> <span data-ttu-id="48ba0-249">Aby zezwolić na poświadczenia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>między źródłami, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="48ba0-250">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="48ba0-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="48ba0-251">Jeśli przeglądarka wysyła poświadczenia, ale odpowiedź nie `Access-Control-Allow-Credentials` zawiera prawidłowego nagłówka, przeglądarka nie udostępnia odpowiedzi na aplikację, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="48ba0-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="48ba0-252">Zezwalanie na poświadczenia między źródłami jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="48ba0-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="48ba0-253">Witryna sieci Web w innej domenie może wysyłać poświadczeń zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="48ba0-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="48ba0-254">Specyfikacja CORS stwierdza również, `"*"` że ustawienie początku (wszystkie `Access-Control-Allow-Credentials` źródła) jest nieprawidłowe, jeśli nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="48ba0-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="48ba0-255">Żądania inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-255">Preflight requests</span></span>

<span data-ttu-id="48ba0-256">W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie [opcji](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) przed wysłaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="48ba0-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="48ba0-257">To żądanie jest nazywane [żądaniem inspekcji wstępnej](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="48ba0-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="48ba0-258">Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są ***wszystkie*** następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="48ba0-259">Metoda żądania to GET, HEAD lub POST.</span><span class="sxs-lookup"><span data-stu-id="48ba0-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="48ba0-260">Aplikacja nie ustawia nagłówków żądań `Accept` `Accept-Language`innych `Content-Language` `Content-Type`niż `Last-Event-ID`, , , lub .</span><span class="sxs-lookup"><span data-stu-id="48ba0-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="48ba0-261">Nagłówek, `Content-Type` jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="48ba0-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="48ba0-262">Reguła nagłówków żądań ustawiona dla żądania klienta ma zastosowanie `setRequestHeader` do `XMLHttpRequest` nagłówków ustawianych przez aplikację, wywołując obiekt.</span><span class="sxs-lookup"><span data-stu-id="48ba0-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="48ba0-263">Specyfikacja CORS wywołuje te nagłówki [nagłówków żądania autora nagłówków](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="48ba0-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="48ba0-264">Reguła nie ma zastosowania do nagłówków, które `User-Agent` `Host`przeglądarka `Content-Length`może ustawić, takich jak , lub .</span><span class="sxs-lookup"><span data-stu-id="48ba0-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="48ba0-265">Poniżej przedstawiono przykładową odpowiedź podobną do żądania inspekcji wstępnej złożonego z przycisku **[Put test]** w sekcji [Test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="48ba0-266">Żądanie inspekcji wstępnej używa metody [OPCJE HTTP.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="48ba0-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="48ba0-267">Może zawierać następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-267">It may include the following headers:</span></span>

* <span data-ttu-id="48ba0-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda HTTP, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="48ba0-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="48ba0-269">[Nagłówki żądania kontroli dostępu:](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)lista nagłówków żądań ustawianych przez aplikację w rzeczywistym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="48ba0-270">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które `User-Agent`ustawia przeglądarka, takich jak .</span><span class="sxs-lookup"><span data-stu-id="48ba0-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="48ba0-271">Metody dostępu-kontrola-zezwalaj</span><span class="sxs-lookup"><span data-stu-id="48ba0-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="48ba0-272">Jeśli żądanie inspekcji wstępnej zostanie odrzucone, `200 OK` aplikacja zwraca odpowiedź, ale nie ustawia nagłówków CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="48ba0-273">W związku z tym przeglądarka nie próbuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="48ba0-274">Na przykład odmowy żądania inspekcji wstępnej, zobacz [sekcji Test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="48ba0-275">Za pomocą narzędzi F12 aplikacja konsoli wyświetla błąd podobny do jednego z następujących, w zależności od przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="48ba0-276">Firefox: Żądanie cross-origin zablokowane: Ta sama polityka pochodzenia nie `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`zezwala na odczytanie zdalnego zasobu w .</span><span class="sxs-lookup"><span data-stu-id="48ba0-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="48ba0-277">(Powód: żądanie CORS nie powiodło się).</span><span class="sxs-lookup"><span data-stu-id="48ba0-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="48ba0-278">Dowiedz się więcej</span><span class="sxs-lookup"><span data-stu-id="48ba0-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="48ba0-279">Chromium na podstawie: Dostęphttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5do pobraniahttps://cors3.azurewebsites.netw ' od początku ' ' został zablokowany przez zasady CORS: Odpowiedź na żądanie inspekcji wstępnej nie przekazuje kontroli dostępu: Nie ma nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="48ba0-280">Jeśli nieprzezroczysta odpowiedź służy Twoim potrzebom, ustaw tryb żądania na "no-cors", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="48ba0-281">Aby zezwolić na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>określone nagłówki, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="48ba0-282">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [nagłówki żądań autora,](https://www.w3.org/TR/cors/#author-request-headers)zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="48ba0-283">Przeglądarki nie są spójne w `Access-Control-Request-Headers`sposobie ustawiania .</span><span class="sxs-lookup"><span data-stu-id="48ba0-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="48ba0-284">Jeśli:</span><span class="sxs-lookup"><span data-stu-id="48ba0-284">If either:</span></span>

* <span data-ttu-id="48ba0-285">Nagłówki są ustawione na coś innego niż`"*"`</span><span class="sxs-lookup"><span data-stu-id="48ba0-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="48ba0-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jest wywoływana: `Accept`Dołącz `Content-Type`co `Origin`najmniej , i , plus wszystkie niestandardowe nagłówki, które chcesz obsługiwać.</span><span class="sxs-lookup"><span data-stu-id="48ba0-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="48ba0-287">Automatyczny kod żądania inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-287">Automatic preflight request code</span></span>

<span data-ttu-id="48ba0-288">Po zastosowaniu zasad CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="48ba0-289">Globalnie, `app.UseCors` `Startup.Configure`wywołując w .</span><span class="sxs-lookup"><span data-stu-id="48ba0-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="48ba0-290">Przy `[EnableCors]` użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="48ba0-291">ASP.NET Core odpowiada na żądanie opcji inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="48ba0-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="48ba0-292">Włączenie mechanizmu CORS na podstawie `RequireCors` punktu końcowego przy użyciu obecnie ***nie*** obsługuje automatycznych żądań inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="48ba0-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="48ba0-293">Sekcja [Test CORS](#testc) tego dokumentu demonstruje to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="48ba0-294">[HttpOptions] atrybut dla żądań inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="48ba0-295">Gdy mechanizm CORS jest włączony z odpowiednią zasadą, ASP.NET Core zazwyczaj automatycznie odpowiada na żądania inspekcji wstępnej CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="48ba0-296">W niektórych scenariuszach może to nie być przypadek.</span><span class="sxs-lookup"><span data-stu-id="48ba0-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="48ba0-297">Na przykład przy użyciu [mechanizmu CORS z routingiem punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="48ba0-298">Poniższy kod używa atrybutu [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) do tworzenia punktów końcowych dla żądań OPCJI:</span><span class="sxs-lookup"><span data-stu-id="48ba0-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="48ba0-299">Zobacz [Test CORS z routingu punktu końcowego i [HttpOptions]](#tcer) instrukcje dotyczące testowania poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="48ba0-300">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-300">Set the preflight expiration time</span></span>

<span data-ttu-id="48ba0-301">Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="48ba0-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="48ba0-302">Aby ustawić ten <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>nagłówek, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="48ba0-303">Jak działa CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-303">How CORS works</span></span>

<span data-ttu-id="48ba0-304">W tej sekcji opisano, co dzieje się w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie wiadomości HTTP.</span><span class="sxs-lookup"><span data-stu-id="48ba0-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="48ba0-305">CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="48ba0-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="48ba0-306">CORS to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="48ba0-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="48ba0-307">Na przykład złośliwy aktor może użyć [skryptów krzyżowych (XSS)](xref:security/cross-site-scripting) przeciwko witrynie i wykonać żądanie między witrynami do witryny obsługującej mechanizm CORS, aby wykraść informacje.</span><span class="sxs-lookup"><span data-stu-id="48ba0-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="48ba0-308">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="48ba0-309">To do klienta (przeglądarki), aby wymusić CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="48ba0-310">Serwer wykonuje żądanie i zwraca odpowiedź, to klient zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="48ba0-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="48ba0-311">Na przykład dowolne z następujących narzędzi wyświetli odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="48ba0-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="48ba0-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="48ba0-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="48ba0-313">Postman</span><span class="sxs-lookup"><span data-stu-id="48ba0-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="48ba0-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="48ba0-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="48ba0-315">Przeglądarka internetowa, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="48ba0-316">Jest to sposób dla serwera, aby umożliwić przeglądarkom wykonać żądanie [XHR xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub [fetch api,](https://developer.mozilla.org/docs/Web/API/Fetch_API) które w przeciwnym razie byłyby zabronione.</span><span class="sxs-lookup"><span data-stu-id="48ba0-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="48ba0-317">Przeglądarki bez CORS nie mogą wykonywać żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="48ba0-318">Przed [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) był używany do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="48ba0-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="48ba0-319">JSONP nie używa XHR, używa `<script>` tagu do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="48ba0-320">Skrypty mogą być ładowane cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="48ba0-321">Specyfikacja [CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="48ba0-322">Jeśli przeglądarka obsługuje cors, ustawia te nagłówki automatycznie dla żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="48ba0-323">Niestandardowy kod JavaScript nie jest wymagany do włączenia usługi CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="48ba0-324">[Przycisk testu PUT](https://cors3.azurewebsites.net/test) na wdrożonym [przykładzie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="48ba0-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="48ba0-325">Poniżej przedstawiono przykład żądania cross-origin z przycisku testu [wartości](https://cors3.azurewebsites.net/) do `https://cors1.azurewebsites.net/api/values`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="48ba0-326">Nagłówek: `Origin`</span><span class="sxs-lookup"><span data-stu-id="48ba0-326">The `Origin` header:</span></span>

* <span data-ttu-id="48ba0-327">Udostępnia domenę witryny, która tworzy żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="48ba0-328">Jest to wymagane i musi być inny niż host.</span><span class="sxs-lookup"><span data-stu-id="48ba0-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="48ba0-329">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="48ba0-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="48ba0-330">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="48ba0-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="48ba0-331">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="48ba0-331">**Request headers**</span></span>

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

<span data-ttu-id="48ba0-332">W `OPTIONS` żądaniach serwer ustawia nagłówek **nagłówków** `Access-Control-Allow-Origin: {allowed origin}` odpowiedzi w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="48ba0-333">Na przykład wdrożone [przykładowe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)żądanie przycisku `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) zawiera następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="48ba0-334">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="48ba0-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="48ba0-335">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="48ba0-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="48ba0-336">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="48ba0-336">**Request headers**</span></span>

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

<span data-ttu-id="48ba0-337">W poprzednich **nagłówkach odpowiedzi**serwer ustawia nagłówek [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="48ba0-338">Wartość `https://cors1.azurewebsites.net` tego nagłówka `Origin` pasuje do nagłówka z żądania.</span><span class="sxs-lookup"><span data-stu-id="48ba0-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="48ba0-339">Jeśli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> jest wywoływana, `Access-Control-Allow-Origin: *`zwracana jest wartość symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="48ba0-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="48ba0-340">`AllowAnyOrigin`dopuszcza dowolne pochodzenie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="48ba0-341">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="48ba0-342">W szczególności przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="48ba0-343">Nawet jeśli serwer zwraca pomyślną odpowiedź, przeglądarka nie udostępnia odpowiedzi do aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="48ba0-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="48ba0-344">Wyświetl żądania OPCJI</span><span class="sxs-lookup"><span data-stu-id="48ba0-344">Display OPTIONS requests</span></span>

<span data-ttu-id="48ba0-345">Domyślnie przeglądarki Chrome i Edge nie wyświetla żądań OPCJI na karcie sieciowe narzędzi F12.</span><span class="sxs-lookup"><span data-stu-id="48ba0-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="48ba0-346">Aby wyświetlić żądania opcji w tych przeglądarkach:</span><span class="sxs-lookup"><span data-stu-id="48ba0-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="48ba0-347">`chrome://flags/#out-of-blink-cors` lub `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="48ba0-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="48ba0-348">wyłączyć flagę.</span><span class="sxs-lookup"><span data-stu-id="48ba0-348">disable the flag.</span></span>
* <span data-ttu-id="48ba0-349">Ponownie uruchomić.</span><span class="sxs-lookup"><span data-stu-id="48ba0-349">restart.</span></span>

<span data-ttu-id="48ba0-350">Firefox domyślnie wyświetla żądania opcji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="48ba0-351">CORS w iis</span><span class="sxs-lookup"><span data-stu-id="48ba0-351">CORS in IIS</span></span>

<span data-ttu-id="48ba0-352">Podczas wdrażania w usługach IIS mechanizm CORS musi być uruchomiony przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="48ba0-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="48ba0-353">Aby obsługiwać ten scenariusz, [moduł IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) musi zostać zainstalowany i skonfigurowany dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="48ba0-354">Badanie CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-354">Test CORS</span></span>

<span data-ttu-id="48ba0-355">[Przykładowe pobieranie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ma kod do testowania CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="48ba0-356">Zobacz, [jak pobrać](xref:index#how-to-download-a-sample)plik .</span><span class="sxs-lookup"><span data-stu-id="48ba0-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="48ba0-357">Przykład jest projektem INTERFEJSU API z dodanymi stronami Razor:</span><span class="sxs-lookup"><span data-stu-id="48ba0-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="48ba0-358">`WithOrigins("https://localhost:<port>");`należy używać tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="48ba0-359">Poniżej `ValuesController` przedstawiono punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="48ba0-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="48ba0-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) jest dostarczany przez [pakiet Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet i wyświetla informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="48ba0-361">Przetestuj poprzedni przykładowy kod przy użyciu jednego z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="48ba0-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="48ba0-362">Użyj wdrożonej przykładowej [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)aplikacji w pliku .</span><span class="sxs-lookup"><span data-stu-id="48ba0-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="48ba0-363">Nie ma potrzeby pobierania próbki.</span><span class="sxs-lookup"><span data-stu-id="48ba0-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="48ba0-364">Uruchom próbkę `dotnet run` przy użyciu `https://localhost:5001`domyślnego adresu URL programu .</span><span class="sxs-lookup"><span data-stu-id="48ba0-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="48ba0-365">Uruchom przykład z programu Visual Studio z portem ustawionym na `https://localhost:44398`44398 dla adresu URL .</span><span class="sxs-lookup"><span data-stu-id="48ba0-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="48ba0-366">Korzystanie z przeglądarki z narzędziami F12:</span><span class="sxs-lookup"><span data-stu-id="48ba0-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="48ba0-367">Wybierz przycisk **Wartości** i przejrzyj nagłówki na karcie **Sieć.**</span><span class="sxs-lookup"><span data-stu-id="48ba0-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="48ba0-368">Wybierz przycisk **testu PUT.**</span><span class="sxs-lookup"><span data-stu-id="48ba0-368">Select the **PUT test** button.</span></span> <span data-ttu-id="48ba0-369">Zobacz [Wyświetlanie opcji opcji, aby](#options) uzyskać instrukcje dotyczące wyświetlania żądania OPCJE.</span><span class="sxs-lookup"><span data-stu-id="48ba0-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="48ba0-370">**Test PUT** tworzy dwa żądania, żądanie inspekcji wstępnej opcje i żądanie PUT.</span><span class="sxs-lookup"><span data-stu-id="48ba0-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="48ba0-371">Wybierz **`GetValues2 [DisableCors]`** przycisk, aby wyzwolić nieudane żądanie CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="48ba0-372">Jak wspomniano w dokumencie, odpowiedź zwraca 200 sukcesów, ale żądanie CORS nie jest.</span><span class="sxs-lookup"><span data-stu-id="48ba0-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="48ba0-373">Wybierz kartę **Konsola,** aby wyświetlić błąd CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="48ba0-374">W zależności od przeglądarki wyświetlany jest błąd podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="48ba0-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="48ba0-375">Dostęp do pobierania `'https://cors1.azurewebsites.net/api/values/GetValues2'` od `'https://cors3.azurewebsites.net'` źródła został zablokowany przez zasady CORS: W żądanym zasobie nie ma nagłówka "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="48ba0-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="48ba0-376">Jeśli nieprzezroczysta odpowiedź służy Twoim potrzebom, ustaw tryb żądania na "no-cors", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="48ba0-377">Punkty końcowe obsługujące mechanizm CORS można testować za pomocą narzędzia, takiego jak [curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)lub [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="48ba0-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="48ba0-378">W przypadku korzystania z narzędzia pochodzenie żądania `Origin` określonego przez nagłówek musi się różnić od hosta odbierającego żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="48ba0-379">Jeśli żądanie nie jest *cross-origin* na podstawie `Origin` wartości nagłówka:</span><span class="sxs-lookup"><span data-stu-id="48ba0-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="48ba0-380">Nie ma potrzeby, aby oprogramowanie pośredniczące CORS przetwarzać żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="48ba0-381">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="48ba0-382">Następujące polecenie używa `curl` do wystawić żądanie OPTIONS z informacjami:</span><span class="sxs-lookup"><span data-stu-id="48ba0-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="48ba0-383">Testowanie cors z routingu punktu końcowego i [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="48ba0-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="48ba0-384">Włączenie mechanizmu CORS dla punktu `RequireCors` końcowego przy użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="48ba0-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="48ba0-385">Należy wziąć pod uwagę następujący kod, który używa [routingu punktu końcowego, aby włączyć CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="48ba0-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="48ba0-386">Poniżej `TodoItems1Controller` przedstawiono punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="48ba0-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="48ba0-387">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=1) wdrożonego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="48ba0-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="48ba0-388">**Przyciski Delete [EnableCors]** i **GET [EnableCors]** powiodą się, ponieważ punkty końcowe mają `[EnableCors]` żądania inspekcji wstępnej i odpowiadają na nie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="48ba0-389">Inne punkty końcowe kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-389">The other endpoints fails.</span></span> <span data-ttu-id="48ba0-390">Przycisk **GET** kończy się niepowodzeniem, ponieważ [javascript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) wysyła:</span><span class="sxs-lookup"><span data-stu-id="48ba0-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="48ba0-391">Poniżej `TodoItems2Controller` przedstawiono podobne punkty końcowe, ale zawiera jawny kod, aby odpowiedzieć na żądania OPCJI:</span><span class="sxs-lookup"><span data-stu-id="48ba0-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="48ba0-392">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=2) wdrożonego przykładu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="48ba0-393">Na liście rozwijanej **Kontroler** wybierz pozycję **Inspekcji wstępnej,** a następnie **ustaw kontroler**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="48ba0-394">Wszystkie wywołania CORS `TodoItems2Controller` do punktów końcowych zakończyć się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48ba0-395">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="48ba0-395">Additional resources</span></span>

* [<span data-ttu-id="48ba0-396">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="48ba0-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="48ba0-397">Wprowadzenie do modułu IIS CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48ba0-398">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48ba0-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="48ba0-399">W tym artykule pokazano, jak włączyć usługę CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="48ba0-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="48ba0-400">Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="48ba0-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="48ba0-401">To ograniczenie jest nazywane *zasadami tego samego pochodzenia*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="48ba0-402">Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny.</span><span class="sxs-lookup"><span data-stu-id="48ba0-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="48ba0-403">Czasami możesz zezwolić innym witrynom na składanie żądań krzyżowych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="48ba0-404">Aby uzyskać więcej informacji, zobacz [artykuł Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="48ba0-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="48ba0-405">[Udostępnianie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="48ba0-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="48ba0-406">Jest standardem W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="48ba0-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="48ba0-407">**Nie** jest to funkcja zabezpieczeń, CORS rozluźnia zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="48ba0-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="48ba0-408">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="48ba0-409">Aby uzyskać więcej informacji, zobacz [Jak działa CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="48ba0-410">Umożliwia serwerowi jawne zezwalanie na niektóre żądania między źródłami, jednocześnie odrzucając inne.</span><span class="sxs-lookup"><span data-stu-id="48ba0-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="48ba0-411">Jest bezpieczniejsza i bardziej elastyczna niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="48ba0-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="48ba0-412">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48ba0-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="48ba0-413">To samo pochodzenie</span><span class="sxs-lookup"><span data-stu-id="48ba0-413">Same origin</span></span>

<span data-ttu-id="48ba0-414">Dwa adresy URL mają to samo pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454).](https://tools.ietf.org/html/rfc6454)</span><span class="sxs-lookup"><span data-stu-id="48ba0-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="48ba0-415">Te dwa adresy URL mają to samo źródło:</span><span class="sxs-lookup"><span data-stu-id="48ba0-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="48ba0-416">Te adresy URL mają inne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="48ba0-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="48ba0-417">`https://example.net`&ndash; Inna domena</span><span class="sxs-lookup"><span data-stu-id="48ba0-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="48ba0-418">`https://www.example.com/foo.html`&ndash; Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="48ba0-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="48ba0-419">`http://example.com/foo.html`&ndash; Inny schemat</span><span class="sxs-lookup"><span data-stu-id="48ba0-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="48ba0-420">`https://example.com:9000/foo.html`&ndash; Inny port</span><span class="sxs-lookup"><span data-stu-id="48ba0-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="48ba0-421">Program Internet Explorer nie bierze pod uwagę portu podczas porównywania źródeł.</span><span class="sxs-lookup"><span data-stu-id="48ba0-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="48ba0-422">CORS z nazwanymi zasadami i oprogramowaniem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="48ba0-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="48ba0-423">Oprogramowanie pośredniczące CORS obsługuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="48ba0-424">Poniższy kod umożliwia CORS dla całej aplikacji z określonym źródłem:</span><span class="sxs-lookup"><span data-stu-id="48ba0-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="48ba0-425">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-425">The preceding code:</span></span>

* <span data-ttu-id="48ba0-426">Ustawia nazwę zasad\_na "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="48ba0-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="48ba0-427">Nazwa zasad jest dowolna.</span><span class="sxs-lookup"><span data-stu-id="48ba0-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="48ba0-428">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia, która umożliwia CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="48ba0-429">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="48ba0-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="48ba0-430">Lambda bierze <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="48ba0-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="48ba0-431">[Opcje konfiguracji](#cors-policy-options), `WithOrigins`takie jak , są opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="48ba0-432"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="48ba0-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="48ba0-433">Aby uzyskać więcej informacji, zobacz [opcje zasad CORS](#cpo) w tym dokumencie .</span><span class="sxs-lookup"><span data-stu-id="48ba0-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="48ba0-434">Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> może łańcuch metod, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="48ba0-435">Uwaga: Adres URL **nie** może zawierać`/`końcowego ukośnika ( ).</span><span class="sxs-lookup"><span data-stu-id="48ba0-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="48ba0-436">Jeśli adres URL `/`zostanie zakończony `false` na , porównanie zwraca się i nie zwracany jest żaden nagłówek.</span><span class="sxs-lookup"><span data-stu-id="48ba0-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="48ba0-437">Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji za pośrednictwem oprogramowania pośredniczącego CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="48ba0-438">Uwaga: `UseCors` należy wywołać przed `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="48ba0-439">Zobacz [Włączanie cors w razor strony, kontrolery i metody działania,](#ecors) aby zastosować zasady CORS na poziomie strony/kontrolera / akcji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="48ba0-440">Zobacz [Test CORS](#test) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="48ba0-441">Włącz cors z atrybutami</span><span class="sxs-lookup"><span data-stu-id="48ba0-441">Enable CORS with attributes</span></span>

<span data-ttu-id="48ba0-442">[EnableCors&rbrack; Atrybut stanowi alternatywę dla stosowania CORS globalnie. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="48ba0-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="48ba0-443">Atrybut `[EnableCors]` umożliwia CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="48ba0-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="48ba0-444">Służy `[EnableCors]` do określania `[EnableCors("{Policy String}")]` zasad domyślnych i określania zasad.</span><span class="sxs-lookup"><span data-stu-id="48ba0-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="48ba0-445">Atrybut `[EnableCors]` można zastosować do:</span><span class="sxs-lookup"><span data-stu-id="48ba0-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="48ba0-446">Strona Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="48ba0-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="48ba0-447">Kontrolera</span><span class="sxs-lookup"><span data-stu-id="48ba0-447">Controller</span></span>
* <span data-ttu-id="48ba0-448">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="48ba0-448">Controller action method</span></span>

<span data-ttu-id="48ba0-449">Można zastosować różne zasady do kontrolera/modelu `[EnableCors]` strony/akcji z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="48ba0-450">Gdy `[EnableCors]` atrybut jest stosowany do controllers/page model/action method, a CORS jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane.</span><span class="sxs-lookup"><span data-stu-id="48ba0-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="48ba0-451">Nie ***zalecamy*** łączenia zasad.</span><span class="sxs-lookup"><span data-stu-id="48ba0-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="48ba0-452">Użyj `[EnableCors]` atrybutu lub oprogramowania pośredniczącego, \***nie oba**.</span><span class="sxs-lookup"><span data-stu-id="48ba0-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="48ba0-453">Podczas `[EnableCors]`korzystania z programu **nie** należy definiować zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="48ba0-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="48ba0-454">Poniższy kod stosuje inną zasadę do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="48ba0-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="48ba0-455">Poniższy kod tworzy domyślną zasadę `"AnotherPolicy"`CORS i zasadę o nazwie:</span><span class="sxs-lookup"><span data-stu-id="48ba0-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="48ba0-456">Wyłącz cors</span><span class="sxs-lookup"><span data-stu-id="48ba0-456">Disable CORS</span></span>

<span data-ttu-id="48ba0-457">Atrybut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) wyłącza cors dla kontrolera/page-model/action.</span><span class="sxs-lookup"><span data-stu-id="48ba0-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="48ba0-458">Warianty polityki CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-458">CORS policy options</span></span>

<span data-ttu-id="48ba0-459">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="48ba0-460">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="48ba0-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="48ba0-461">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="48ba0-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="48ba0-462">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="48ba0-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="48ba0-463">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="48ba0-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="48ba0-464">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="48ba0-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="48ba0-465">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="48ba0-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48ba0-467">W przypadku niektórych opcji warto najpierw przeczytać sekcję [Jak działa CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="48ba0-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="48ba0-468">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="48ba0-468">Set the allowed origins</span></span>

<span data-ttu-id="48ba0-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umożliwia żądania CORS ze wszystkich źródeł`http` `https`z dowolnym schematem ( lub ).</span><span class="sxs-lookup"><span data-stu-id="48ba0-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="48ba0-470">`AllowAnyOrigin`jest niezabezpieczona, ponieważ *każda witryna sieci Web* może składać żądania cross-origin do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="48ba0-471">Określanie `AllowAnyOrigin` `AllowCredentials` i jest niezabezpieczona konfiguracja i może spowodować fałszerstwa żądań między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="48ba0-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="48ba0-472">W przypadku bezpiecznej aplikacji określ dokładną listę źródeł, jeśli klient musi autoryzować się, aby uzyskać dostęp do zasobów serwera.</span><span class="sxs-lookup"><span data-stu-id="48ba0-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="48ba0-473">`AllowAnyOrigin`wpływa na żądania inspekcji `Access-Control-Allow-Origin` wstępnej i nagłówek.</span><span class="sxs-lookup"><span data-stu-id="48ba0-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="48ba0-474">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="48ba0-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="48ba0-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> właściwość zasad jako funkcję, która umożliwia początek, aby dopasować skonfigurowaną domenę symboli wieloznacznych podczas oceny, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="48ba0-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="48ba0-476">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="48ba0-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="48ba0-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="48ba0-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="48ba0-478">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="48ba0-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="48ba0-479">Wpływa na żądania inspekcji `Access-Control-Allow-Methods` wstępnej i nagłówka.</span><span class="sxs-lookup"><span data-stu-id="48ba0-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="48ba0-480">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="48ba0-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="48ba0-481">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="48ba0-481">Set the allowed request headers</span></span>

<span data-ttu-id="48ba0-482">Aby zezwolić na wysyłane określone nagłówki w żądaniu CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> nazywane *nagłówkami żądań autora*, zadzwoń i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="48ba0-483">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>nagłówki żądań autora, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="48ba0-484">To ustawienie ma wpływ na `Access-Control-Request-Headers` żądania inspekcji wstępnej i nagłówek.</span><span class="sxs-lookup"><span data-stu-id="48ba0-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="48ba0-485">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="48ba0-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="48ba0-486">Oprogramowanie pośredniczące CORS zawsze `Access-Control-Request-Headers` umożliwia cztery nagłówki w celu wysłania niezależnie od wartości skonfigurowanych w CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="48ba0-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="48ba0-487">Ta lista nagłówków zawiera:</span><span class="sxs-lookup"><span data-stu-id="48ba0-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="48ba0-488">Rozważmy na przykład aplikację skonfigurowaną w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="48ba0-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="48ba0-489">Oprogramowanie pośredniczące cors odpowiada pomyślnie na żądanie inspekcji `Content-Language` wstępnej z następującym nagłówkiem żądania, ponieważ jest zawsze na białej liście:</span><span class="sxs-lookup"><span data-stu-id="48ba0-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="48ba0-490">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="48ba0-490">Set the exposed response headers</span></span>

<span data-ttu-id="48ba0-491">Domyślnie przeglądarka nie udostępnia wszystkie nagłówki odpowiedzi do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="48ba0-492">Aby uzyskać więcej informacji, zobacz [Udostępnianie zasobów W3C Cross-Origin (Terminologia): Nagłówek prostej odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="48ba0-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="48ba0-493">Nagłówki odpowiedzi, które są domyślnie dostępne, to:</span><span class="sxs-lookup"><span data-stu-id="48ba0-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="48ba0-494">Specyfikacja CORS wywołuje te nagłówki *proste nagłówki odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="48ba0-495">Aby udostępnić aplikacji inne nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>zadzwoń do:</span><span class="sxs-lookup"><span data-stu-id="48ba0-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="48ba0-496">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="48ba0-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="48ba0-497">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="48ba0-498">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="48ba0-499">Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP.</span><span class="sxs-lookup"><span data-stu-id="48ba0-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="48ba0-500">Aby wysłać poświadczenia z żądaniem cross-origin, klient musi ustawić `XMLHttpRequest.withCredentials` na `true`.</span><span class="sxs-lookup"><span data-stu-id="48ba0-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="48ba0-501">Korzystanie `XMLHttpRequest` bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="48ba0-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="48ba0-502">Korzystanie z jQuery:</span><span class="sxs-lookup"><span data-stu-id="48ba0-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="48ba0-503">Korzystanie z [interfejsu API pobierania:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="48ba0-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="48ba0-504">Serwer musi zezwolić na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="48ba0-504">The server must allow the credentials.</span></span> <span data-ttu-id="48ba0-505">Aby zezwolić na poświadczenia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>między źródłami, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="48ba0-506">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="48ba0-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="48ba0-507">Jeśli przeglądarka wysyła poświadczenia, ale odpowiedź nie `Access-Control-Allow-Credentials` zawiera prawidłowego nagłówka, przeglądarka nie udostępnia odpowiedzi na aplikację, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="48ba0-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="48ba0-508">Zezwalanie na poświadczenia między źródłami jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="48ba0-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="48ba0-509">Witryna sieci Web w innej domenie może wysyłać poświadczeń zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="48ba0-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="48ba0-510">Specyfikacja CORS stwierdza również, `"*"` że ustawienie początku (wszystkie `Access-Control-Allow-Credentials` źródła) jest nieprawidłowe, jeśli nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="48ba0-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="48ba0-511">Żądania inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-511">Preflight requests</span></span>

<span data-ttu-id="48ba0-512">W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie przed wysłaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="48ba0-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="48ba0-513">To żądanie jest nazywane *żądaniem inspekcji wstępnej*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="48ba0-514">Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="48ba0-515">Metoda żądania to GET, HEAD lub POST.</span><span class="sxs-lookup"><span data-stu-id="48ba0-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="48ba0-516">Aplikacja nie ustawia nagłówków żądań `Accept` `Accept-Language`innych `Content-Language` `Content-Type`niż `Last-Event-ID`, , , lub .</span><span class="sxs-lookup"><span data-stu-id="48ba0-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="48ba0-517">Nagłówek, `Content-Type` jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="48ba0-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="48ba0-518">Reguła nagłówków żądań ustawiona dla żądania klienta ma zastosowanie `setRequestHeader` do `XMLHttpRequest` nagłówków ustawianych przez aplikację, wywołując obiekt.</span><span class="sxs-lookup"><span data-stu-id="48ba0-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="48ba0-519">Specyfikacja CORS wywołuje te nagłówki *nagłówków żądania autora nagłówków*.</span><span class="sxs-lookup"><span data-stu-id="48ba0-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="48ba0-520">Reguła nie ma zastosowania do nagłówków, które `User-Agent` `Host`przeglądarka `Content-Length`może ustawić, takich jak , lub .</span><span class="sxs-lookup"><span data-stu-id="48ba0-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="48ba0-521">Oto przykład żądania inspekcji wstępnej:</span><span class="sxs-lookup"><span data-stu-id="48ba0-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="48ba0-522">Żądanie przed lotem używa metody OPCJE HTTP.</span><span class="sxs-lookup"><span data-stu-id="48ba0-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="48ba0-523">Zawiera dwa specjalne nagłówki:</span><span class="sxs-lookup"><span data-stu-id="48ba0-523">It includes two special headers:</span></span>

* <span data-ttu-id="48ba0-524">`Access-Control-Request-Method`: Metoda HTTP, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="48ba0-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="48ba0-525">`Access-Control-Request-Headers`: Lista nagłówków żądań ustawianych przez aplikację w rzeczywistym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="48ba0-526">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które `User-Agent`ustawia przeglądarka, takich jak .</span><span class="sxs-lookup"><span data-stu-id="48ba0-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="48ba0-527">Gdy mechanizm CORS jest włączony z odpowiednią zasadą, ASP.NET Core zazwyczaj automatycznie odpowiada na żądania inspekcji wstępnej CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="48ba0-528">Zobacz [atrybut [HttpOptions] dla żądań inspekcji wstępnej](#pro).</span><span class="sxs-lookup"><span data-stu-id="48ba0-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="48ba0-529">Żądanie inspekcji wstępnej `Access-Control-Request-Headers` cors może zawierać nagłówek, który wskazuje serwerowi nagłówki, które są wysyłane z rzeczywistym żądaniem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="48ba0-530">Aby zezwolić na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>określone nagłówki, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="48ba0-531">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>nagłówki żądań autora, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="48ba0-532">Przeglądarki nie są całkowicie spójne w `Access-Control-Request-Headers`sposobie ustawiania.</span><span class="sxs-lookup"><span data-stu-id="48ba0-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="48ba0-533">Jeśli nagłówki są ustawione na `"*"` coś <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>innego niż (lub `Accept` `Content-Type`użycie), `Origin`należy dołączyć co najmniej , i , plus wszystkie niestandardowe nagłówki, które chcesz obsługiwać.</span><span class="sxs-lookup"><span data-stu-id="48ba0-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="48ba0-534">Poniżej przedstawiono przykładową odpowiedź na żądanie inspekcji wstępnej (przy założeniu, że serwer zezwala na żądanie):</span><span class="sxs-lookup"><span data-stu-id="48ba0-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="48ba0-535">Odpowiedź zawiera `Access-Control-Allow-Methods` nagłówek, który zawiera listę dozwolonych `Access-Control-Allow-Headers` metod i opcjonalnie nagłówek, który zawiera listę dozwolonych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="48ba0-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="48ba0-536">Jeśli żądanie inspekcji wstępnej zakończy się pomyślnie, przeglądarka wysyła rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="48ba0-537">Jeśli żądanie inspekcji wstępnej zostanie odrzucone, aplikacja zwraca odpowiedź *200 OK,* ale nie wysyła nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="48ba0-538">W związku z tym przeglądarka nie próbuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="48ba0-539">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="48ba0-539">Set the preflight expiration time</span></span>

<span data-ttu-id="48ba0-540">Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="48ba0-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="48ba0-541">Aby ustawić ten <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>nagłówek, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="48ba0-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="48ba0-542">Jak działa CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-542">How CORS works</span></span>

<span data-ttu-id="48ba0-543">W tej sekcji opisano, co dzieje się w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie wiadomości HTTP.</span><span class="sxs-lookup"><span data-stu-id="48ba0-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="48ba0-544">CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="48ba0-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="48ba0-545">CORS to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="48ba0-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="48ba0-546">Na przykład złośliwy aktor może użyć [prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) przeciwko witrynie i wykonać żądanie między witrynami do witryny obsługującej cors do kradzieży informacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="48ba0-547">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="48ba0-548">To do klienta (przeglądarki), aby wymusić CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="48ba0-549">Serwer wykonuje żądanie i zwraca odpowiedź, to klient zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="48ba0-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="48ba0-550">Na przykład dowolne z następujących narzędzi wyświetli odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="48ba0-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="48ba0-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="48ba0-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="48ba0-552">Postman</span><span class="sxs-lookup"><span data-stu-id="48ba0-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="48ba0-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="48ba0-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="48ba0-554">Przeglądarka internetowa, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="48ba0-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="48ba0-555">Jest to sposób dla serwera, aby umożliwić przeglądarkom wykonać żądanie [XHR xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub [fetch api,](https://developer.mozilla.org/docs/Web/API/Fetch_API) które w przeciwnym razie byłyby zabronione.</span><span class="sxs-lookup"><span data-stu-id="48ba0-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="48ba0-556">Przeglądarki (bez CORS) nie mogą wykonywać żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="48ba0-557">Przed [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) był używany do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="48ba0-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="48ba0-558">JSONP nie używa XHR, używa `<script>` tagu do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="48ba0-559">Skrypty mogą być ładowane cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="48ba0-560">Specyfikacja [CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="48ba0-561">Jeśli przeglądarka obsługuje cors, ustawia te nagłówki automatycznie dla żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="48ba0-562">Niestandardowy kod JavaScript nie jest wymagany do włączenia usługi CORS.</span><span class="sxs-lookup"><span data-stu-id="48ba0-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="48ba0-563">Poniżej przedstawiono przykład żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="48ba0-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="48ba0-564">Nagłówek `Origin` udostępnia domenę witryny, która tworzy żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="48ba0-565">Nagłówek `Origin` jest wymagany i musi być inny niż host.</span><span class="sxs-lookup"><span data-stu-id="48ba0-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="48ba0-566">Jeśli serwer zezwala na żądanie, `Access-Control-Allow-Origin` ustawia nagłówek w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="48ba0-567">Wartość tego nagłówka jest `Origin` zgodna z nagłówkiem z żądania `"*"`lub jest wartością symboli wieloznacznych, co oznacza, że dowolne pochodzenie jest dozwolone:</span><span class="sxs-lookup"><span data-stu-id="48ba0-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="48ba0-568">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="48ba0-569">W szczególności przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="48ba0-570">Nawet jeśli serwer zwraca pomyślną odpowiedź, przeglądarka nie udostępnia odpowiedzi do aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="48ba0-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="48ba0-571">Badanie CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-571">Test CORS</span></span>

<span data-ttu-id="48ba0-572">Aby przetestować CORS:</span><span class="sxs-lookup"><span data-stu-id="48ba0-572">To test CORS:</span></span>

1. <span data-ttu-id="48ba0-573">[Tworzenie projektu interfejsu API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="48ba0-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="48ba0-574">Alternatywnie można [pobrać przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="48ba0-575">Włącz CORS przy użyciu jednego z podejść w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="48ba0-576">Przykład:</span><span class="sxs-lookup"><span data-stu-id="48ba0-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="48ba0-577">`WithOrigins("https://localhost:<port>");`należy używać tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="48ba0-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="48ba0-578">Utwórz projekt aplikacji internetowej (Razor Pages lub MVC).</span><span class="sxs-lookup"><span data-stu-id="48ba0-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="48ba0-579">W przykładzie użyto stron Razor.</span><span class="sxs-lookup"><span data-stu-id="48ba0-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="48ba0-580">Aplikację sieci web można utworzyć w tym samym rozwiązaniu co projekt interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="48ba0-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="48ba0-581">Dodaj następujący wyróżniony kod do pliku *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="48ba0-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="48ba0-582">W poprzednim kodzie `url: 'https://<web app>.azurewebsites.net/api/values/1',` zastąp adresem URL wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="48ba0-583">Wdrażanie projektu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="48ba0-583">Deploy the API project.</span></span> <span data-ttu-id="48ba0-584">Na przykład [wdrożyć na platformie Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="48ba0-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="48ba0-585">Uruchom stronę Razor Pages lub aplikację MVC z pulpitu i kliknij przycisk **Testuj.**</span><span class="sxs-lookup"><span data-stu-id="48ba0-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="48ba0-586">Narzędzia F12 umożliwia przeglądanie komunikatów o błędach.</span><span class="sxs-lookup"><span data-stu-id="48ba0-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="48ba0-587">Usuń pochodzenie localhost `WithOrigins` z i wdrożyć aplikację.</span><span class="sxs-lookup"><span data-stu-id="48ba0-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="48ba0-588">Alternatywnie uruchom aplikację kliencką z innym portem.</span><span class="sxs-lookup"><span data-stu-id="48ba0-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="48ba0-589">Na przykład uruchom z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="48ba0-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="48ba0-590">Przetestuj za pomocą aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="48ba0-590">Test with the client app.</span></span> <span data-ttu-id="48ba0-591">Błędy mechanizmu CORS zwracają błąd, ale komunikat o błędzie nie jest dostępny w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="48ba0-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="48ba0-592">Użyj karty konsoli w narzędziach F12, aby zobaczyć błąd.</span><span class="sxs-lookup"><span data-stu-id="48ba0-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="48ba0-593">W zależności od przeglądarki pojawia się błąd (w konsoli narzędzi F12) podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="48ba0-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="48ba0-594">Korzystanie z przeglądarki Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="48ba0-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="48ba0-595">**SEC7120: [CORS] `https://localhost:44375` Źródło nie `https://localhost:44375` został odnaleźć w nagłówku odpowiedzi Access-Control-Allow-Origin dla zasobu między źródłami w`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="48ba0-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="48ba0-596">Korzystanie z Chrome:</span><span class="sxs-lookup"><span data-stu-id="48ba0-596">Using Chrome:</span></span>

     <span data-ttu-id="48ba0-597">**Dostęp do protokołu XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` u źródła `https://localhost:44375` został zablokowany przez zasady CORS: W żądanym zasobie nie ma nagłówka "Access-Control-Allow-Origin".**</span><span class="sxs-lookup"><span data-stu-id="48ba0-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="48ba0-598">Punkty końcowe obsługujące mechanizm CORS można testować za pomocą narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) lub [Listonosz.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="48ba0-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="48ba0-599">W przypadku korzystania z narzędzia pochodzenie żądania `Origin` określonego przez nagłówek musi się różnić od hosta odbierającego żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="48ba0-600">Jeśli żądanie nie jest *cross-origin* na podstawie `Origin` wartości nagłówka:</span><span class="sxs-lookup"><span data-stu-id="48ba0-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="48ba0-601">Nie ma potrzeby, aby oprogramowanie pośredniczące CORS przetwarzać żądanie.</span><span class="sxs-lookup"><span data-stu-id="48ba0-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="48ba0-602">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="48ba0-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="48ba0-603">CORS w iis</span><span class="sxs-lookup"><span data-stu-id="48ba0-603">CORS in IIS</span></span>

<span data-ttu-id="48ba0-604">Podczas wdrażania w usługach IIS mechanizm CORS musi być uruchomiony przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="48ba0-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="48ba0-605">Aby obsługiwać ten scenariusz, [moduł IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) musi zostać zainstalowany i skonfigurowany dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="48ba0-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48ba0-606">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="48ba0-606">Additional resources</span></span>

* [<span data-ttu-id="48ba0-607">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="48ba0-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="48ba0-608">Wprowadzenie do modułu IIS CORS</span><span class="sxs-lookup"><span data-stu-id="48ba0-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
