---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Odkryj, jak obsługiwać błędy w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753117"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="279a9-103">Obsługa błędów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="279a9-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="279a9-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="279a9-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="279a9-105">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="279a9-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="279a9-106">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="279a9-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="279a9-107">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="279a9-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="279a9-108">([Jak pobrać](xref:index#how-to-download-a-sample)) Karta Sieć w narzędziach deweloperskich w przeglądarce F12 jest przydatna podczas testowania przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="279a9-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="279a9-109">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="279a9-109">Developer Exception Page</span></span>

<span data-ttu-id="279a9-110">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="279a9-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="279a9-111">Szablony ASP.NET Core generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="279a9-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="279a9-112">Poprzedni wyróżniony kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="279a9-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="279a9-113">Szablony są umieszczane <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> wcześnie w potoku pośredniczącym, dzięki czemu mogą przechwytywać wyjątki zgłoszone w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="279a9-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="279a9-114">Poprzedni kod włącza stronę wyjątków dla deweloperów \***tylko** wtedy, gdy aplikacja działa w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="279a9-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="279a9-115">Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="279a9-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="279a9-116">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="279a9-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="279a9-117">Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="279a9-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="279a9-118">_ Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="279a9-118">_ Stack trace</span></span>
* <span data-ttu-id="279a9-119">Parametry ciągu zapytania, jeśli istnieją</span><span class="sxs-lookup"><span data-stu-id="279a9-119">Query string parameters if any</span></span>
* <span data-ttu-id="279a9-120">Cookies, jeśli jakieś</span><span class="sxs-lookup"><span data-stu-id="279a9-120">Cookies if any</span></span>
* <span data-ttu-id="279a9-121">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="279a9-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="279a9-122">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="279a9-122">Exception handler page</span></span>

<span data-ttu-id="279a9-123">Aby skonfigurować niestandardową stronę obsługi błędów dla [środowiska produkcyjnego](xref:fundamentals/environments), wywołaj polecenie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="279a9-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="279a9-124">Ten wyjątek obsługuje oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="279a9-124">This exception handling middleware:</span></span>

* <span data-ttu-id="279a9-125">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="279a9-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="279a9-126">Wykonuje ponowne żądanie w alternatywnym potoku przy użyciu wskazanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="279a9-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="279a9-127">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="279a9-128">Kod wygenerowany przez szablon ponownie wykonuje żądanie przy użyciu `/Error` ścieżki.</span><span class="sxs-lookup"><span data-stu-id="279a9-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="279a9-129">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="279a9-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="279a9-130">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="279a9-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="279a9-131">W przypadku aplikacji MVC szablon projektu zawiera `Error` metodę akcji i widok błędów dla kontrolera macierzystego.</span><span class="sxs-lookup"><span data-stu-id="279a9-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="279a9-132">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="279a9-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="279a9-133">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="279a9-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="279a9-134">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.</span><span class="sxs-lookup"><span data-stu-id="279a9-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="279a9-135">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="279a9-135">Access the exception</span></span>

