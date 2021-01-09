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
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058379"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Obsługa błędów w ASP.NET Core interfejsów API sieci Web

W tym artykule opisano sposób obsługi i dostosowywania obsługi błędów przy użyciu ASP.NET Core interfejsów API sieci Web.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Strona wyjątków dla deweloperów

[Strona wyjątków dla deweloperów](xref:fundamentals/error-handling) to przydatne narzędzie do uzyskiwania szczegółowych śladów stosu dla błędów serwera. Używa <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> do przechwytywania wyjątków synchronicznych i asynchronicznych z potoku HTTP oraz do generowania odpowiedzi na błędy. Aby zilustrować, należy wziąć pod uwagę następujące działania kontrolera:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Uruchom następujące `curl` polecenie, aby przetestować poprzednią akcję:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

W ASP.NET Core 3,0 i nowszych na stronie wyjątków dla deweloperów zostanie wyświetlona odpowiedź w postaci zwykłego tekstu, jeśli klient nie zażąda danych wyjściowych w formacie HTML. Wyświetlane są następujące dane wyjściowe:

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

Aby zamiast tego wyświetlić odpowiedź sformatowaną w formacie HTML, należy ustawić `Accept` nagłówek żądania HTTP na `text/html` Typ nośnika. Na przykład:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

Rozważmy następujący fragment z odpowiedzi HTTP:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

W ASP.NET Core 2,2 i wcześniejszych na stronie wyjątku dla deweloperów zostanie wyświetlona odpowiedź w formacie HTML. Rozważmy na przykład następujący fragment z odpowiedzi HTTP:

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

Odpowiedź w formacie HTML jest przydatna podczas testowania za pomocą narzędzi, takich jak Poster. Na poniższym zrzucie ekranu są widoczne odpowiedzi zarówno w formacie zwykłego tekstu, jak i w formacie HTML.

![Testowanie stron wyjątków dla deweloperów w programie Poster](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Stronę wyjątku dla deweloperów należy włączyć tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim**. Nie udostępniaj publicznie szczegółowych informacji o wyjątkach, gdy aplikacja jest uruchamiana w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .
>
> Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` . Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody akcji. Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć błąd.

## <a name="exception-handler"></a>Procedura obsługi wyjątków

W środowiskach innych niż programowanie [wyjątek obsługujący oprogramowanie pośredniczące](xref:fundamentals/error-handling) może być używany do tworzenia ładunku błędu:

1. W programie `Startup.Configure` Wywołaj, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> Aby użyć oprogramowania pośredniczącego:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Skonfiguruj akcję kontrolera, aby odpowiedzieć na `/error` trasę:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Poprzednia `Error` Akcja wysyła do klienta ładunek zgodny ze standardem [RFC 7807](https://tools.ietf.org/html/rfc7807).

Wyjątek obsługujący oprogramowanie pośredniczące może również dostarczyć bardziej szczegółowe dane wyjściowe negocjowane z zawartością w lokalnym środowisku programistycznym. Wykonaj następujące kroki, aby utworzyć spójny format ładunku w środowisku deweloperskim i produkcyjnym:

1. W programie `Startup.Configure` zarejestruj wyjątek specyficzny dla środowiska obsługi oprogramowania pośredniczącego:

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

    W poprzednim kodzie oprogramowanie pośredniczące jest zarejestrowane w:

    * Trasa `/error-local-development` w środowisku deweloperskim.
    * Trasa `/error` w środowiskach, które nie są przeznaczone do programowania.
    
1. Zastosuj Routing atrybutów do akcji kontrolera:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    Poprzedni kod wywołuje [ControllerBase. problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) w celu utworzenia <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> odpowiedzi.

## <a name="use-exceptions-to-modify-the-response"></a>Modyfikowanie odpowiedzi przy użyciu wyjątków

Zawartość odpowiedzi można modyfikować poza kontrolerem. W przypadku interfejsu API sieci Web ASP.NET 4. x jeden ze sposobów na to zrobić przy użyciu <xref:System.Web.Http.HttpResponseException> typu. ASP.NET Core nie zawiera równoważnego typu. Pomoc techniczną dla programu `HttpResponseException` można dodać, wykonując następujące czynności:

1. Utwórz dobrze znany typ wyjątku o nazwie `HttpResponseException` :

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Utwórz filtr akcji o nazwie `HttpResponseExceptionFilter` :

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    Poprzedni filtr Określa `Order` maksymalną wartość całkowitą minus 10. Pozwala to na wykonywanie innych filtrów na końcu potoku.

1. W programie `Startup.ConfigureServices` Dodaj filtr akcji do kolekcji filters:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Odpowiedź na błąd niepowodzenia weryfikacji

W przypadku kontrolerów interfejsu API sieci Web MVC reaguje na <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> Typ odpowiedzi, gdy Walidacja modelu kończy się niepowodzeniem. MVC używa wyników w <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> celu skonstruowania odpowiedzi na błąd w przypadku niepowodzenia walidacji. Poniższy przykład używa fabryki do zmiany domyślnego typu odpowiedzi na wartość <xref:Microsoft.AspNetCore.Mvc.SerializableError> w `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Odpowiedź na błąd klienta

*Wynik błędu* jest definiowany w wyniku przy użyciu kodu stanu HTTP 400 lub wyższego. W przypadku kontrolerów interfejsu API sieci Web MVC przekształca wynik błędu z wynikiem <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2,1 generuje odpowiedź na szczegóły problemu, która jest niemal zgodna ze specyfikacją RFC 7807. Jeśli 100 procent zgodności jest ważne, Uaktualnij projekt do ASP.NET Core 2,2 lub nowszego.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Odpowiedź na błąd można skonfigurować w jeden z następujących sposobów:

1. [Implementuj ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Użyj ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Wprowadzą `ProblemDetailsFactory`

MVC używa <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> do tworzenia wszystkich wystąpień <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> i <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . Obejmuje to odpowiedzi na błędy klientów, odpowiedzi na błędy walidacji i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> metody pomocnika.

Aby dostosować odpowiedź dotyczącą szczegółów problemu, zarejestruj niestandardową implementację programu <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> w programie `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Odpowiedź na błąd można skonfigurować zgodnie z opisem w sekcji [use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Użyj ApiBehaviorOptions. ClientErrorMapping

Użyj <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> właściwości, aby skonfigurować zawartość `ProblemDetails` odpowiedzi. Na przykład poniższy kod w programie `Startup.ConfigureServices` aktualizuje `type` właściwość dla 404 odpowiedzi:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a>Niestandardowe oprogramowanie pośredniczące do obsługi wyjątków

Wartości domyślne w oprogramowaniu do obsługi wyjątków działają dobrze w przypadku większości aplikacji. W przypadku aplikacji, które wymagają wyspecjalizowanej obsługi wyjątków, należy rozważyć [dostosowanie obsługi wyjątków przez oprogramowanie pośredniczące](xref:fundamentals/error-handling#exception-handler-lambda).
