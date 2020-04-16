---
title: Używanie gRPC w aplikacjach przeglądarki
author: jamesnk
description: Dowiedz się, jak skonfigurować usługi gRPC na ASP.NET Core, aby były wywoływane z aplikacji przeglądarki za pomocą gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440769"
---
# <a name="use-grpc-in-browser-apps"></a>Używanie gRPC w aplikacjach przeglądarki

Przez [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-Web wsparcie w .NET jest eksperymentalne**
>
> gRPC-Web dla .NET jest projektem eksperymentalnym, a nie produktem zatwierdzonym. Chcemy:
>
> * Sprawdź, czy nasze podejście do wdrażania gRPC-Web działa.
> * Uzyskaj opinię na temat tego, czy takie podejście jest przydatne dla deweloperów platformy .NET w porównaniu z tradycyjnym sposobem konfigurowania gRPC-Web za pośrednictwem serwera proxy.
>
> Prosimy o [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) wysunienie opinii, aby upewnić się, że budujemy coś, co deweloperzy lubią i są produktywni.

Nie można wywołać usługi HTTP/2 gRPC z aplikacji opartej na przeglądarce. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) to protokół, który pozwala przeglądarkom JavaScript i aplikacjom Blazor dzwonić do usług gRPC. W tym artykule wyjaśniono, jak używać gRPC-Web w .NET Core.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web w ASP.NET Core vs. Wysłannik

Istnieją dwie możliwości dodawania gRPC-Web do aplikacji ASP.NET Core:

