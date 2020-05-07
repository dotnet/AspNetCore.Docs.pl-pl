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
# <a name="test-aspnet-core-middleware"></a>Testowanie ASP.NET Core oprogramowania pośredniczącego

[Krzysztof Ross](https://github.com/Tratcher)

Oprogramowanie pośredniczące może być testowane w izolacji <xref:Microsoft.AspNetCore.TestHost.TestServer>z. Umożliwia to:

* Tworzenie wystąpienia potoku aplikacji zawierającego tylko składniki, które należy przetestować.
* Wysyłaj żądania niestandardowe, aby zweryfikować zachowanie oprogramowania pośredniczącego.

Zalety:

* Żądania są wysyłane w pamięci, a nie serializowane przez sieć.
* Pozwala to uniknąć dodatkowych zagadnień, takich jak zarządzanie portami i certyfikaty HTTPS.
* Wyjątki w oprogramowaniu pośredniczącym mogą przepływać bezpośrednio z powrotem do testu wywołującego.
* Można dostosować struktury danych serwera, takie jak <xref:Microsoft.AspNetCore.Http.HttpContext>, bezpośrednio w teście.

## <a name="set-up-the-testserver"></a>Konfigurowanie TestServer

W projekcie testowym Utwórz test:

* Kompiluj i uruchom hosta, który używa <xref:Microsoft.AspNetCore.TestHost.TestServer>programu.
* Dodaj wymagane usługi używane przez oprogramowanie pośredniczące.
* Skonfiguruj potok przetwarzania tak, aby korzystał z oprogramowania pośredniczącego dla testu.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Wysyłanie żądań za pomocą HttpClient
Wyślij żądanie przy użyciu <xref:System.Net.Http.HttpClient>:

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Potwierdź wynik. Najpierw ustaw potwierdzenie jako przeciwieństwo oczekiwanego wyniku. Początkowy przebieg z fałszywie pozytywnym potwierdzeniem potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące działa prawidłowo. Uruchom test i upewnij się, że test zakończy się niepowodzeniem.

W poniższym przykładzie oprogramowanie pośredniczące powinno zwrócić kod stanu 404 (*nie można go znaleźć*) po zażądaniu głównego punktu końcowego. Wykonaj pierwszy przebieg testu z `Assert.NotEqual( ... );`, co powinno zakończyć się niepowodzeniem:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Zmień potwierdzenie, aby przetestować oprogramowanie pośredniczące w normalnych warunkach operacyjnych. Końcowy test używa `Assert.Equal( ... );`. Uruchom ponownie test, aby upewnić się, że jest on przekazywany.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Wysyłanie żądań z obiektem HttpContext

Aplikacja testowa może również wysyłać żądanie przy użyciu [SendAsync (Akcja\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). W poniższym przykładzie podczas `https://example.com/A/Path/?and=query` przetwarzania przez oprogramowanie pośredniczące wykonywane są liczne operacje:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>zezwala na bezpośrednią konfigurację <xref:Microsoft.AspNetCore.Http.HttpContext> obiektu, a nie za <xref:System.Net.Http.HttpClient> pomocą abstrakcji. Służy <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> do manipulowania strukturami dostępnymi tylko na serwerze, takimi jak [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) lub [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Tak jak w przypadku wcześniejszego przykładu, który został przetestowany dla odpowiedzi o *404 — nie znaleziono* , sprawdź przeciwieństwo dla każdej `Assert` instrukcji w poprzednim teście. Sprawdzanie potwierdza, że test zakończy się niepowodzeniem, gdy oprogramowanie pośredniczące normalnie działa. Po potwierdzeniu, że test fałszywie pozytywny działa, ustaw końcowe `Assert` instrukcje dla oczekiwanych warunków i wartości testu. Uruchom ją ponownie, aby upewnić się, że test zakończy się pomyślnie.
