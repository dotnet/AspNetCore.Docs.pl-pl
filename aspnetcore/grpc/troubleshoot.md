---
title: Rozwiązywanie problemów z gRPC w programie .NET Core
author: jamesnk
description: Rozwiązywanie problemów z błędami podczas korzystania z gRPC w programie .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664130"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Rozwiązywanie problemów z gRPC w programie .NET Core

Przez [James Newton-King](https://twitter.com/jamesnk)

W tym dokumencie omówiono często spotykane problemy podczas tworzenia aplikacji gRPC w programie .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Niezgodność między konfiguracją SSL/TLS klienta i usługi

Szablon gRPC i przykłady używają [zabezpieczeń TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) do domyślnego zabezpieczania usług gRPC. Klienci gRPC muszą korzystać z bezpiecznego połączenia, aby pomyślnie wywołać zabezpieczone usługi gRPC.

Możesz sprawdzić, ASP.NET usługa Core gRPC używa protokołu TLS w dziennikach zapisanych w uruchomieniu aplikacji. Usługa będzie nasłuchiwanie w punkcie końcowym HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Klient .NET Core `https` musi używać w adresie serwera do nawiązywać połączenia z zabezpieczonym połączeniem:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Wszystkie implementacje klienta gRPC obsługują TLS. Klienci gRPC z innych języków zazwyczaj wymagają `SslCredentials`kanału skonfigurowany z . `SslCredentials`określa certyfikat, który będzie używany przez klienta i musi być używany zamiast niezabezpieczonych poświadczeń. Przykłady konfigurowania różnych implementacji klienta gRPC w celu używania protokołu TLS można znaleźć w części [GRPC Authentication](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Wywoływanie usługi gRPC z niezaufanym/nieprawidłowym certyfikatem

Klient gRPC .NET wymaga, aby usługa miała zaufany certyfikat. Podczas wywoływania usługi gRPC bez zaufanego certyfikatu zwracany jest następujący komunikat o błędzie:

> Nieobsługiwał wyjątek. System.Net.http.httpRequestException: Nie można ustanowić połączenia SSL, zobacz wyjątek wewnętrzny.
> ---> System.Security.Authentication.AuthenticationException: Certyfikat zdalny jest nieprawidłowy zgodnie z procedurą sprawdzania poprawności.

Ten błąd może zostać wyświetlony, jeśli testujesz aplikację lokalnie, a ASP.NET certyfikat rozwoju HTTPS core nie jest zaufany. Aby uzyskać instrukcje dotyczące rozwiązania tego problemu, zobacz [Ufanie ASP.NET podstawowy certyfikat rozwoju HTTPS w systemach Windows i macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Jeśli wywołujesz usługę gRPC na innym komputerze i nie możesz ufać certyfikatowi, klient gRPC można skonfigurować tak, aby ignorował nieprawidłowy certyfikat. Poniższy kod używa [httpClientHandler.ServerCertificateCustomValidationCallback,](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) aby zezwolić na wywołania bez zaufanego certyfikatu:

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Niezaufane certyfikaty powinny być używane tylko podczas tworzenia aplikacji. Aplikacje produkcyjne powinny zawsze używać prawidłowych certyfikatów.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Wywoływanie niezabezpieczonych usług gRPC za pomocą klienta .NET Core

Dodatkowa konfiguracja jest wymagana do wywoływania niezabezpieczonych usług gRPC za pomocą klienta .NET Core. Klient gRPC musi `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` ustawić `true` przełącznik `http` i używać w adresie serwera:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Nie można uruchomić aplikacji ASP.NET Core gRPC w systemie macOS

Pustułka nie obsługuje protokołu HTTP/2 z protokołem TLS w systemie macOS i starszych wersjach systemu Windows, takich jak Windows 7. Szablon gRPC ASP.NET Core i przykłady domyślnie używają protokołu TLS. Podczas próby uruchomienia serwera gRPC zostanie wyświetlony następujący komunikat o błędzie:

> Nie można https://localhost:5001 powiązać z interfejsem zwrotnym IPv4: "HTTP/2 over TLS nie jest obsługiwany w systemie macOS z powodu braku obsługi sieci ALPN.".

Aby obejść ten problem, skonfiguruj Kestrel i klienta gRPC do używania protokołu HTTP/2 *bez* protokołu TLS. Należy to zrobić tylko podczas rozwoju. Niekorzyżyć TLS spowoduje wiadomości gRPC są wysyłane bez szyfrowania.

Pustułka musi skonfigurować punkt końcowy HTTP/2 bez TLS w *Program.cs:*

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Gdy punkt końcowy HTTP/2 jest skonfigurowany bez protokołu TLS, punkt końcowy [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) musi być ustawiony na `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2`nie można użyć, ponieważ protokół TLS jest wymagany do negocjowania protokołu HTTP/2. Bez protokołu TLS wszystkie połączenia z domyślnym punktem końcowym http/1.1 i wywołania gRPC kończą się niepowodzeniem.

Klient gRPC musi być również skonfigurowany tak, aby nie używał protokołu TLS. Aby uzyskać więcej informacji, zobacz [Wywoływanie niezabezpieczonych usług gRPC za pomocą klienta .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> Http/2 bez protokołu TLS powinny być używane tylko podczas tworzenia aplikacji. Aplikacje produkcyjne powinny zawsze używać zabezpieczeń transportu. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń w gRPC dla ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>Zasoby gRPC C# nie są kodem generowanym z plików .proto

Generowanie kodu gRPC konkretnych klientów i klas podstawowych usług wymaga, aby pliki protobuf i oprzyrządowanie były odwoływane z projektu. Należy dołączyć:

* *.proto* plików, których chcesz `<Protobuf>` użyć w grupie elementów. [Importowane pliki *proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muszą się odwoływać do projektu.
* Odniesienie do pakietu oprzyrządowania gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Aby uzyskać więcej informacji na temat generowania <xref:grpc/basics>zasobów gRPC C#, zobacz .

Domyślnie `<Protobuf>` odwołanie generuje klienta betonu i klasy podstawowej usługi. Atrybut elementu odniesienia `GrpcServices` może służyć do ograniczenia generowania zasobów C#. Prawidłowe `GrpcServices` opcje to:

* `Both`(domyślnie, gdy nie ma)
* `Server`
* `Client`
* `None`

Aplikacja sieci Web ASP.NET Core obsługująca usługi gRPC wymaga tylko wygenerowanej klasy podstawowej usługi:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Aplikacja kliencka gRPC dokonująca wywołań gRPC wymaga tylko wygenerowanego konkretnego klienta:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Projekty WPF nie mogą wygenerować zasobów gRPC C# z plików .proto

Projekty WPF mają [znany problem,](https://github.com/dotnet/wpf/issues/810) który uniemożliwia generowanie kodu gRPC działa poprawnie. Wszystkie typy gRPC generowane w projekcie `Grpc.Tools` WPF przez odwołanie i *.proto* pliki utworzy błędy kompilacji, gdy używane:

> błąd CS0246: Nie można odnaleźć nazwy typu lub obszaru nazw "MyGrpcServices" (czy brakuje dyrektywy lub odwołania do zestawu?)

Można obejść ten problem, aby:

1. Utwórz nowy projekt biblioteki klas .NET Core.
2. W nowym projekcie dodaj odwołania, aby włączyć [generowanie kodu C# z * \*plików .proto:* ](xref:grpc/basics#generated-c-assets)
    * Dodaj odwołanie do pakietu [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
    * Dodaj * \** pliki .proto `<Protobuf>` do grupy elementów.
3. W aplikacji WPF dodaj odwołanie do nowego projektu.

Aplikacja WPF można użyć gRPC generowane typy z nowego projektu biblioteki klas.

[!INCLUDE[](~/includes/gRPCazure.md)]
