---
title: Obsługa błędów w ASP.NET Core interfejsów API sieci Web
author: pranavkm
description: Dowiedz się więcej o obsłudze błędów przy użyciu ASP.NET Core interfejsów API sieci Web.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: f9cc38f444bd3db826595b7de64db05792915a23
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585724"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="a16ab-103">Obsługa błędów w ASP.NET Core interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="a16ab-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="a16ab-104">W tym artykule opisano sposób obsługi i dostosowywania obsługi błędów przy użyciu ASP.NET Core interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a16ab-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="a16ab-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a16ab-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a16ab-106">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="a16ab-106">Developer Exception Page</span></span>

<span data-ttu-id="a16ab-107">[Strona wyjątków dla deweloperów](xref:fundamentals/error-handling) to przydatne narzędzie do uzyskiwania szczegółowych śladów stosu dla błędów serwera.</span><span class="sxs-lookup"><span data-stu-id="a16ab-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="a16ab-108">Używa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> do przechwytywania wyjątków synchronicznych i asynchronicznych z potoku HTTP oraz do generowania odpowiedzi na błędy.</span><span class="sxs-lookup"><span data-stu-id="a16ab-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="a16ab-109">Aby zilustrować, należy wziąć pod uwagę następujące działania kontrolera:</span><span class="sxs-lookup"><span data-stu-id="a16ab-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="a16ab-110">Uruchom następujące `curl` polecenie, aby przetestować poprzednią akcję:</span><span class="sxs-lookup"><span data-stu-id="a16ab-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a16ab-111">W ASP.NET Core 3,0 i nowszych na stronie wyjątków dla deweloperów zostanie wyświetlona odpowiedź w postaci zwykłego tekstu, jeśli klient nie zażąda danych wyjściowych w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="a16ab-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="a16ab-112">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="a16ab-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="a16ab-113">Aby zamiast tego wyświetlić odpowiedź sformatowaną w formacie HTML, należy ustawić `Accept` nagłówek żądania HTTP na `text/html` Typ nośnika.</span><span class="sxs-lookup"><span data-stu-id="a16ab-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="a16ab-114">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a16ab-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="a16ab-115">Rozważmy następujący fragment z odpowiedzi HTTP:</span><span class="sxs-lookup"><span data-stu-id="a16ab-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a16ab-116">W ASP.NET Core 2,2 i wcześniejszych na stronie wyjątku dla deweloperów zostanie wyświetlona odpowiedź w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="a16ab-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="a16ab-117">Rozważmy na przykład następujący fragment z odpowiedzi HTTP:</span><span class="sxs-lookup"><span data-stu-id="a16ab-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a16ab-118">Odpowiedź w formacie HTML jest przydatna podczas testowania za pomocą narzędzi, takich jak Poster.</span><span class="sxs-lookup"><span data-stu-id="a16ab-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="a16ab-119">Na poniższym zrzucie ekranu są widoczne odpowiedzi zarówno w formacie zwykłego tekstu, jak i w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="a16ab-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Testowanie stron wyjątków dla deweloperów w programie Poster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="a16ab-121">Stronę wyjątku dla deweloperów należy włączyć tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="a16ab-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a16ab-122">Nie udostępniaj publicznie szczegółowych informacji o wyjątkach, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a16ab-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a16ab-123">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="a16ab-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="a16ab-124">Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="a16ab-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="a16ab-125">Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody akcji.</span><span class="sxs-lookup"><span data-stu-id="a16ab-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="a16ab-126">Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć błąd.</span><span class="sxs-lookup"><span data-stu-id="a16ab-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="a16ab-127">Procedura obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="a16ab-127">Exception handler</span></span>

