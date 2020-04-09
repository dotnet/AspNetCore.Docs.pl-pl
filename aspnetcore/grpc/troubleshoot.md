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
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="2f090-103">Rozwiązywanie problemów z gRPC w programie .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f090-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="2f090-104">Przez [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2f090-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2f090-105">W tym dokumencie omówiono często spotykane problemy podczas tworzenia aplikacji gRPC w programie .NET.</span><span class="sxs-lookup"><span data-stu-id="2f090-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="2f090-106">Niezgodność między konfiguracją SSL/TLS klienta i usługi</span><span class="sxs-lookup"><span data-stu-id="2f090-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="2f090-107">Szablon gRPC i przykłady używają [zabezpieczeń TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) do domyślnego zabezpieczania usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f090-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="2f090-108">Klienci gRPC muszą korzystać z bezpiecznego połączenia, aby pomyślnie wywołać zabezpieczone usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f090-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="2f090-109">Możesz sprawdzić, ASP.NET usługa Core gRPC używa protokołu TLS w dziennikach zapisanych w uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2f090-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="2f090-110">Usługa będzie nasłuchiwanie w punkcie końcowym HTTPS:</span><span class="sxs-lookup"><span data-stu-id="2f090-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="2f090-111">Klient .NET Core `https` musi używać w adresie serwera do nawiązywać połączenia z zabezpieczonym połączeniem:</span><span class="sxs-lookup"><span data-stu-id="2f090-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="2f090-112">Wszystkie implementacje klienta gRPC obsługują TLS.</span><span class="sxs-lookup"><span data-stu-id="2f090-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="2f090-113">Klienci gRPC z innych języków zazwyczaj wymagają `SslCredentials`kanału skonfigurowany z .</span><span class="sxs-lookup"><span data-stu-id="2f090-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="2f090-114">`SslCredentials`określa certyfikat, który będzie używany przez klienta i musi być używany zamiast niezabezpieczonych poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="2f090-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="2f090-115">Przykłady konfigurowania różnych implementacji klienta gRPC w celu używania protokołu TLS można znaleźć w części [GRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="2f090-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="2f090-116">Wywoływanie usługi gRPC z niezaufanym/nieprawidłowym certyfikatem</span><span class="sxs-lookup"><span data-stu-id="2f090-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="2f090-117">Klient gRPC .NET wymaga, aby usługa miała zaufany certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2f090-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="2f090-118">Podczas wywoływania usługi gRPC bez zaufanego certyfikatu zwracany jest następujący komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="2f090-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="2f090-119">Nieobsługiwał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="2f090-119">Unhandled exception.</span></span> <span data-ttu-id="2f090-120">System.Net.http.httpRequestException: Nie można ustanowić połączenia SSL, zobacz wyjątek wewnętrzny.</span><span class="sxs-lookup"><span data-stu-id="2f090-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="2f090-121">---> System.Security.Authentication.AuthenticationException: Certyfikat zdalny jest nieprawidłowy zgodnie z procedurą sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="2f090-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="2f090-122">Ten błąd może zostać wyświetlony, jeśli testujesz aplikację lokalnie, a ASP.NET certyfikat rozwoju HTTPS core nie jest zaufany.</span><span class="sxs-lookup"><span data-stu-id="2f090-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="2f090-123">Aby uzyskać instrukcje dotyczące rozwiązania tego problemu, zobacz [Ufanie ASP.NET podstawowy certyfikat rozwoju HTTPS w systemach Windows i macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="2f090-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="2f090-124">Jeśli wywołujesz usługę gRPC na innym komputerze i nie możesz ufać certyfikatowi, klient gRPC można skonfigurować tak, aby ignorował nieprawidłowy certyfikat.</span><span class="sxs-lookup"><span data-stu-id="2f090-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="2f090-125">Poniższy kod używa [httpClientHandler.ServerCertificateCustomValidationCallback,](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) aby zezwolić na wywołania bez zaufanego certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="2f090-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

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
> <span data-ttu-id="2f090-126">Niezaufane certyfikaty powinny być używane tylko podczas tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2f090-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="2f090-127">Aplikacje produkcyjne powinny zawsze używać prawidłowych certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="2f090-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="2f090-128">Wywoływanie niezabezpieczonych usług gRPC za pomocą klienta .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f090-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="2f090-129">Dodatkowa konfiguracja jest wymagana do wywoływania niezabezpieczonych usług gRPC za pomocą klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f090-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="2f090-130">Klient gRPC musi `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` ustawić `true` przełącznik `http` i używać w adresie serwera:</span><span class="sxs-lookup"><span data-stu-id="2f090-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="2f090-131">Nie można uruchomić aplikacji ASP.NET Core gRPC w systemie macOS</span><span class="sxs-lookup"><span data-stu-id="2f090-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="2f090-132">Pustułka nie obsługuje protokołu HTTP/2 z protokołem TLS w systemie macOS i starszych wersjach systemu Windows, takich jak Windows 7.</span><span class="sxs-lookup"><span data-stu-id="2f090-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="2f090-133">Szablon gRPC ASP.NET Core i przykłady domyślnie używają protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2f090-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="2f090-134">Podczas próby uruchomienia serwera gRPC zostanie wyświetlony następujący komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="2f090-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="2f090-135">Nie można https://localhost:5001 powiązać z interfejsem zwrotnym IPv4: "HTTP/2 over TLS nie jest obsługiwany w systemie macOS z powodu braku obsługi sieci ALPN.".</span><span class="sxs-lookup"><span data-stu-id="2f090-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="2f090-136">Aby obejść ten problem, skonfiguruj Kestrel i klienta gRPC do używania protokołu HTTP/2 *bez* protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2f090-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="2f090-137">Należy to zrobić tylko podczas rozwoju.</span><span class="sxs-lookup"><span data-stu-id="2f090-137">You should only do this during development.</span></span> <span data-ttu-id="2f090-138">Niekorzyżyć TLS spowoduje wiadomości gRPC są wysyłane bez szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="2f090-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="2f090-139">Pustułka musi skonfigurować punkt końcowy HTTP/2 bez TLS w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="2f090-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="2f090-140">Gdy punkt końcowy HTTP/2 jest skonfigurowany bez protokołu TLS, punkt końcowy [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) musi być ustawiony na `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="2f090-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="2f090-141">`HttpProtocols.Http1AndHttp2`nie można użyć, ponieważ protokół TLS jest wymagany do negocjowania protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2f090-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="2f090-142">Bez protokołu TLS wszystkie połączenia z domyślnym punktem końcowym http/1.1 i wywołania gRPC kończą się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="2f090-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="2f090-143">Klient gRPC musi być również skonfigurowany tak, aby nie używał protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="2f090-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="2f090-144">Aby uzyskać więcej informacji, zobacz [Wywoływanie niezabezpieczonych usług gRPC za pomocą klienta .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="2f090-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="2f090-145">Http/2 bez protokołu TLS powinny być używane tylko podczas tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2f090-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="2f090-146">Aplikacje produkcyjne powinny zawsze używać zabezpieczeń transportu.</span><span class="sxs-lookup"><span data-stu-id="2f090-146">Production apps should always use transport security.</span></span> <span data-ttu-id="2f090-147">Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń w gRPC dla ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="2f090-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="2f090-148">Zasoby gRPC C# nie są kodem generowanym z plików .proto</span><span class="sxs-lookup"><span data-stu-id="2f090-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="2f090-149">Generowanie kodu gRPC konkretnych klientów i klas podstawowych usług wymaga, aby pliki protobuf i oprzyrządowanie były odwoływane z projektu.</span><span class="sxs-lookup"><span data-stu-id="2f090-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="2f090-150">Należy dołączyć:</span><span class="sxs-lookup"><span data-stu-id="2f090-150">You must include:</span></span>

* <span data-ttu-id="2f090-151">*.proto* plików, których chcesz `<Protobuf>` użyć w grupie elementów.</span><span class="sxs-lookup"><span data-stu-id="2f090-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="2f090-152">[Importowane pliki *proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muszą się odwoływać do projektu.</span><span class="sxs-lookup"><span data-stu-id="2f090-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="2f090-153">Odniesienie do pakietu oprzyrządowania gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="2f090-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="2f090-154">Aby uzyskać więcej informacji na temat generowania <xref:grpc/basics>zasobów gRPC C#, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2f090-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="2f090-155">Domyślnie `<Protobuf>` odwołanie generuje klienta betonu i klasy podstawowej usługi.</span><span class="sxs-lookup"><span data-stu-id="2f090-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="2f090-156">Atrybut elementu odniesienia `GrpcServices` może służyć do ograniczenia generowania zasobów C#.</span><span class="sxs-lookup"><span data-stu-id="2f090-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="2f090-157">Prawidłowe `GrpcServices` opcje to:</span><span class="sxs-lookup"><span data-stu-id="2f090-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="2f090-158">`Both`(domyślnie, gdy nie ma)</span><span class="sxs-lookup"><span data-stu-id="2f090-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="2f090-159">Aplikacja sieci Web ASP.NET Core obsługująca usługi gRPC wymaga tylko wygenerowanej klasy podstawowej usługi:</span><span class="sxs-lookup"><span data-stu-id="2f090-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="2f090-160">Aplikacja kliencka gRPC dokonująca wywołań gRPC wymaga tylko wygenerowanego konkretnego klienta:</span><span class="sxs-lookup"><span data-stu-id="2f090-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="2f090-161">Projekty WPF nie mogą wygenerować zasobów gRPC C# z plików .proto</span><span class="sxs-lookup"><span data-stu-id="2f090-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="2f090-162">Projekty WPF mają [znany problem,](https://github.com/dotnet/wpf/issues/810) który uniemożliwia generowanie kodu gRPC działa poprawnie.</span><span class="sxs-lookup"><span data-stu-id="2f090-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="2f090-163">Wszystkie typy gRPC generowane w projekcie `Grpc.Tools` WPF przez odwołanie i *.proto* pliki utworzy błędy kompilacji, gdy używane:</span><span class="sxs-lookup"><span data-stu-id="2f090-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="2f090-164">błąd CS0246: Nie można odnaleźć nazwy typu lub obszaru nazw "MyGrpcServices" (czy brakuje dyrektywy lub odwołania do zestawu?)</span><span class="sxs-lookup"><span data-stu-id="2f090-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="2f090-165">Można obejść ten problem, aby:</span><span class="sxs-lookup"><span data-stu-id="2f090-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="2f090-166">Utwórz nowy projekt biblioteki klas .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f090-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="2f090-167">W nowym projekcie dodaj odwołania, aby włączyć [generowanie kodu C# z \* \*plików .proto:\* ](xref:grpc/basics#generated-c-assets)</span><span class="sxs-lookup"><span data-stu-id="2f090-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="2f090-168">Dodaj odwołanie do pakietu [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="2f090-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="2f090-169">Dodaj \* \*\* pliki .proto `<Protobuf>` do grupy elementów.</span><span class="sxs-lookup"><span data-stu-id="2f090-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="2f090-170">W aplikacji WPF dodaj odwołanie do nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="2f090-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="2f090-171">Aplikacja WPF można użyć gRPC generowane typy z nowego projektu biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="2f090-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
