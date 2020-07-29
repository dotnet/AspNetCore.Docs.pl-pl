---
title: Obsługa błędów w ASP.NET Core interfejsów API sieci Web
author: pranavkm
description: Dowiedz się więcej o obsłudze błędów przy użyciu ASP.NET Core interfejsów API sieci Web.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 07/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/handle-errors
ms.openlocfilehash: d9517bc14ced15094f279029abb791477f7a0004
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159740"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="b1f2a-103">Obsługa błędów w ASP.NET Core interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="b1f2a-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="b1f2a-104">W tym artykule opisano sposób obsługi i dostosowywania obsługi błędów przy użyciu ASP.NET Core interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="b1f2a-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1f2a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b1f2a-106">Strona wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="b1f2a-106">Developer Exception Page</span></span>

<span data-ttu-id="b1f2a-107">[Strona wyjątków dla deweloperów](xref:fundamentals/error-handling) to przydatne narzędzie do uzyskiwania szczegółowych śladów stosu dla błędów serwera.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="b1f2a-108">Używa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> do przechwytywania wyjątków synchronicznych i asynchronicznych z potoku HTTP oraz do generowania odpowiedzi na błędy.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="b1f2a-109">Aby zilustrować, należy wziąć pod uwagę następujące działania kontrolera:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="b1f2a-110">Uruchom następujące `curl` polecenie, aby przetestować poprzednią akcję:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1f2a-111">W ASP.NET Core 3,0 i nowszych na stronie wyjątków dla deweloperów zostanie wyświetlona odpowiedź w postaci zwykłego tekstu, jeśli klient nie zażąda danych wyjściowych w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="b1f2a-112">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-112">The following output appears:</span></span>

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

<span data-ttu-id="b1f2a-113">Aby zamiast tego wyświetlić odpowiedź sformatowaną w formacie HTML, należy ustawić `Accept` nagłówek żądania HTTP na `text/html` Typ nośnika.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="b1f2a-114">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="b1f2a-115">Rozważmy następujący fragment z odpowiedzi HTTP:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b1f2a-116">W ASP.NET Core 2,2 i wcześniejszych na stronie wyjątku dla deweloperów zostanie wyświetlona odpowiedź w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="b1f2a-117">Rozważmy na przykład następujący fragment z odpowiedzi HTTP:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-117">For example, consider the following excerpt from the HTTP response:</span></span>

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

<span data-ttu-id="b1f2a-118">Odpowiedź w formacie HTML jest przydatna podczas testowania za pomocą narzędzi, takich jak Poster.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="b1f2a-119">Na poniższym zrzucie ekranu są widoczne odpowiedzi zarówno w formacie zwykłego tekstu, jak i w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Testowanie stron wyjątków dla deweloperów w programie Poster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="b1f2a-121">Stronę wyjątku dla deweloperów należy włączyć tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="b1f2a-122">Nie chcesz udostępniać szczegółowych informacji o wyjątku publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="b1f2a-123">Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="b1f2a-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="b1f2a-124">Procedura obsługi wyjątków</span><span class="sxs-lookup"><span data-stu-id="b1f2a-124">Exception handler</span></span>