<span data-ttu-id="a16ab-128">W środowiskach innych niż programowanie [wyjątek obsługujący oprogramowanie pośredniczące](xref:fundamentals/error-handling) może być używany do tworzenia ładunku błędu:</span><span class="sxs-lookup"><span data-stu-id="a16ab-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="a16ab-129">W programie `Startup.Configure` Wywołaj, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> Aby użyć oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="a16ab-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="a16ab-130">Skonfiguruj akcję kontrolera, aby odpowiedzieć na `/error` trasę:</span><span class="sxs-lookup"><span data-stu-id="a16ab-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="a16ab-131">Poprzednia `Error` Akcja wysyła do klienta ładunek zgodny ze standardem [RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="a16ab-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="a16ab-132">Wyjątek obsługujący oprogramowanie pośredniczące może również dostarczyć bardziej szczegółowe dane wyjściowe negocjowane z zawartością w lokalnym środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="a16ab-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="a16ab-133">Wykonaj następujące kroki, aby utworzyć spójny format ładunku w środowisku deweloperskim i produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="a16ab-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="a16ab-134">W programie `Startup.Configure` zarejestruj wyjątek specyficzny dla środowiska obsługi oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="a16ab-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="a16ab-135">W poprzednim kodzie oprogramowanie pośredniczące jest zarejestrowane w:</span><span class="sxs-lookup"><span data-stu-id="a16ab-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="a16ab-136">Trasa `/error-local-development` w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="a16ab-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="a16ab-137">Trasa `/error` w środowiskach, które nie są przeznaczone do programowania.</span><span class="sxs-lookup"><span data-stu-id="a16ab-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="a16ab-138">Zastosuj Routing atrybutów do akcji kontrolera:</span><span class="sxs-lookup"><span data-stu-id="a16ab-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="a16ab-139">Poprzedni kod wywołuje [ControllerBase. problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) w celu utworzenia <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a16ab-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="a16ab-140">Modyfikowanie odpowiedzi przy użyciu wyjątków</span><span class="sxs-lookup"><span data-stu-id="a16ab-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="a16ab-141">Zawartość odpowiedzi można modyfikować poza kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="a16ab-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="a16ab-142">W przypadku interfejsu API sieci Web ASP.NET 4. x jeden ze sposobów na to zrobić przy użyciu <xref:System.Web.Http.HttpResponseException> typu.</span><span class="sxs-lookup"><span data-stu-id="a16ab-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="a16ab-143">ASP.NET Core nie zawiera równoważnego typu.</span><span class="sxs-lookup"><span data-stu-id="a16ab-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="a16ab-144">Pomoc techniczną dla programu `HttpResponseException` można dodać, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a16ab-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="a16ab-145">Utwórz dobrze znany typ wyjątku o nazwie `HttpResponseException` :</span><span class="sxs-lookup"><span data-stu-id="a16ab-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="a16ab-146">Utwórz filtr akcji o nazwie `HttpResponseExceptionFilter` :</span><span class="sxs-lookup"><span data-stu-id="a16ab-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="a16ab-147">Poprzedni filtr Określa `Order` maksymalną wartość całkowitą minus 10.</span><span class="sxs-lookup"><span data-stu-id="a16ab-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="a16ab-148">Pozwala to na wykonywanie innych filtrów na końcu potoku.</span><span class="sxs-lookup"><span data-stu-id="a16ab-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="a16ab-149">W programie `Startup.ConfigureServices` Dodaj filtr akcji do kolekcji filters:</span><span class="sxs-lookup"><span data-stu-id="a16ab-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="a16ab-150">Odpowiedź na błąd niepowodzenia weryfikacji</span><span class="sxs-lookup"><span data-stu-id="a16ab-150">Validation failure error response</span></span>

<span data-ttu-id="a16ab-151">W przypadku kontrolerów interfejsu API sieci Web MVC reaguje na <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> Typ odpowiedzi, gdy Walidacja modelu kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="a16ab-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="a16ab-152">MVC używa wyników w <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> celu skonstruowania odpowiedzi na błąd w przypadku niepowodzenia walidacji.</span><span class="sxs-lookup"><span data-stu-id="a16ab-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="a16ab-153">Poniższy przykład używa fabryki do zmiany domyślnego typu odpowiedzi na wartość <xref:Microsoft.AspNetCore.Mvc.SerializableError> w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a16ab-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="a16ab-154">Odpowiedź na błąd klienta</span><span class="sxs-lookup"><span data-stu-id="a16ab-154">Client error response</span></span>

<span data-ttu-id="a16ab-155">*Wynik błędu* jest definiowany w wyniku przy użyciu kodu stanu HTTP 400 lub wyższego.</span><span class="sxs-lookup"><span data-stu-id="a16ab-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="a16ab-156">W przypadku kontrolerów interfejsu API sieci Web MVC przekształca wynik błędu z wynikiem <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="a16ab-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="a16ab-157">ASP.NET Core 2,1 generuje odpowiedź na szczegóły problemu, która jest niemal zgodna ze specyfikacją RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="a16ab-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="a16ab-158">Jeśli 100 procent zgodności jest ważne, Uaktualnij projekt do ASP.NET Core 2,2 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="a16ab-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a16ab-159">Odpowiedź na błąd można skonfigurować w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="a16ab-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="a16ab-160">Implementuj ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="a16ab-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="a16ab-161">Użyj ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="a16ab-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="a16ab-162">Wprowadzą `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="a16ab-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="a16ab-163">MVC używa <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> do tworzenia wszystkich wystąpień <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> i <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="a16ab-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="a16ab-164">Obejmuje to odpowiedzi na błędy klientów, odpowiedzi na błędy walidacji i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> metody pomocnika.</span><span class="sxs-lookup"><span data-stu-id="a16ab-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="a16ab-165">Aby dostosować odpowiedź dotyczącą szczegółów problemu, zarejestruj niestandardową implementację programu <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a16ab-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a16ab-166">Odpowiedź na błąd można skonfigurować zgodnie z opisem w sekcji [use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="a16ab-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="a16ab-167">Użyj ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="a16ab-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="a16ab-168">Użyj <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> właściwości, aby skonfigurować zawartość `ProblemDetails` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="a16ab-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="a16ab-169">Na przykład poniższy kod w programie `Startup.ConfigureServices` aktualizuje `type` właściwość dla 404 odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="a16ab-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="a16ab-170">Niestandardowe oprogramowanie pośredniczące do obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="a16ab-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="a16ab-171">Wartości domyślne w oprogramowaniu do obsługi wyjątków działają dobrze w przypadku większości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a16ab-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="a16ab-172">W przypadku aplikacji, które wymagają wyspecjalizowanej obsługi wyjątków, należy rozważyć [dostosowanie obsługi wyjątków przez oprogramowanie pośredniczące](xref:fundamentals/error-handling#exception-handler-lambda).</span><span class="sxs-lookup"><span data-stu-id="a16ab-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
