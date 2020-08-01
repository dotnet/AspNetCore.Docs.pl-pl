---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Odkryj, jak obsługiwać błędy w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 7bc21901fe1e9ddf604abf3b5bfecdb8a319f12c
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444101"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="8cf38-103">Obsługa błędów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8cf38-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="8cf38-104">Autorzy [Dykstra](https://github.com/tdykstra/) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8cf38-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8cf38-105">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8cf38-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="8cf38-106">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8cf38-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="8cf38-107">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="8cf38-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="8cf38-108">([Jak pobrać](xref:index#how-to-download-a-sample)) Artykuł zawiera instrukcje dotyczące sposobu ustawiania dyrektyw preprocesora ( `#if` , `#endif` , `#define` ) w przykładowej aplikacji w celu włączenia różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="8cf38-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="8cf38-109">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="8cf38-109">Developer Exception Page</span></span>

<span data-ttu-id="8cf38-110">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="8cf38-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="8cf38-111">Strona jest udostępniana przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , który znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8cf38-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="8cf38-112">Dodaj kod do `Startup.Configure` metody, aby włączyć stronę, gdy aplikacja jest uruchomiona w [środowisku](xref:fundamentals/environments)deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="8cf38-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="8cf38-113">Umieść wywołanie <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> przed dowolnym oprogramowanie pośredniczące, które ma przechwytywać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="8cf38-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="8cf38-114">Stronę wyjątku dla deweloperów należy włączyć tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="8cf38-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="8cf38-115">Nie chcesz udostępniać szczegółowych informacji o wyjątku publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="8cf38-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="8cf38-116">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="8cf38-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8cf38-117">Na stronie znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="8cf38-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="8cf38-118">Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="8cf38-118">Stack trace</span></span>
* <span data-ttu-id="8cf38-119">Parametry ciągu zapytania (jeśli istnieją)</span><span class="sxs-lookup"><span data-stu-id="8cf38-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="8cf38-120">Pliki cookie (jeśli istnieją)</span><span class="sxs-lookup"><span data-stu-id="8cf38-120">Cookies (if any)</span></span>
* <span data-ttu-id="8cf38-121">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="8cf38-121">Headers</span></span>

<span data-ttu-id="8cf38-122">Aby wyświetlić stronę wyjątku dla deweloperów w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj `DevEnvironment` dyrektywy preprocesora i wybierz opcję **Wyzwól wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="8cf38-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="8cf38-123">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="8cf38-123">Exception handler page</span></span>

<span data-ttu-id="8cf38-124">Aby skonfigurować niestandardową stronę obsługi błędów dla środowiska produkcyjnego, należy użyć wyjątku obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="8cf38-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="8cf38-125">Oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="8cf38-125">The middleware:</span></span>

* <span data-ttu-id="8cf38-126">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="8cf38-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="8cf38-127">Wykonuje ponowne żądanie w alternatywnym potoku dla wskazanej strony lub kontrolera.</span><span class="sxs-lookup"><span data-stu-id="8cf38-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="8cf38-128">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8cf38-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="8cf38-129">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="8cf38-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="8cf38-130">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="8cf38-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="8cf38-131">W przypadku aplikacji MVC szablon projektu zawiera metodę akcji błędu i widok błędu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="8cf38-132">Oto Metoda działania:</span><span class="sxs-lookup"><span data-stu-id="8cf38-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="8cf38-133">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="8cf38-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="8cf38-134">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody.</span><span class="sxs-lookup"><span data-stu-id="8cf38-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="8cf38-135">Zezwalaj na anonimowy dostęp do metody, aby nieuwierzytelnionym użytkownikom mogli odebrać widok błędów.</span><span class="sxs-lookup"><span data-stu-id="8cf38-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="8cf38-136">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="8cf38-136">Access the exception</span></span>

<span data-ttu-id="8cf38-137">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie procedury obsługi błędów:</span><span class="sxs-lookup"><span data-stu-id="8cf38-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="8cf38-138">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="8cf38-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="8cf38-139">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="8cf38-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="8cf38-140">Aby wyświetlić stronę obsługa wyjątków w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj `ProdEnvironment` `ErrorHandlerPage` dyrektyw i preprocesora, a następnie wybierz opcję **Wyzwól wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="8cf38-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="8cf38-141">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="8cf38-141">Exception handler lambda</span></span>

<span data-ttu-id="8cf38-142">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="8cf38-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="8cf38-143">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8cf38-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="8cf38-144">Oto przykład użycia wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="8cf38-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="8cf38-145">W powyższym kodzie `await context.Response.WriteAsync(new string(' ', 512));` zostanie dodany, aby przeglądarka Internet Explorer wyświetlała komunikat o błędzie zamiast komunikatu o błędzie programu IE.</span><span class="sxs-lookup"><span data-stu-id="8cf38-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="8cf38-146">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16144)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="8cf38-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="8cf38-147">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="8cf38-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="8cf38-148">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="8cf38-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="8cf38-149">Aby zobaczyć wynik obsługi wyjątku lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj `ProdEnvironment` `ErrorHandlerLambda` dyrektyw i preprocesora, a następnie wybierz **Wyzwól wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="8cf38-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="8cf38-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="8cf38-150">UseStatusCodePages</span></span>

