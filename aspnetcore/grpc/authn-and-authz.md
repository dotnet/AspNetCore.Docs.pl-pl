---
title: Uwierzytelnianie i autoryzacja w programie gRPC for ASP.NET Core
author: jamesnk
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji w programie gRPC for ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673981"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="3f9e4-103">Uwierzytelnianie i autoryzacja w programie gRPC for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f9e4-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="3f9e4-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3f9e4-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3f9e4-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3f9e4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="3f9e4-106">Uwierzytelnianie użytkowników wywołujących usługę gRPC</span><span class="sxs-lookup"><span data-stu-id="3f9e4-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="3f9e4-107">gRPC można użyć z [uwierzytelnianiem ASP.NET Core](xref:security/authentication/identity) , aby skojarzyć użytkownika z każdym wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="3f9e4-108">Poniżej przedstawiono przykład `Startup.Configure` użycia gRPC i uwierzytelniania ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="3f9e4-109">Kolejność, w której zarejestrowano zagadnienia dotyczące oprogramowania pośredniczącego uwierzytelniania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="3f9e4-110">Zawsze używaj metody Call `UseAuthentication` i `UseAuthorization` After `UseRouting` i Before `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="3f9e4-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="3f9e4-111">Mechanizm uwierzytelniania używany przez aplikację podczas wywołania musi być skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="3f9e4-112">Konfiguracja uwierzytelniania jest dodawana w programie `Startup.ConfigureServices` i będzie się różnić w zależności od mechanizmu uwierzytelniania używanego przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="3f9e4-113">Przykłady metod zabezpieczania aplikacji ASP.NET Core można znaleźć w temacie [przykłady uwierzytelniania](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="3f9e4-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="3f9e4-114">Po skonfigurowaniu uwierzytelniania można uzyskać dostęp do użytkownika w metodach usługi gRPC za pośrednictwem `ServerCallContext` .</span><span class="sxs-lookup"><span data-stu-id="3f9e4-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="3f9e4-115">Uwierzytelnianie tokenu okaziciela</span><span class="sxs-lookup"><span data-stu-id="3f9e4-115">Bearer token authentication</span></span>

<span data-ttu-id="3f9e4-116">Klient może udostępnić token dostępu do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="3f9e4-117">Serwer sprawdza token i używa go do identyfikowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="3f9e4-118">Na serwerze uwierzytelnianie tokenu okaziciela jest konfigurowane przy użyciu [oprogramowania pośredniczącego okaziciela JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="3f9e4-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="3f9e4-119">W kliencie .NET gRPC token może być wysyłany z wywołaniami przy użyciu `Metadata` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="3f9e4-120">Wpisy w `Metadata` kolekcji są wysyłane z wywołaniem gRPC jako nagłówki http:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

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

<span data-ttu-id="3f9e4-121">Konfigurowanie `ChannelCredentials` w kanale jest alternatywnym sposobem wysyłania tokenu do usługi za pomocą wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="3f9e4-122">`ChannelCredentials`Może zawierać `CallCredentials` , która umożliwia automatyczne ustawianie `Metadata` .</span><span class="sxs-lookup"><span data-stu-id="3f9e4-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="3f9e4-123">`CallCredentials` jest uruchamiany za każdym razem, gdy nastąpi wywołanie gRPC, które pozwala uniknąć konieczności pisania kodu w wielu miejscach w celu samodzielnego przekazania tokenu.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="3f9e4-124">Należy pamiętać, że `CallCredentials` są stosowane tylko wtedy, gdy kanał jest zabezpieczony przy użyciu protokołu TLS.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="3f9e4-125">`CallCredentials` nie są stosowane dla niezabezpieczonych kanałów niezwiązanych z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="3f9e4-126">Poświadczenie w poniższym przykładzie służy do konfigurowania kanału do wysyłania tokenu przy każdym wywołaniu gRPC:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="3f9e4-127">Uwierzytelnianie certyfikatu klienta</span><span class="sxs-lookup"><span data-stu-id="3f9e4-127">Client certificate authentication</span></span>

<span data-ttu-id="3f9e4-128">Klient może również udostępnić certyfikat klienta na potrzeby uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="3f9e4-129">[Uwierzytelnianie certyfikatu](https://tools.ietf.org/html/rfc5246#section-7.4.4) odbywa się na poziomie protokołu TLS, o ile nie zostanie kiedykolwiek przeASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="3f9e4-130">Gdy żądanie zostanie wprowadzone ASP.NET Core, [Pakiet uwierzytelniania certyfikatu klienta](xref:security/authentication/certauth) umożliwia rozpoznanie certyfikatu w usłudze `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="3f9e4-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="3f9e4-131">Skonfiguruj serwer do akceptowania certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="3f9e4-132">Aby uzyskać informacje dotyczące akceptowania certyfikatów klienta w usłudze Kestrel, usługach IIS i platformie Azure, zobacz <xref:security/authentication/certauth#configure-your-server-to-require-certificates> .</span><span class="sxs-lookup"><span data-stu-id="3f9e4-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="3f9e4-133">W kliencie .NET gRPC certyfikat klienta zostaje dodany do programu `HttpClientHandler` , który jest następnie używany do tworzenia klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="3f9e4-134">Inne mechanizmy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="3f9e4-134">Other authentication mechanisms</span></span>

<span data-ttu-id="3f9e4-135">Wiele ASP.NET Core obsługiwanych mechanizmów uwierzytelniania współpracuje z gRPC:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="3f9e4-136">Usługa Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3f9e4-136">Azure Active Directory</span></span>
* <span data-ttu-id="3f9e4-137">Certyfikat klienta</span><span class="sxs-lookup"><span data-stu-id="3f9e4-137">Client Certificate</span></span>
* <span data-ttu-id="3f9e4-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="3f9e4-138">IdentityServer</span></span>
* <span data-ttu-id="3f9e4-139">Token JWT</span><span class="sxs-lookup"><span data-stu-id="3f9e4-139">JWT Token</span></span>
* <span data-ttu-id="3f9e4-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="3f9e4-140">OAuth 2.0</span></span>
* <span data-ttu-id="3f9e4-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="3f9e4-141">OpenID Connect</span></span>
* <span data-ttu-id="3f9e4-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="3f9e4-142">WS-Federation</span></span>

<span data-ttu-id="3f9e4-143">Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania na serwerze, zobacz [ASP.NET Core Authentication](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="3f9e4-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="3f9e4-144">Skonfigurowanie klienta gRPC do korzystania z uwierzytelniania będzie zależeć od używanego mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="3f9e4-145">W poprzednim tokenie okaziciela i certyfikatach klienta przedstawiono kilka sposobów skonfigurowania klienta gRPC do wysyłania metadanych uwierzytelniania z wywołaniami gRPC:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="3f9e4-146">Klienci z jednoznacznie określonym typem gRPC używają `HttpClient` wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="3f9e4-147">Uwierzytelnianie można skonfigurować w usłudze [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)lub poprzez dodanie wystąpień [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do programu `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="3f9e4-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="3f9e4-148">Każde wywołanie gRPC ma opcjonalny `CallOptions` argument.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="3f9e4-149">Nagłówki niestandardowe można wysyłać przy użyciu kolekcji nagłówków opcji.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="3f9e4-150">Uwierzytelnianie systemu Windows (NTLM/Kerberos/Negotiate) nie może być używane z gRPC.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="3f9e4-151">gRPC wymaga protokołu HTTP/2, a protokół HTTP/2 nie obsługuje uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="3f9e4-152">Autoryzuj użytkowników do uzyskiwania dostępu do usług i metod usług</span><span class="sxs-lookup"><span data-stu-id="3f9e4-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="3f9e4-153">Domyślnie wszystkie metody w usłudze mogą być wywoływane przez nieuwierzytelnionych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="3f9e4-154">Aby wymagać uwierzytelniania, należy zastosować [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut do usługi:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="3f9e4-155">Można użyć argumentów konstruktora i właściwości `[Authorize]` atrybutu w celu ograniczenia dostępu tylko do użytkowników zgodnych z określonymi [zasadami autoryzacji](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="3f9e4-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="3f9e4-156">Na przykład jeśli masz zasady autoryzacji niestandardowej o nazwie `MyAuthorizationPolicy` , upewnij się, że tylko użytkownicy pasujący do tych zasad mogą uzyskać dostęp do usługi przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="3f9e4-157">Do poszczególnych metod usługi można `[Authorize]` zastosować atrybut.</span><span class="sxs-lookup"><span data-stu-id="3f9e4-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="3f9e4-158">Jeśli bieżący użytkownik nie jest zgodny z zasadami stosowanymi **zarówno** w metodzie, jak i klasie, do obiektu wywołującego jest zwracany błąd:</span><span class="sxs-lookup"><span data-stu-id="3f9e4-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3f9e4-159">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3f9e4-159">Additional resources</span></span>

* [<span data-ttu-id="3f9e4-160">Uwierzytelnianie tokenu okaziciela w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f9e4-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="3f9e4-161">Konfigurowanie uwierzytelniania certyfikatu klienta w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f9e4-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
