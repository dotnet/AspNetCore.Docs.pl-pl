---
title: Testowanie ASP.NET Core oprogramowania pośredniczącego
author: tratcher
description: Dowiedz się, jak testować ASP.NET Core oprogramowania pośredniczącego za pomocą TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: ed0925259bf3d4fee6c903ff55cdf1dae2355af7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631904"
---
# <a name="test-aspnet-core-middleware"></a>Testowanie ASP.NET Core oprogramowania pośredniczącego

[Krzysztof Ross](https://github.com/Tratcher)

Oprogramowanie pośredniczące może być testowane w izolacji z <xref:Microsoft.AspNetCore.TestHost.TestServer> . Umożliwia to:

* Tworzenie wystąpienia potoku aplikacji zawierającego tylko składniki, które należy przetestować.
* Wysyłaj żądania niestandardowe, aby zweryfikować zachowanie oprogramowania pośredniczącego.

Zalety:

* Żądania są wysyłane w pamięci, a nie serializowane przez sieć.
* Pozwala to uniknąć dodatkowych zagadnień, takich jak zarządzanie portami i certyfikaty HTTPS.
* Wyjątki w oprogramowaniu pośredniczącym mogą przepływać bezpośrednio z powrotem do testu wywołującego.
* Można dostosować struktury danych serwera, takie jak <xref:Microsoft.AspNetCore.Http.HttpContext> , bezpośrednio w teście.

## <a name="set-up-the-testserver"></a>Konfigurowanie TestServer

W projekcie testowym Utwórz test:

* Kompiluj i uruchom hosta, który używa programu <xref:Microsoft.AspNetCore.TestHost.TestServer> .
* Dodaj wymagane usługi używane przez oprogramowanie pośredniczące.
* Dodaj pakiet NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) do projektu:
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Skonfiguruj potok przetwarzania tak, aby korzystał z oprogramowania pośredniczącego dla testu.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Wysyłanie żądań za pomocą HttpClient
Wyślij żądanie przy użyciu <xref:System.Net.Http.HttpClient> :

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Potwierdź wynik. Najpierw ustaw potwierdzenie jako przeciwieństwo oczekiwanego wyniku. Początkowy przebieg z fałszywie pozytywnym potwierdzeniem potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące działa prawidłowo. Uruchom test i upewnij się, że test zakończy się niepowodzeniem.

W poniższym przykładzie oprogramowanie pośredniczące powinno zwrócić kod stanu 404 (*nie można go znaleźć*) po zażądaniu głównego punktu końcowego. Wykonaj pierwszy przebieg testu z `Assert.NotEqual( ... );` , co powinno zakończyć się niepowodzeniem:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Zmień potwierdzenie, aby przetestować oprogramowanie pośredniczące w normalnych warunkach operacyjnych. Końcowy test używa `Assert.Equal( ... );` . Uruchom ponownie test, aby upewnić się, że jest on przekazywany.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Wysyłanie żądań z obiektem HttpContext

Aplikacja testowa może również wysyłać żądanie przy użyciu [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). W poniższym przykładzie podczas `https://example.com/A/Path/?and=query` przetwarzania przez oprogramowanie pośredniczące wykonywane są liczne operacje:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> zezwala na bezpośrednią konfigurację <xref:Microsoft.AspNetCore.Http.HttpContext> obiektu, a nie za pomocą <xref:System.Net.Http.HttpClient> abstrakcji. Służy <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> do manipulowania strukturami dostępnymi tylko na serwerze, takimi jak [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) lub [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Tak jak w przypadku wcześniejszego przykładu, który został przetestowany dla odpowiedzi o *404 — nie znaleziono* , sprawdź przeciwieństwo dla każdej `Assert` instrukcji w poprzednim teście. Sprawdzanie potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące normalnie działa. Po potwierdzeniu, że test fałszywie pozytywny działa, ustaw końcowe `Assert` instrukcje dla oczekiwanych warunków i wartości testu. Uruchom ją ponownie, aby upewnić się, że test zakończy się pomyślnie.

## <a name="testserver-limitations"></a>Ograniczenia TestServer

TestServer:

* Został utworzony w celu replikowania zachowań serwera do testowania oprogramowania pośredniczącego.
* Nie ***próbuje*** replikować wszystkich <xref:System.Net.Http.HttpClient> zachowań.
* Program podejmuje próbę przyznania Klientowi możliwie dużą kontrolę nad serwerem i o ile to możliwe, na serwerze, jak to możliwe. Na przykład może zgłosić wyjątki, które nie są zwykle zgłaszane przez program, `HttpClient` Aby można było bezpośrednio komunikować się ze stanem serwera.
* Nie ustawia domyślnie niektórych nagłówków specyficznych dla transportu, ponieważ nie są one zazwyczaj odpowiednie dla oprogramowania pośredniczącego. Aby uzyskać więcej informacji, zobacz następną sekcję.

### <a name="content-length-and-transfer-encoding-headers"></a>Nagłówki Content-Length i Transfer-Encoding

TestServer nie ***Ustawia żądań*** związanych z transportem lub nagłówków odpowiedzi, takich jak [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) lub [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding). Aplikacje należy unikać w zależności od tych nagłówków, ponieważ ich użycie różni się od klienta, scenariusza i protokołu. Jeśli `Content-Length` i `Transfer-Encoding` są niezbędne do przetestowania określonego scenariusza, można je określić w teście podczas redagowania <xref:System.Net.Http.HttpRequestMessage> lub <xref:Microsoft.AspNetCore.Http.HttpContext> . Aby uzyskać więcej informacji, zobacz następujące problemy dotyczące usługi GitHub:

* [dotnet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore # 18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore # 13273](https://github.com/dotnet/aspnetcore/issues/13273)
