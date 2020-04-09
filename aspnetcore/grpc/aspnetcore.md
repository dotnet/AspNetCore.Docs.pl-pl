---
title: Usługi gRPC na platformie ASP.NET Core
author: juntaoluo
description: Poznaj podstawowe pojęcia podczas pisania usług gRPC z ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667630"
---
# <a name="grpc-services-with-aspnet-core"></a>Usługi gRPC na platformie ASP.NET Core

W tym dokumencie pokazano, jak rozpocząć pracę z usługami gRPC przy użyciu ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Wprowadzenie do usługi gRPC na platformie ASP.NET Core

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) [(jak pobrać).](xref:index#how-to-download-a-sample)

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Szczegółowe instrukcje dotyczące tworzenia projektu gRPC można znaleźć w serwisie Get started z [usługami gRPC.](xref:tutorials/grpc/grpc-start)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Uruchom `dotnet new grpc -o GrpcGreeter` z wiersza polecenia.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Dodawanie usług gRPC do aplikacji ASP.NET Core

gRPC wymaga pakietu [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)

### <a name="configure-grpc"></a>Konfigurowanie gRPC

W *Startup.cs:*

* gRPC jest włączona `AddGrpc` za pomocą metody.
* Każda usługa gRPC jest dodawany do `MapGrpcService` potoku routingu za pomocą metody.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET podstawowe oprogramowanie pośredniczące i funkcje współużytkują potok routingu, dlatego aplikację można skonfigurować do obsługi dodatkowych programów obsługi żądań. Dodatkowe programy obsługi żądań, takie jak kontrolery MVC, działają równolegle ze skonfigurowanymi usługami gRPC.

### <a name="configure-kestrel"></a>Konfigurowanie pustułki

Punkty końcowe pustułki gRPC:

* Wymagaj protokołu HTTP/2.
* Powinny być zabezpieczone za pomocą [zabezpieczeń warstwy transportowej (TLS)](https://tools.ietf.org/html/rfc5246).

#### <a name="http2"></a>HTTP/2

GRPC wymaga protokołu HTTP/2. gRPC dla ASP.NET Core sprawdza poprawność [httpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) jest `HTTP/2`.

Kestrel [obsługuje protokół HTTP/2](xref:fundamentals/servers/kestrel#http2-support) w większości nowoczesnych systemów operacyjnych. Punkty końcowe pustułki są domyślnie skonfigurowane do obsługi połączeń HTTP/1.1 i HTTP/2.

#### <a name="tls"></a>TLS

Punkty końcowe pustułki stosowane w gRPC powinny być zabezpieczone TLS. W rozwoju punktu końcowego zabezpieczonego tls `https://localhost:5001` jest tworzony automatycznie w momencie obecności certyfikatu rozwoju ASP.NET Core. Nie jest wymagana żadna konfiguracja. `https` Prefiks weryfikuje kestrel punkt końcowy używa TLS.

W produkcji TLS musi być jawnie skonfigurowany. W poniższym przykładzie *appsettings.json* znajduje się punkt końcowy HTTP/2 zabezpieczony protokołem TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternatywnie, punkty końcowe kestrel można skonfigurować w *Program.cs:*

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Negocjacje protokołu

TLS służy do więcej niż zabezpieczania komunikacji. Uzgadnianie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS służy do negocjowania protokołu połączenia między klientem a serwerem, gdy punkt końcowy obsługuje wiele protokołów. Ta negocjacja określa, czy połączenie używa protokołu HTTP/1.1 lub HTTP/2.

Jeśli punkt końcowy HTTP/2 jest skonfigurowany bez protokołu TLS, punkt końcowy [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) musi być ustawiony na `HttpProtocols.Http2`. Punktu końcowego z wieloma protokołami `HttpProtocols.Http1AndHttp2`(na przykład ) nie można używać bez protokołu TLS, ponieważ nie ma żadnych negocjacji. Wszystkie połączenia z niezabezpieczonym punktem końcowym domyślnie HTTP/1.1 i wywołania gRPC nie powiedzie się.

Aby uzyskać więcej informacji na temat włączania protokołu HTTP/2 i TLS z kestrelem, zobacz [Konfiguracja punktu końcowego kestrelu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> system macOS nie obsługuje ASP.NET core gRPC z TLS. Dodatkowa konfiguracja jest wymagana do pomyślnego uruchomienia usług gRPC w systemie macOS. Aby uzyskać więcej informacji, zobacz [Nie można uruchomić aplikacji ASP.NET Core gRPC w systemie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Integracja z ASP.NET podstawowymi interfejsami API

Usługi gRPC mają pełny dostęp do ASP.NET podstawowych funkcji, takich jak [iniekcja zależności](xref:fundamentals/dependency-injection) (DI) i [rejestrowanie.](xref:fundamentals/logging/index) Na przykład implementacji usługi można rozwiązać usługi rejestratora z kontenera DI za pośrednictwem konstruktora:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Domyślnie implementacja usługi gRPC można rozwiązać inne usługi DI z dowolnego okresu istnienia (Singleton, Scoped lub Przejściowy).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Rozwiązywanie problemów z protokołem HttpContext w metodach gRPC

Interfejs API gRPC zapewnia dostęp do niektórych danych komunikatów HTTP/2, takich jak metoda, host, nagłówek i zwiastuny. Dostęp odbywa `ServerCallContext` się za pośrednictwem argumentu przekazywanego do każdej metody gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`nie zapewnia pełnego `HttpContext` dostępu do wszystkich ASP.NET interfejsów API. Metoda `GetHttpContext` rozszerzenia zapewnia pełny `HttpContext` dostęp do reprezentującego podstawową wiadomość HTTP/2 w ASP.NET interfejsów API:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
