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
ms.openlocfilehash: 1687195bc5d80d50289db51a5e8ac6113ca8b05f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586127"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="5702f-103">Obsługa błędów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5702f-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5702f-104">Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5702f-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5702f-105">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5702f-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="5702f-106">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5702f-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="5702f-107">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5702f-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="5702f-108">([Jak pobrać](xref:index#how-to-download-a-sample)) Karta Sieć w narzędziach deweloperskich w przeglądarce F12 jest przydatna podczas testowania przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5702f-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="5702f-109">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="5702f-109">Developer Exception Page</span></span>

<span data-ttu-id="5702f-110">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="5702f-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="5702f-111">Szablony ASP.NET Core generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="5702f-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="5702f-112">Poprzedni wyróżniony kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5702f-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5702f-113">Szablony są umieszczane <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> wcześnie w potoku pośredniczącym, dzięki czemu mogą przechwytywać wyjątki zgłoszone w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="5702f-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="5702f-114">Poprzedni kod włącza stronę wyjątku dewelopera ***tylko*** wtedy, gdy aplikacja jest uruchamiana w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="5702f-114">The preceding code enables the Developer Exception Page ***only*** when the app runs in the Development environment.</span></span> <span data-ttu-id="5702f-115">Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="5702f-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="5702f-116">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="5702f-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5702f-117">Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="5702f-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="5702f-118">Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="5702f-118">Stack trace</span></span>
* <span data-ttu-id="5702f-119">Parametry ciągu zapytania, jeśli istnieją</span><span class="sxs-lookup"><span data-stu-id="5702f-119">Query string parameters if any</span></span>
* <span data-ttu-id="5702f-120">Cookies, jeśli jakieś</span><span class="sxs-lookup"><span data-stu-id="5702f-120">Cookies if any</span></span>
* <span data-ttu-id="5702f-121">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="5702f-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="5702f-122">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="5702f-122">Exception handler page</span></span>

<span data-ttu-id="5702f-123">Aby skonfigurować niestandardową stronę obsługi błędów dla [środowiska produkcyjnego](xref:fundamentals/environments), wywołaj polecenie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="5702f-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="5702f-124">Ten wyjątek obsługuje oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="5702f-124">This exception handling middleware:</span></span>

* <span data-ttu-id="5702f-125">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5702f-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="5702f-126">Wykonuje ponowne żądanie w alternatywnym potoku przy użyciu wskazanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5702f-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="5702f-127">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="5702f-128">Kod wygenerowany przez szablon ponownie wykonuje żądanie przy użyciu `/Error` ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5702f-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="5702f-129">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="5702f-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="5702f-130">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="5702f-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="5702f-131">W przypadku aplikacji MVC szablon projektu zawiera `Error` metodę akcji i widok błędów dla kontrolera macierzystego.</span><span class="sxs-lookup"><span data-stu-id="5702f-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="5702f-132">Wyjątek obsługujący oprogramowanie pośredniczące ponowne wykonuje żądanie przy użyciu *oryginalnej* metody http.</span><span class="sxs-lookup"><span data-stu-id="5702f-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="5702f-133">Jeśli punkt końcowy programu obsługi błędów jest ograniczony do określonego zestawu metod HTTP, działa tylko dla tych metod HTTP.</span><span class="sxs-lookup"><span data-stu-id="5702f-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="5702f-134">Na przykład akcja kontrolera MVC, która używa `[HttpGet]` atrybutu jest uruchamiana tylko dla żądań GET.</span><span class="sxs-lookup"><span data-stu-id="5702f-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="5702f-135">Aby upewnić się, że *wszystkie* żądania docierają do niestandardowej strony obsługi błędów, nie ograniczaj ich do określonego zestawu metod http.</span><span class="sxs-lookup"><span data-stu-id="5702f-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="5702f-136">Aby obsłużyć wyjątki inaczej w oparciu o oryginalną metodę HTTP:</span><span class="sxs-lookup"><span data-stu-id="5702f-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="5702f-137">W przypadku Razor stron Utwórz wiele metod obsługi.</span><span class="sxs-lookup"><span data-stu-id="5702f-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="5702f-138">Na przykład użyj, aby obsłużyć `OnGet` pobieranie wyjątków i używać `OnPost` do obsługi wyjątków post.</span><span class="sxs-lookup"><span data-stu-id="5702f-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="5702f-139">W przypadku MVC Zastosuj atrybuty czasownika HTTP do wielu akcji.</span><span class="sxs-lookup"><span data-stu-id="5702f-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="5702f-140">Na przykład użyj, aby obsłużyć `[HttpGet]` pobieranie wyjątków i używać `[HttpPost]` do obsługi wyjątków post.</span><span class="sxs-lookup"><span data-stu-id="5702f-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="5702f-141">Aby umożliwić nieuwierzytelnionym użytkownikom wyświetlanie strony obsługi błędów niestandardowych, należy się upewnić, że obsługuje ona dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="5702f-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="5702f-142">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="5702f-142">Access the exception</span></span>