<span data-ttu-id="279a9-136">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w programie obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="279a9-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="279a9-137">Poniższy kod dodaje `ExceptionMessage` do domyślnych *stron/Error. cshtml. cs* wygenerowanych przez szablony ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="279a9-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="279a9-138">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="279a9-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="279a9-139">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="279a9-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="279a9-140">Aby przetestować wyjątek w [aplikacji przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="279a9-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="279a9-141">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="279a9-141">Set the environment to production.</span></span>
* <span data-ttu-id="279a9-142">Usuń Komentarze z `webBuilder.UseStartup<Startup>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="279a9-143">Na stronie głównej wybierz opcję **Wyzwól wyjątek** .</span><span class="sxs-lookup"><span data-stu-id="279a9-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="279a9-144">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="279a9-144">Exception handler lambda</span></span>

<span data-ttu-id="279a9-145">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="279a9-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="279a9-146">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="279a9-147">Poniższy kod używa wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="279a9-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="279a9-148">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="279a9-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="279a9-149">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="279a9-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="279a9-150">Aby przetestować obsługę wyjątków lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="279a9-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="279a9-151">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="279a9-151">Set the environment to production.</span></span>
* <span data-ttu-id="279a9-152">Usuń Komentarze z `webBuilder.UseStartup<StartupLambda>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="279a9-153">Na stronie głównej wybierz opcję **Wyzwól wyjątek** .</span><span class="sxs-lookup"><span data-stu-id="279a9-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="279a9-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="279a9-154">UseStatusCodePages</span></span>

<span data-ttu-id="279a9-155">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu błędu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="279a9-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="279a9-156">Gdy aplikacja napotka kod stanu błędu HTTP 400-599, który nie ma treści, zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-156">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="279a9-157">Aby zapewnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="279a9-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="279a9-158">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="279a9-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="279a9-159">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="279a9-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="279a9-160">Na przykład, wywołaj `UseStatusCodePages` przed plikami statycznymi i punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="279a9-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="279a9-161">Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="279a9-162">Na przykład przechodzenie do `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="279a9-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="279a9-163">Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:</span><span class="sxs-lookup"><span data-stu-id="279a9-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="279a9-164">`UseStatusCodePages` zwykle nie jest używana w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="279a9-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="279a9-165">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="279a9-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="279a9-166">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="279a9-166">Set the environment to production.</span></span>
* <span data-ttu-id="279a9-167">Usuń Komentarze z `webBuilder.UseStartup<StartupUseStatusCodePages>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="279a9-168">Wybierz linki na stronie głównej na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="279a9-168">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="279a9-169">Oprogramowanie pośredniczące stron kodu **stanu nie przechwytuje** wyjątków.</span><span class="sxs-lookup"><span data-stu-id="279a9-169">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="279a9-170">Aby zapewnić niestandardową stronę obsługi błędów, użyj [strony obsługi wyjątków](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="279a9-170">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="279a9-171">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="279a9-171">UseStatusCodePages with format string</span></span>

<span data-ttu-id="279a9-172">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="279a9-172">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="279a9-173">W poprzednim kodzie, `{0}` jest symbolem zastępczym kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="279a9-173">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="279a9-174">`UseStatusCodePages` ciąg formatu nie jest zazwyczaj używany w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="279a9-174">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="279a9-175">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupFormat>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-175">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="279a9-176">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="279a9-176">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="279a9-177">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="279a9-177">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="279a9-178">`UseStatusCodePages` wyrażenie lambda nie jest zwykle używane w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="279a9-178">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="279a9-179">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupStatusLambda>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-179">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="279a9-180">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="279a9-180">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="279a9-181"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="279a9-181">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="279a9-182">Wysyła do klienta kod stanu [znaleziony przez 302](https://developer.mozilla.org/docs/Web/HTTP/Status/302) .</span><span class="sxs-lookup"><span data-stu-id="279a9-182">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="279a9-183">Przekierowuje klienta do punktu końcowego obsługi błędu podanego w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="279a9-183">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="279a9-184">Punkt końcowy obsługi błędów zazwyczaj wyświetla informacje o błędach i zwraca HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="279a9-184">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="279a9-185">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="279a9-185">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="279a9-186">Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="279a9-186">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="279a9-187">Podczas określania punktu końcowego w aplikacji Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-187">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="279a9-188">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/StatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="279a9-188">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="279a9-189">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="279a9-189">This method is commonly used when the app:</span></span>

* <span data-ttu-id="279a9-190">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="279a9-190">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="279a9-191">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="279a9-191">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="279a9-192">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="279a9-192">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="279a9-193">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCredirect>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-193">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="279a9-194">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="279a9-194">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="279a9-195"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="279a9-195">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="279a9-196">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="279a9-196">Returns the original status code to the client.</span></span>
* <span data-ttu-id="279a9-197">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="279a9-197">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="279a9-198">Jeśli określono punkt końcowy w aplikacji, należy utworzyć widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-198">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="279a9-199">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="279a9-199">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="279a9-200">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="279a9-200">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="279a9-201">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="279a9-201">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="279a9-202">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-202">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="279a9-203">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="279a9-203">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="279a9-204">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="279a9-204">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="279a9-205">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy `{0}` kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="279a9-205">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="279a9-206">Szablon adresu URL musi zaczynać się od `/` .</span><span class="sxs-lookup"><span data-stu-id="279a9-206">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="279a9-207">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="279a9-207">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="279a9-208">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="279a9-208">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="279a9-209">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCreX>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="279a9-209">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="279a9-210">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="279a9-210">Disable status code pages</span></span>

<span data-ttu-id="279a9-211">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, Użyj atrybutu [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="279a9-211">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="279a9-212">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="279a9-212">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="279a9-213">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="279a9-213">Exception-handling code</span></span>

<span data-ttu-id="279a9-214">Kod na stronach obsługi wyjątków może również generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="279a9-214">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="279a9-215">Strony błędów produkcyjnych należy dokładnie przetestować i zachować dodatkową ostrożność, aby uniknąć zgłaszania wyjątków.</span><span class="sxs-lookup"><span data-stu-id="279a9-215">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="279a9-216">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="279a9-216">Response headers</span></span>

<span data-ttu-id="279a9-217">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="279a9-217">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="279a9-218">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-218">The app can't change the response's status code.</span></span>
* <span data-ttu-id="279a9-219">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="279a9-219">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="279a9-220">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="279a9-220">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="279a9-221">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="279a9-221">Server exception handling</span></span>

<span data-ttu-id="279a9-222">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="279a9-222">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="279a9-223">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła `500 - Internal Server Error` odpowiedź bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-223">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="279a9-224">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="279a9-224">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="279a9-225">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="279a9-225">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="279a9-226">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="279a9-226">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="279a9-227">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="279a9-227">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="279a9-228">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="279a9-228">Startup exception handling</span></span>

<span data-ttu-id="279a9-229">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="279a9-229">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="279a9-230">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="279a9-230">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="279a9-231">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="279a9-231">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="279a9-232">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="279a9-232">If binding fails:</span></span>

* <span data-ttu-id="279a9-233">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="279a9-233">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="279a9-234">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="279a9-234">The dotnet process crashes.</span></span>
* <span data-ttu-id="279a9-235">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="279a9-235">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="279a9-236">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="279a9-236">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="279a9-237">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="279a9-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="279a9-238">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="279a9-238">Database error page</span></span>

<span data-ttu-id="279a9-239">Filtr wyjątku strony dewelopera bazy danych `AddDatabaseDeveloperPageExceptionFilter` przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="279a9-239">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="279a9-240">Kiedy te wyjątki wystąpią, odpowiedź HTML jest generowana z szczegółowymi informacjami o możliwych akcjach w celu rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="279a9-240">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="279a9-241">Ta strona jest włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="279a9-241">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="279a9-242">Następujący kod został wygenerowany przez Razor Szablony stron ASP.NET Core po określeniu poszczególnych kont użytkowników:</span><span class="sxs-lookup"><span data-stu-id="279a9-242">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="279a9-243">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="279a9-243">Exception filters</span></span>

<span data-ttu-id="279a9-244">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="279a9-244">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="279a9-245">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="279a9-245">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="279a9-246">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="279a9-246">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="279a9-247">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="279a9-247">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="279a9-248">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne jak wbudowany [wyjątek obsługujący oprogramowanie pośredniczące](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="279a9-248">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="279a9-249">Zalecamy używanie `UseExceptionHandler` , chyba że konieczna jest obsługa błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="279a9-249">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="279a9-250">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="279a9-250">Model state errors</span></span>

<span data-ttu-id="279a9-251">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="279a9-251">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="279a9-252">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="279a9-252">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="279a9-253">Autorzy  [Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="279a9-253">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="279a9-254">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="279a9-254">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="279a9-255">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="279a9-255">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="279a9-256">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="279a9-256">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="279a9-257">([Jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="279a9-257">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="279a9-258">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="279a9-258">Developer Exception Page</span></span>

<span data-ttu-id="279a9-259">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="279a9-259">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="279a9-260">Szablony ASP.NET Core generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="279a9-260">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="279a9-261">Poprzedni kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="279a9-261">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="279a9-262">Szablony należy umieścić <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> przed jakimkolwiek oprogramowanie pośredniczące, dlatego wyjątki są przechwytywane w oprogramowaniu pośredniczącym poniżej.</span><span class="sxs-lookup"><span data-stu-id="279a9-262">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="279a9-263">Poprzedni kod włącza stronę wyjątku dewelopera tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="279a9-263">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="279a9-264">Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="279a9-264">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="279a9-265">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="279a9-265">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="279a9-266">Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="279a9-266">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="279a9-267">Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="279a9-267">Stack trace</span></span>
* <span data-ttu-id="279a9-268">Parametry ciągu zapytania, jeśli istnieją</span><span class="sxs-lookup"><span data-stu-id="279a9-268">Query string parameters if any</span></span>
* <span data-ttu-id="279a9-269">Cookies, jeśli jakieś</span><span class="sxs-lookup"><span data-stu-id="279a9-269">Cookies if any</span></span>
* <span data-ttu-id="279a9-270">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="279a9-270">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="279a9-271">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="279a9-271">Exception handler page</span></span>

<span data-ttu-id="279a9-272">Aby skonfigurować niestandardową stronę obsługi błędów dla środowiska produkcyjnego, należy użyć wyjątku obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="279a9-272">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="279a9-273">Oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="279a9-273">The middleware:</span></span>

* <span data-ttu-id="279a9-274">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="279a9-274">Catches and logs exceptions.</span></span>
* <span data-ttu-id="279a9-275">Wykonuje ponowne żądanie w alternatywnym potoku dla wskazanej strony lub kontrolera.</span><span class="sxs-lookup"><span data-stu-id="279a9-275">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="279a9-276">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-276">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="279a9-277">Kod wygenerowany przez szablon ponownie wykonuje żądanie do `/Error` .</span><span class="sxs-lookup"><span data-stu-id="279a9-277">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="279a9-278">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="279a9-278">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="279a9-279">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="279a9-279">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="279a9-280">W przypadku aplikacji MVC szablon projektu zawiera metodę akcji błędu i widok błędów w kontrolerze głównym.</span><span class="sxs-lookup"><span data-stu-id="279a9-280">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="279a9-281">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="279a9-281">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="279a9-282">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody.</span><span class="sxs-lookup"><span data-stu-id="279a9-282">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="279a9-283">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.</span><span class="sxs-lookup"><span data-stu-id="279a9-283">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="279a9-284">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="279a9-284">Access the exception</span></span>

<span data-ttu-id="279a9-285">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie procedury obsługi błędów:</span><span class="sxs-lookup"><span data-stu-id="279a9-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="279a9-286">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="279a9-286">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="279a9-287">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="279a9-287">Serving errors is a security risk.</span></span>

<span data-ttu-id="279a9-288">Aby wyzwolić stronę obsługi przed wyjątkami, należy ustawić środowisko na produkcje i wymusić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="279a9-288">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="279a9-289">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="279a9-289">Exception handler lambda</span></span>

<span data-ttu-id="279a9-290">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="279a9-290">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="279a9-291">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-291">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="279a9-292">Oto przykład użycia wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="279a9-292">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="279a9-293">W powyższym kodzie `await context.Response.WriteAsync(new string(' ', 512));` zostanie dodany, aby przeglądarka Internet Explorer wyświetlała komunikat o błędzie zamiast komunikatu o błędzie programu IE.</span><span class="sxs-lookup"><span data-stu-id="279a9-293">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="279a9-294">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16144)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="279a9-294">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="279a9-295">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="279a9-295">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="279a9-296">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="279a9-296">Serving errors is a security risk.</span></span>

<span data-ttu-id="279a9-297">Aby zobaczyć wynik obsługi wyjątku lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj `ProdEnvironment` `ErrorHandlerLambda` dyrektyw i preprocesora, a następnie wybierz **Wyzwól wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="279a9-297">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="279a9-298">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="279a9-298">UseStatusCodePages</span></span>

<span data-ttu-id="279a9-299">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="279a9-299">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="279a9-300">Aplikacja zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-300">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="279a9-301">Aby udostępnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="279a9-301">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="279a9-302">Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="279a9-302">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="279a9-303">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="279a9-303">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="279a9-304">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego (na przykład statycznych plików oprogramowania pośredniczącego i oprogramowania MVC).</span><span class="sxs-lookup"><span data-stu-id="279a9-304">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="279a9-305">Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-305">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="279a9-306">Na przykład przechodzenie do `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="279a9-306">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="279a9-307">Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:</span><span class="sxs-lookup"><span data-stu-id="279a9-307">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="279a9-308">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="279a9-308">UseStatusCodePages with format string</span></span>

<span data-ttu-id="279a9-309">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="279a9-309">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="279a9-310">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="279a9-310">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="279a9-311">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="279a9-311">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="279a9-312">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="279a9-312">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="279a9-313"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="279a9-313">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="279a9-314">Wysyła do klienta kod stanu *znaleziony przez 302* .</span><span class="sxs-lookup"><span data-stu-id="279a9-314">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="279a9-315">Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="279a9-315">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="279a9-316">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="279a9-316">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="279a9-317">Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="279a9-317">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="279a9-318">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-318">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="279a9-319">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="279a9-319">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="279a9-320">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="279a9-320">This method is commonly used when the app:</span></span>

* <span data-ttu-id="279a9-321">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="279a9-321">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="279a9-322">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="279a9-322">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="279a9-323">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="279a9-323">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="279a9-324">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="279a9-324">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="279a9-325"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="279a9-325">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="279a9-326">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="279a9-326">Returns the original status code to the client.</span></span>
* <span data-ttu-id="279a9-327">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="279a9-327">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="279a9-328">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-328">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="279a9-329">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="279a9-329">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="279a9-330">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="279a9-330">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="279a9-331">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="279a9-331">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="279a9-332">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="279a9-332">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="279a9-333">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="279a9-333">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="279a9-334">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="279a9-334">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="279a9-335">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy ( `{0}` ) dla kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="279a9-335">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="279a9-336">Szablon adresu URL musi rozpoczynać się od ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="279a9-336">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="279a9-337">W przypadku używania symbolu zastępczego w ścieżce upewnij się, że punkt końcowy (strona lub kontroler) może przetworzyć segment ścieżki.</span><span class="sxs-lookup"><span data-stu-id="279a9-337">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="279a9-338">Na przykład Razor Strona dla błędów powinna akceptować opcjonalną wartość segmentu ścieżki przy użyciu `@page` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="279a9-338">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="279a9-339">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="279a9-339">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="279a9-340">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="279a9-340">Disable status code pages</span></span>

<span data-ttu-id="279a9-341">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, użyj [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="279a9-341">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="279a9-342">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="279a9-342">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="279a9-343">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="279a9-343">Exception-handling code</span></span>

<span data-ttu-id="279a9-344">Kod na stronach obsługi wyjątków może generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="279a9-344">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="279a9-345">Często dobrym pomysłem jest, aby strony błędów produkcyjnych obejmowały czysto statyczną zawartość.</span><span class="sxs-lookup"><span data-stu-id="279a9-345">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="279a9-346">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="279a9-346">Response headers</span></span>

<span data-ttu-id="279a9-347">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="279a9-347">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="279a9-348">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-348">The app can't change the response's status code.</span></span>
* <span data-ttu-id="279a9-349">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="279a9-349">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="279a9-350">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="279a9-350">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="279a9-351">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="279a9-351">Server exception handling</span></span>

<span data-ttu-id="279a9-352">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="279a9-352">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="279a9-353">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła do *500 — wewnętrzny błąd serwera* bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="279a9-353">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="279a9-354">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="279a9-354">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="279a9-355">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="279a9-355">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="279a9-356">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="279a9-356">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="279a9-357">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="279a9-357">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="279a9-358">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="279a9-358">Startup exception handling</span></span>

<span data-ttu-id="279a9-359">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="279a9-359">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="279a9-360">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="279a9-360">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="279a9-361">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="279a9-361">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="279a9-362">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="279a9-362">If binding fails:</span></span>

* <span data-ttu-id="279a9-363">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="279a9-363">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="279a9-364">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="279a9-364">The dotnet process crashes.</span></span>
* <span data-ttu-id="279a9-365">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="279a9-365">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="279a9-366">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="279a9-366">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="279a9-367">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="279a9-367">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="279a9-368">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="279a9-368">Database error page</span></span>

<span data-ttu-id="279a9-369">Strona błędu bazy danych oprogramowanie pośredniczące przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="279a9-369">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="279a9-370">Kiedy te wyjątki wystąpią, zostanie wygenerowana odpowiedź HTML z szczegółowymi działaniami umożliwiającymi rozwiązanie problemu.</span><span class="sxs-lookup"><span data-stu-id="279a9-370">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="279a9-371">Ta strona powinna być włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="279a9-371">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="279a9-372">Włącz stronę, dodając kod do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="279a9-372">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="279a9-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> wymaga pakietu NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="279a9-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="279a9-374">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="279a9-374">Exception filters</span></span>

<span data-ttu-id="279a9-375">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="279a9-375">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="279a9-376">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="279a9-376">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="279a9-377">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="279a9-377">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="279a9-378">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="279a9-378">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="279a9-379">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne, jak wyjątek obsługujący oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="279a9-379">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="279a9-380">Zalecamy używanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="279a9-380">We recommend using the middleware.</span></span> <span data-ttu-id="279a9-381">Używaj filtrów tylko wtedy, gdy musisz wykonać obsługę błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="279a9-381">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="279a9-382">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="279a9-382">Model state errors</span></span>

<span data-ttu-id="279a9-383">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="279a9-383">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="279a9-384">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="279a9-384">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
