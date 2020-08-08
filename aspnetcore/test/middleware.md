---
title: Testowanie ASP.NET Core oprogramowania pośredniczącego
author: tratcher
description: Dowiedz się, jak testować ASP.NET Core oprogramowania pośredniczącego za pomocą TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 235010c95cdd0c7ce1368b4abd91e75d81ae094b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021903"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="638fc-103">Testowanie ASP.NET Core oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="638fc-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="638fc-104">[Krzysztof Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="638fc-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="638fc-105">Oprogramowanie pośredniczące może być testowane w izolacji z <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="638fc-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="638fc-106">Umożliwia to:</span><span class="sxs-lookup"><span data-stu-id="638fc-106">It allows you to:</span></span>

* <span data-ttu-id="638fc-107">Tworzenie wystąpienia potoku aplikacji zawierającego tylko składniki, które należy przetestować.</span><span class="sxs-lookup"><span data-stu-id="638fc-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="638fc-108">Wysyłaj żądania niestandardowe, aby zweryfikować zachowanie oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="638fc-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="638fc-109">Zalety:</span><span class="sxs-lookup"><span data-stu-id="638fc-109">Advantages:</span></span>

* <span data-ttu-id="638fc-110">Żądania są wysyłane w pamięci, a nie serializowane przez sieć.</span><span class="sxs-lookup"><span data-stu-id="638fc-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="638fc-111">Pozwala to uniknąć dodatkowych zagadnień, takich jak zarządzanie portami i certyfikaty HTTPS.</span><span class="sxs-lookup"><span data-stu-id="638fc-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="638fc-112">Wyjątki w oprogramowaniu pośredniczącym mogą przepływać bezpośrednio z powrotem do testu wywołującego.</span><span class="sxs-lookup"><span data-stu-id="638fc-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="638fc-113">Można dostosować struktury danych serwera, takie jak <xref:Microsoft.AspNetCore.Http.HttpContext> , bezpośrednio w teście.</span><span class="sxs-lookup"><span data-stu-id="638fc-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="638fc-114">Konfigurowanie TestServer</span><span class="sxs-lookup"><span data-stu-id="638fc-114">Set up the TestServer</span></span>

<span data-ttu-id="638fc-115">W projekcie testowym Utwórz test:</span><span class="sxs-lookup"><span data-stu-id="638fc-115">In the test project, create a test:</span></span>

* <span data-ttu-id="638fc-116">Kompiluj i uruchom hosta, który używa programu <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="638fc-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="638fc-117">Dodaj wymagane usługi używane przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="638fc-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="638fc-118">Dodaj pakiet NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) do projektu:</span><span class="sxs-lookup"><span data-stu-id="638fc-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="638fc-119">Skonfiguruj potok przetwarzania tak, aby korzystał z oprogramowania pośredniczącego dla testu.</span><span class="sxs-lookup"><span data-stu-id="638fc-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="638fc-120">Wysyłanie żądań za pomocą HttpClient</span><span class="sxs-lookup"><span data-stu-id="638fc-120">Send requests with HttpClient</span></span>
<span data-ttu-id="638fc-121">Wyślij żądanie przy użyciu <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="638fc-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="638fc-122">Potwierdź wynik.</span><span class="sxs-lookup"><span data-stu-id="638fc-122">Assert the result.</span></span> <span data-ttu-id="638fc-123">Najpierw ustaw potwierdzenie jako przeciwieństwo oczekiwanego wyniku.</span><span class="sxs-lookup"><span data-stu-id="638fc-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="638fc-124">Początkowy przebieg z fałszywie pozytywnym potwierdzeniem potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące działa prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="638fc-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="638fc-125">Uruchom test i upewnij się, że test zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="638fc-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="638fc-126">W poniższym przykładzie oprogramowanie pośredniczące powinno zwrócić kod stanu 404 (*nie można go znaleźć*) po zażądaniu głównego punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="638fc-126">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="638fc-127">Wykonaj pierwszy przebieg testu z `Assert.NotEqual( ... );` , co powinno zakończyć się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="638fc-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="638fc-128">Zmień potwierdzenie, aby przetestować oprogramowanie pośredniczące w normalnych warunkach operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="638fc-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="638fc-129">Końcowy test używa `Assert.Equal( ... );` .</span><span class="sxs-lookup"><span data-stu-id="638fc-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="638fc-130">Uruchom ponownie test, aby upewnić się, że jest on przekazywany.</span><span class="sxs-lookup"><span data-stu-id="638fc-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="638fc-131">Wysyłanie żądań z obiektem HttpContext</span><span class="sxs-lookup"><span data-stu-id="638fc-131">Send requests with HttpContext</span></span>