<span data-ttu-id="5702f-143">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w programie obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="5702f-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="5702f-144">Poniższy kod dodaje `ExceptionMessage` do domyślnych *stron/Error. cshtml. cs* wygenerowanych przez szablony ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5702f-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="5702f-145">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="5702f-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="5702f-146">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="5702f-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="5702f-147">Aby przetestować wyjątek w [aplikacji przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="5702f-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="5702f-148">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="5702f-148">Set the environment to production.</span></span>
* <span data-ttu-id="5702f-149">Usuń Komentarze z `webBuilder.UseStartup<Startup>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="5702f-150">Na stronie głównej wybierz opcję **Wyzwól wyjątek** .</span><span class="sxs-lookup"><span data-stu-id="5702f-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="5702f-151">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="5702f-151">Exception handler lambda</span></span>

<span data-ttu-id="5702f-152">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="5702f-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="5702f-153">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="5702f-154">Poniższy kod używa wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="5702f-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="5702f-155">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="5702f-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="5702f-156">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="5702f-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="5702f-157">Aby przetestować obsługę wyjątków lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="5702f-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="5702f-158">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="5702f-158">Set the environment to production.</span></span>
* <span data-ttu-id="5702f-159">Usuń Komentarze z `webBuilder.UseStartup<StartupLambda>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="5702f-160">Na stronie głównej wybierz opcję **Wyzwól wyjątek** .</span><span class="sxs-lookup"><span data-stu-id="5702f-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="5702f-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="5702f-161">UseStatusCodePages</span></span>

<span data-ttu-id="5702f-162">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu błędu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="5702f-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="5702f-163">Gdy aplikacja napotka kod stanu błędu HTTP 400-599, który nie ma treści, zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="5702f-164">Aby zapewnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="5702f-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="5702f-165">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="5702f-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="5702f-166">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="5702f-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="5702f-167">Na przykład, wywołaj `UseStatusCodePages` przed plikami statycznymi i punktami końcowymi.</span><span class="sxs-lookup"><span data-stu-id="5702f-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="5702f-168">Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="5702f-169">Na przykład przechodzenie do `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="5702f-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="5702f-170">Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:</span><span class="sxs-lookup"><span data-stu-id="5702f-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="5702f-171">`UseStatusCodePages` zwykle nie jest używana w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="5702f-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="5702f-172">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="5702f-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="5702f-173">Ustaw środowisko na produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="5702f-173">Set the environment to production.</span></span>
* <span data-ttu-id="5702f-174">Usuń Komentarze z `webBuilder.UseStartup<StartupUseStatusCodePages>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="5702f-175">Wybierz linki na stronie głównej na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="5702f-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="5702f-176">Oprogramowanie pośredniczące stron kodu **stanu nie przechwytuje** wyjątków.</span><span class="sxs-lookup"><span data-stu-id="5702f-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="5702f-177">Aby zapewnić niestandardową stronę obsługi błędów, użyj [strony obsługi wyjątków](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="5702f-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="5702f-178">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="5702f-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="5702f-179">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="5702f-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="5702f-180">W poprzednim kodzie, `{0}` jest symbolem zastępczym kodu błędu.</span><span class="sxs-lookup"><span data-stu-id="5702f-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="5702f-181">`UseStatusCodePages` ciąg formatu nie jest zazwyczaj używany w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="5702f-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="5702f-182">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupFormat>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="5702f-183">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="5702f-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="5702f-184">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="5702f-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="5702f-185">`UseStatusCodePages` wyrażenie lambda nie jest zwykle używane w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="5702f-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="5702f-186">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupStatusLambda>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="5702f-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="5702f-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="5702f-188"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="5702f-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="5702f-189">Wysyła do klienta kod stanu [znaleziony przez 302](https://developer.mozilla.org/docs/Web/HTTP/Status/302) .</span><span class="sxs-lookup"><span data-stu-id="5702f-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="5702f-190">Przekierowuje klienta do punktu końcowego obsługi błędu podanego w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="5702f-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="5702f-191">Punkt końcowy obsługi błędów zazwyczaj wyświetla informacje o błędach i zwraca HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5702f-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="5702f-192">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="5702f-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="5702f-193">Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="5702f-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="5702f-194">Podczas określania punktu końcowego w aplikacji Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="5702f-195">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/StatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="5702f-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="5702f-196">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5702f-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="5702f-197">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="5702f-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="5702f-198">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="5702f-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="5702f-199">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="5702f-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="5702f-200">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCredirect>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="5702f-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="5702f-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="5702f-202"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="5702f-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="5702f-203">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="5702f-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="5702f-204">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="5702f-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="5702f-205">Jeśli określono punkt końcowy w aplikacji, należy utworzyć widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="5702f-206">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="5702f-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="5702f-207">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="5702f-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="5702f-208">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="5702f-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="5702f-209">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="5702f-210">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="5702f-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="5702f-211">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="5702f-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="5702f-212">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy `{0}` kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="5702f-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="5702f-213">Szablon adresu URL musi zaczynać się od `/` .</span><span class="sxs-lookup"><span data-stu-id="5702f-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="5702f-214">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5702f-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="5702f-215">Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="5702f-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="5702f-216">Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCreX>();` programu w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5702f-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="5702f-217">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="5702f-217">Disable status code pages</span></span>