<span data-ttu-id="b1f2a-125">W środowiskach innych niż programowanie [wyjątek obsługujący oprogramowanie pośredniczące](xref:fundamentals/error-handling) może być używany do tworzenia ładunku błędu:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="b1f2a-126">W programie `Startup.Configure` Wywołaj, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> Aby użyć oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="b1f2a-127">Skonfiguruj akcję kontrolera, aby odpowiedzieć na `/error` trasę:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="b1f2a-128">Poprzednia `Error` Akcja wysyła do klienta ładunek zgodny ze standardem [RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="b1f2a-128">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="b1f2a-129">Wyjątek obsługujący oprogramowanie pośredniczące może również dostarczyć bardziej szczegółowe dane wyjściowe negocjowane z zawartością w lokalnym środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="b1f2a-130">Wykonaj następujące kroki, aby utworzyć spójny format ładunku w środowisku deweloperskim i produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="b1f2a-131">W programie `Startup.Configure` zarejestruj wyjątek specyficzny dla środowiska obsługi oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="b1f2a-132">W poprzednim kodzie oprogramowanie pośredniczące jest zarejestrowane w:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="b1f2a-133">Trasa `/error-local-development` w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="b1f2a-134">Trasa `/error` w środowiskach, które nie są przeznaczone do programowania.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="b1f2a-135">Zastosuj Routing atrybutów do akcji kontrolera:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="b1f2a-136">Modyfikowanie odpowiedzi przy użyciu wyjątków</span><span class="sxs-lookup"><span data-stu-id="b1f2a-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="b1f2a-137">Zawartość odpowiedzi można modyfikować poza kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="b1f2a-138">W przypadku interfejsu API sieci Web ASP.NET 4. x jeden ze sposobów na to zrobić przy użyciu <xref:System.Web.Http.HttpResponseException> typu.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="b1f2a-139">ASP.NET Core nie zawiera równoważnego typu.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="b1f2a-140">Pomoc techniczną dla programu `HttpResponseException` można dodać, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="b1f2a-141">Utwórz dobrze znany typ wyjątku o nazwie `HttpResponseException` :</span><span class="sxs-lookup"><span data-stu-id="b1f2a-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="b1f2a-142">Utwórz filtr akcji o nazwie `HttpResponseExceptionFilter` :</span><span class="sxs-lookup"><span data-stu-id="b1f2a-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="b1f2a-143">W poprzednim filtrze Magiczna liczba 10 jest odejmowana od maksymalnej wartości całkowitej.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-143">In the preceding filter, the magic number 10 is subtracted from the maximum integer value.</span></span> <span data-ttu-id="b1f2a-144">Odjęcie tej liczby umożliwia wykonywanie innych filtrów na bardzo końcu potoku.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-144">Subtracting this number allows other filters to run at the very end of the pipeline.</span></span>

1. <span data-ttu-id="b1f2a-145">W programie `Startup.ConfigureServices` Dodaj filtr akcji do kolekcji filters:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-145">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="b1f2a-146">Odpowiedź na błąd niepowodzenia weryfikacji</span><span class="sxs-lookup"><span data-stu-id="b1f2a-146">Validation failure error response</span></span>

<span data-ttu-id="b1f2a-147">W przypadku kontrolerów interfejsu API sieci Web MVC reaguje na <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> Typ odpowiedzi, gdy Walidacja modelu kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-147">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="b1f2a-148">MVC używa wyników w <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> celu skonstruowania odpowiedzi na błąd w przypadku niepowodzenia walidacji.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-148">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="b1f2a-149">Poniższy przykład używa fabryki do zmiany domyślnego typu odpowiedzi na wartość <xref:Microsoft.AspNetCore.Mvc.SerializableError> w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b1f2a-149">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="b1f2a-150">Odpowiedź na błąd klienta</span><span class="sxs-lookup"><span data-stu-id="b1f2a-150">Client error response</span></span>

<span data-ttu-id="b1f2a-151">*Wynik błędu* jest definiowany w wyniku przy użyciu kodu stanu HTTP 400 lub wyższego.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-151">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="b1f2a-152">W przypadku kontrolerów interfejsu API sieci Web MVC przekształca wynik błędu z wynikiem <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="b1f2a-152">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="b1f2a-153">ASP.NET Core 2,1 generuje odpowiedź na szczegóły problemu, która jest niemal zgodna ze specyfikacją RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-153">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="b1f2a-154">Jeśli 100 procent zgodności jest ważne, Uaktualnij projekt do ASP.NET Core 2,2 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-154">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1f2a-155">Odpowiedź na błąd można skonfigurować w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-155">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="b1f2a-156">Implementuj ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="b1f2a-156">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="b1f2a-157">Użyj ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="b1f2a-157">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="b1f2a-158">Wprowadzą`ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="b1f2a-158">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="b1f2a-159">MVC używa <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> do tworzenia wszystkich wystąpień <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> i <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="b1f2a-159">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="b1f2a-160">Obejmuje to odpowiedzi na błędy klientów, odpowiedzi na błędy walidacji i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> metody pomocnika.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-160">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="b1f2a-161">Aby dostosować odpowiedź dotyczącą szczegółów problemu, zarejestruj niestandardową implementację programu <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b1f2a-161">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b1f2a-162">Odpowiedź na błąd można skonfigurować zgodnie z opisem w sekcji [use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="b1f2a-162">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="b1f2a-163">Użyj ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="b1f2a-163">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="b1f2a-164">Użyj <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> właściwości, aby skonfigurować zawartość `ProblemDetails` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1f2a-164">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="b1f2a-165">Na przykład poniższy kod w programie `Startup.ConfigureServices` aktualizuje `type` właściwość dla 404 odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="b1f2a-165">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
