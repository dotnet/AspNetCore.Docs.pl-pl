---
title: Uwierzytelnianie i autoryzacja w gRPC dla ASP.NET Core
author: jamesnk
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji w gRPC dla ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78964439"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Uwierzytelnianie i autoryzacja w gRPC dla ASP.NET Core

Przez [James Newton-King](https://twitter.com/jamesnk)

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Uwierzytelnianie użytkowników wywołujących usługę gRPC

GRPC może być używany z [uwierzytelnianiem ASP.NET Core,](xref:security/authentication/identity) aby skojarzyć użytkownika z każdym wywołaniem.

Oto `Startup.Configure` przykład, który używa gRPC i ASP.NET uwierzytelniania core:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> Kolejność rejestracji oprogramowania pośredniczącego uwierzytelniania ASP.NET Core ma znaczenie. Zawsze `UseAuthentication` dzwonić `UseRouting` i `UseEndpoints` `UseAuthorization` po i przed .

Mechanizm uwierzytelniania używany przez aplikację podczas wywołania musi zostać skonfigurowany. Konfiguracja uwierzytelniania `Startup.ConfigureServices` jest dodawana i będzie się różnić w zależności od mechanizmu uwierzytelniania używanego przez aplikację. Aby zapoznać się z przykładami zabezpieczania aplikacji ASP.NET Core, zobacz [Przykłady uwierzytelniania](xref:security/authentication/samples).

Po skonfigurowaniu uwierzytelniania użytkownik może uzyskać dostęp w metodach `ServerCallContext`usługi gRPC za pośrednictwem pliku .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Uwierzytelnianie tokena na okaziciela

Klient może podać token dostępu do uwierzytelniania. Serwer sprawdza poprawność tokenu i używa go do identyfikowania użytkownika.

Na serwerze uwierzytelnianie tokena okaziciela jest konfigurowane przy użyciu [oprogramowania pośredniczącego JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

W kliencie gRPC .NET token może być wysyłany z wywołaniami jako nagłówek:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

Konfigurowanie `ChannelCredentials` na kanale jest alternatywnym sposobem wysyłania tokenu do usługi za pomocą wywołań gRPC. Poświadczenia są uruchamiane za każdym razem, gdy nawiązywania wywołania gRPC, co pozwala uniknąć konieczności pisania kodu w wielu miejscach, aby samodzielnie przekazać token.

Poświadczenia w poniższym przykładzie konfiguruje kanał do wysyłania tokenu przy każdym wywołaniu gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Uwierzytelnianie certyfikatu klienta

Klient może alternatywnie dostarczyć certyfikat klienta do uwierzytelniania. [Uwierzytelnianie certyfikatów](https://tools.ietf.org/html/rfc5246#section-7.4.4) odbywa się na poziomie TLS, na długo przed tym, jak kiedykolwiek zostanie ASP.NET Core. Gdy żądanie wejdzie ASP.NET Core, [pakiet uwierzytelniania certyfikatu klienta](xref:security/authentication/certauth) umożliwia rozpoznanie certyfikatu do . `ClaimsPrincipal`

> [!NOTE]
> Host musi być skonfigurowany do akceptowania certyfikatów klientów. Zobacz [konfigurowanie hosta tak, aby wymagał certyfikatów, aby uzyskać](xref:security/authentication/certauth#configure-your-host-to-require-certificates) informacje na temat akceptowania certyfikatów klientów w kestrel, usługach IIS i platformie Azure.

W kliencie gRPC .NET certyfikat klienta `HttpClientHandler` jest dodawany do tego jest następnie używany do tworzenia klienta gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Inne mechanizmy uwierzytelniania

Wiele obsługiwanych ASP.NET przez core mechanizmów uwierzytelniania współpracuje z gRPC:

* Usługa Azure Active Directory
* Certyfikat klienta
* Serwer tożsamości
* JWT Token
* OAuth 2.0
* OpenID Connect
* WS-Federation

Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania na serwerze, zobacz [ASP.NET uwierzytelnianie podstawowe](xref:security/authentication/identity).

Konfigurowanie klienta gRPC do korzystania z uwierzytelniania będzie zależeć od używanego mechanizmu uwierzytelniania. Poprzednie przykłady tokenu nośnika i certyfikatu klienta pokazują kilka sposobów skonfigurowania klienta gRPC do wysyłania metadanych uwierzytelniania za pomocą wywołań gRPC:

* Silnie typizowane gRPC `HttpClient` klienci używają wewnętrznie. Uwierzytelnianie można skonfigurować na [stronie HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)lub przez dodanie niestandardowych `HttpClient`wystąpień Usługi [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do pliku .
* Każde wywołanie gRPC `CallOptions` ma opcjonalny argument. Nagłówki niestandardowe mogą być wysyłane przy użyciu kolekcji nagłówków opcji.

> [!NOTE]
> Uwierzytelniania systemu Windows (NTLM/Kerberos/Negotiate) nie można używać z gRPC. GRPC wymaga protokołu HTTP/2, a protokół HTTP/2 nie obsługuje uwierzytelniania systemu Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autoryzowanie użytkowników do uzyskiwania dostępu do usług i metod serwisowych

Domyślnie wszystkie metody w usłudze mogą być wywoływane przez nieuwierzyliowanych użytkowników. Aby wymagać uwierzytelnienia, zastosuj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut do usługi:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Można użyć argumentów konstruktora `[Authorize]` i właściwości atrybutu, aby ograniczyć dostęp tylko do użytkowników pasujących do określonych [zasad autoryzacji](xref:security/authorization/policies). Na przykład jeśli masz niestandardowe `MyAuthorizationPolicy`zasady autoryzacji o nazwie , upewnij się, że tylko użytkownicy pasujące do tej zasady mogą uzyskać dostęp do usługi przy użyciu następującego kodu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Poszczególne metody usługi `[Authorize]` mogą mieć atrybut stosowane, jak również. Jeśli bieżący użytkownik nie pasuje do zasad zastosowanych zarówno do metody, **jak** i klasy, do obiektu wywołującego jest zwracany błąd:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Uwierzytelnianie tokena nośnika w ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Konfigurowanie uwierzytelniania certyfikatu klienta w ASP.NET Core](xref:security/authentication/certauth)