<span data-ttu-id="5702f-218">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, Użyj atrybutu [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="5702f-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="5702f-219">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="5702f-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="5702f-220">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="5702f-220">Exception-handling code</span></span>

<span data-ttu-id="5702f-221">Kod na stronach obsługi wyjątków może również generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5702f-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="5702f-222">Strony błędów produkcyjnych należy dokładnie przetestować i zachować dodatkową ostrożność, aby uniknąć zgłaszania wyjątków.</span><span class="sxs-lookup"><span data-stu-id="5702f-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="5702f-223">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="5702f-223">Response headers</span></span>

<span data-ttu-id="5702f-224">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="5702f-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="5702f-225">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="5702f-226">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="5702f-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="5702f-227">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="5702f-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="5702f-228">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="5702f-228">Server exception handling</span></span>

<span data-ttu-id="5702f-229">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5702f-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="5702f-230">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła `500 - Internal Server Error` odpowiedź bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="5702f-231">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="5702f-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="5702f-232">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="5702f-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="5702f-233">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="5702f-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="5702f-234">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="5702f-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="5702f-235">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="5702f-235">Startup exception handling</span></span>

<span data-ttu-id="5702f-236">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5702f-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="5702f-237">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="5702f-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="5702f-238">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="5702f-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="5702f-239">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="5702f-239">If binding fails:</span></span>

* <span data-ttu-id="5702f-240">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="5702f-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="5702f-241">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="5702f-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="5702f-242">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="5702f-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5702f-243">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="5702f-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="5702f-244">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5702f-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="5702f-245">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5702f-245">Database error page</span></span>

<span data-ttu-id="5702f-246">Filtr wyjątku strony dewelopera bazy danych `AddDatabaseDeveloperPageExceptionFilter` przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5702f-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="5702f-247">Kiedy te wyjątki wystąpią, odpowiedź HTML jest generowana z szczegółowymi informacjami o możliwych akcjach w celu rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="5702f-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="5702f-248">Ta strona jest włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="5702f-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="5702f-249">Następujący kod został wygenerowany przez Razor Szablony stron ASP.NET Core po określeniu poszczególnych kont użytkowników:</span><span class="sxs-lookup"><span data-stu-id="5702f-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="5702f-250">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="5702f-250">Exception filters</span></span>

<span data-ttu-id="5702f-251">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="5702f-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="5702f-252">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="5702f-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="5702f-253">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="5702f-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="5702f-254">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="5702f-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="5702f-255">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne jak wbudowany [wyjątek obsługujący oprogramowanie pośredniczące](https://github.com/dotnet/aspnetcore/blob/main/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="5702f-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/main/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="5702f-256">Zalecamy używanie `UseExceptionHandler` , chyba że konieczna jest obsługa błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="5702f-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="5702f-257">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="5702f-257">Model state errors</span></span>

<span data-ttu-id="5702f-258">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5702f-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5702f-259">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5702f-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5702f-260">Autorzy  [Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5702f-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5702f-261">W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5702f-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="5702f-262">Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5702f-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="5702f-263">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5702f-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="5702f-264">([Jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5702f-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="5702f-265">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="5702f-265">Developer Exception Page</span></span>

<span data-ttu-id="5702f-266">Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania.</span><span class="sxs-lookup"><span data-stu-id="5702f-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="5702f-267">Szablony ASP.NET Core generują następujący kod:</span><span class="sxs-lookup"><span data-stu-id="5702f-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="5702f-268">Poprzedni kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5702f-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5702f-269">Szablony należy umieścić <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> przed jakimkolwiek oprogramowanie pośredniczące, dlatego wyjątki są przechwytywane w oprogramowaniu pośredniczącym poniżej.</span><span class="sxs-lookup"><span data-stu-id="5702f-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="5702f-270">Poprzedni kod włącza stronę wyjątku dewelopera tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="5702f-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="5702f-271">Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="5702f-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="5702f-272">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="5702f-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5702f-273">Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:</span><span class="sxs-lookup"><span data-stu-id="5702f-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="5702f-274">Ślad stosu</span><span class="sxs-lookup"><span data-stu-id="5702f-274">Stack trace</span></span>
* <span data-ttu-id="5702f-275">Parametry ciągu zapytania, jeśli istnieją</span><span class="sxs-lookup"><span data-stu-id="5702f-275">Query string parameters if any</span></span>
* <span data-ttu-id="5702f-276">Cookies, jeśli jakieś</span><span class="sxs-lookup"><span data-stu-id="5702f-276">Cookies if any</span></span>
* <span data-ttu-id="5702f-277">Nagłówki</span><span class="sxs-lookup"><span data-stu-id="5702f-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="5702f-278">Strona obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="5702f-278">Exception handler page</span></span>

<span data-ttu-id="5702f-279">Aby skonfigurować niestandardową stronę obsługi błędów dla środowiska produkcyjnego, należy użyć wyjątku obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="5702f-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="5702f-280">Oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="5702f-280">The middleware:</span></span>

* <span data-ttu-id="5702f-281">Przechwytuje i rejestruje wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5702f-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="5702f-282">Wykonuje ponowne żądanie w alternatywnym potoku dla wskazanej strony lub kontrolera.</span><span class="sxs-lookup"><span data-stu-id="5702f-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="5702f-283">Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="5702f-284">Kod wygenerowany przez szablon ponownie wykonuje żądanie do `/Error` .</span><span class="sxs-lookup"><span data-stu-id="5702f-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="5702f-285">W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:</span><span class="sxs-lookup"><span data-stu-id="5702f-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="5702f-286">RazorSzablon aplikacji strony zawiera stronę błędów (*. cshtml*) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* .</span><span class="sxs-lookup"><span data-stu-id="5702f-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="5702f-287">W przypadku aplikacji MVC szablon projektu zawiera metodę akcji błędu i widok błędów w kontrolerze głównym.</span><span class="sxs-lookup"><span data-stu-id="5702f-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="5702f-288">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="5702f-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="5702f-289">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody.</span><span class="sxs-lookup"><span data-stu-id="5702f-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="5702f-290">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.</span><span class="sxs-lookup"><span data-stu-id="5702f-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="5702f-291">Uzyskaj dostęp do wyjątku</span><span class="sxs-lookup"><span data-stu-id="5702f-291">Access the exception</span></span>

<span data-ttu-id="5702f-292">Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie procedury obsługi błędów:</span><span class="sxs-lookup"><span data-stu-id="5702f-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="5702f-293">**Nie** należy podawać poufnych informacji o błędach do klientów.</span><span class="sxs-lookup"><span data-stu-id="5702f-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="5702f-294">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="5702f-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="5702f-295">Aby wyzwolić stronę obsługi przed wyjątkami, należy ustawić środowisko na produkcje i wymusić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="5702f-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="5702f-296">Procedura obsługi wyjątków lambda</span><span class="sxs-lookup"><span data-stu-id="5702f-296">Exception handler lambda</span></span>

<span data-ttu-id="5702f-297">Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="5702f-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="5702f-298">Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="5702f-299">Oto przykład użycia wyrażenia lambda dla obsługi wyjątków:</span><span class="sxs-lookup"><span data-stu-id="5702f-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="5702f-300">W powyższym kodzie `await context.Response.WriteAsync(new string(' ', 512));` zostanie dodany, aby przeglądarka Internet Explorer wyświetlała komunikat o błędzie zamiast komunikatu o błędzie programu IE.</span><span class="sxs-lookup"><span data-stu-id="5702f-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="5702f-301">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16144)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="5702f-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="5702f-302">**Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do.</span><span class="sxs-lookup"><span data-stu-id="5702f-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="5702f-303">Obsługa błędów stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="5702f-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="5702f-304">Aby zobaczyć wynik obsługi wyjątku lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples), użyj `ProdEnvironment` `ErrorHandlerLambda` dyrektyw i preprocesora, a następnie wybierz **Wyzwól wyjątek** na stronie głównej.</span><span class="sxs-lookup"><span data-stu-id="5702f-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="5702f-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="5702f-305">UseStatusCodePages</span></span>

<span data-ttu-id="5702f-306">Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu HTTP, takich jak *404 — nie znaleziono*.</span><span class="sxs-lookup"><span data-stu-id="5702f-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="5702f-307">Aplikacja zwraca kod stanu i pustą treść odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="5702f-308">Aby udostępnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.</span><span class="sxs-lookup"><span data-stu-id="5702f-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="5702f-309">Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="5702f-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="5702f-310">Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:</span><span class="sxs-lookup"><span data-stu-id="5702f-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="5702f-311">Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego (na przykład statycznych plików oprogramowania pośredniczącego i oprogramowania MVC).</span><span class="sxs-lookup"><span data-stu-id="5702f-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="5702f-312">Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="5702f-313">Na przykład przechodzenie do `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="5702f-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="5702f-314">Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:</span><span class="sxs-lookup"><span data-stu-id="5702f-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="5702f-315">UseStatusCodePages z ciągiem formatu</span><span class="sxs-lookup"><span data-stu-id="5702f-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="5702f-316">Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:</span><span class="sxs-lookup"><span data-stu-id="5702f-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="5702f-317">UseStatusCodePages z wyrażeniem lambda</span><span class="sxs-lookup"><span data-stu-id="5702f-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="5702f-318">Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:</span><span class="sxs-lookup"><span data-stu-id="5702f-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="5702f-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="5702f-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="5702f-320"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="5702f-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="5702f-321">Wysyła do klienta kod stanu *znaleziony przez 302* .</span><span class="sxs-lookup"><span data-stu-id="5702f-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="5702f-322">Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.</span><span class="sxs-lookup"><span data-stu-id="5702f-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="5702f-323">Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="5702f-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="5702f-324">Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="5702f-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="5702f-325">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="5702f-326">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5702f-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="5702f-327">Ta metoda jest często używana w przypadku aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5702f-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="5702f-328">Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd.</span><span class="sxs-lookup"><span data-stu-id="5702f-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="5702f-329">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="5702f-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="5702f-330">Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="5702f-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="5702f-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="5702f-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="5702f-332"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="5702f-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="5702f-333">Zwraca oryginalny kod stanu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="5702f-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="5702f-334">Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.</span><span class="sxs-lookup"><span data-stu-id="5702f-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="5702f-335">Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="5702f-336">Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu.</span><span class="sxs-lookup"><span data-stu-id="5702f-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="5702f-337">Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="5702f-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="5702f-338">Ta metoda jest często używana, gdy aplikacja powinna:</span><span class="sxs-lookup"><span data-stu-id="5702f-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="5702f-339">Przetwórz żądanie bez przekierowania do innego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="5702f-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="5702f-340">W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.</span><span class="sxs-lookup"><span data-stu-id="5702f-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="5702f-341">Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.</span><span class="sxs-lookup"><span data-stu-id="5702f-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="5702f-342">Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy ( `{0}` ) dla kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="5702f-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="5702f-343">Szablon adresu URL musi rozpoczynać się od ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="5702f-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="5702f-344">W przypadku używania symbolu zastępczego w ścieżce upewnij się, że punkt końcowy (strona lub kontroler) może przetworzyć segment ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5702f-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="5702f-345">Na przykład Razor Strona dla błędów powinna akceptować opcjonalną wartość segmentu ścieżki przy użyciu `@page` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="5702f-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="5702f-346">Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5702f-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

<span data-ttu-id="5702f-347">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="5702f-347">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="5702f-348">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody.</span><span class="sxs-lookup"><span data-stu-id="5702f-348">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="5702f-349">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.</span><span class="sxs-lookup"><span data-stu-id="5702f-349">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="5702f-350">Wyłącz strony kodu stanu</span><span class="sxs-lookup"><span data-stu-id="5702f-350">Disable status code pages</span></span>

<span data-ttu-id="5702f-351">Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, użyj [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5702f-351">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="5702f-352">Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="5702f-352">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="5702f-353">Kod obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="5702f-353">Exception-handling code</span></span>

<span data-ttu-id="5702f-354">Kod na stronach obsługi wyjątków może generować wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5702f-354">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="5702f-355">Często dobrym pomysłem jest, aby strony błędów produkcyjnych obejmowały czysto statyczną zawartość.</span><span class="sxs-lookup"><span data-stu-id="5702f-355">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="5702f-356">Nagłówki odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="5702f-356">Response headers</span></span>

<span data-ttu-id="5702f-357">Po wysłaniu nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="5702f-357">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="5702f-358">Aplikacja nie może zmienić kodu stanu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-358">The app can't change the response's status code.</span></span>
* <span data-ttu-id="5702f-359">Nie można uruchomić żadnych stron ani programów wyjątków.</span><span class="sxs-lookup"><span data-stu-id="5702f-359">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="5702f-360">Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="5702f-360">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="5702f-361">Obsługa wyjątków serwera</span><span class="sxs-lookup"><span data-stu-id="5702f-361">Server exception handling</span></span>

<span data-ttu-id="5702f-362">Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki.</span><span class="sxs-lookup"><span data-stu-id="5702f-362">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="5702f-363">Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła do *500 — wewnętrzny błąd serwera* bez treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5702f-363">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="5702f-364">Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie.</span><span class="sxs-lookup"><span data-stu-id="5702f-364">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="5702f-365">Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="5702f-365">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="5702f-366">Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera.</span><span class="sxs-lookup"><span data-stu-id="5702f-366">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="5702f-367">Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.</span><span class="sxs-lookup"><span data-stu-id="5702f-367">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="5702f-368">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="5702f-368">Startup exception handling</span></span>

<span data-ttu-id="5702f-369">Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5702f-369">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="5702f-370">Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="5702f-370">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="5702f-371">W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu.</span><span class="sxs-lookup"><span data-stu-id="5702f-371">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="5702f-372">Jeśli powiązanie nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="5702f-372">If binding fails:</span></span>

* <span data-ttu-id="5702f-373">Warstwa hostingu rejestruje wyjątek krytyczny.</span><span class="sxs-lookup"><span data-stu-id="5702f-373">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="5702f-374">Proces dotnet ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="5702f-374">The dotnet process crashes.</span></span>
* <span data-ttu-id="5702f-375">Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="5702f-375">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5702f-376">W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu.</span><span class="sxs-lookup"><span data-stu-id="5702f-376">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="5702f-377">Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5702f-377">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="5702f-378">Strona błędu bazy danych</span><span class="sxs-lookup"><span data-stu-id="5702f-378">Database error page</span></span>

<span data-ttu-id="5702f-379">Strona błędu bazy danych oprogramowanie pośredniczące przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="5702f-379">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="5702f-380">Kiedy te wyjątki wystąpią, zostanie wygenerowana odpowiedź HTML z szczegółowymi działaniami umożliwiającymi rozwiązanie problemu.</span><span class="sxs-lookup"><span data-stu-id="5702f-380">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="5702f-381">Ta strona powinna być włączona tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="5702f-381">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="5702f-382">Włącz stronę, dodając kod do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="5702f-382">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="5702f-383"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> wymaga pakietu NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="5702f-383"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="5702f-384">Filtry wyjątków</span><span class="sxs-lookup"><span data-stu-id="5702f-384">Exception filters</span></span>

<span data-ttu-id="5702f-385">W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji.</span><span class="sxs-lookup"><span data-stu-id="5702f-385">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="5702f-386">Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony.</span><span class="sxs-lookup"><span data-stu-id="5702f-386">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="5702f-387">Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru.</span><span class="sxs-lookup"><span data-stu-id="5702f-387">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="5702f-388">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="5702f-388">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="5702f-389">Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne, jak wyjątek obsługujący oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="5702f-389">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="5702f-390">Zalecamy używanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="5702f-390">We recommend using the middleware.</span></span> <span data-ttu-id="5702f-391">Używaj filtrów tylko wtedy, gdy musisz wykonać obsługę błędów w różny sposób, w zależności od wybranej akcji MVC.</span><span class="sxs-lookup"><span data-stu-id="5702f-391">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="5702f-392">Błędy stanu modelu</span><span class="sxs-lookup"><span data-stu-id="5702f-392">Model state errors</span></span>

<span data-ttu-id="5702f-393">Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5702f-393">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5702f-394">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5702f-394">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
