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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="99f53-103">Uwierzytelnianie i autoryzacja w gRPC dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99f53-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="99f53-104">Przez [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="99f53-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="99f53-105">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="99f53-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="99f53-106">Uwierzytelnianie użytkowników wywołujących usługę gRPC</span><span class="sxs-lookup"><span data-stu-id="99f53-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="99f53-107">GRPC może być używany z [uwierzytelnianiem ASP.NET Core,](xref:security/authentication/identity) aby skojarzyć użytkownika z każdym wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="99f53-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="99f53-108">Oto `Startup.Configure` przykład, który używa gRPC i ASP.NET uwierzytelniania core:</span><span class="sxs-lookup"><span data-stu-id="99f53-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="99f53-109">Kolejność rejestracji oprogramowania pośredniczącego uwierzytelniania ASP.NET Core ma znaczenie.</span><span class="sxs-lookup"><span data-stu-id="99f53-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="99f53-110">Zawsze `UseAuthentication` dzwonić `UseRouting` i `UseEndpoints` `UseAuthorization` po i przed .</span><span class="sxs-lookup"><span data-stu-id="99f53-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="99f53-111">Mechanizm uwierzytelniania używany przez aplikację podczas wywołania musi zostać skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="99f53-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="99f53-112">Konfiguracja uwierzytelniania `Startup.ConfigureServices` jest dodawana i będzie się różnić w zależności od mechanizmu uwierzytelniania używanego przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="99f53-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="99f53-113">Aby zapoznać się z przykładami zabezpieczania aplikacji ASP.NET Core, zobacz [Przykłady uwierzytelniania](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="99f53-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="99f53-114">Po skonfigurowaniu uwierzytelniania użytkownik może uzyskać dostęp w metodach `ServerCallContext`usługi gRPC za pośrednictwem pliku .</span><span class="sxs-lookup"><span data-stu-id="99f53-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="99f53-115">Uwierzytelnianie tokena na okaziciela</span><span class="sxs-lookup"><span data-stu-id="99f53-115">Bearer token authentication</span></span>

<span data-ttu-id="99f53-116">Klient może podać token dostępu do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="99f53-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="99f53-117">Serwer sprawdza poprawność tokenu i używa go do identyfikowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="99f53-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="99f53-118">Na serwerze uwierzytelnianie tokena okaziciela jest konfigurowane przy użyciu [oprogramowania pośredniczącego JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="99f53-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="99f53-119">W kliencie gRPC .NET token może być wysyłany z wywołaniami jako nagłówek:</span><span class="sxs-lookup"><span data-stu-id="99f53-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="99f53-120">Konfigurowanie `ChannelCredentials` na kanale jest alternatywnym sposobem wysyłania tokenu do usługi za pomocą wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="99f53-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="99f53-121">Poświadczenia są uruchamiane za każdym razem, gdy nawiązywania wywołania gRPC, co pozwala uniknąć konieczności pisania kodu w wielu miejscach, aby samodzielnie przekazać token.</span><span class="sxs-lookup"><span data-stu-id="99f53-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="99f53-122">Poświadczenia w poniższym przykładzie konfiguruje kanał do wysyłania tokenu przy każdym wywołaniu gRPC:</span><span class="sxs-lookup"><span data-stu-id="99f53-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="99f53-123">Uwierzytelnianie certyfikatu klienta</span><span class="sxs-lookup"><span data-stu-id="99f53-123">Client certificate authentication</span></span>

<span data-ttu-id="99f53-124">Klient może alternatywnie dostarczyć certyfikat klienta do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="99f53-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="99f53-125">[Uwierzytelnianie certyfikatów](https://tools.ietf.org/html/rfc5246#section-7.4.4) odbywa się na poziomie TLS, na długo przed tym, jak kiedykolwiek zostanie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99f53-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="99f53-126">Gdy żądanie wejdzie ASP.NET Core, [pakiet uwierzytelniania certyfikatu klienta](xref:security/authentication/certauth) umożliwia rozpoznanie certyfikatu do . `ClaimsPrincipal`</span><span class="sxs-lookup"><span data-stu-id="99f53-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="99f53-127">Host musi być skonfigurowany do akceptowania certyfikatów klientów.</span><span class="sxs-lookup"><span data-stu-id="99f53-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="99f53-128">Zobacz [konfigurowanie hosta tak, aby wymagał certyfikatów, aby uzyskać](xref:security/authentication/certauth#configure-your-host-to-require-certificates) informacje na temat akceptowania certyfikatów klientów w kestrel, usługach IIS i platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="99f53-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="99f53-129">W kliencie gRPC .NET certyfikat klienta `HttpClientHandler` jest dodawany do tego jest następnie używany do tworzenia klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="99f53-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="99f53-130">Inne mechanizmy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="99f53-130">Other authentication mechanisms</span></span>

<span data-ttu-id="99f53-131">Wiele obsługiwanych ASP.NET przez core mechanizmów uwierzytelniania współpracuje z gRPC:</span><span class="sxs-lookup"><span data-stu-id="99f53-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="99f53-132">Usługa Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="99f53-132">Azure Active Directory</span></span>
* <span data-ttu-id="99f53-133">Certyfikat klienta</span><span class="sxs-lookup"><span data-stu-id="99f53-133">Client Certificate</span></span>
* <span data-ttu-id="99f53-134">Serwer tożsamości</span><span class="sxs-lookup"><span data-stu-id="99f53-134">IdentityServer</span></span>
* <span data-ttu-id="99f53-135">JWT Token</span><span class="sxs-lookup"><span data-stu-id="99f53-135">JWT Token</span></span>
* <span data-ttu-id="99f53-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="99f53-136">OAuth 2.0</span></span>
* <span data-ttu-id="99f53-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="99f53-137">OpenID Connect</span></span>
* <span data-ttu-id="99f53-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="99f53-138">WS-Federation</span></span>

<span data-ttu-id="99f53-139">Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania na serwerze, zobacz [ASP.NET uwierzytelnianie podstawowe](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="99f53-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="99f53-140">Konfigurowanie klienta gRPC do korzystania z uwierzytelniania będzie zależeć od używanego mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="99f53-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="99f53-141">Poprzednie przykłady tokenu nośnika i certyfikatu klienta pokazują kilka sposobów skonfigurowania klienta gRPC do wysyłania metadanych uwierzytelniania za pomocą wywołań gRPC:</span><span class="sxs-lookup"><span data-stu-id="99f53-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="99f53-142">Silnie typizowane gRPC `HttpClient` klienci używają wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="99f53-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="99f53-143">Uwierzytelnianie można skonfigurować na [stronie HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)lub przez dodanie niestandardowych `HttpClient`wystąpień Usługi [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do pliku .</span><span class="sxs-lookup"><span data-stu-id="99f53-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="99f53-144">Każde wywołanie gRPC `CallOptions` ma opcjonalny argument.</span><span class="sxs-lookup"><span data-stu-id="99f53-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="99f53-145">Nagłówki niestandardowe mogą być wysyłane przy użyciu kolekcji nagłówków opcji.</span><span class="sxs-lookup"><span data-stu-id="99f53-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="99f53-146">Uwierzytelniania systemu Windows (NTLM/Kerberos/Negotiate) nie można używać z gRPC.</span><span class="sxs-lookup"><span data-stu-id="99f53-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="99f53-147">GRPC wymaga protokołu HTTP/2, a protokół HTTP/2 nie obsługuje uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="99f53-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="99f53-148">Autoryzowanie użytkowników do uzyskiwania dostępu do usług i metod serwisowych</span><span class="sxs-lookup"><span data-stu-id="99f53-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="99f53-149">Domyślnie wszystkie metody w usłudze mogą być wywoływane przez nieuwierzyliowanych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="99f53-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="99f53-150">Aby wymagać uwierzytelnienia, zastosuj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut do usługi:</span><span class="sxs-lookup"><span data-stu-id="99f53-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="99f53-151">Można użyć argumentów konstruktora `[Authorize]` i właściwości atrybutu, aby ograniczyć dostęp tylko do użytkowników pasujących do określonych [zasad autoryzacji](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="99f53-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="99f53-152">Na przykład jeśli masz niestandardowe `MyAuthorizationPolicy`zasady autoryzacji o nazwie , upewnij się, że tylko użytkownicy pasujące do tej zasady mogą uzyskać dostęp do usługi przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="99f53-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="99f53-153">Poszczególne metody usługi `[Authorize]` mogą mieć atrybut stosowane, jak również.</span><span class="sxs-lookup"><span data-stu-id="99f53-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="99f53-154">Jeśli bieżący użytkownik nie pasuje do zasad zastosowanych zarówno do metody, **jak** i klasy, do obiektu wywołującego jest zwracany błąd:</span><span class="sxs-lookup"><span data-stu-id="99f53-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="99f53-155">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="99f53-155">Additional resources</span></span>

* [<span data-ttu-id="99f53-156">Uwierzytelnianie tokena nośnika w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99f53-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="99f53-157">Konfigurowanie uwierzytelniania certyfikatu klienta w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99f53-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