* Obsługa gRPC-Web wraz z gRPC HTTP/2 w ASP.NET Core. Ta opcja używa oprogramowania `Grpc.AspNetCore.Web` pośredniczącego dostarczonego przez pakiet.
* Użyj obsługi gRPC-Web [serwera proxy wysłannika,](https://www.envoyproxy.io/) aby przetłumaczyć gRPC-Web na gRPC HTTP/2. Przetłumaczone połączenie jest następnie przekazywane do aplikacji ASP.NET Core.

Istnieją plusy i minusy każdego podejścia. Jeśli używasz już wysłannika jako serwera proxy w środowisku aplikacji, może warto również użyć go do zapewnienia obsługi gRPC-Web. Jeśli chcesz proste rozwiązanie dla gRPC-Web, który wymaga `Grpc.AspNetCore.Web` tylko ASP.NET Core, jest dobrym wyborem.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurowanie gRPC-Web w ASP.NET Core

Usługi gRPC hostowane w ASP.NET Core można skonfigurować do obsługi gRPC-Web obok HTTP/2 gRPC. gRPC-Web nie wymaga żadnych zmian w usługach. Jedyną modyfikacją jest konfiguracja uruchamiania.

Aby włączyć gRPC-Web z usługą ASP.NET Core gRPC:

* Dodaj odwołanie do pakietu [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)
* Skonfiguruj aplikację do korzystania z `AddGrpcWeb` `UseGrpcWeb` gRPC-Web, dodając i *Startup.cs:*

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Powyższy kod ma następujące działanie:

* Dodaje oprogramowanie pośredniczące gRPC-Web, `UseGrpcWeb`po routingu i przed punktami końcowymi.
* Określa metodę, która `endpoints.MapGrpcService<GreeterService>()` obsługuje gRPC-Web za pomocą `EnableGrpcWeb`pliku . 

Alternatywnie należy skonfigurować wszystkie usługi do obsługi `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` gRPC-Web, dodając do ConfigureServices.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> Istnieje znany problem, który powoduje, że gRPC-Web nie powiedzie się, gdy [hostowane przez http.sys](xref:fundamentals/servers/httpsys) w .NET Core 3.x.
>
> Obejście, aby uzyskać gRPC-Web pracy na Http.sys jest dostępny [tutaj](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web i CORS

Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej składanie żądań do innej domeny niż ta, która obsługiwała stronę sieci Web. To ograniczenie dotyczy wykonywania połączeń gRPC-Web z aplikacjami przeglądarki. Na przykład aplikacja przeglądarki obsługiwana przez `https://www.contoso.com` jest zablokowana w wywoływaniu `https://services.contoso.com`gRPC-Web usług hostowanych na . Udostępnianie zasobów cross origin (CORS) może służyć do złagodzenia tego ograniczenia.

Aby umożliwić aplikacji przeglądarki wykonywanie połączeń gRPC-Web typu cross-origin, [skonfiguruj CORS w ASP.NET Core](xref:security/cors). Użyj wbudowanej obsługi CORS i udostępnij nagłówki specyficzne <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>dla gRPC za pomocą programu .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Powyższy kod ma następujące działanie:

* Wywołania, `AddCors` aby dodać usługi CORS i konfiguruje zasady CORS, który udostępnia gRPC specyficzne nagłówki.
* Wywołania, `UseCors` aby dodać oprogramowanie pośredniczące CORS po routingu i przed punktami końcowymi.
* Określa metodę `endpoints.MapGrpcService<GreeterService>()` obsługuje CORS `RequiresCors`z .

## <a name="call-grpc-web-from-the-browser"></a>Zadzwoń do gRPC-Web z przeglądarki

Aplikacje przeglądarki mogą używać gRPC-Web do wywoływania usług gRPC. Istnieją pewne wymagania i ograniczenia podczas wywoływania usług gRPC z gRPC-Web z przeglądarki:

* Serwer musi być skonfigurowany do obsługi gRPC-Web.
* Przesyłanie strumieniowe klienta i dwukierunkowe przesyłanie strumieniowe nie są obsługiwane. Przesyłanie strumieniowe serwera jest obsługiwane.
* Wywoływanie usług gRPC w innej domenie wymaga skonfigurowania [usługi CORS](xref:security/cors) na serwerze.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Klient sieci Web

Istnieje javascript gRPC-web klienta. Aby uzyskać instrukcje dotyczące korzystania z gRPC-Web z Języka JavaScript, zobacz [pisanie kodu klienta JavaScript z gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurowanie gRPC-Web za pomocą klienta gRPC .NET

Klienta gRPC .NET można skonfigurować do wykonywania wywołań gRPC-Web. Jest to przydatne w przypadku aplikacji [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) które są hostowane w przeglądarce i mają takie same ograniczenia HTTP kodu JavaScript. Wywołanie gRPC-Web za pomocą klienta .NET jest [takie samo jak HTTP/2 gRPC](xref:grpc/client). Jedyną modyfikacją jest sposób tworzenia kanału.

Aby użyć gRPC-Web:

* Dodaj odwołanie do pakietu [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)
* Upewnij się, że odwołanie do pakietu [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) jest 2.27.0 lub więcej.
* Skonfiguruj kanał `GrpcWebHandler`do używania:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Powyższy kod ma następujące działanie:

* Konfiguruje kanał do korzystania z gRPC-Web.
* Tworzy klienta i nawiązuje połączenie za pomocą kanału.

Ma `GrpcWebHandler` następujące opcje konfiguracji podczas tworzenia:

* **InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Podstawowe, które sprawia, że żądanie `HttpClientHandler`HTTP gRPC, na przykład.
* **Tryb**: Typ wyliczenia określający, czy żądanie `Content-Type` `application/grpc-web` HTTP `application/grpc-web-text`gRPC jest czy .
    * `GrpcWebMode.GrpcWeb`konfiguruje zawartość, która ma być wysyłana bez kodowania. Wartość domyślna.
    * `GrpcWebMode.GrpcWebText`konfiguruje zawartość do zakodowania base64. Wymagane do przesyłania strumieniowego przez serwer połączeń w przeglądarkach.
* **HttpVersion**: `Version` Protokół HTTP używany do [ustawiania HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) na podstawowe żądanie HTTP gRPC. gRPC-Web nie wymaga określonej wersji i nie zastępuje wartości domyślnej, chyba że określono.

> [!IMPORTANT]
> Wygenerowane gRPC klienci mają synchronizacji i asynchronicznych metod wywoływania metod arysowych. Na przykład `SayHello` jest `SayHelloAsync` synchronizowana i jest asynchronizowana. Wywołanie metody synchronizacji w aplikacji Blazor WebAssembly spowoduje, że aplikacja przestanie odpowiadać. Metody asynchronizowe muszą być zawsze używane w Blazor WebAssembly.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [gRPC dla klientów sieci Web Projekt GitHub](https://github.com/grpc/grpc-web)
* <xref:security/cors>
