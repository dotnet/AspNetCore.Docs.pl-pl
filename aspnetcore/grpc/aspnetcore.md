---
title: Usługi gRPC na platformie ASP.NET Core
author: juntaoluo
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi pisania usług gRPC Services przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
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
uid: grpc/aspnetcore
ms.openlocfilehash: bd97ece1d42b1b90d0b8740e33924d80b91bf49a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016443"
---
# <a name="grpc-services-with-aspnet-core"></a>Usługi gRPC na platformie ASP.NET Core

W tym dokumencie przedstawiono sposób rozpoczynania pracy z usługami gRPC Services przy użyciu ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Wprowadzenie do usługi gRPC na platformie ASP.NET Core

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Szczegółowe instrukcje dotyczące tworzenia projektu gRPC można znaleźć w temacie Wprowadzenie do [usług gRPC Services](xref:tutorials/grpc/grpc-start) .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Uruchom `dotnet new grpc -o GrpcGreeter` polecenie w wierszu polecenia.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Dodawanie usług gRPC do aplikacji ASP.NET Core

gRPC wymaga pakietu [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .

### <a name="configure-grpc"></a>Konfigurowanie gRPC

W *Startup.cs*:

* gRPC jest włączona z `AddGrpc` metodą.
* Każda usługa gRPC jest dodawana do potoku routingu za pomocą `MapGrpcService` metody.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core middlewares i funkcje korzystają z potoku routingu, w związku z czym aplikacja może być skonfigurowana w taki sposób, aby obsługiwała dodatkowe procedury obsługi żądań. Dodatkowe programy obsługi żądań, takie jak kontrolery MVC, pracują równolegle ze skonfigurowanymi usługami gRPC.

### <a name="configure-kestrel"></a>Konfigurowanie Kestrel

Punkty końcowe Kestrel gRPC:

* Wymagaj protokołu HTTP/2.
* Powinien być zabezpieczony przy użyciu [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).

#### <a name="http2"></a>HTTP/2

gRPC wymaga protokołu HTTP/2. gRPC dla ASP.NET Core sprawdza poprawność elementu [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .

Kestrel [obsługuje protokół HTTP/2](xref:fundamentals/servers/kestrel#http2-support) w większości nowoczesnych systemów operacyjnych. Punkty końcowe Kestrel są domyślnie skonfigurowane do obsługi połączeń HTTP/1.1 i HTTP/2.

#### <a name="tls"></a>TLS

Punkty końcowe Kestrel używane dla gRPC powinny być zabezpieczone przy użyciu protokołu TLS. W trakcie opracowywania, punkt końcowy zabezpieczony przy użyciu protokołu TLS jest automatycznie tworzony na `https://localhost:5001` czas, gdy obecny jest ASP.NET Core certyfikat deweloperski. Nie jest wymagana żadna konfiguracja. `https`Prefiks sprawdza, czy punkt końcowy Kestrel korzysta z protokołu TLS.

W środowisku produkcyjnym należy jawnie skonfigurować protokół TLS. W poniższym *appsettings.jsna* przykład jest dostępny punkt końcowy HTTP/2 zabezpieczony przy użyciu protokołu TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternatywnie można skonfigurować punkty końcowe Kestrel w *program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Negocjowanie protokołu

Protokół TLS jest używany przez więcej niż Zabezpieczanie komunikacji. Uzgadnianie [protokołu warstwy aplikacji TLS (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) służy do negocjowania protokołu połączenia między klientem a serwerem, gdy punkt końcowy obsługuje wiele protokołów. Ta negocjacja określa, czy połączenie używa protokołu HTTP/1.1 czy HTTP/2.

Jeśli punkt końcowy protokołu HTTP/2 jest skonfigurowany bez protokołu TLS, [ListenOptions. protokoły](xref:fundamentals/servers/kestrel#listenoptionsprotocols) punktu końcowego muszą mieć ustawioną wartość `HttpProtocols.Http2` . Punkt końcowy z wieloma protokołami (na przykład `HttpProtocols.Http1AndHttp2` ) nie może być używany bez protokołu TLS, ponieważ nie ma negocjacji. Wszystkie połączenia z niezabezpieczonym punktem końcowym domyślne do protokołu HTTP/1.1 i wywołania gRPC kończą się niepowodzeniem.

Aby uzyskać więcej informacji na temat włączania protokołów HTTP/2 i TLS przy użyciu usługi Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS. Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Integracja z interfejsami API ASP.NET Core

usługi gRPC mają pełny dostęp do funkcji ASP.NET Core, takich jak [iniekcja zależności](xref:fundamentals/dependency-injection) (di) i [Rejestrowanie](xref:fundamentals/logging/index). Na przykład implementacja usługi może rozpoznać usługę rejestratora z kontenera DI za pośrednictwem konstruktora:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Domyślnie implementacja usługi gRPC może rozwiązywać inne usługi DI z dowolnym okresem istnienia (pojedynczym, zakresowym lub przejściowym).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Rozwiąż element HttpContext w metodach gRPC

Interfejs API gRPC zapewnia dostęp do niektórych danych komunikatów HTTP/2, takich jak metoda, host, nagłówek i przyczepy. Dostęp odbywa się za pomocą `ServerCallContext` argumentu przesłanego do każdej metody gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`nie zapewnia pełnego dostępu do `HttpContext` wszystkich interfejsów api ASP.NET. `GetHttpContext`Metoda rozszerzająca zapewnia pełny dostęp do `HttpContext` reprezentowania podstawowego komunikatu http/2 w interfejsach API ASP.NET:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
