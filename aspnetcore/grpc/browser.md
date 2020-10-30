---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby możliwe było wywoływanie z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058912"
---
# <a name="use-grpc-in-browser-apps"></a>Używanie gRPC w aplikacjach przeglądarki

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

 Dowiedz się, jak skonfigurować istniejącą ASP.NET Core usługę gRPC, która ma być wywoływana z aplikacji przeglądarki, przy użyciu protokołu [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) . gRPC-Web umożliwia przeglądarce JavaScript i Blazor aplikacjom wywoływanie usług gRPC Services. Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce. usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2.


Aby uzyskać instrukcje dotyczące dodawania usługi gRPC do istniejącej aplikacji ASP.NET Core, zobacz [Dodawanie usług gRPC do aplikacji ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Instrukcje dotyczące tworzenia projektu gRPC można znaleźć w temacie <xref:tutorials/grpc/grpc-start> .

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC — sieć Web w ASP.NET Core a wysłannika

Dostępne są dwa sposoby dodawania usługi gRPC-Web do aplikacji ASP.NET Core:

* Obsługa gRPC-Web i gRPC protokołu HTTP/2 w ASP.NET Core. Ta opcja powoduje użycie oprogramowania pośredniczącego dostarczonego przez `Grpc.AspNetCore.Web` pakiet.
* Użyj funkcji gRPC-Web [wysłannika serwera proxy](https://www.envoyproxy.io/) , aby przetłumaczyć GRPC-Web na gRPC http/2. Przetłumaczone wywołanie jest następnie przekazywane do aplikacji ASP.NET Core.

Każde podejście ma pewne wady i zalety. Jeśli środowisko aplikacji korzysta już z wysłannika jako serwera proxy, warto również użyć wysłannika do zapewnienia obsługi gRPC-sieci Web. W przypadku rozwiązania Basic for gRPC-Web, które wymaga tylko ASP.NET Core, `Grpc.AspNetCore.Web` to dobry wybór.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurowanie gRPC-sieci Web w ASP.NET Core

usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2. gRPC — sieć Web nie wymaga żadnych zmian w usługach. Jedyną modyfikacją jest konfiguracja uruchamiania.

Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:

* Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Skonfiguruj aplikację do używania gRPC-Web, dodając `UseGrpcWeb` i `EnableGrpcWeb` do *Startup.cs* :

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Powyższy kod ma następujące działanie:

* Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb` po stronie routingu i przed punktami końcowymi.
* Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje gRPC-Web with `EnableGrpcWeb` . 

Alternatywnie można skonfigurować oprogramowanie pośredniczące gRPC-Web, tak aby wszystkie usługi obsługiwały usługę gRPC-Web domyślnie i `EnableGrpcWeb` nie są wymagane. Określ `new GrpcWebOptions { DefaultEnabled = true }` , kiedy zostanie dodane oprogramowanie pośredniczące.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Występuje znany problem, który powoduje niepowodzenie gRPC-Web, gdy jest [hostowany przez Http.sys](xref:fundamentals/servers/httpsys) w programie .NET Core 3. x.
>
> Obejście problemu z gRPC-Web Work on Http.sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC — Web i CORS

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web. To ograniczenie dotyczy tworzenia połączeń sieci Web gRPC z aplikacjami przeglądarki. Na przykład aplikacja przeglądarki obsługiwana przez program `https://www.contoso.com` ma zablokowany dostęp do usług gRPC — sieci Web hostowanych w systemie `https://services.contoso.com` . Aby osłabić to ograniczenie, można użyć funkcji udostępniania zasobów między źródłami (CORS).

Aby zezwolić aplikacji przeglądarki na wykonywanie wywołań sieci Web między źródłami gRPC, skonfiguruj mechanizm [CORS w ASP.NET Core](xref:security/cors). Użyj wbudowanej obsługi mechanizmu CORS i Uwidocznij nagłówki specyficzne dla gRPC za pomocą <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Powyższy kod ma następujące działanie:

* Wywołania `AddCors` dodawania usług CORS i konfigurowania zasad CORS, które ujawniają nagłówki specyficzne dla gRPC.
* Wywołuje `UseCors` Dodawanie oprogramowania CORS po stronie routingu i przed punktami końcowymi.
* Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje mechanizm CORS z `RequiresCors` .

### <a name="grpc-web-and-streaming"></a>gRPC — sieć Web i przesyłanie strumieniowe

Tradycyjna gRPC za pośrednictwem protokołu HTTP/2 obsługuje przesyłanie strumieniowe we wszystkich kierunkach. gRPC-Web oferuje ograniczoną obsługę przesyłania strumieniowego:

* gRPC — klienci korzystający z przeglądarki sieci Web nie obsługują wywoływania przesyłania strumieniowego i dwukierunkowego przesyłania strumieniowego klienta.
* ASP.NET Core usługi gRPC Services hostowane na Azure App Service i usługach IIS nie obsługują przesyłania strumieniowego dwukierunkowego.

W przypadku korzystania z usługi gRPC-Web zalecamy użycie metod jednoargumentowych i metod przesyłania strumieniowego serwera.

## <a name="call-grpc-web-from-the-browser"></a>Wywołaj gRPC-Web z przeglądarki

Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services. Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:

* Serwer musi być skonfigurowany do obsługi gRPC-Web.
* Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane. Przesyłanie strumieniowe serwera jest obsługiwane.
* Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC — klient sieci Web

Istnieje skrypt JavaScript gRPC-Web Client. Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC

Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web. Jest to przydatne w przypadku [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplikacji, które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript. Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client). Jedyną modyfikacją jest sposób tworzenia kanału.

Aby użyć gRPC-Web:

* Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Upewnij się, że odwołanie do [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) Package ma wartość 2.29.0 lub nowszą.
* Skonfiguruj kanał, aby używał `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Powyższy kod ma następujące działanie:

* Konfiguruje kanał do korzystania z gRPC-sieci Web.
* Tworzy klienta i wywołuje połączenie przy użyciu kanału.

`GrpcWebHandler` dostępne są następujące opcje konfiguracji:

* **InnerHandler** : podstawowy, <xref:System.Net.Http.HttpMessageHandler> który wysyła żądanie HTTP gRPC, na przykład `HttpClientHandler` .
* **GrpcWebMode** : typ wyliczeniowy, który określa, czy żądanie HTTP gRPC `Content-Type` ma wartość `application/grpc-web` lub `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb` konfiguruje zawartość do wysłania bez kodowania. Wartość domyślna.
    * `GrpcWebMode.GrpcWebText` konfiguruje zawartość do kodowania base64. Wymagane dla wywołań przesyłania strumieniowego serwera w przeglądarkach.
* **HttpVersion** : protokół http `Version` służący do ustawiania [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) w źródłowym żądaniu HTTP gRPC. gRPC — sieć Web nie wymaga określonej wersji i nie przesłania jej domyślnie, chyba że zostanie określona.

> [!IMPORTANT]
> Wygenerowane gRPC klienci mają metody synchronizacji i asynchroniczne do wywoływania metod jednoargumentowych. Na przykład `SayHello` jest synchronizowana i `SayHelloAsync` jest asynchroniczna. Wywołanie metody synchronizacji w Blazor WebAssembly aplikacji spowoduje, że aplikacja przestanie odpowiadać. Metody asynchroniczne muszą być zawsze używane w programie Blazor WebAssembly .

### <a name="use-grpc-client-factory-with-grpc-web"></a>Używanie fabryki klienta gRPC z usługą gRPC-Web

Klienta .NET zgodnego z programem gRPC-Web można utworzyć przy użyciu integracji usługi gRPC z usługą [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).

Aby użyć gRPC-Web z fabryką klienta:

* Dodaj odwołania do pakietu do pliku projektu dla następujących pakietów:
  * [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [GRPC .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* Zarejestruj klienta gRPC z iniekcją zależności (DI) przy użyciu uniwersalnej `AddGrpcClient` metody rozszerzania. W Blazor WebAssembly aplikacji usługi są zarejestrowane przy użyciu programu "di in" `Program.cs` .
* Skonfiguruj `GrpcWebHandler` przy użyciu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> metody rozszerzenia.

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

Aby uzyskać więcej informacji, zobacz <xref:grpc/clientfactory>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [gRPC dla klientów sieci Web — projekt GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
