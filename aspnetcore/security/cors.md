---
title: Włączanie żądań między źródłami (CORS) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak cors jako standard zezwalania lub odrzucania żądań cross-origin w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440912"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="507a0-103">Włączanie żądań między źródłami (CORS) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="507a0-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="507a0-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="507a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="507a0-105">W tym artykule pokazano, jak włączyć usługę CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="507a0-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="507a0-106">Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="507a0-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="507a0-107">To ograniczenie jest nazywane *zasadami tego samego pochodzenia*.</span><span class="sxs-lookup"><span data-stu-id="507a0-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="507a0-108">Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny.</span><span class="sxs-lookup"><span data-stu-id="507a0-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="507a0-109">Czasami możesz zezwolić innym witrynom na składanie żądań cross-origin do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="507a0-110">Aby uzyskać więcej informacji, zobacz [artykuł Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="507a0-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="507a0-111">[Udostępnianie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="507a0-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="507a0-112">Jest standardem W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="507a0-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="507a0-113">**Nie** jest to funkcja zabezpieczeń, CORS rozluźnia zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="507a0-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="507a0-114">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="507a0-115">Aby uzyskać więcej informacji, zobacz [Jak działa CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="507a0-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="507a0-116">Umożliwia serwerowi jawne zezwalanie na niektóre żądania między źródłami, jednocześnie odrzucając inne.</span><span class="sxs-lookup"><span data-stu-id="507a0-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="507a0-117">Jest bezpieczniejsza i bardziej elastyczna niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="507a0-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="507a0-118">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="507a0-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="507a0-119">To samo pochodzenie</span><span class="sxs-lookup"><span data-stu-id="507a0-119">Same origin</span></span>

<span data-ttu-id="507a0-120">Dwa adresy URL mają to samo pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454).](https://tools.ietf.org/html/rfc6454)</span><span class="sxs-lookup"><span data-stu-id="507a0-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="507a0-121">Te dwa adresy URL mają to samo źródło:</span><span class="sxs-lookup"><span data-stu-id="507a0-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="507a0-122">Te adresy URL mają inne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="507a0-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="507a0-123">`https://example.net`&ndash; Inna domena</span><span class="sxs-lookup"><span data-stu-id="507a0-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="507a0-124">`https://www.example.com/foo.html`&ndash; Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="507a0-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="507a0-125">`http://example.com/foo.html`&ndash; Inny schemat</span><span class="sxs-lookup"><span data-stu-id="507a0-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="507a0-126">`https://example.com:9000/foo.html`&ndash; Inny port</span><span class="sxs-lookup"><span data-stu-id="507a0-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="507a0-127">Włączanie mechanizmu CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-127">Enable CORS</span></span>

<span data-ttu-id="507a0-128">Istnieją trzy sposoby włączenia cors:</span><span class="sxs-lookup"><span data-stu-id="507a0-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="507a0-129">W oprogramowaniu pośredniczącym przy użyciu [nazwanych zasad](#np) lub [zasad domyślnych](#dp).</span><span class="sxs-lookup"><span data-stu-id="507a0-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="507a0-130">Korzystanie z [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="507a0-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="507a0-131">Z atrybutem [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="507a0-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="507a0-132">Za pomocą [atrybutu [EnableCors]](#attr) z nazwanych zasad zapewnia najwyższą kontrolę w ograniczaniu punktów końcowych, które obsługują CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="507a0-133">Każde podejście jest szczegółowo opisane w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="507a0-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="507a0-134">CORS z nazwanymi zasadami i oprogramowaniem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="507a0-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="507a0-135">Oprogramowanie pośredniczące CORS obsługuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="507a0-136">Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji o określonych źródłach:</span><span class="sxs-lookup"><span data-stu-id="507a0-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="507a0-137">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="507a0-137">The preceding code:</span></span>

* <span data-ttu-id="507a0-138">Ustawia nazwę zasady `_myAllowSpecificOrigins`na .</span><span class="sxs-lookup"><span data-stu-id="507a0-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="507a0-139">Nazwa zasad jest dowolna.</span><span class="sxs-lookup"><span data-stu-id="507a0-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="507a0-140">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia i `_myAllowSpecificOrigins` określa zasady CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="507a0-141">`UseCors`dodaje oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="507a0-142">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="507a0-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="507a0-143">Lambda bierze <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="507a0-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="507a0-144">[Opcje konfiguracji](#cors-policy-options), `WithOrigins`takie jak , są opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="507a0-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="507a0-145">Włącza `_myAllowSpecificOrigins` zasady CORS dla wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="507a0-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="507a0-146">Zobacz [routing punktu końcowego,](#ecors) aby zastosować zasady CORS do określonych punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="507a0-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="507a0-147">W obszarze routingu punktu końcowego oprogramowanie pośredniczące CORS `UseRouting` `UseEndpoints` ***musi*** być skonfigurowane do wykonywania między wywołaniami do i .</span><span class="sxs-lookup"><span data-stu-id="507a0-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="507a0-148">Zobacz [Test CORS](#testc) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="507a0-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="507a0-149"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="507a0-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="507a0-150">Aby uzyskać więcej informacji, zobacz [opcje zasad CORS](#cpo) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="507a0-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="507a0-151">Metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> mogą być połączone łańcuchem, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="507a0-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="507a0-152">Uwaga: Określony adres URL **nie** może`/`zawierać końcowego ukośnika ( ).</span><span class="sxs-lookup"><span data-stu-id="507a0-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="507a0-153">Jeśli adres URL `/`zostanie zakończony `false` na , porównanie zwraca się i nie zwracany jest żaden nagłówek.</span><span class="sxs-lookup"><span data-stu-id="507a0-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="507a0-154">CORS z domyślną zasadą i oprogramowaniem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="507a0-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="507a0-155">Poniższy wyróżniony kod umożliwia domyślną zasadę CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="507a0-156">Powyższy kod stosuje domyślne zasady CORS do wszystkich punktów końcowych kontrolera.</span><span class="sxs-lookup"><span data-stu-id="507a0-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="507a0-157">Włącz cors z routingiem punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="507a0-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="507a0-158">Włączenie mechanizmu CORS dla punktu `RequireCors` końcowego przy użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="507a0-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="507a0-159">Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/aspnetcore/issues/20709) i [test cors z routingiem punktu końcowego i [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="507a0-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="507a0-160">Za pomocą routingu punktu końcowego cors można włączyć na <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> podstawie punktu końcowego przy użyciu zestawu metod rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="507a0-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="507a0-161">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="507a0-161">In the preceding code:</span></span>

* <span data-ttu-id="507a0-162">`app.UseCors`włącza oprogramowanie pośredniczące CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="507a0-163">Ponieważ domyślna zasada nie została `app.UseCors()` skonfigurowana, sama nie włącza mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="507a0-164">Punkty `/echo` końcowe i kontrolera zezwalają na żądania cross-origin przy użyciu określonych zasad.</span><span class="sxs-lookup"><span data-stu-id="507a0-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="507a0-165">Punkty `/echo2` końcowe stron i Razor ***pages nie*** zezwalają na żądania między źródłami, ponieważ nie określono zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="507a0-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="507a0-166">Atrybut [[DisableCors]](#dc) ***nie*** wyłącza mechanizmu CORS, który został `RequireCors`włączony przez routing punktu końcowego za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="507a0-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="507a0-167">Zobacz [Test CORS z routingu punktu końcowego i [HttpOptions]](#tcer) instrukcje dotyczące testowania kodu podobnego do poprzedniego.</span><span class="sxs-lookup"><span data-stu-id="507a0-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="507a0-168">Włącz cors z atrybutami</span><span class="sxs-lookup"><span data-stu-id="507a0-168">Enable CORS with attributes</span></span>

<span data-ttu-id="507a0-169">Włączenie cors z [atrybutem [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) i stosowanie nazwanych zasad tylko do tych punktów końcowych, które wymagają CORS zapewnia najwyższą kontrolę.</span><span class="sxs-lookup"><span data-stu-id="507a0-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="507a0-170">Atrybut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) stanowi alternatywę dla globalnego stosowania usługi CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="507a0-171">Atrybut `[EnableCors]` umożliwia CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych:</span><span class="sxs-lookup"><span data-stu-id="507a0-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="507a0-172">`[EnableCors]`określa domyślną zasadę.</span><span class="sxs-lookup"><span data-stu-id="507a0-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="507a0-173">`[EnableCors("{Policy String}")]`określa nazwaną zasadę.</span><span class="sxs-lookup"><span data-stu-id="507a0-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="507a0-174">Atrybut `[EnableCors]` można zastosować do:</span><span class="sxs-lookup"><span data-stu-id="507a0-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="507a0-175">Strona Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="507a0-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="507a0-176">Kontrolera</span><span class="sxs-lookup"><span data-stu-id="507a0-176">Controller</span></span>
* <span data-ttu-id="507a0-177">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="507a0-177">Controller action method</span></span>

<span data-ttu-id="507a0-178">Różne zasady mogą być stosowane do kontrolerów, modeli `[EnableCors]` stron lub metod akcji z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="507a0-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="507a0-179">Gdy `[EnableCors]` atrybut jest stosowany do kontrolera, modelu strony lub metody akcji, a cors jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane.</span><span class="sxs-lookup"><span data-stu-id="507a0-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="507a0-180">***Zalecamy, aby nie łączyć zasad. Użyj atrybutu*** `[EnableCors]` ***lub oprogramowania pośredniczącego, a nie w tej samej aplikacji.***</span><span class="sxs-lookup"><span data-stu-id="507a0-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="507a0-181">Poniższy kod stosuje inną zasadę do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="507a0-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="507a0-182">Poniższy kod tworzy dwie zasady CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="507a0-183">Aby uzyskać najlepszą kontrolę ograniczania żądań CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="507a0-184">Użyj `[EnableCors("MyPolicy")]` z nazwanymi zasadami.</span><span class="sxs-lookup"><span data-stu-id="507a0-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="507a0-185">Nie definiuj zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="507a0-185">Don't define a default policy.</span></span>
* <span data-ttu-id="507a0-186">Nie używaj [routingu punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="507a0-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="507a0-187">Kod w następnej sekcji spełnia poprzednią listę.</span><span class="sxs-lookup"><span data-stu-id="507a0-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="507a0-188">Zobacz [Test CORS](#testc) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="507a0-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="507a0-189">Wyłącz cors</span><span class="sxs-lookup"><span data-stu-id="507a0-189">Disable CORS</span></span>

<span data-ttu-id="507a0-190">Atrybut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***nie*** wyłącza mechanizmu CORS, który został włączony przez [routing punktu końcowego](#ecors).</span><span class="sxs-lookup"><span data-stu-id="507a0-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="507a0-191">Poniższy kod definiuje zasady `"MyPolicy"`CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="507a0-192">Następujący kod wyłącza cors `GetValues2` dla akcji:</span><span class="sxs-lookup"><span data-stu-id="507a0-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="507a0-193">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="507a0-193">The preceding code:</span></span>

* <span data-ttu-id="507a0-194">Nie włącza mechanizmu CORS z [routingiem punktów końcowych.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="507a0-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="507a0-195">Nie definiuje [domyślnej zasady CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="507a0-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="507a0-196">Używa [funkcji [EnableCors("MyPolicy")],](#attr) `"MyPolicy"` aby włączyć zasady CORS dla kontrolera.</span><span class="sxs-lookup"><span data-stu-id="507a0-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="507a0-197">Wyłącza CORS `GetValues2` dla metody.</span><span class="sxs-lookup"><span data-stu-id="507a0-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="507a0-198">Zobacz [Test CORS](#testc) instrukcje dotyczące testowania poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="507a0-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="507a0-199">Warianty polityki CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-199">CORS policy options</span></span>

<span data-ttu-id="507a0-200">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="507a0-201">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="507a0-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="507a0-202">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="507a0-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="507a0-203">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="507a0-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="507a0-204">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="507a0-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="507a0-205">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="507a0-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="507a0-206">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="507a0-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="507a0-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="507a0-208">W przypadku niektórych opcji warto najpierw przeczytać sekcję [Jak działa CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="507a0-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="507a0-209">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="507a0-209">Set the allowed origins</span></span>

<span data-ttu-id="507a0-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umożliwia żądania CORS ze wszystkich źródeł`http` `https`z dowolnym schematem ( lub ).</span><span class="sxs-lookup"><span data-stu-id="507a0-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="507a0-211">`AllowAnyOrigin`jest niezabezpieczona, ponieważ *każda witryna sieci Web* może składać żądania cross-origin do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="507a0-212">Określanie `AllowAnyOrigin` `AllowCredentials` i jest niezabezpieczona konfiguracja i może spowodować fałszerstwa żądań między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="507a0-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="507a0-213">Usługa CORS zwraca nieprawidłową odpowiedź CORS, gdy aplikacja jest skonfigurowana przy obu metodach.</span><span class="sxs-lookup"><span data-stu-id="507a0-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="507a0-214">`AllowAnyOrigin`wpływa na żądania inspekcji `Access-Control-Allow-Origin` wstępnej i nagłówek.</span><span class="sxs-lookup"><span data-stu-id="507a0-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="507a0-215">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="507a0-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="507a0-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> właściwość zasad jako funkcję, która umożliwia początek, aby dopasować skonfigurowaną domenę symboli wieloznacznych podczas oceny, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="507a0-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="507a0-217">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="507a0-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="507a0-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="507a0-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="507a0-219">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="507a0-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="507a0-220">Wpływa na żądania inspekcji `Access-Control-Allow-Methods` wstępnej i nagłówka.</span><span class="sxs-lookup"><span data-stu-id="507a0-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="507a0-221">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="507a0-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="507a0-222">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="507a0-222">Set the allowed request headers</span></span>

<span data-ttu-id="507a0-223">Aby zezwolić na wysyłane określone nagłówki w żądaniu CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> nazywane [nagłówkami żądań autora](https://xhr.spec.whatwg.org/#request), zadzwoń i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="507a0-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="507a0-224">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [nagłówki żądań autora,](https://www.w3.org/TR/cors/#author-request-headers)zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="507a0-225">`AllowAnyHeader`wpływa na żądania inspekcji wstępnej i nagłówka [nagłówków żądania kontroli dostępu.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="507a0-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="507a0-226">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="507a0-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="507a0-227">Zasady oprogramowania pośredniczącego CORS są `WithHeaders` zgodne z określonymi nagłówkami określonymi przez jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie odpowiadają nagłówkom podanym w pliku `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="507a0-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="507a0-228">Rozważmy na przykład aplikację skonfigurowaną w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="507a0-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="507a0-229">Oprogramowanie pośredniczące cors odrzuca żądanie inspekcji wstępnej `Content-Language` z następującym nagłówkiem żądania, ponieważ ( `WithHeaders`[HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nie jest wymieniony w :</span><span class="sxs-lookup"><span data-stu-id="507a0-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="507a0-230">Aplikacja zwraca odpowiedź *200 OK,* ale nie wysyła nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="507a0-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="507a0-231">W związku z tym przeglądarka nie próbuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="507a0-232">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="507a0-232">Set the exposed response headers</span></span>

<span data-ttu-id="507a0-233">Domyślnie przeglądarka nie udostępnia wszystkie nagłówki odpowiedzi do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="507a0-234">Aby uzyskać więcej informacji, zobacz [Udostępnianie zasobów W3C Cross-Origin (Terminologia): Nagłówek prostej odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="507a0-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="507a0-235">Nagłówki odpowiedzi, które są domyślnie dostępne, to:</span><span class="sxs-lookup"><span data-stu-id="507a0-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="507a0-236">Specyfikacja CORS wywołuje te nagłówki *proste nagłówki odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="507a0-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="507a0-237">Aby udostępnić aplikacji inne nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>zadzwoń do:</span><span class="sxs-lookup"><span data-stu-id="507a0-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="507a0-238">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="507a0-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="507a0-239">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="507a0-240">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="507a0-241">Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP.</span><span class="sxs-lookup"><span data-stu-id="507a0-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="507a0-242">Aby wysłać poświadczenia z żądaniem cross-origin, klient musi ustawić `XMLHttpRequest.withCredentials` na `true`.</span><span class="sxs-lookup"><span data-stu-id="507a0-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="507a0-243">Korzystanie `XMLHttpRequest` bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="507a0-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="507a0-244">Korzystanie z jQuery:</span><span class="sxs-lookup"><span data-stu-id="507a0-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="507a0-245">Korzystanie z [interfejsu API pobierania:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="507a0-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="507a0-246">Serwer musi zezwolić na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="507a0-246">The server must allow the credentials.</span></span> <span data-ttu-id="507a0-247">Aby zezwolić na poświadczenia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>między źródłami, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="507a0-248">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="507a0-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="507a0-249">Jeśli przeglądarka wysyła poświadczenia, ale odpowiedź nie `Access-Control-Allow-Credentials` zawiera prawidłowego nagłówka, przeglądarka nie udostępnia odpowiedzi na aplikację, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="507a0-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="507a0-250">Zezwalanie na poświadczenia między źródłami jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="507a0-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="507a0-251">Witryna sieci Web w innej domenie może wysyłać poświadczeń zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="507a0-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="507a0-252">Specyfikacja CORS stwierdza również, `"*"` że ustawienie początku (wszystkie `Access-Control-Allow-Credentials` źródła) jest nieprawidłowe, jeśli nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="507a0-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="507a0-253">Żądania inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-253">Preflight requests</span></span>

<span data-ttu-id="507a0-254">W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie [opcji](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) przed wysłaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="507a0-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="507a0-255">To żądanie jest nazywane [żądaniem inspekcji wstępnej](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="507a0-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="507a0-256">Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są ***wszystkie*** następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="507a0-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="507a0-257">Metoda żądania to GET, HEAD lub POST.</span><span class="sxs-lookup"><span data-stu-id="507a0-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="507a0-258">Aplikacja nie ustawia nagłówków żądań `Accept` `Accept-Language`innych `Content-Language` `Content-Type`niż `Last-Event-ID`, , , lub .</span><span class="sxs-lookup"><span data-stu-id="507a0-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="507a0-259">Nagłówek, `Content-Type` jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="507a0-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="507a0-260">Reguła nagłówków żądań ustawiona dla żądania klienta ma zastosowanie `setRequestHeader` do `XMLHttpRequest` nagłówków ustawianych przez aplikację, wywołując obiekt.</span><span class="sxs-lookup"><span data-stu-id="507a0-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="507a0-261">Specyfikacja CORS wywołuje te nagłówki [nagłówków żądania autora nagłówków](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="507a0-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="507a0-262">Reguła nie ma zastosowania do nagłówków, które `User-Agent` `Host`przeglądarka `Content-Length`może ustawić, takich jak , lub .</span><span class="sxs-lookup"><span data-stu-id="507a0-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="507a0-263">Poniżej przedstawiono przykładową odpowiedź podobną do żądania inspekcji wstępnej złożonego z przycisku **[Put test]** w sekcji [Test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="507a0-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="507a0-264">Żądanie inspekcji wstępnej używa metody [OPCJE HTTP.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="507a0-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="507a0-265">Może zawierać następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="507a0-265">It may include the following headers:</span></span>

* <span data-ttu-id="507a0-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda HTTP, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="507a0-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="507a0-267">[Nagłówki żądania kontroli dostępu:](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)lista nagłówków żądań ustawianych przez aplikację w rzeczywistym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="507a0-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="507a0-268">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które `User-Agent`ustawia przeglądarka, takich jak .</span><span class="sxs-lookup"><span data-stu-id="507a0-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="507a0-269">Metody dostępu-kontrola-zezwalaj</span><span class="sxs-lookup"><span data-stu-id="507a0-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="507a0-270">Jeśli żądanie inspekcji wstępnej zostanie odrzucone, `200 OK` aplikacja zwraca odpowiedź, ale nie ustawia nagłówków CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="507a0-271">W związku z tym przeglądarka nie próbuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="507a0-272">Na przykład odmowy żądania inspekcji wstępnej, zobacz [sekcji Test CORS](#testc) tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="507a0-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="507a0-273">Za pomocą narzędzi F12 aplikacja konsoli wyświetla błąd podobny do jednego z następujących, w zależności od przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="507a0-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="507a0-274">Firefox: Żądanie cross-origin zablokowane: Ta sama polityka pochodzenia nie `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`zezwala na odczytanie zdalnego zasobu w .</span><span class="sxs-lookup"><span data-stu-id="507a0-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="507a0-275">(Powód: żądanie CORS nie powiodło się).</span><span class="sxs-lookup"><span data-stu-id="507a0-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="507a0-276">Dowiedz się więcej</span><span class="sxs-lookup"><span data-stu-id="507a0-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="507a0-277">Chromium na podstawie: Dostęphttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5do pobraniahttps://cors3.azurewebsites.netw ' od początku ' ' został zablokowany przez zasady CORS: Odpowiedź na żądanie inspekcji wstępnej nie przekazuje kontroli dostępu: Nie ma nagłówka "Access-Control-Allow-Origin" w żądanym zasobie.</span><span class="sxs-lookup"><span data-stu-id="507a0-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="507a0-278">Jeśli nieprzezroczysta odpowiedź służy Twoim potrzebom, ustaw tryb żądania na "no-cors", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="507a0-279">Aby zezwolić na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>określone nagłówki, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="507a0-280">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [nagłówki żądań autora,](https://www.w3.org/TR/cors/#author-request-headers)zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="507a0-281">Przeglądarki nie są spójne w `Access-Control-Request-Headers`sposobie ustawiania .</span><span class="sxs-lookup"><span data-stu-id="507a0-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="507a0-282">Jeśli:</span><span class="sxs-lookup"><span data-stu-id="507a0-282">If either:</span></span>

* <span data-ttu-id="507a0-283">Nagłówki są ustawione na coś innego niż`"*"`</span><span class="sxs-lookup"><span data-stu-id="507a0-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="507a0-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jest wywoływana: `Accept`Dołącz `Content-Type`co `Origin`najmniej , i , plus wszystkie niestandardowe nagłówki, które chcesz obsługiwać.</span><span class="sxs-lookup"><span data-stu-id="507a0-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="507a0-285">Automatyczny kod żądania inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-285">Automatic preflight request code</span></span>

<span data-ttu-id="507a0-286">Po zastosowaniu zasad CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="507a0-287">Globalnie, `app.UseCors` `Startup.Configure`wywołując w .</span><span class="sxs-lookup"><span data-stu-id="507a0-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="507a0-288">Przy `[EnableCors]` użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="507a0-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="507a0-289">ASP.NET Core odpowiada na żądanie opcji inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="507a0-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="507a0-290">Włączenie mechanizmu CORS na podstawie `RequireCors` punktu końcowego przy użyciu obecnie ***nie*** obsługuje automatycznych żądań inspekcji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="507a0-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="507a0-291">Sekcja [Test CORS](#testc) tego dokumentu demonstruje to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="507a0-292">[HttpOptions] atrybut dla żądań inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="507a0-293">Gdy mechanizm CORS jest włączony z odpowiednią zasadą, ASP.NET Core zazwyczaj automatycznie odpowiada na żądania inspekcji wstępnej CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="507a0-294">W niektórych scenariuszach może to nie być przypadek.</span><span class="sxs-lookup"><span data-stu-id="507a0-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="507a0-295">Na przykład przy użyciu [mechanizmu CORS z routingiem punktów końcowych](#ecors).</span><span class="sxs-lookup"><span data-stu-id="507a0-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="507a0-296">Poniższy kod używa atrybutu [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) do tworzenia punktów końcowych dla żądań OPCJI:</span><span class="sxs-lookup"><span data-stu-id="507a0-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="507a0-297">Zobacz [Test CORS z routingu punktu końcowego i [HttpOptions]](#tcer) instrukcje dotyczące testowania poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="507a0-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="507a0-298">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-298">Set the preflight expiration time</span></span>

<span data-ttu-id="507a0-299">Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="507a0-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="507a0-300">Aby ustawić ten <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>nagłówek, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="507a0-301">Jak działa CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-301">How CORS works</span></span>

<span data-ttu-id="507a0-302">W tej sekcji opisano, co dzieje się w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie wiadomości HTTP.</span><span class="sxs-lookup"><span data-stu-id="507a0-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="507a0-303">CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="507a0-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="507a0-304">CORS to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="507a0-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="507a0-305">Na przykład złośliwy aktor może użyć [skryptów krzyżowych (XSS)](xref:security/cross-site-scripting) przeciwko witrynie i wykonać żądanie między witrynami do witryny obsługującej mechanizm CORS, aby wykraść informacje.</span><span class="sxs-lookup"><span data-stu-id="507a0-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="507a0-306">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="507a0-307">To do klienta (przeglądarki), aby wymusić CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="507a0-308">Serwer wykonuje żądanie i zwraca odpowiedź, to klient zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="507a0-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="507a0-309">Na przykład dowolne z następujących narzędzi wyświetli odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="507a0-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="507a0-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="507a0-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="507a0-311">Postman</span><span class="sxs-lookup"><span data-stu-id="507a0-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="507a0-312">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="507a0-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="507a0-313">Przeglądarka internetowa, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="507a0-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="507a0-314">Jest to sposób dla serwera, aby umożliwić przeglądarkom wykonać żądanie [XHR xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub [fetch api,](https://developer.mozilla.org/docs/Web/API/Fetch_API) które w przeciwnym razie byłyby zabronione.</span><span class="sxs-lookup"><span data-stu-id="507a0-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="507a0-315">Przeglądarki bez CORS nie mogą wykonywać żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="507a0-316">Przed [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) był używany do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="507a0-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="507a0-317">JSONP nie używa XHR, używa `<script>` tagu do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="507a0-318">Skrypty mogą być ładowane cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="507a0-319">Specyfikacja [CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="507a0-320">Jeśli przeglądarka obsługuje cors, ustawia te nagłówki automatycznie dla żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="507a0-321">Niestandardowy kod JavaScript nie jest wymagany do włączenia usługi CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="507a0-322">[Przycisk testu PUT](https://cors3.azurewebsites.net/test) na wdrożonym [przykładzie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="507a0-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="507a0-323">Poniżej przedstawiono przykład żądania cross-origin z przycisku testu [wartości](https://cors3.azurewebsites.net/) do `https://cors1.azurewebsites.net/api/values`.</span><span class="sxs-lookup"><span data-stu-id="507a0-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="507a0-324">Nagłówek: `Origin`</span><span class="sxs-lookup"><span data-stu-id="507a0-324">The `Origin` header:</span></span>

* <span data-ttu-id="507a0-325">Udostępnia domenę witryny, która tworzy żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="507a0-326">Jest to wymagane i musi być inny niż host.</span><span class="sxs-lookup"><span data-stu-id="507a0-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="507a0-327">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="507a0-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="507a0-328">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="507a0-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="507a0-329">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="507a0-329">**Request headers**</span></span>

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

<span data-ttu-id="507a0-330">W `OPTIONS` żądaniach serwer ustawia nagłówek **nagłówków** `Access-Control-Allow-Origin: {allowed origin}` odpowiedzi w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="507a0-331">Na przykład wdrożone [przykładowe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)żądanie przycisku `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) zawiera następujące nagłówki:</span><span class="sxs-lookup"><span data-stu-id="507a0-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="507a0-332">**Nagłówki ogólne**</span><span class="sxs-lookup"><span data-stu-id="507a0-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="507a0-333">**Nagłówki odpowiedzi**</span><span class="sxs-lookup"><span data-stu-id="507a0-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="507a0-334">**Nagłówki żądań**</span><span class="sxs-lookup"><span data-stu-id="507a0-334">**Request headers**</span></span>

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

<span data-ttu-id="507a0-335">W poprzednich **nagłówkach odpowiedzi**serwer ustawia nagłówek [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="507a0-336">Wartość `https://cors1.azurewebsites.net` tego nagłówka `Origin` pasuje do nagłówka z żądania.</span><span class="sxs-lookup"><span data-stu-id="507a0-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="507a0-337">Jeśli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> jest wywoływana, `Access-Control-Allow-Origin: *`zwracana jest wartość symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="507a0-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="507a0-338">`AllowAnyOrigin`dopuszcza dowolne pochodzenie.</span><span class="sxs-lookup"><span data-stu-id="507a0-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="507a0-339">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="507a0-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="507a0-340">W szczególności przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="507a0-341">Nawet jeśli serwer zwraca pomyślną odpowiedź, przeglądarka nie udostępnia odpowiedzi do aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="507a0-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="507a0-342">Wyświetl żądania OPCJI</span><span class="sxs-lookup"><span data-stu-id="507a0-342">Display OPTIONS requests</span></span>

<span data-ttu-id="507a0-343">Domyślnie przeglądarki Chrome i Edge nie wyświetla żądań OPCJI na karcie sieciowe narzędzi F12.</span><span class="sxs-lookup"><span data-stu-id="507a0-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="507a0-344">Aby wyświetlić żądania opcji w tych przeglądarkach:</span><span class="sxs-lookup"><span data-stu-id="507a0-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="507a0-345">`chrome://flags/#out-of-blink-cors` lub `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="507a0-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="507a0-346">wyłączyć flagę.</span><span class="sxs-lookup"><span data-stu-id="507a0-346">disable the flag.</span></span>
* <span data-ttu-id="507a0-347">Ponownie uruchomić.</span><span class="sxs-lookup"><span data-stu-id="507a0-347">restart.</span></span>

<span data-ttu-id="507a0-348">Firefox domyślnie wyświetla żądania opcji.</span><span class="sxs-lookup"><span data-stu-id="507a0-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="507a0-349">CORS w iis</span><span class="sxs-lookup"><span data-stu-id="507a0-349">CORS in IIS</span></span>

<span data-ttu-id="507a0-350">Podczas wdrażania w usługach IIS mechanizm CORS musi być uruchomiony przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="507a0-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="507a0-351">Aby obsługiwać ten scenariusz, [moduł IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) musi zostać zainstalowany i skonfigurowany dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="507a0-352">Badanie CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-352">Test CORS</span></span>

<span data-ttu-id="507a0-353">[Przykładowe pobieranie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ma kod do testowania CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="507a0-354">Zobacz, [jak pobrać](xref:index#how-to-download-a-sample)plik .</span><span class="sxs-lookup"><span data-stu-id="507a0-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="507a0-355">Przykład jest projektem INTERFEJSU API z dodanymi stronami Razor:</span><span class="sxs-lookup"><span data-stu-id="507a0-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="507a0-356">`WithOrigins("https://localhost:<port>");`należy używać tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="507a0-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="507a0-357">Poniżej `ValuesController` przedstawiono punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="507a0-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="507a0-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) jest dostarczany przez [pakiet Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet i wyświetla informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="507a0-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="507a0-359">Przetestuj poprzedni przykładowy kod przy użyciu jednego z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="507a0-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="507a0-360">Użyj wdrożonej przykładowej [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)aplikacji w pliku .</span><span class="sxs-lookup"><span data-stu-id="507a0-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="507a0-361">Nie ma potrzeby pobierania próbki.</span><span class="sxs-lookup"><span data-stu-id="507a0-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="507a0-362">Uruchom próbkę `dotnet run` przy użyciu `https://localhost:5001`domyślnego adresu URL programu .</span><span class="sxs-lookup"><span data-stu-id="507a0-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="507a0-363">Uruchom przykład z programu Visual Studio z portem ustawionym na `https://localhost:44398`44398 dla adresu URL .</span><span class="sxs-lookup"><span data-stu-id="507a0-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="507a0-364">Korzystanie z przeglądarki z narzędziami F12:</span><span class="sxs-lookup"><span data-stu-id="507a0-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="507a0-365">Wybierz przycisk **Wartości** i przejrzyj nagłówki na karcie **Sieć.**</span><span class="sxs-lookup"><span data-stu-id="507a0-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="507a0-366">Wybierz przycisk **testu PUT.**</span><span class="sxs-lookup"><span data-stu-id="507a0-366">Select the **PUT test** button.</span></span> <span data-ttu-id="507a0-367">Zobacz [Wyświetlanie opcji opcji, aby](#options) uzyskać instrukcje dotyczące wyświetlania żądania OPCJE.</span><span class="sxs-lookup"><span data-stu-id="507a0-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="507a0-368">**Test PUT** tworzy dwa żądania, żądanie inspekcji wstępnej opcje i żądanie PUT.</span><span class="sxs-lookup"><span data-stu-id="507a0-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="507a0-369">Wybierz **`GetValues2 [DisableCors]`** przycisk, aby wyzwolić nieudane żądanie CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="507a0-370">Jak wspomniano w dokumencie, odpowiedź zwraca 200 sukcesów, ale żądanie CORS nie jest.</span><span class="sxs-lookup"><span data-stu-id="507a0-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="507a0-371">Wybierz kartę **Konsola,** aby wyświetlić błąd CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="507a0-372">W zależności od przeglądarki wyświetlany jest błąd podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="507a0-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="507a0-373">Dostęp do pobierania `'https://cors1.azurewebsites.net/api/values/GetValues2'` od `'https://cors3.azurewebsites.net'` źródła został zablokowany przez zasady CORS: W żądanym zasobie nie ma nagłówka "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="507a0-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="507a0-374">Jeśli nieprzezroczysta odpowiedź służy Twoim potrzebom, ustaw tryb żądania na "no-cors", aby pobrać zasób z wyłączonym mechanizmem CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="507a0-375">Punkty końcowe obsługujące mechanizm CORS można testować za pomocą narzędzia, takiego jak [curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)lub [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="507a0-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="507a0-376">W przypadku korzystania z narzędzia pochodzenie żądania `Origin` określonego przez nagłówek musi się różnić od hosta odbierającego żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="507a0-377">Jeśli żądanie nie jest *cross-origin* na podstawie `Origin` wartości nagłówka:</span><span class="sxs-lookup"><span data-stu-id="507a0-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="507a0-378">Nie ma potrzeby, aby oprogramowanie pośredniczące CORS przetwarzać żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="507a0-379">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="507a0-380">Następujące polecenie używa `curl` do wystawić żądanie OPTIONS z informacjami:</span><span class="sxs-lookup"><span data-stu-id="507a0-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="507a0-381">Testowanie cors z routingu punktu końcowego i [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="507a0-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="507a0-382">Włączenie mechanizmu CORS dla punktu `RequireCors` końcowego przy użyciu obecnie ***nie*** obsługuje [automatycznych żądań inspekcji wstępnej](#apf).</span><span class="sxs-lookup"><span data-stu-id="507a0-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="507a0-383">Należy wziąć pod uwagę następujący kod, który używa [routingu punktu końcowego, aby włączyć CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="507a0-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="507a0-384">Poniżej `TodoItems1Controller` przedstawiono punkty końcowe do testowania:</span><span class="sxs-lookup"><span data-stu-id="507a0-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="507a0-385">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=1) wdrożonego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="507a0-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="507a0-386">**Przyciski Delete [EnableCors]** i **GET [EnableCors]** powiodą się, ponieważ punkty końcowe mają `[EnableCors]` żądania inspekcji wstępnej i odpowiadają na nie.</span><span class="sxs-lookup"><span data-stu-id="507a0-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="507a0-387">Inne punkty końcowe kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="507a0-387">The other endpoints fails.</span></span> <span data-ttu-id="507a0-388">Przycisk **GET** kończy się niepowodzeniem, ponieważ [javascript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) wysyła:</span><span class="sxs-lookup"><span data-stu-id="507a0-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="507a0-389">Poniżej `TodoItems2Controller` przedstawiono podobne punkty końcowe, ale zawiera jawny kod, aby odpowiedzieć na żądania OPCJI:</span><span class="sxs-lookup"><span data-stu-id="507a0-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="507a0-390">Przetestuj poprzedni kod ze [strony testowej](https://cors1.azurewebsites.net/test?number=2) wdrożonego przykładu.</span><span class="sxs-lookup"><span data-stu-id="507a0-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="507a0-391">Na liście rozwijanej **Kontroler** wybierz pozycję **Inspekcji wstępnej,** a następnie **ustaw kontroler**.</span><span class="sxs-lookup"><span data-stu-id="507a0-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="507a0-392">Wszystkie wywołania CORS `TodoItems2Controller` do punktów końcowych zakończyć się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="507a0-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="507a0-393">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="507a0-393">Additional resources</span></span>

* [<span data-ttu-id="507a0-394">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="507a0-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="507a0-395">Wprowadzenie do modułu IIS CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="507a0-396">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="507a0-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="507a0-397">W tym artykule pokazano, jak włączyć usługę CORS w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="507a0-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="507a0-398">Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web.</span><span class="sxs-lookup"><span data-stu-id="507a0-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="507a0-399">To ograniczenie jest nazywane *zasadami tego samego pochodzenia*.</span><span class="sxs-lookup"><span data-stu-id="507a0-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="507a0-400">Zasady tego samego pochodzenia uniemożliwiają złośliwej witrynie odczytywanie poufnych danych z innej witryny.</span><span class="sxs-lookup"><span data-stu-id="507a0-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="507a0-401">Czasami możesz zezwolić innym witrynom na składanie żądań krzyżowych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="507a0-402">Aby uzyskać więcej informacji, zobacz [artykuł Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="507a0-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="507a0-403">[Udostępnianie zasobów między źródłami](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="507a0-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="507a0-404">Jest standardem W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="507a0-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="507a0-405">**Nie** jest to funkcja zabezpieczeń, CORS rozluźnia zabezpieczenia.</span><span class="sxs-lookup"><span data-stu-id="507a0-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="507a0-406">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="507a0-407">Aby uzyskać więcej informacji, zobacz [Jak działa CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="507a0-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="507a0-408">Umożliwia serwerowi jawne zezwalanie na niektóre żądania między źródłami, jednocześnie odrzucając inne.</span><span class="sxs-lookup"><span data-stu-id="507a0-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="507a0-409">Jest bezpieczniejsza i bardziej elastyczna niż wcześniejsze techniki, takie jak [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="507a0-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="507a0-410">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="507a0-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="507a0-411">To samo pochodzenie</span><span class="sxs-lookup"><span data-stu-id="507a0-411">Same origin</span></span>

<span data-ttu-id="507a0-412">Dwa adresy URL mają to samo pochodzenie, jeśli mają identyczne schematy, hosty i porty[(RFC 6454).](https://tools.ietf.org/html/rfc6454)</span><span class="sxs-lookup"><span data-stu-id="507a0-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="507a0-413">Te dwa adresy URL mają to samo źródło:</span><span class="sxs-lookup"><span data-stu-id="507a0-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="507a0-414">Te adresy URL mają inne źródła niż poprzednie dwa adresy URL:</span><span class="sxs-lookup"><span data-stu-id="507a0-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="507a0-415">`https://example.net`&ndash; Inna domena</span><span class="sxs-lookup"><span data-stu-id="507a0-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="507a0-416">`https://www.example.com/foo.html`&ndash; Inna poddomena</span><span class="sxs-lookup"><span data-stu-id="507a0-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="507a0-417">`http://example.com/foo.html`&ndash; Inny schemat</span><span class="sxs-lookup"><span data-stu-id="507a0-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="507a0-418">`https://example.com:9000/foo.html`&ndash; Inny port</span><span class="sxs-lookup"><span data-stu-id="507a0-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="507a0-419">Program Internet Explorer nie bierze pod uwagę portu podczas porównywania źródeł.</span><span class="sxs-lookup"><span data-stu-id="507a0-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="507a0-420">CORS z nazwanymi zasadami i oprogramowaniem pośredniczącym</span><span class="sxs-lookup"><span data-stu-id="507a0-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="507a0-421">Oprogramowanie pośredniczące CORS obsługuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="507a0-422">Poniższy kod umożliwia CORS dla całej aplikacji z określonym źródłem:</span><span class="sxs-lookup"><span data-stu-id="507a0-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="507a0-423">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="507a0-423">The preceding code:</span></span>

* <span data-ttu-id="507a0-424">Ustawia nazwę zasad\_na "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="507a0-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="507a0-425">Nazwa zasad jest dowolna.</span><span class="sxs-lookup"><span data-stu-id="507a0-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="507a0-426">Wywołuje <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodę rozszerzenia, która umożliwia CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="507a0-427">Wywołania <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> z [wyrażeniem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="507a0-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="507a0-428">Lambda bierze <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> obiekt.</span><span class="sxs-lookup"><span data-stu-id="507a0-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="507a0-429">[Opcje konfiguracji](#cors-policy-options), `WithOrigins`takie jak , są opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="507a0-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="507a0-430"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Wywołanie metody dodaje usługi CORS do kontenera usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="507a0-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="507a0-431">Aby uzyskać więcej informacji, zobacz [opcje zasad CORS](#cpo) w tym dokumencie .</span><span class="sxs-lookup"><span data-stu-id="507a0-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="507a0-432">Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> może łańcuch metod, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="507a0-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="507a0-433">Uwaga: Adres URL **nie** może zawierać`/`końcowego ukośnika ( ).</span><span class="sxs-lookup"><span data-stu-id="507a0-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="507a0-434">Jeśli adres URL `/`zostanie zakończony `false` na , porównanie zwraca się i nie zwracany jest żaden nagłówek.</span><span class="sxs-lookup"><span data-stu-id="507a0-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="507a0-435">Poniższy kod stosuje zasady CORS do wszystkich punktów końcowych aplikacji za pośrednictwem oprogramowania pośredniczącego CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="507a0-436">Uwaga: `UseCors` należy wywołać przed `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="507a0-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="507a0-437">Zobacz [Włączanie cors w razor strony, kontrolery i metody działania,](#ecors) aby zastosować zasady CORS na poziomie strony/kontrolera / akcji.</span><span class="sxs-lookup"><span data-stu-id="507a0-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="507a0-438">Zobacz [Test CORS](#test) instrukcje dotyczące testowania kodu podobnego do poprzedniego kodu.</span><span class="sxs-lookup"><span data-stu-id="507a0-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="507a0-439">Włącz cors z atrybutami</span><span class="sxs-lookup"><span data-stu-id="507a0-439">Enable CORS with attributes</span></span>

<span data-ttu-id="507a0-440">[EnableCors&rbrack; Atrybut stanowi alternatywę dla stosowania CORS globalnie. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="507a0-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="507a0-441">Atrybut `[EnableCors]` umożliwia CORS dla wybranych punktów końcowych, a nie wszystkich punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="507a0-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="507a0-442">Służy `[EnableCors]` do określania `[EnableCors("{Policy String}")]` zasad domyślnych i określania zasad.</span><span class="sxs-lookup"><span data-stu-id="507a0-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="507a0-443">Atrybut `[EnableCors]` można zastosować do:</span><span class="sxs-lookup"><span data-stu-id="507a0-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="507a0-444">Strona Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="507a0-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="507a0-445">Kontrolera</span><span class="sxs-lookup"><span data-stu-id="507a0-445">Controller</span></span>
* <span data-ttu-id="507a0-446">Metoda akcji kontrolera</span><span class="sxs-lookup"><span data-stu-id="507a0-446">Controller action method</span></span>

<span data-ttu-id="507a0-447">Można zastosować różne zasady do kontrolera/modelu `[EnableCors]` strony/akcji z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="507a0-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="507a0-448">Gdy `[EnableCors]` atrybut jest stosowany do controllers/page model/action method, a CORS jest włączona w oprogramowaniu pośredniczącym, ***obie*** zasady są stosowane.</span><span class="sxs-lookup"><span data-stu-id="507a0-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="507a0-449">Nie ***zalecamy*** łączenia zasad.</span><span class="sxs-lookup"><span data-stu-id="507a0-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="507a0-450">Użyj `[EnableCors]` atrybutu lub oprogramowania pośredniczącego, \***nie oba**.</span><span class="sxs-lookup"><span data-stu-id="507a0-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="507a0-451">Podczas `[EnableCors]`korzystania z programu **nie** należy definiować zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="507a0-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="507a0-452">Poniższy kod stosuje inną zasadę do każdej metody:</span><span class="sxs-lookup"><span data-stu-id="507a0-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="507a0-453">Poniższy kod tworzy domyślną zasadę `"AnotherPolicy"`CORS i zasadę o nazwie:</span><span class="sxs-lookup"><span data-stu-id="507a0-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="507a0-454">Wyłącz cors</span><span class="sxs-lookup"><span data-stu-id="507a0-454">Disable CORS</span></span>

<span data-ttu-id="507a0-455">Atrybut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) wyłącza cors dla kontrolera/page-model/action.</span><span class="sxs-lookup"><span data-stu-id="507a0-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="507a0-456">Warianty polityki CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-456">CORS policy options</span></span>

<span data-ttu-id="507a0-457">W tej sekcji opisano różne opcje, które można ustawić w zasadach CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="507a0-458">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="507a0-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="507a0-459">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="507a0-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="507a0-460">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="507a0-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="507a0-461">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="507a0-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="507a0-462">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="507a0-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="507a0-463">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="507a0-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>jest wywoływana w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="507a0-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="507a0-465">W przypadku niektórych opcji warto najpierw przeczytać sekcję [Jak działa CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="507a0-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="507a0-466">Ustawianie dozwolonych źródeł</span><span class="sxs-lookup"><span data-stu-id="507a0-466">Set the allowed origins</span></span>

<span data-ttu-id="507a0-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umożliwia żądania CORS ze wszystkich źródeł`http` `https`z dowolnym schematem ( lub ).</span><span class="sxs-lookup"><span data-stu-id="507a0-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="507a0-468">`AllowAnyOrigin`jest niezabezpieczona, ponieważ *każda witryna sieci Web* może składać żądania cross-origin do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="507a0-469">Określanie `AllowAnyOrigin` `AllowCredentials` i jest niezabezpieczona konfiguracja i może spowodować fałszerstwa żądań między lokacjami.</span><span class="sxs-lookup"><span data-stu-id="507a0-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="507a0-470">W przypadku bezpiecznej aplikacji określ dokładną listę źródeł, jeśli klient musi autoryzować się, aby uzyskać dostęp do zasobów serwera.</span><span class="sxs-lookup"><span data-stu-id="507a0-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="507a0-471">`AllowAnyOrigin`wpływa na żądania inspekcji `Access-Control-Allow-Origin` wstępnej i nagłówek.</span><span class="sxs-lookup"><span data-stu-id="507a0-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="507a0-472">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="507a0-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="507a0-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Ustawia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> właściwość zasad jako funkcję, która umożliwia początek, aby dopasować skonfigurowaną domenę symboli wieloznacznych podczas oceny, czy pochodzenie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="507a0-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="507a0-474">Ustawianie dozwolonych metod HTTP</span><span class="sxs-lookup"><span data-stu-id="507a0-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="507a0-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="507a0-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="507a0-476">Zezwala na dowolną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="507a0-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="507a0-477">Wpływa na żądania inspekcji `Access-Control-Allow-Methods` wstępnej i nagłówka.</span><span class="sxs-lookup"><span data-stu-id="507a0-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="507a0-478">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="507a0-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="507a0-479">Ustawianie nagłówków dozwolonych żądań</span><span class="sxs-lookup"><span data-stu-id="507a0-479">Set the allowed request headers</span></span>

<span data-ttu-id="507a0-480">Aby zezwolić na wysyłane określone nagłówki w żądaniu CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> nazywane *nagłówkami żądań autora*, zadzwoń i określ dozwolone nagłówki:</span><span class="sxs-lookup"><span data-stu-id="507a0-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="507a0-481">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>nagłówki żądań autora, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="507a0-482">To ustawienie ma wpływ na `Access-Control-Request-Headers` żądania inspekcji wstępnej i nagłówek.</span><span class="sxs-lookup"><span data-stu-id="507a0-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="507a0-483">Aby uzyskać więcej informacji, zobacz sekcję [Żądania inspekcji wstępnej.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="507a0-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="507a0-484">Oprogramowanie pośredniczące CORS zawsze `Access-Control-Request-Headers` umożliwia cztery nagłówki w celu wysłania niezależnie od wartości skonfigurowanych w CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="507a0-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="507a0-485">Ta lista nagłówków zawiera:</span><span class="sxs-lookup"><span data-stu-id="507a0-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="507a0-486">Rozważmy na przykład aplikację skonfigurowaną w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="507a0-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="507a0-487">Oprogramowanie pośredniczące cors odpowiada pomyślnie na żądanie inspekcji `Content-Language` wstępnej z następującym nagłówkiem żądania, ponieważ jest zawsze na białej liście:</span><span class="sxs-lookup"><span data-stu-id="507a0-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="507a0-488">Ustawianie nagłówków narażonych odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="507a0-488">Set the exposed response headers</span></span>

<span data-ttu-id="507a0-489">Domyślnie przeglądarka nie udostępnia wszystkie nagłówki odpowiedzi do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="507a0-490">Aby uzyskać więcej informacji, zobacz [Udostępnianie zasobów W3C Cross-Origin (Terminologia): Nagłówek prostej odpowiedzi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="507a0-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="507a0-491">Nagłówki odpowiedzi, które są domyślnie dostępne, to:</span><span class="sxs-lookup"><span data-stu-id="507a0-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="507a0-492">Specyfikacja CORS wywołuje te nagłówki *proste nagłówki odpowiedzi*.</span><span class="sxs-lookup"><span data-stu-id="507a0-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="507a0-493">Aby udostępnić aplikacji inne nagłówki, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>zadzwoń do:</span><span class="sxs-lookup"><span data-stu-id="507a0-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="507a0-494">Poświadczenia w żądaniach między źródłami</span><span class="sxs-lookup"><span data-stu-id="507a0-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="507a0-495">Poświadczenia wymagają specjalnej obsługi w żądaniu CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="507a0-496">Domyślnie przeglądarka nie wysyła poświadczeń z żądaniem cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="507a0-497">Poświadczenia obejmują pliki cookie i schematy uwierzytelniania HTTP.</span><span class="sxs-lookup"><span data-stu-id="507a0-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="507a0-498">Aby wysłać poświadczenia z żądaniem cross-origin, klient musi ustawić `XMLHttpRequest.withCredentials` na `true`.</span><span class="sxs-lookup"><span data-stu-id="507a0-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="507a0-499">Korzystanie `XMLHttpRequest` bezpośrednio:</span><span class="sxs-lookup"><span data-stu-id="507a0-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="507a0-500">Korzystanie z jQuery:</span><span class="sxs-lookup"><span data-stu-id="507a0-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="507a0-501">Korzystanie z [interfejsu API pobierania:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="507a0-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="507a0-502">Serwer musi zezwolić na poświadczenia.</span><span class="sxs-lookup"><span data-stu-id="507a0-502">The server must allow the credentials.</span></span> <span data-ttu-id="507a0-503">Aby zezwolić na poświadczenia <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>między źródłami, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="507a0-504">Odpowiedź HTTP zawiera `Access-Control-Allow-Credentials` nagłówek, który informuje przeglądarkę, że serwer zezwala na poświadczenia dla żądania między źródłami.</span><span class="sxs-lookup"><span data-stu-id="507a0-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="507a0-505">Jeśli przeglądarka wysyła poświadczenia, ale odpowiedź nie `Access-Control-Allow-Credentials` zawiera prawidłowego nagłówka, przeglądarka nie udostępnia odpowiedzi na aplikację, a żądanie między źródłami nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="507a0-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="507a0-506">Zezwalanie na poświadczenia między źródłami jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="507a0-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="507a0-507">Witryna sieci Web w innej domenie może wysyłać poświadczeń zalogowanego użytkownika do aplikacji w imieniu użytkownika bez wiedzy użytkownika.</span><span class="sxs-lookup"><span data-stu-id="507a0-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="507a0-508">Specyfikacja CORS stwierdza również, `"*"` że ustawienie początku (wszystkie `Access-Control-Allow-Credentials` źródła) jest nieprawidłowe, jeśli nagłówek jest obecny.</span><span class="sxs-lookup"><span data-stu-id="507a0-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="507a0-509">Żądania inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-509">Preflight requests</span></span>

<span data-ttu-id="507a0-510">W przypadku niektórych żądań CORS przeglądarka wysyła dodatkowe żądanie przed wysłaniem rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="507a0-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="507a0-511">To żądanie jest nazywane *żądaniem inspekcji wstępnej*.</span><span class="sxs-lookup"><span data-stu-id="507a0-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="507a0-512">Przeglądarka może pominąć żądanie inspekcji wstępnej, jeśli spełnione są następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="507a0-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="507a0-513">Metoda żądania to GET, HEAD lub POST.</span><span class="sxs-lookup"><span data-stu-id="507a0-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="507a0-514">Aplikacja nie ustawia nagłówków żądań `Accept` `Accept-Language`innych `Content-Language` `Content-Type`niż `Last-Event-ID`, , , lub .</span><span class="sxs-lookup"><span data-stu-id="507a0-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="507a0-515">Nagłówek, `Content-Type` jeśli jest ustawiony, ma jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="507a0-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="507a0-516">Reguła nagłówków żądań ustawiona dla żądania klienta ma zastosowanie `setRequestHeader` do `XMLHttpRequest` nagłówków ustawianych przez aplikację, wywołując obiekt.</span><span class="sxs-lookup"><span data-stu-id="507a0-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="507a0-517">Specyfikacja CORS wywołuje te nagłówki *nagłówków żądania autora nagłówków*.</span><span class="sxs-lookup"><span data-stu-id="507a0-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="507a0-518">Reguła nie ma zastosowania do nagłówków, które `User-Agent` `Host`przeglądarka `Content-Length`może ustawić, takich jak , lub .</span><span class="sxs-lookup"><span data-stu-id="507a0-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="507a0-519">Oto przykład żądania inspekcji wstępnej:</span><span class="sxs-lookup"><span data-stu-id="507a0-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="507a0-520">Żądanie przed lotem używa metody OPCJE HTTP.</span><span class="sxs-lookup"><span data-stu-id="507a0-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="507a0-521">Zawiera dwa specjalne nagłówki:</span><span class="sxs-lookup"><span data-stu-id="507a0-521">It includes two special headers:</span></span>

* <span data-ttu-id="507a0-522">`Access-Control-Request-Method`: Metoda HTTP, która będzie używana dla rzeczywistego żądania.</span><span class="sxs-lookup"><span data-stu-id="507a0-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="507a0-523">`Access-Control-Request-Headers`: Lista nagłówków żądań ustawianych przez aplikację w rzeczywistym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="507a0-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="507a0-524">Jak wspomniano wcześniej, nie obejmuje to nagłówków, które `User-Agent`ustawia przeglądarka, takich jak .</span><span class="sxs-lookup"><span data-stu-id="507a0-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="507a0-525">Gdy mechanizm CORS jest włączony z odpowiednią zasadą, ASP.NET Core zazwyczaj automatycznie odpowiada na żądania inspekcji wstępnej CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="507a0-526">Zobacz [atrybut [HttpOptions] dla żądań inspekcji wstępnej](#pro).</span><span class="sxs-lookup"><span data-stu-id="507a0-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="507a0-527">Żądanie inspekcji wstępnej `Access-Control-Request-Headers` cors może zawierać nagłówek, który wskazuje serwerowi nagłówki, które są wysyłane z rzeczywistym żądaniem.</span><span class="sxs-lookup"><span data-stu-id="507a0-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="507a0-528">Aby zezwolić na <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>określone nagłówki, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="507a0-529">Aby zezwolić na wszystkie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>nagłówki żądań autora, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="507a0-530">Przeglądarki nie są całkowicie spójne w `Access-Control-Request-Headers`sposobie ustawiania.</span><span class="sxs-lookup"><span data-stu-id="507a0-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="507a0-531">Jeśli nagłówki są ustawione na `"*"` coś <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>innego niż (lub `Accept` `Content-Type`użycie), `Origin`należy dołączyć co najmniej , i , plus wszystkie niestandardowe nagłówki, które chcesz obsługiwać.</span><span class="sxs-lookup"><span data-stu-id="507a0-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="507a0-532">Poniżej przedstawiono przykładową odpowiedź na żądanie inspekcji wstępnej (przy założeniu, że serwer zezwala na żądanie):</span><span class="sxs-lookup"><span data-stu-id="507a0-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="507a0-533">Odpowiedź zawiera `Access-Control-Allow-Methods` nagłówek, który zawiera listę dozwolonych `Access-Control-Allow-Headers` metod i opcjonalnie nagłówek, który zawiera listę dozwolonych nagłówków.</span><span class="sxs-lookup"><span data-stu-id="507a0-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="507a0-534">Jeśli żądanie inspekcji wstępnej zakończy się pomyślnie, przeglądarka wysyła rzeczywiste żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="507a0-535">Jeśli żądanie inspekcji wstępnej zostanie odrzucone, aplikacja zwraca odpowiedź *200 OK,* ale nie wysyła nagłówków CORS z powrotem.</span><span class="sxs-lookup"><span data-stu-id="507a0-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="507a0-536">W związku z tym przeglądarka nie próbuje żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="507a0-537">Ustawianie czasu wygaśnięcia inspekcji wstępnej</span><span class="sxs-lookup"><span data-stu-id="507a0-537">Set the preflight expiration time</span></span>

<span data-ttu-id="507a0-538">Nagłówek `Access-Control-Max-Age` określa, jak długo odpowiedź na żądanie inspekcji wstępnej może być buforowana.</span><span class="sxs-lookup"><span data-stu-id="507a0-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="507a0-539">Aby ustawić ten <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>nagłówek, zadzwoń:</span><span class="sxs-lookup"><span data-stu-id="507a0-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="507a0-540">Jak działa CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-540">How CORS works</span></span>

<span data-ttu-id="507a0-541">W tej sekcji opisano, co dzieje się w żądaniu [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na poziomie wiadomości HTTP.</span><span class="sxs-lookup"><span data-stu-id="507a0-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="507a0-542">CORS **nie** jest funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="507a0-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="507a0-543">CORS to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</span><span class="sxs-lookup"><span data-stu-id="507a0-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="507a0-544">Na przykład złośliwy aktor może użyć [prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) przeciwko witrynie i wykonać żądanie między witrynami do witryny obsługującej cors do kradzieży informacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="507a0-545">Interfejs API nie jest bezpieczniejsze, umożliwiając CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="507a0-546">To do klienta (przeglądarki), aby wymusić CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="507a0-547">Serwer wykonuje żądanie i zwraca odpowiedź, to klient zwraca błąd i blokuje odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="507a0-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="507a0-548">Na przykład dowolne z następujących narzędzi wyświetli odpowiedź serwera:</span><span class="sxs-lookup"><span data-stu-id="507a0-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="507a0-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="507a0-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="507a0-550">Postman</span><span class="sxs-lookup"><span data-stu-id="507a0-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="507a0-551">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="507a0-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="507a0-552">Przeglądarka internetowa, wprowadzając adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="507a0-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="507a0-553">Jest to sposób dla serwera, aby umożliwić przeglądarkom wykonać żądanie [XHR xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) lub [fetch api,](https://developer.mozilla.org/docs/Web/API/Fetch_API) które w przeciwnym razie byłyby zabronione.</span><span class="sxs-lookup"><span data-stu-id="507a0-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="507a0-554">Przeglądarki (bez CORS) nie mogą wykonywać żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="507a0-555">Przed [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) był używany do obejścia tego ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="507a0-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="507a0-556">JSONP nie używa XHR, używa `<script>` tagu do odbierania odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="507a0-557">Skrypty mogą być ładowane cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="507a0-558">Specyfikacja [CORS](https://www.w3.org/TR/cors/) wprowadziła kilka nowych nagłówków HTTP, które umożliwiają żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="507a0-559">Jeśli przeglądarka obsługuje cors, ustawia te nagłówki automatycznie dla żądań cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="507a0-560">Niestandardowy kod JavaScript nie jest wymagany do włączenia usługi CORS.</span><span class="sxs-lookup"><span data-stu-id="507a0-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="507a0-561">Poniżej przedstawiono przykład żądania cross-origin.</span><span class="sxs-lookup"><span data-stu-id="507a0-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="507a0-562">Nagłówek `Origin` udostępnia domenę witryny, która tworzy żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="507a0-563">Nagłówek `Origin` jest wymagany i musi być inny niż host.</span><span class="sxs-lookup"><span data-stu-id="507a0-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="507a0-564">Jeśli serwer zezwala na żądanie, `Access-Control-Allow-Origin` ustawia nagłówek w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="507a0-565">Wartość tego nagłówka jest `Origin` zgodna z nagłówkiem z żądania `"*"`lub jest wartością symboli wieloznacznych, co oznacza, że dowolne pochodzenie jest dozwolone:</span><span class="sxs-lookup"><span data-stu-id="507a0-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="507a0-566">Jeśli odpowiedź nie zawiera `Access-Control-Allow-Origin` nagłówka, żądanie między źródłami kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="507a0-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="507a0-567">W szczególności przeglądarka nie zezwala na żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="507a0-568">Nawet jeśli serwer zwraca pomyślną odpowiedź, przeglądarka nie udostępnia odpowiedzi do aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="507a0-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="507a0-569">Badanie CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-569">Test CORS</span></span>

<span data-ttu-id="507a0-570">Aby przetestować CORS:</span><span class="sxs-lookup"><span data-stu-id="507a0-570">To test CORS:</span></span>

1. <span data-ttu-id="507a0-571">[Tworzenie projektu interfejsu API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="507a0-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="507a0-572">Alternatywnie można [pobrać przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="507a0-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="507a0-573">Włącz CORS przy użyciu jednego z podejść w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="507a0-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="507a0-574">Przykład:</span><span class="sxs-lookup"><span data-stu-id="507a0-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="507a0-575">`WithOrigins("https://localhost:<port>");`należy używać tylko do testowania przykładowej aplikacji podobnej do [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="507a0-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="507a0-576">Utwórz projekt aplikacji internetowej (Razor Pages lub MVC).</span><span class="sxs-lookup"><span data-stu-id="507a0-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="507a0-577">W przykładzie użyto stron Razor.</span><span class="sxs-lookup"><span data-stu-id="507a0-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="507a0-578">Aplikację sieci web można utworzyć w tym samym rozwiązaniu co projekt interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="507a0-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="507a0-579">Dodaj następujący wyróżniony kod do pliku *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="507a0-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="507a0-580">W poprzednim kodzie `url: 'https://<web app>.azurewebsites.net/api/values/1',` zastąp adresem URL wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="507a0-581">Wdrażanie projektu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="507a0-581">Deploy the API project.</span></span> <span data-ttu-id="507a0-582">Na przykład [wdrożyć na platformie Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="507a0-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="507a0-583">Uruchom stronę Razor Pages lub aplikację MVC z pulpitu i kliknij przycisk **Testuj.**</span><span class="sxs-lookup"><span data-stu-id="507a0-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="507a0-584">Narzędzia F12 umożliwia przeglądanie komunikatów o błędach.</span><span class="sxs-lookup"><span data-stu-id="507a0-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="507a0-585">Usuń pochodzenie localhost `WithOrigins` z i wdrożyć aplikację.</span><span class="sxs-lookup"><span data-stu-id="507a0-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="507a0-586">Alternatywnie uruchom aplikację kliencką z innym portem.</span><span class="sxs-lookup"><span data-stu-id="507a0-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="507a0-587">Na przykład uruchom z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="507a0-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="507a0-588">Przetestuj za pomocą aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="507a0-588">Test with the client app.</span></span> <span data-ttu-id="507a0-589">Błędy mechanizmu CORS zwracają błąd, ale komunikat o błędzie nie jest dostępny w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="507a0-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="507a0-590">Użyj karty konsoli w narzędziach F12, aby zobaczyć błąd.</span><span class="sxs-lookup"><span data-stu-id="507a0-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="507a0-591">W zależności od przeglądarki pojawia się błąd (w konsoli narzędzi F12) podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="507a0-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="507a0-592">Korzystanie z przeglądarki Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="507a0-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="507a0-593">**SEC7120: [CORS] `https://localhost:44375` Źródło nie `https://localhost:44375` został odnaleźć w nagłówku odpowiedzi Access-Control-Allow-Origin dla zasobu między źródłami w`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="507a0-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="507a0-594">Korzystanie z Chrome:</span><span class="sxs-lookup"><span data-stu-id="507a0-594">Using Chrome:</span></span>

     <span data-ttu-id="507a0-595">**Dostęp do protokołu XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` u źródła `https://localhost:44375` został zablokowany przez zasady CORS: W żądanym zasobie nie ma nagłówka "Access-Control-Allow-Origin".**</span><span class="sxs-lookup"><span data-stu-id="507a0-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="507a0-596">Punkty końcowe obsługujące mechanizm CORS można testować za pomocą narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) lub [Listonosz.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="507a0-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="507a0-597">W przypadku korzystania z narzędzia pochodzenie żądania `Origin` określonego przez nagłówek musi się różnić od hosta odbierającego żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="507a0-598">Jeśli żądanie nie jest *cross-origin* na podstawie `Origin` wartości nagłówka:</span><span class="sxs-lookup"><span data-stu-id="507a0-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="507a0-599">Nie ma potrzeby, aby oprogramowanie pośredniczące CORS przetwarzać żądanie.</span><span class="sxs-lookup"><span data-stu-id="507a0-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="507a0-600">Nagłówki CORS nie są zwracane w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="507a0-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="507a0-601">CORS w iis</span><span class="sxs-lookup"><span data-stu-id="507a0-601">CORS in IIS</span></span>

<span data-ttu-id="507a0-602">Podczas wdrażania w usługach IIS mechanizm CORS musi być uruchomiony przed uwierzytelnianiem systemu Windows, jeśli serwer nie jest skonfigurowany do zezwalania na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="507a0-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="507a0-603">Aby obsługiwać ten scenariusz, [moduł IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) musi zostać zainstalowany i skonfigurowany dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="507a0-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="507a0-604">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="507a0-604">Additional resources</span></span>

* [<span data-ttu-id="507a0-605">Współużytkowanie zasobów między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="507a0-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="507a0-606">Wprowadzenie do modułu IIS CORS</span><span class="sxs-lookup"><span data-stu-id="507a0-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
