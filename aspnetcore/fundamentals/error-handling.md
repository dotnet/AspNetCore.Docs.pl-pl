---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak radzić sobie z błędami w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658817"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="a6145-103">Obsługa błędów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6145-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="a6145-104">Przez [Tom Dykstra](https://github.com/tdykstra/) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a6145-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a6145-105">W tym artykule omówiono typowe podejścia do obsługi błędów w ASP.NET podstawowych aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="a6145-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="a6145-106">Zobacz <xref:web-api/handle-errors> interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a6145-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="a6145-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a6145-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="a6145-108">(Jak[pobrać.)](xref:index#how-to-download-a-sample) Artykuł zawiera instrukcje dotyczące ustawiania dyrektyw preprocesora `#endif` `#define`(`#if`, , ) w przykładowej aplikacji, aby włączyć różne scenariusze.</span><span class="sxs-lookup"><span data-stu-id="a6145-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a6145-109">Strona wyjątku dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="a6145-109">Developer Exception Page</span></span>

<span data-ttu-id="a6145-110">Strona *wyjątków dewelopera* wyświetla szczegółowe informacje o wyjątkach żądań.</span><span class="sxs-lookup"><span data-stu-id="a6145-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="a6145-111">Strona jest udostępniana przez pakiet [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a6145-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a6145-112">Dodaj kod `Startup.Configure` do metody, aby włączyć stronę, gdy aplikacja jest uruchomiona w [środowisku programistycznym:](xref:fundamentals/environments)</span><span class="sxs-lookup"><span data-stu-id="a6145-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="a6145-113">Umieść wywołanie <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> przed każdym oprogramowaniem pośredniczącym, które chcesz złapać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="a6145-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="a6145-114">Włącz stronę wyjątku dewelopera **tylko wtedy, gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="a6145-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a6145-115">Nie chcesz udostępniać szczegółowych informacji o wyjątku publicznie, gdy aplikacja działa w produkcji.</span><span class="sxs-lookup"><span data-stu-id="a6145-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a6145-116">Aby uzyskać więcej informacji na <xref:fundamentals/environments>temat konfigurowania środowisk, zobacz .</span><span class="sxs-lookup"><span data-stu-id="a6145-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a6145-117">Strona zawiera następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="a6145-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="a6145-118">Śledzenie stosu</span><span class="sxs-lookup"><span data-stu-id="a6145-118">Stack trace</span></span>
* <span data-ttu-id="a6145-119">Parametry ciągu kwerendy (jeśli istnieją)</span><span class="sxs-lookup"><span data-stu-id="a6145-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="a6145-120">Pliki cookie (jeśli istnieją)</span><span class="sxs-lookup"><span data-stu-id="a6145-120">Cookies (if any)</span></span>
* <span data-ttu-id="a6145-121">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="a6145-121">Headers</span></span>

<span data-ttu-id="a6145-122">Aby wyświetlić stronę wyjątków dla deweloperów w [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj dyrektywy `DevEnvironment` preprocesora i wybierz pozycję **Wyzwalaj wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="a6145-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="a6145-123">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="a6145-123">Exception handler page</span></span>

<span data-ttu-id="a6145-124">Aby skonfigurować stronę niestandardowej obsługi błędów dla środowiska produkcyjnego, należy użyć oprogramowania pośredniczącego obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a6145-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="a6145-125">Oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="a6145-125">The middleware:</span></span>

* <span data-ttu-id="a6145-126">Połowy i dzienniki wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a6145-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="a6145-127">Ponownie wykonuje żądanie w alternatywnym potoku dla strony lub kontrolera wskazanego.</span><span class="sxs-lookup"><span data-stu-id="a6145-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="a6145-128">Żądanie nie jest ponownie wykonywane, jeśli odpowiedź została uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="a6145-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="a6145-129">W poniższym <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> przykładzie dodaje oprogramowanie pośredniczące obsługi wyjątków w środowiskach innych niż deweloperów:</span><span class="sxs-lookup"><span data-stu-id="a6145-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="a6145-130">Szablon aplikacji Razor Pages zawiera stronę Błąd *(cshtml)* i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę (`ErrorModel`) w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="a6145-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="a6145-131">W przypadku aplikacji MVC szablon projektu zawiera metodę akcji Błąd i widok błędu.</span><span class="sxs-lookup"><span data-stu-id="a6145-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="a6145-132">Oto metoda działania:</span><span class="sxs-lookup"><span data-stu-id="a6145-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="a6145-133">Nie oznaczaj metody akcji obsługi błędów atrybutami `HttpGet`metody HTTP, takimi jak .</span><span class="sxs-lookup"><span data-stu-id="a6145-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="a6145-134">Jawne zlecenia uniemożliwiają niektóre żądania dotarcia do metody.</span><span class="sxs-lookup"><span data-stu-id="a6145-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="a6145-135">Zezwalaj na anonimowy dostęp do metody, aby nieuwierzylicy użytkownicy mogli odbierać widok błędu.</span><span class="sxs-lookup"><span data-stu-id="a6145-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="a6145-136">Dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="a6145-136">Access the exception</span></span>

<span data-ttu-id="a6145-137">Służy <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> do uzyskiwania dostępu do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie obsługi błędów:</span><span class="sxs-lookup"><span data-stu-id="a6145-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="a6145-138">**Nie należy** podawać poufnych informacji o błędach dla klientów.</span><span class="sxs-lookup"><span data-stu-id="a6145-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="a6145-139">Błędy obsługi jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="a6145-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="a6145-140">Aby wyświetlić stronę obsługi wyjątków w `ProdEnvironment` `ErrorHandlerPage` [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj dyrektyw i preprocesora i wybierz **wyzwalanie wyjątku** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="a6145-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="a6145-141">Obsługa wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="a6145-141">Exception handler lambda</span></span>

<span data-ttu-id="a6145-142">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>zapewnienie lambda do .</span><span class="sxs-lookup"><span data-stu-id="a6145-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="a6145-143">Za pomocą lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a6145-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="a6145-144">Oto przykład użycia lambda do obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a6145-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="a6145-145">W poprzednim kodzie `await context.Response.WriteAsync(new string(' ', 512));` jest dodawany, więc przeglądarka Internet Explorer wyświetla komunikat o błędzie, a nie komunikat o błędzie IE.</span><span class="sxs-lookup"><span data-stu-id="a6145-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="a6145-146">Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="a6145-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="a6145-147">**Nie należy** podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> klientów ani <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> do klientów.</span><span class="sxs-lookup"><span data-stu-id="a6145-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="a6145-148">Błędy obsługi jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="a6145-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="a6145-149">Aby wyświetlić wynik obsługi wyjątków lambda w `ProdEnvironment` [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj dyrektyw i `ErrorHandlerLambda` preprocesora i wybierz **wyzwalacz wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="a6145-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="a6145-150">Strony UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="a6145-150">UseStatusCodePages</span></span>

<span data-ttu-id="a6145-151">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodu stanu dla kodów stanu HTTP, takich jak *404 - Nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="a6145-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="a6145-152">Aplikacja zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a6145-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="a6145-153">Aby udostępnić strony kodów stanu, użyj oprogramowania pośredniczącego Strony kodów stanu.</span><span class="sxs-lookup"><span data-stu-id="a6145-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="a6145-154">Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a6145-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a6145-155">Aby włączyć domyślne programy obsługi tekstu dla <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> typowych `Startup.Configure` kodów stanu błędów, wywołaj metodę:</span><span class="sxs-lookup"><span data-stu-id="a6145-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="a6145-156">Wywołanie `UseStatusCodePages` przed żądaniem obsługi oprogramowania pośredniczącego (na przykład static file middleware i MVC Middleware).</span><span class="sxs-lookup"><span data-stu-id="a6145-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="a6145-157">Oto przykład tekstu wyświetlanego przez domyślne programy obsługi:</span><span class="sxs-lookup"><span data-stu-id="a6145-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="a6145-158">Aby wyświetlić jeden z różnych formatów stron kodu stanu w [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj `StatusCodePages`jednej z dyrektyw preprocesora, które zaczynają się od programu , i wybierz **pozycję Wyzwalaj 404** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="a6145-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="a6145-159">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="a6145-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="a6145-160">Aby dostosować typ zawartości odpowiedzi i tekst, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> użyj przeciążenia, które przyjmuje typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="a6145-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="a6145-161">UseStatusCodePages z lambda</span><span class="sxs-lookup"><span data-stu-id="a6145-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="a6145-162">Aby określić niestandardowy kod obsługi błędów i <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> pisania odpowiedzi, należy użyć przeciążenia, które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="a6145-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="a6145-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="a6145-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="a6145-164">Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a6145-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="a6145-165">Wysyła *302 — znaleziony* kod stanu do klienta.</span><span class="sxs-lookup"><span data-stu-id="a6145-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="a6145-166">Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a6145-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="a6145-167">Szablon adresu URL `{0}` może zawierać symbol zastępczy kodu stanu, jak pokazano w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a6145-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="a6145-168">Jeśli szablon adresu URL zaczyna się od tyldy (~), tylda `PathBase`zostanie zastąpiona przez aplikację .</span><span class="sxs-lookup"><span data-stu-id="a6145-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="a6145-169">Jeśli wskażesz punkt końcowy w aplikacji, utwórz widok MVC lub stronę Razor dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="a6145-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a6145-170">Przykład strony razor można znaleźć na *stronach/statuscode.cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a6145-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="a6145-171">Ta metoda jest często używana, gdy aplikacja:</span><span class="sxs-lookup"><span data-stu-id="a6145-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="a6145-172">Należy przekierować klienta do innego punktu końcowego, zwykle w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="a6145-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="a6145-173">W przypadku aplikacji sieci web pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="a6145-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="a6145-174">Nie należy zachowywać i zwracać oryginalnego kodu stanu z początkową odpowiedzią przekierowania.</span><span class="sxs-lookup"><span data-stu-id="a6145-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="a6145-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="a6145-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="a6145-176">Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a6145-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="a6145-177">Zwraca oryginalny kod stanu do klienta.</span><span class="sxs-lookup"><span data-stu-id="a6145-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="a6145-178">Generuje treść odpowiedzi, wykonując ponownie potok żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="a6145-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="a6145-179">Jeśli wskażesz punkt końcowy w aplikacji, utwórz widok MVC lub stronę Razor dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="a6145-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a6145-180">Przykład strony razor można znaleźć na *stronach/statuscode.cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a6145-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="a6145-181">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="a6145-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="a6145-182">Przetwórz żądanie bez przekierowywania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="a6145-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="a6145-183">W przypadku aplikacji sieci web pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="a6145-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="a6145-184">Zachowaj i zwróć oryginalny kod stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="a6145-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="a6145-185">Szablony adresów URL i ciągów`{0}`zapytań mogą zawierać symbol zastępczy ( ) dla kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="a6145-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="a6145-186">Szablon adresu URL musi zaczynać się od ukośnika (`/`).</span><span class="sxs-lookup"><span data-stu-id="a6145-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="a6145-187">Korzystając z symbolu zastępczego w ścieżce, upewnij się, że punkt końcowy (strona lub kontroler) może przetwarzać segment ścieżki.</span><span class="sxs-lookup"><span data-stu-id="a6145-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="a6145-188">Na przykład razor page dla błędów należy zaakceptować `@page` wartość segmentu ścieżki opcjonalne z dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="a6145-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="a6145-189">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a6145-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="a6145-190">Wyłączanie stron kodów stanu</span><span class="sxs-lookup"><span data-stu-id="a6145-190">Disable status code pages</span></span>

<span data-ttu-id="a6145-191">Aby wyłączyć strony kodu stanu dla kontrolera MVC lub metody akcji, użyj atrybutu. [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute)</span><span class="sxs-lookup"><span data-stu-id="a6145-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="a6145-192">Aby wyłączyć strony kodu stanu dla określonych żądań w metodzie obsługi <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>stron Razor lub w kontrolerze MVC, należy użyć:</span><span class="sxs-lookup"><span data-stu-id="a6145-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="a6145-193">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="a6145-193">Exception-handling code</span></span>

<span data-ttu-id="a6145-194">Kod w strony obsługi wyjątków można zgłaszać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="a6145-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="a6145-195">Często dobrym pomysłem jest, aby strony błędów produkcyjnych składały się z zawartości czysto statycznej.</span><span class="sxs-lookup"><span data-stu-id="a6145-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="a6145-196">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="a6145-196">Response headers</span></span>

<span data-ttu-id="a6145-197">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a6145-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="a6145-198">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a6145-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="a6145-199">Nie można uruchomić żadnych stron wyjątków ani programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="a6145-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="a6145-200">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="a6145-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="a6145-201">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="a6145-201">Server exception handling</span></span>

<span data-ttu-id="a6145-202">Oprócz logiki obsługi wyjątków w aplikacji [implementacja serwera HTTP](xref:fundamentals/servers/index) może obsługiwać niektóre wyjątki.</span><span class="sxs-lookup"><span data-stu-id="a6145-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="a6145-203">Jeśli serwer złapie wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła *500 — Odpowiedź na wewnętrzny błąd serwera* bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a6145-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="a6145-204">Jeśli serwer złapie wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamyka połączenie.</span><span class="sxs-lookup"><span data-stu-id="a6145-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="a6145-205">Żądania, które nie są obsługiwane przez aplikację są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="a6145-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="a6145-206">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="a6145-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="a6145-207">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="a6145-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="a6145-208">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="a6145-208">Startup exception handling</span></span>

<span data-ttu-id="a6145-209">Tylko warstwa hostingu może obsługiwać wyjątki, które mają miejsce podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6145-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="a6145-210">Hosta można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="a6145-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="a6145-211">Warstwa hostingu może wyświetlać stronę błędu dla przechwyconego błędu uruchamiania tylko wtedy, gdy błąd występuje po powiązaniu adresu/portu hosta.</span><span class="sxs-lookup"><span data-stu-id="a6145-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="a6145-212">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="a6145-212">If binding fails:</span></span>

* <span data-ttu-id="a6145-213">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="a6145-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="a6145-214">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="a6145-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="a6145-215">Nie jest wyświetlana strona błędu, gdy serwer HTTP to [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a6145-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a6145-216">Podczas uruchamiania na [usługach IIS](/iis) (lub usłudze Azure App Service) lub [usługach IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)błąd *502.5 — błąd procesu* jest zwracany przez moduł ASP.NET [Core,](xref:host-and-deploy/aspnet-core-module) jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="a6145-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="a6145-217">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="a6145-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="a6145-218">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="a6145-218">Database error page</span></span>

<span data-ttu-id="a6145-219">Oprogramowanie pośredniczące strony błędów bazy danych przechwytuje wyjątki związane z bazą danych, które można rozwiązać przy użyciu migracji programu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="a6145-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="a6145-220">Gdy wystąpią te wyjątki, jest generowana odpowiedź HTML ze szczegółami możliwych akcji w celu rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="a6145-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="a6145-221">Ta strona powinna być włączona tylko w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="a6145-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="a6145-222">Włącz stronę, dodając `Startup.Configure`kod do:</span><span class="sxs-lookup"><span data-stu-id="a6145-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="a6145-223">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="a6145-223">Exception filters</span></span>

<span data-ttu-id="a6145-224">W aplikacjach MVC filtry wyjątków można skonfigurować globalnie lub na podstawie na kontroler lub na akcję.</span><span class="sxs-lookup"><span data-stu-id="a6145-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="a6145-225">W aplikacjach Razor Pages można je skonfigurować globalnie lub na model strony.</span><span class="sxs-lookup"><span data-stu-id="a6145-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="a6145-226">Filtry te obsługują wszelkie nieobsługiwał wyjątek, który występuje podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="a6145-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="a6145-227">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="a6145-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="a6145-228">Filtry wyjątków są przydatne w przypadku zalewkowania wyjątków, które występują w ramach akcji MVC, ale nie są tak elastyczne, jak oprogramowanie pośredniczące obsługi wyjątków.</span><span class="sxs-lookup"><span data-stu-id="a6145-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="a6145-229">Zalecamy użycie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="a6145-229">We recommend using the middleware.</span></span> <span data-ttu-id="a6145-230">Filtry należy używać tylko wtedy, gdy trzeba wykonać obsługę błędów inaczej na podstawie tego, na którym wybrano akcję MVC.</span><span class="sxs-lookup"><span data-stu-id="a6145-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="a6145-231">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="a6145-231">Model state errors</span></span>

<span data-ttu-id="a6145-232">Aby uzyskać informacje dotyczące obsługi błędów stanu modelu, zobacz [Powiązanie modelu](xref:mvc/models/model-binding) i [Sprawdzanie poprawności modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="a6145-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6145-233">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a6145-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