<span data-ttu-id="638fc-132">Aplikacja testowa może również wysyłać żądanie przy użyciu [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="638fc-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="638fc-133">W poniższym przykładzie podczas `https://example.com/A/Path/?and=query` przetwarzania przez oprogramowanie pośredniczące wykonywane są liczne operacje:</span><span class="sxs-lookup"><span data-stu-id="638fc-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="638fc-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>zezwala na bezpośrednią konfigurację <xref:Microsoft.AspNetCore.Http.HttpContext> obiektu, a nie za pomocą <xref:System.Net.Http.HttpClient> abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="638fc-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="638fc-135">Służy <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> do manipulowania strukturami dostępnymi tylko na serwerze, takimi jak [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) lub [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="638fc-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="638fc-136">Tak jak w przypadku wcześniejszego przykładu, który został przetestowany dla odpowiedzi o *404 — nie znaleziono* , sprawdź przeciwieństwo dla każdej `Assert` instrukcji w poprzednim teście.</span><span class="sxs-lookup"><span data-stu-id="638fc-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="638fc-137">Sprawdzanie potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące normalnie działa.</span><span class="sxs-lookup"><span data-stu-id="638fc-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="638fc-138">Po potwierdzeniu, że test fałszywie pozytywny działa, ustaw końcowe `Assert` instrukcje dla oczekiwanych warunków i wartości testu.</span><span class="sxs-lookup"><span data-stu-id="638fc-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="638fc-139">Uruchom ją ponownie, aby upewnić się, że test zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="638fc-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="638fc-140">Ograniczenia TestServer</span><span class="sxs-lookup"><span data-stu-id="638fc-140">TestServer limitations</span></span>

<span data-ttu-id="638fc-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="638fc-141">TestServer:</span></span>

* <span data-ttu-id="638fc-142">Został utworzony w celu replikowania zachowań serwera do testowania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="638fc-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="638fc-143">Nie ***próbuje*** replikować wszystkich <xref:System.Net.Http.HttpClient> zachowań.</span><span class="sxs-lookup"><span data-stu-id="638fc-143">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="638fc-144">Program podejmuje próbę przyznania Klientowi możliwie dużą kontrolę nad serwerem i o ile to możliwe, na serwerze, jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="638fc-144">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="638fc-145">Na przykład może zgłosić wyjątki, które nie są zwykle zgłaszane przez program, `HttpClient` Aby można było bezpośrednio komunikować się ze stanem serwera.</span><span class="sxs-lookup"><span data-stu-id="638fc-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="638fc-146">Nie ustawia domyślnie niektórych nagłówków specyficznych dla transportu, ponieważ nie są one zazwyczaj odpowiednie dla oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="638fc-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="638fc-147">Aby uzyskać więcej informacji, zobacz następną sekcję.</span><span class="sxs-lookup"><span data-stu-id="638fc-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="638fc-148">Nagłówki Content-Length i Transfer-Encoding</span><span class="sxs-lookup"><span data-stu-id="638fc-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="638fc-149">TestServer nie ***Ustawia żądań*** związanych z transportem lub nagłówków odpowiedzi, takich jak [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) lub [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="638fc-149">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="638fc-150">Aplikacje należy unikać w zależności od tych nagłówków, ponieważ ich użycie różni się od klienta, scenariusza i protokołu.</span><span class="sxs-lookup"><span data-stu-id="638fc-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="638fc-151">Jeśli `Content-Length` i `Transfer-Encoding` są niezbędne do przetestowania określonego scenariusza, można je określić w teście podczas redagowania <xref:System.Net.Http.HttpRequestMessage> lub <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="638fc-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="638fc-152">Aby uzyskać więcej informacji, zobacz następujące problemy dotyczące usługi GitHub:</span><span class="sxs-lookup"><span data-stu-id="638fc-152">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="638fc-153">dotnet/aspnetcore # 21677</span><span class="sxs-lookup"><span data-stu-id="638fc-153">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="638fc-154">dotnet/aspnetcore # 18463</span><span class="sxs-lookup"><span data-stu-id="638fc-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="638fc-155">dotnet/aspnetcore # 13273</span><span class="sxs-lookup"><span data-stu-id="638fc-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
