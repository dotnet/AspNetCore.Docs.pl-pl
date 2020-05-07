---
title: Testowanie ASP.NET Core oprogramowania pośredniczącego
author: tratcher
description: Dowiedz się, jak testować ASP.NET Core oprogramowania pośredniczącego za pomocą TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876438"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="24e9b-103">Testowanie ASP.NET Core oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="24e9b-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="24e9b-104">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="24e9b-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="24e9b-105">Oprogramowanie pośredniczące może być testowane w izolacji <xref:Microsoft.AspNetCore.TestHost.TestServer>z.</span><span class="sxs-lookup"><span data-stu-id="24e9b-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="24e9b-106">Umożliwia to:</span><span class="sxs-lookup"><span data-stu-id="24e9b-106">It allows you to:</span></span>

* <span data-ttu-id="24e9b-107">Tworzenie wystąpienia potoku aplikacji zawierającego tylko składniki, które należy przetestować.</span><span class="sxs-lookup"><span data-stu-id="24e9b-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="24e9b-108">Wysyłaj żądania niestandardowe, aby zweryfikować zachowanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="24e9b-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="24e9b-109">Zalety:</span><span class="sxs-lookup"><span data-stu-id="24e9b-109">Advantages:</span></span>

* <span data-ttu-id="24e9b-110">Żądania są wysyłane w pamięci, a nie serializowane przez sieć.</span><span class="sxs-lookup"><span data-stu-id="24e9b-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="24e9b-111">Pozwala to uniknąć dodatkowych zagadnień, takich jak zarządzanie portami i certyfikaty HTTPS.</span><span class="sxs-lookup"><span data-stu-id="24e9b-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="24e9b-112">Wyjątki w oprogramowaniu pośredniczącym mogą przepływać bezpośrednio z powrotem do testu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="24e9b-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="24e9b-113">Można dostosować struktury danych serwera, takie jak <xref:Microsoft.AspNetCore.Http.HttpContext>, bezpośrednio w teście.</span><span class="sxs-lookup"><span data-stu-id="24e9b-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="24e9b-114">Konfigurowanie TestServer</span><span class="sxs-lookup"><span data-stu-id="24e9b-114">Set up the TestServer</span></span>

<span data-ttu-id="24e9b-115">W projekcie testowym Utwórz test:</span><span class="sxs-lookup"><span data-stu-id="24e9b-115">In the test project, create a test:</span></span>

* <span data-ttu-id="24e9b-116">Kompiluj i uruchom hosta, który używa <xref:Microsoft.AspNetCore.TestHost.TestServer>programu.</span><span class="sxs-lookup"><span data-stu-id="24e9b-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="24e9b-117">Dodaj wymagane usługi używane przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="24e9b-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="24e9b-118">Skonfiguruj potok przetwarzania tak, aby korzystał z oprogramowania pośredniczącego dla testu.</span><span class="sxs-lookup"><span data-stu-id="24e9b-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="24e9b-119">Wysyłanie żądań za pomocą HttpClient</span><span class="sxs-lookup"><span data-stu-id="24e9b-119">Send requests with HttpClient</span></span>
<span data-ttu-id="24e9b-120">Wyślij żądanie przy użyciu <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="24e9b-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="24e9b-121">Potwierdź wynik.</span><span class="sxs-lookup"><span data-stu-id="24e9b-121">Assert the result.</span></span> <span data-ttu-id="24e9b-122">Najpierw ustaw potwierdzenie jako przeciwieństwo oczekiwanego wyniku.</span><span class="sxs-lookup"><span data-stu-id="24e9b-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="24e9b-123">Początkowy przebieg z fałszywie pozytywnym potwierdzeniem potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące działa prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="24e9b-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="24e9b-124">Uruchom test i upewnij się, że test zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="24e9b-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="24e9b-125">W poniższym przykładzie oprogramowanie pośredniczące powinno zwrócić kod stanu 404 (*nie można go znaleźć*) po zażądaniu głównego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="24e9b-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="24e9b-126">Wykonaj pierwszy przebieg testu z `Assert.NotEqual( ... );`, co powinno zakończyć się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="24e9b-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="24e9b-127">Zmień potwierdzenie, aby przetestować oprogramowanie pośredniczące w normalnych warunkach operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="24e9b-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="24e9b-128">Końcowy test używa `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="24e9b-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="24e9b-129">Uruchom ponownie test, aby upewnić się, że jest on przekazywany.</span><span class="sxs-lookup"><span data-stu-id="24e9b-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="24e9b-130">Wysyłanie żądań z obiektem HttpContext</span><span class="sxs-lookup"><span data-stu-id="24e9b-130">Send requests with HttpContext</span></span>

<span data-ttu-id="24e9b-131">Aplikacja testowa może również wysyłać żądanie przy użyciu [SendAsync (Akcja\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="24e9b-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="24e9b-132">W poniższym przykładzie podczas `https://example.com/A/Path/?and=query` przetwarzania przez oprogramowanie pośredniczące wykonywane są liczne operacje:</span><span class="sxs-lookup"><span data-stu-id="24e9b-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="24e9b-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>zezwala na bezpośrednią konfigurację <xref:Microsoft.AspNetCore.Http.HttpContext> obiektu, a nie za <xref:System.Net.Http.HttpClient> pomocą abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="24e9b-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="24e9b-134">Służy <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> do manipulowania strukturami dostępnymi tylko na serwerze, takimi jak [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) lub [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="24e9b-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="24e9b-135">Tak jak w przypadku wcześniejszego przykładu, który został przetestowany dla odpowiedzi o *404 — nie znaleziono* , sprawdź przeciwieństwo dla każdej `Assert` instrukcji w poprzednim teście.</span><span class="sxs-lookup"><span data-stu-id="24e9b-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="24e9b-136">Sprawdzanie potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące normalnie działa.</span><span class="sxs-lookup"><span data-stu-id="24e9b-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="24e9b-137">Po potwierdzeniu, że test fałszywie pozytywny działa, ustaw końcowe `Assert` instrukcje dla oczekiwanych warunków i wartości testu.</span><span class="sxs-lookup"><span data-stu-id="24e9b-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="24e9b-138">Uruchom ją ponownie, aby upewnić się, że test zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="24e9b-138">Run it again to confirm that the test passes.</span></span>