<span data-ttu-id="8cf38-151">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="8cf38-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="8cf38-152">Aplikacja zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8cf38-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="8cf38-153">Aby udostępnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="8cf38-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="8cf38-154">Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , który znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8cf38-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8cf38-155">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="8cf38-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="8cf38-156">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego (na przykład statycznych plików oprogramowania pośredniczącego i oprogramowania MVC).</span><span class="sxs-lookup"><span data-stu-id="8cf38-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="8cf38-157">Oto przykład tekstu wyświetlanego przez domyślne programy obsługi:</span><span class="sxs-lookup"><span data-stu-id="8cf38-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="8cf38-158">Aby wyświetlić jeden z różnych formatów stron kodu stanu w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), należy użyć jednej z dyrektyw preprocesora, która zaczyna się od `StatusCodePages` , i na stronie głównej wybierz pozycję **Wyzwól a 404** .</span><span class="sxs-lookup"><span data-stu-id="8cf38-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="8cf38-159">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="8cf38-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="8cf38-160">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="8cf38-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="8cf38-161">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="8cf38-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="8cf38-162">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="8cf38-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="8cf38-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="8cf38-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="8cf38-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="8cf38-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="8cf38-165">Wysyła do klienta kod stanu *znaleziony przez 302* .</span><span class="sxs-lookup"><span data-stu-id="8cf38-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="8cf38-166">Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="8cf38-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="8cf38-167">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8cf38-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="8cf38-168">Jeśli szablon adresu URL zaczyna się od tyldy (~), tylda jest zastępowana przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="8cf38-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="8cf38-169">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8cf38-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="8cf38-170">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="8cf38-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="8cf38-171">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8cf38-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="8cf38-172">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="8cf38-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="8cf38-173">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="8cf38-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="8cf38-174">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="8cf38-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="8cf38-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="8cf38-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="8cf38-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="8cf38-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="8cf38-177">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="8cf38-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="8cf38-178">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="8cf38-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="8cf38-179">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8cf38-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="8cf38-180">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="8cf38-181">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="8cf38-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="8cf38-182">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="8cf38-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="8cf38-183">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8cf38-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="8cf38-184">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="8cf38-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="8cf38-185">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="8cf38-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="8cf38-186">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy ( `{0}` ) dla kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="8cf38-187">Szablon adresu URL musi rozpoczynać się od ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="8cf38-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="8cf38-188">W przypadku używania symbolu zastępczego w ścieżce upewnij się, że punkt końcowy (strona lub kontroler) może przetworzyć segment ścieżki.</span><span class="sxs-lookup"><span data-stu-id="8cf38-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="8cf38-189">Na przykład Razor Strona dla błędów powinna akceptować opcjonalną wartość segmentu ścieżki przy użyciu `@page` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="8cf38-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="8cf38-190">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8cf38-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="8cf38-191">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="8cf38-191">Disable status code pages</span></span>

<span data-ttu-id="8cf38-192">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, użyj [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="8cf38-193">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="8cf38-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="8cf38-194">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="8cf38-194">Exception-handling code</span></span>

<span data-ttu-id="8cf38-195">Kod na stronach obsługi wyjątków może generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="8cf38-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="8cf38-196">Często dobrym pomysłem jest, aby strony błędów produkcyjnych obejmowały czysto statyczną zawartość.</span><span class="sxs-lookup"><span data-stu-id="8cf38-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="8cf38-197">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="8cf38-197">Response headers</span></span>

<span data-ttu-id="8cf38-198">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="8cf38-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="8cf38-199">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8cf38-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="8cf38-200">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="8cf38-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="8cf38-201">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="8cf38-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="8cf38-202">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="8cf38-202">Server exception handling</span></span>

<span data-ttu-id="8cf38-203">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="8cf38-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="8cf38-204">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła do *500 — wewnętrzny błąd serwera* bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8cf38-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="8cf38-205">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="8cf38-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="8cf38-206">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="8cf38-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="8cf38-207">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="8cf38-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="8cf38-208">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="8cf38-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="8cf38-209">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="8cf38-209">Startup exception handling</span></span>

<span data-ttu-id="8cf38-210">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8cf38-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="8cf38-211">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="8cf38-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="8cf38-212">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="8cf38-213">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="8cf38-213">If binding fails:</span></span>

* <span data-ttu-id="8cf38-214">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="8cf38-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="8cf38-215">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="8cf38-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="8cf38-216">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="8cf38-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="8cf38-217">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="8cf38-218">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="8cf38-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="8cf38-219">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="8cf38-219">Database error page</span></span>

<span data-ttu-id="8cf38-220">Strona błędu bazy danych oprogramowanie pośredniczące przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8cf38-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="8cf38-221">Kiedy te wyjątki wystąpią, zostanie wygenerowana odpowiedź HTML z szczegółowymi działaniami umożliwiającymi rozwiązanie problemu.</span><span class="sxs-lookup"><span data-stu-id="8cf38-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="8cf38-222">Ta strona powinna być włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="8cf38-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="8cf38-223">Włącz stronę, dodając kod do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8cf38-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="8cf38-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>wymaga pakietu NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="8cf38-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="8cf38-225">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="8cf38-225">Exception filters</span></span>

<span data-ttu-id="8cf38-226">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="8cf38-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="8cf38-227">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="8cf38-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="8cf38-228">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="8cf38-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="8cf38-229">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="8cf38-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="8cf38-230">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne, jak wyjątek obsługujący oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="8cf38-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="8cf38-231">Zalecamy używanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="8cf38-231">We recommend using the middleware.</span></span> <span data-ttu-id="8cf38-232">Używaj filtrów tylko wtedy, gdy musisz wykonać obsługę błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="8cf38-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="8cf38-233">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="8cf38-233">Model state errors</span></span>

<span data-ttu-id="8cf38-234">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="8cf38-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8cf38-235">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8cf38-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
