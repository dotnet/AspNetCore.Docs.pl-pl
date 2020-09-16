---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Odkryj, jak obsługiwać błędy w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
uid: fundamentals/error-handling
ms.openlocfilehash: da7f50b27e447b86bd8a06851b767488d51b7050
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592894"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="b3898-103">Obsługa błędów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3898-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b3898-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b3898-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b3898-105">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3898-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="b3898-106">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b3898-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="b3898-107">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b3898-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="b3898-108">([Jak pobrać](xref:index#how-to-download-a-sample)) Karta Sieć w narzędziach deweloperskich w przeglądarce F12 jest przydatna podczas testowania przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3898-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b3898-109">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="b3898-109">Developer Exception Page</span></span>

<span data-ttu-id="b3898-110">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="b3898-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="b3898-111">Szablony ASP.NET Core generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="b3898-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="b3898-112">Poprzedni wyróżniony kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b3898-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b3898-113">Szablony są umieszczane <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> wcześnie w potoku pośredniczącym, dzięki czemu mogą przechwytywać wyjątki zgłoszone w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="b3898-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="b3898-114">Poprzedni kod włącza stronę wyjątku dewelopera ***tylko*** wtedy, gdy aplikacja jest uruchamiana w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b3898-114">The preceding code enables the Developer Exception Page ***only*** when the app runs in the Development environment.</span></span> <span data-ttu-id="b3898-115">Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="b3898-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="b3898-116">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="b3898-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b3898-117">Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="b3898-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="b3898-118">Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="b3898-118">Stack trace</span></span>
* <span data-ttu-id="b3898-119">Parametry ciągu zapytania, jeśli istnieją</span><span class="sxs-lookup"><span data-stu-id="b3898-119">Query string parameters if any</span></span>
* <span data-ttu-id="b3898-120">Cookies, jeśli jakieś</span><span class="sxs-lookup"><span data-stu-id="b3898-120">Cookies if any</span></span>
* <span data-ttu-id="b3898-121">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="b3898-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="b3898-122">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="b3898-122">Exception handler page</span></span>

<span data-ttu-id="b3898-123">Aby skonfigurować niestandardową stronę obsługi błędów dla [środowiska produkcyjnego](xref:fundamentals/environments), wywołaj polecenie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="b3898-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b3898-124">Ten wyjątek obsługuje oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="b3898-124">This exception handling middleware:</span></span>

* <span data-ttu-id="b3898-125">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b3898-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="b3898-126">Wykonuje ponowne żądanie w alternatywnym potoku przy użyciu wskazanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="b3898-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="b3898-127">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="b3898-128">Kod wygenerowany przez szablon ponownie wykonuje żądanie przy użyciu `/Error` ścieżki.</span><span class="sxs-lookup"><span data-stu-id="b3898-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="b3898-129">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="b3898-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="b3898-130">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="b3898-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="b3898-131">W przypadku aplikacji MVC szablon projektu zawiera `Error` metodę akcji i widok błędów dla kontrolera macierzystego.</span><span class="sxs-lookup"><span data-stu-id="b3898-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="b3898-132">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="b3898-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="b3898-133">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="b3898-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="b3898-134">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.</span><span class="sxs-lookup"><span data-stu-id="b3898-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="b3898-135">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="b3898-135">Access the exception</span></span>

<span data-ttu-id="b3898-136">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w programie obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="b3898-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="b3898-137">Poniższy kod dodaje `ExceptionMessage` do domyślnych *stron/Error. cshtml. cs* wygenerowanych przez szablony ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3898-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="b3898-138">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="b3898-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="b3898-139">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="b3898-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3898-140">Aby przetestować wyjątek w [aplikacji przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="b3898-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b3898-141">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="b3898-141">Set the environment to production.</span></span>
* <span data-ttu-id="b3898-142">Usuń Komentarze z `webBuilder.UseStartup<Startup>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b3898-143">Na stronie głównej wybierz opcję **Wyzwól wyjątek** .</span><span class="sxs-lookup"><span data-stu-id="b3898-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="b3898-144">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="b3898-144">Exception handler lambda</span></span>

<span data-ttu-id="b3898-145">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="b3898-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b3898-146">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="b3898-147">Poniższy kod używa wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="b3898-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="b3898-148">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="b3898-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="b3898-149">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="b3898-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3898-150">Aby przetestować obsługę wyjątków lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="b3898-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b3898-151">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="b3898-151">Set the environment to production.</span></span>
* <span data-ttu-id="b3898-152">Usuń Komentarze z `webBuilder.UseStartup<StartupLambda>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b3898-153">Na stronie głównej wybierz opcję **Wyzwól wyjątek** .</span><span class="sxs-lookup"><span data-stu-id="b3898-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="b3898-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b3898-154">UseStatusCodePages</span></span>

<span data-ttu-id="b3898-155">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu błędu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="b3898-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="b3898-156">Gdy aplikacja napotka warunek błędu HTTP 400-499, który nie ma treści, zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="b3898-157">Aby zapewnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="b3898-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="b3898-158">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="b3898-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="b3898-159">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="b3898-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="b3898-160">Na przykład, wywołaj `UseStatusCodePages` przed plikami statycznymi i punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="b3898-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="b3898-161">Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="b3898-162">Na przykład przechodzenie do `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="b3898-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="b3898-163">Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:</span><span class="sxs-lookup"><span data-stu-id="b3898-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="b3898-164">`UseStatusCodePages` zwykle nie jest używana w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="b3898-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b3898-165">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="b3898-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b3898-166">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="b3898-166">Set the environment to production.</span></span>
* <span data-ttu-id="b3898-167">Usuń Komentarze z `webBuilder.UseStartup<StartupUseStatusCodePages>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b3898-168">Wybierz linki na stronie głównej na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="b3898-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="b3898-169">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="b3898-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="b3898-170">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="b3898-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="b3898-171">W poprzednim kodzie, `{0}` jest symbolem zastępczym kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="b3898-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="b3898-172">`UseStatusCodePages` ciąg formatu nie jest zazwyczaj używany w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="b3898-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b3898-173">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupFormat>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="b3898-174">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="b3898-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="b3898-175">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="b3898-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="b3898-176">`UseStatusCodePages` wyrażenie lambda nie jest zwykle używane w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="b3898-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b3898-177">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupStatusLambda>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="b3898-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="b3898-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="b3898-179"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="b3898-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="b3898-180">Wysyła do klienta kod stanu [znaleziony przez 302](https://developer.mozilla.org/docs/Web/HTTP/Status/302) .</span><span class="sxs-lookup"><span data-stu-id="b3898-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="b3898-181">Przekierowuje klienta do punktu końcowego obsługi błędu podanego w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="b3898-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="b3898-182">Punkt końcowy obsługi błędów zazwyczaj wyświetla informacje o błędach i zwraca HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="b3898-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="b3898-183">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="b3898-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="b3898-184">Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="b3898-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="b3898-185">Podczas określania punktu końcowego w aplikacji Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-185">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3898-186">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/StatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="b3898-186">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b3898-187">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b3898-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="b3898-188">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="b3898-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="b3898-189">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="b3898-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="b3898-190">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="b3898-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="b3898-191">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCredirect>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="b3898-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="b3898-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="b3898-193"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="b3898-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="b3898-194">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="b3898-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="b3898-195">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="b3898-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="b3898-196">Jeśli określono punkt końcowy w aplikacji, należy utworzyć widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-196">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3898-197">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="b3898-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="b3898-198">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="b3898-198">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b3898-199">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="b3898-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="b3898-200">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="b3898-201">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="b3898-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="b3898-202">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="b3898-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="b3898-203">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy `{0}` kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="b3898-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="b3898-204">Szablon adresu URL musi zaczynać się od `/` .</span><span class="sxs-lookup"><span data-stu-id="b3898-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="b3898-205">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b3898-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="b3898-206">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="b3898-206">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b3898-207">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCreX>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b3898-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="b3898-208">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="b3898-208">Disable status code pages</span></span>

<span data-ttu-id="b3898-209">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, Użyj atrybutu [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b3898-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="b3898-210">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="b3898-210">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="b3898-211">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="b3898-211">Exception-handling code</span></span>

<span data-ttu-id="b3898-212">Kod na stronach obsługi wyjątków może również generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b3898-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="b3898-213">Strony błędów produkcyjnych należy dokładnie przetestować i zachować dodatkową ostrożność, aby uniknąć zgłaszania wyjątków.</span><span class="sxs-lookup"><span data-stu-id="b3898-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="b3898-214">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="b3898-214">Response headers</span></span>

<span data-ttu-id="b3898-215">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="b3898-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="b3898-216">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="b3898-217">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="b3898-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="b3898-218">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="b3898-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="b3898-219">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="b3898-219">Server exception handling</span></span>

<span data-ttu-id="b3898-220">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b3898-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="b3898-221">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła `500 - Internal Server Error` odpowiedź bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="b3898-222">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="b3898-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="b3898-223">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="b3898-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="b3898-224">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="b3898-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="b3898-225">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="b3898-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="b3898-226">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b3898-226">Startup exception handling</span></span>

<span data-ttu-id="b3898-227">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3898-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="b3898-228">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="b3898-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="b3898-229">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="b3898-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="b3898-230">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="b3898-230">If binding fails:</span></span>

* <span data-ttu-id="b3898-231">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="b3898-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="b3898-232">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="b3898-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="b3898-233">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="b3898-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b3898-234">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="b3898-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="b3898-235">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b3898-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="b3898-236">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3898-236">Database error page</span></span>

<span data-ttu-id="b3898-237">Filtr wyjątku strony dewelopera bazy danych `AddDatabaseDeveloperPageExceptionFilter` przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b3898-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="b3898-238">Kiedy te wyjątki wystąpią, odpowiedź HTML jest generowana z szczegółowymi informacjami o możliwych akcjach w celu rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="b3898-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="b3898-239">Ta strona jest włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b3898-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="b3898-240">Następujący kod został wygenerowany przez Razor Szablony stron ASP.NET Core po określeniu poszczególnych kont użytkowników:</span><span class="sxs-lookup"><span data-stu-id="b3898-240">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="b3898-241">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="b3898-241">Exception filters</span></span>

<span data-ttu-id="b3898-242">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="b3898-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="b3898-243">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="b3898-243">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="b3898-244">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="b3898-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="b3898-245">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="b3898-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="b3898-246">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne jak wbudowany [wyjątek obsługujący oprogramowanie pośredniczące](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="b3898-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="b3898-247">Zalecamy używanie `UseExceptionHandler` , chyba że konieczna jest obsługa błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="b3898-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="b3898-248">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="b3898-248">Model state errors</span></span>

<span data-ttu-id="b3898-249">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b3898-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3898-250">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b3898-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b3898-251">Autorzy  [Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b3898-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b3898-252">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b3898-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="b3898-253">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b3898-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="b3898-254">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b3898-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="b3898-255">([Jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b3898-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b3898-256">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="b3898-256">Developer Exception Page</span></span>

<span data-ttu-id="b3898-257">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="b3898-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="b3898-258">Szablony ASP.NET Core generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="b3898-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="b3898-259">Poprzedni kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b3898-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b3898-260">Szablony należy umieścić <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> przed jakimkolwiek oprogramowanie pośredniczące, dlatego wyjątki są przechwytywane w oprogramowaniu pośredniczącym poniżej.</span><span class="sxs-lookup"><span data-stu-id="b3898-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="b3898-261">Poprzedni kod włącza stronę wyjątku dewelopera tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="b3898-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="b3898-262">Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="b3898-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="b3898-263">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="b3898-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b3898-264">Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="b3898-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="b3898-265">Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="b3898-265">Stack trace</span></span>
* <span data-ttu-id="b3898-266">Parametry ciągu zapytania, jeśli istnieją</span><span class="sxs-lookup"><span data-stu-id="b3898-266">Query string parameters if any</span></span>
* <span data-ttu-id="b3898-267">Cookies, jeśli jakieś</span><span class="sxs-lookup"><span data-stu-id="b3898-267">Cookies if any</span></span>
* <span data-ttu-id="b3898-268">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="b3898-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="b3898-269">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="b3898-269">Exception handler page</span></span>

<span data-ttu-id="b3898-270">Aby skonfigurować niestandardową stronę obsługi błędów dla środowiska produkcyjnego, należy użyć wyjątku obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="b3898-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="b3898-271">Oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="b3898-271">The middleware:</span></span>

* <span data-ttu-id="b3898-272">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b3898-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="b3898-273">Wykonuje ponowne żądanie w alternatywnym potoku dla wskazanej strony lub kontrolera.</span><span class="sxs-lookup"><span data-stu-id="b3898-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="b3898-274">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="b3898-275">Kod wygenerowany przez szablon ponownie wykonuje żądanie do `/Error` .</span><span class="sxs-lookup"><span data-stu-id="b3898-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="b3898-276">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="b3898-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="b3898-277">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="b3898-277">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="b3898-278">W przypadku aplikacji MVC szablon projektu zawiera metodę akcji błędu i widok błędów w kontrolerze głównym.</span><span class="sxs-lookup"><span data-stu-id="b3898-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="b3898-279">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="b3898-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="b3898-280">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody.</span><span class="sxs-lookup"><span data-stu-id="b3898-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="b3898-281">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.</span><span class="sxs-lookup"><span data-stu-id="b3898-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="b3898-282">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="b3898-282">Access the exception</span></span>

<span data-ttu-id="b3898-283">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie procedury obsługi błędów:</span><span class="sxs-lookup"><span data-stu-id="b3898-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="b3898-284">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="b3898-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="b3898-285">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="b3898-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3898-286">Aby wyzwolić stronę obsługi przed wyjątkami, należy ustawić środowisko na produkcje i wymusić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="b3898-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="b3898-287">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="b3898-287">Exception handler lambda</span></span>

<span data-ttu-id="b3898-288">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="b3898-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b3898-289">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="b3898-290">Oto przykład użycia wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="b3898-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="b3898-291">W powyższym kodzie `await context.Response.WriteAsync(new string(' ', 512));` zostanie dodany, aby przeglądarka Internet Explorer wyświetlała komunikat o błędzie zamiast komunikatu o błędzie programu IE.</span><span class="sxs-lookup"><span data-stu-id="b3898-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="b3898-292">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16144)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="b3898-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="b3898-293">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="b3898-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="b3898-294">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="b3898-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="b3898-295">Aby zobaczyć wynik obsługi wyjątku lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj `ProdEnvironment` `ErrorHandlerLambda` dyrektyw i preprocesora, a następnie wybierz **Wyzwól wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="b3898-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="b3898-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b3898-296">UseStatusCodePages</span></span>

<span data-ttu-id="b3898-297">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="b3898-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="b3898-298">Aplikacja zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="b3898-299">Aby udostępnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="b3898-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="b3898-300">Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="b3898-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="b3898-301">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="b3898-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="b3898-302">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego (na przykład statycznych plików oprogramowania pośredniczącego i oprogramowania MVC).</span><span class="sxs-lookup"><span data-stu-id="b3898-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="b3898-303">Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="b3898-304">Na przykład przechodzenie do `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="b3898-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="b3898-305">Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:</span><span class="sxs-lookup"><span data-stu-id="b3898-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="b3898-306">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="b3898-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="b3898-307">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="b3898-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="b3898-308">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="b3898-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="b3898-309">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="b3898-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="b3898-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="b3898-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="b3898-311"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="b3898-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="b3898-312">Wysyła do klienta kod stanu *znaleziony przez 302* .</span><span class="sxs-lookup"><span data-stu-id="b3898-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="b3898-313">Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="b3898-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="b3898-314">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b3898-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="b3898-315">Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="b3898-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="b3898-316">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-316">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3898-317">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b3898-317">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="b3898-318">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b3898-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="b3898-319">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="b3898-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="b3898-320">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="b3898-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="b3898-321">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="b3898-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="b3898-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="b3898-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="b3898-323"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="b3898-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="b3898-324">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="b3898-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="b3898-325">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="b3898-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="b3898-326">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-326">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b3898-327">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="b3898-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="b3898-328">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b3898-328">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="b3898-329">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="b3898-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="b3898-330">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="b3898-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="b3898-331">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="b3898-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="b3898-332">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="b3898-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="b3898-333">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy ( `{0}` ) dla kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="b3898-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="b3898-334">Szablon adresu URL musi rozpoczynać się od ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="b3898-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="b3898-335">W przypadku używania symbolu zastępczego w ścieżce upewnij się, że punkt końcowy (strona lub kontroler) może przetworzyć segment ścieżki.</span><span class="sxs-lookup"><span data-stu-id="b3898-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="b3898-336">Na przykład Razor Strona dla błędów powinna akceptować opcjonalną wartość segmentu ścieżki przy użyciu `@page` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="b3898-336">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="b3898-337">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b3898-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="b3898-338">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="b3898-338">Disable status code pages</span></span>

<span data-ttu-id="b3898-339">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, użyj [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="b3898-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="b3898-340">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="b3898-340">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="b3898-341">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="b3898-341">Exception-handling code</span></span>

<span data-ttu-id="b3898-342">Kod na stronach obsługi wyjątków może generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b3898-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="b3898-343">Często dobrym pomysłem jest, aby strony błędów produkcyjnych obejmowały czysto statyczną zawartość.</span><span class="sxs-lookup"><span data-stu-id="b3898-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="b3898-344">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="b3898-344">Response headers</span></span>

<span data-ttu-id="b3898-345">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="b3898-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="b3898-346">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="b3898-347">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="b3898-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="b3898-348">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="b3898-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="b3898-349">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="b3898-349">Server exception handling</span></span>

<span data-ttu-id="b3898-350">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b3898-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="b3898-351">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła do *500 — wewnętrzny błąd serwera* bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b3898-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="b3898-352">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="b3898-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="b3898-353">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="b3898-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="b3898-354">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="b3898-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="b3898-355">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="b3898-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="b3898-356">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b3898-356">Startup exception handling</span></span>

<span data-ttu-id="b3898-357">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b3898-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="b3898-358">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="b3898-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="b3898-359">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="b3898-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="b3898-360">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="b3898-360">If binding fails:</span></span>

* <span data-ttu-id="b3898-361">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="b3898-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="b3898-362">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="b3898-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="b3898-363">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="b3898-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b3898-364">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="b3898-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="b3898-365">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b3898-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="b3898-366">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="b3898-366">Database error page</span></span>

<span data-ttu-id="b3898-367">Strona błędu bazy danych oprogramowanie pośredniczące przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b3898-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="b3898-368">Kiedy te wyjątki wystąpią, zostanie wygenerowana odpowiedź HTML z szczegółowymi działaniami umożliwiającymi rozwiązanie problemu.</span><span class="sxs-lookup"><span data-stu-id="b3898-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="b3898-369">Ta strona powinna być włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b3898-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="b3898-370">Włącz stronę, dodając kod do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b3898-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="b3898-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> wymaga pakietu NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="b3898-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="b3898-372">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="b3898-372">Exception filters</span></span>

<span data-ttu-id="b3898-373">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="b3898-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="b3898-374">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="b3898-374">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="b3898-375">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="b3898-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="b3898-376">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="b3898-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="b3898-377">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne, jak wyjątek obsługujący oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="b3898-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="b3898-378">Zalecamy używanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="b3898-378">We recommend using the middleware.</span></span> <span data-ttu-id="b3898-379">Używaj filtrów tylko wtedy, gdy musisz wykonać obsługę błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="b3898-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="b3898-380">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="b3898-380">Model state errors</span></span>

<span data-ttu-id="b3898-381">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b3898-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3898-382">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b3898-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
