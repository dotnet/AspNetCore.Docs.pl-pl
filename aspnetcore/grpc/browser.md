---
title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="use-grpc-in-browser-apps"></a>Używanie gRPC w aplikacjach przeglądarki

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Nie można wywołać usługi gRPC protokołu HTTP/2 z poziomu aplikacji opartej na przeglądarce. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który umożliwia przeglądarce JavaScript i Blazor aplikacji Wywoływanie usługi gRPC Services. W tym artykule wyjaśniono, jak używać gRPC-Web w programie .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC — sieć Web w ASP.NET Core a wysłannika

Dostępne są dwa sposoby dodawania usługi gRPC-Web do aplikacji ASP.NET Core:

* Obsługa gRPC-Web i gRPC protokołu HTTP/2 w ASP.NET Core. Ta opcja powoduje użycie oprogramowania pośredniczącego dostarczonego przez `Grpc.AspNetCore.Web` pakiet.
* Użyj funkcji gRPC-Web [wysłannika serwera proxy](https://www.envoyproxy.io/) , aby przetłumaczyć GRPC-Web na gRPC http/2. Przetłumaczone wywołanie jest następnie przekazywane do aplikacji ASP.NET Core.

Każde podejście ma pewne wady i zalety. Jeśli używasz już wysłannika jako serwera proxy w środowisku aplikacji, warto również użyć go do zapewnienia obsługi gRPC-sieci Web. Jeśli potrzebujesz prostego rozwiązania dla gRPC-Web, które wymaga ASP.NET Core, `Grpc.AspNetCore.Web` to dobry wybór.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurowanie gRPC-sieci Web w ASP.NET Core

usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web, a nie gRPC protokołu HTTP/2. gRPC — sieć Web nie wymaga żadnych zmian w usługach. Jedyną modyfikacją jest konfiguracja uruchamiania.

Aby włączyć usługę gRPC-Web za pomocą usługi gRPC ASP.NET Core:

* Dodaj odwołanie do pakietu [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .
* Skonfiguruj aplikację do używania gRPC-Web, dodając `UseGrpcWeb` i `EnableGrpcWeb` do *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Powyższy kod ma następujące działanie:

* Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb` po stronie routingu i przed punktami końcowymi.
* Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje gRPC-Web with `EnableGrpcWeb` . 

Alternatywnie można skonfigurować oprogramowanie pośredniczące gRPC-Web, tak aby wszystkie usługi obsługiwały usługę gRPC-Web domyślnie i `EnableGrpcWeb` nie są wymagane. Określ `new GrpcWebOptions { DefaultEnabled = true }` , kiedy zostanie dodane oprogramowanie pośredniczące.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Występuje znany problem, który powoduje niepowodzenie gRPC-Web w przypadku [hostowania przez http. sys](xref:fundamentals/servers/httpsys) w programie .NET Core 3. x.
>
> Obejście problemu z gRPC-Web Working for http. sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC — Web i CORS

Zabezpieczenia przeglądarki uniemożliwiają stronom sieci Web wykonywanie żądań do innej domeny niż ta, która była obsługiwana przez stronę sieci Web. To ograniczenie dotyczy tworzenia połączeń sieci Web gRPC z aplikacjami przeglądarki. Na przykład aplikacja przeglądarki obsługiwana przez program `https://www.contoso.com` ma zablokowany dostęp do usług gRPC — sieci Web hostowanych w systemie `https://services.contoso.com` . Aby osłabić to ograniczenie, można użyć funkcji udostępniania zasobów między źródłami (CORS).

Aby zezwolić aplikacji przeglądarki na wykonywanie wywołań sieci Web między źródłami gRPC, skonfiguruj mechanizm [CORS w ASP.NET Core](xref:security/cors). Użyj wbudowanej obsługi mechanizmu CORS i Uwidocznij nagłówki specyficzne dla gRPC za pomocą <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Powyższy kod ma następujące działanie:

* Wywołania `AddCors` dodawania usług CORS i konfigurowania zasad CORS, które ujawniają nagłówki specyficzne dla gRPC.
* Wywołuje `UseCors` Dodawanie oprogramowania CORS po stronie routingu i przed punktami końcowymi.
* Określa, że `endpoints.MapGrpcService<GreeterService>()` Metoda obsługuje mechanizm CORS z `RequiresCors` .

## <a name="call-grpc-web-from-the-browser"></a>Wywołaj gRPC-Web z przeglądarki

Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC Services. Istnieją pewne wymagania i ograniczenia dotyczące wywoływania usług gRPC Services z usługą gRPC-Web w przeglądarce:

* Serwer musi być skonfigurowany do obsługi gRPC-Web.
* Wywołania przesyłania strumieniowego klientów i połączeń dwukierunkowych nie są obsługiwane. Przesyłanie strumieniowe serwera jest obsługiwane.
* Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [CORS](xref:security/cors) na serwerze.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC — klient sieci Web

Istnieje skrypt JavaScript gRPC-Web Client. Aby uzyskać instrukcje dotyczące korzystania z usługi gRPC-Web w języku JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurowanie gRPC-sieci Web za pomocą klienta .NET gRPC

Klienta .NET gRPC można skonfigurować w taki sposób, aby wykonywać wywołania gRPC-sieci Web. Jest to przydatne w przypadku aplikacji [ Blazor webassembly](xref:blazor/index#blazor-webassembly) , które są hostowane w przeglądarce i mają te same ograniczenia http związane z kodem JavaScript. Wywołanie gRPC-Web z klientem .NET jest [takie samo jak http/2 gRPC](xref:grpc/client). Jedyną modyfikacją jest sposób tworzenia kanału.

Aby użyć gRPC-Web:

* Dodaj odwołanie do pakietu [GRPC .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .
* Upewnij się, że odwołanie do [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) Package ma wartość 2.29.0 lub nowszą.
* Skonfiguruj kanał, aby używał `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Powyższy kod ma następujące działanie:

* Konfiguruje kanał do korzystania z gRPC-sieci Web.
* Tworzy klienta i wywołuje połączenie przy użyciu kanału.

`GrpcWebHandler`dostępne są następujące opcje konfiguracji:

* **InnerHandler**: podstawowy, <xref:System.Net.Http.HttpMessageHandler> który wysyła żądanie HTTP gRPC, na przykład `HttpClientHandler` .
* **GrpcWebMode**: typ wyliczeniowy, który określa, czy żądanie HTTP gRPC `Content-Type` ma wartość `application/grpc-web` lub `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb`konfiguruje zawartość do wysłania bez kodowania. Wartość domyślna.
    * `GrpcWebMode.GrpcWebText`konfiguruje zawartość do kodowania base64. Wymagane dla wywołań przesyłania strumieniowego serwera w przeglądarkach.
* **HttpVersion**: protokół http `Version` służący do ustawiania [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) w źródłowym żądaniu HTTP gRPC. gRPC — sieć Web nie wymaga określonej wersji i nie przesłania jej domyślnie, chyba że zostanie określona.

> [!IMPORTANT]
> Wygenerowane gRPC klienci mają metody synchronizacji i asynchroniczne do wywoływania metod jednoargumentowych. Na przykład `SayHello` jest synchronizowana i `SayHelloAsync` jest asynchroniczna. Wywołanie metody synchronizacji w Blazor aplikacji webassembly spowoduje, że aplikacja przestanie odpowiadać. Metody asynchroniczne muszą być zawsze używane w Blazor zestawie webassembly.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [gRPC dla klientów sieci Web — projekt GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>
