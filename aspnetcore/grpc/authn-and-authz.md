---
<span data-ttu-id="98248-101">title: Author: Description: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="98248-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="98248-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98248-102">'Blazor'</span></span>
- <span data-ttu-id="98248-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98248-103">'Identity'</span></span>
- <span data-ttu-id="98248-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98248-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="98248-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98248-105">'Razor'</span></span>
- <span data-ttu-id="98248-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="98248-106">'SignalR' uid:</span></span> 

---

# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="98248-107">Uwierzytelnianie i autoryzacja w programie gRPC for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98248-107">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="98248-108">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="98248-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="98248-109">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="98248-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="98248-110">Uwierzytelnianie użytkowników wywołujących usługę gRPC</span><span class="sxs-lookup"><span data-stu-id="98248-110">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="98248-111">gRPC można użyć z [uwierzytelnianiem ASP.NET Core](xref:security/authentication/identity) , aby skojarzyć użytkownika z każdym wywołaniem.</span><span class="sxs-lookup"><span data-stu-id="98248-111">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="98248-112">Poniżej przedstawiono przykład `Startup.Configure` użycia gRPC i uwierzytelniania ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="98248-112">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="98248-113">Kolejność, w której zarejestrowano zagadnienia dotyczące oprogramowania pośredniczącego uwierzytelniania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98248-113">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="98248-114">Zawsze używaj metody Call `UseAuthentication` i `UseAuthorization` After `UseRouting` i Before `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="98248-114">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="98248-115">Mechanizm uwierzytelniania używany przez aplikację podczas wywołania musi być skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="98248-115">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="98248-116">Konfiguracja uwierzytelniania jest dodawana w programie `Startup.ConfigureServices` i będzie się różnić w zależności od mechanizmu uwierzytelniania używanego przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="98248-116">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="98248-117">Przykłady metod zabezpieczania aplikacji ASP.NET Core można znaleźć w temacie [przykłady uwierzytelniania](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="98248-117">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="98248-118">Po skonfigurowaniu uwierzytelniania można uzyskać dostęp do użytkownika w metodach usługi gRPC za pośrednictwem `ServerCallContext` .</span><span class="sxs-lookup"><span data-stu-id="98248-118">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="98248-119">Uwierzytelnianie tokenu okaziciela</span><span class="sxs-lookup"><span data-stu-id="98248-119">Bearer token authentication</span></span>

<span data-ttu-id="98248-120">Klient może udostępnić token dostępu do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="98248-120">The client can provide an access token for authentication.</span></span> <span data-ttu-id="98248-121">Serwer sprawdza token i używa go do identyfikowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="98248-121">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="98248-122">Na serwerze uwierzytelnianie tokenu okaziciela jest konfigurowane przy użyciu [oprogramowania pośredniczącego okaziciela JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="98248-122">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="98248-123">W kliencie .NET gRPC token może być wysyłany z wywołaniami jako nagłówek:</span><span class="sxs-lookup"><span data-stu-id="98248-123">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="98248-124">Konfigurowanie `ChannelCredentials` w kanale jest alternatywnym sposobem wysyłania tokenu do usługi za pomocą wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="98248-124">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="98248-125">Poświadczenie jest uruchamiane za każdym razem, gdy nastąpi wywołanie gRPC, które pozwala uniknąć konieczności pisania kodu w wielu miejscach w celu samodzielnego przekazania tokenu.</span><span class="sxs-lookup"><span data-stu-id="98248-125">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="98248-126">Poświadczenie w poniższym przykładzie służy do konfigurowania kanału do wysyłania tokenu przy każdym wywołaniu gRPC:</span><span class="sxs-lookup"><span data-stu-id="98248-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="98248-127">Uwierzytelnianie certyfikatu klienta</span><span class="sxs-lookup"><span data-stu-id="98248-127">Client certificate authentication</span></span>

<span data-ttu-id="98248-128">Klient może również udostępnić certyfikat klienta na potrzeby uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="98248-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="98248-129">[Uwierzytelnianie certyfikatu](https://tools.ietf.org/html/rfc5246#section-7.4.4) odbywa się na poziomie protokołu TLS, o ile nie zostanie kiedykolwiek przeASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98248-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="98248-130">Gdy żądanie zostanie wprowadzone ASP.NET Core, [Pakiet uwierzytelniania certyfikatu klienta](xref:security/authentication/certauth) umożliwia rozpoznanie certyfikatu w usłudze `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="98248-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="98248-131">Host musi być skonfigurowany do akceptowania certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="98248-131">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="98248-132">Zobacz [Konfigurowanie hosta, aby wymagać certyfikatów](xref:security/authentication/certauth#configure-your-host-to-require-certificates) w celu uzyskania informacji na temat akceptowania certyfikatów klienta w usługach Kestrel, IIS i Azure.</span><span class="sxs-lookup"><span data-stu-id="98248-132">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="98248-133">W kliencie .NET gRPC certyfikat klienta zostaje dodany do programu `HttpClientHandler` , który jest następnie używany do tworzenia klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="98248-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="98248-134">Inne mechanizmy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="98248-134">Other authentication mechanisms</span></span>

<span data-ttu-id="98248-135">Wiele ASP.NET Core obsługiwanych mechanizmów uwierzytelniania współpracuje z gRPC:</span><span class="sxs-lookup"><span data-stu-id="98248-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="98248-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="98248-136">Azure Active Directory</span></span>
* <span data-ttu-id="98248-137">Certyfikat klienta</span><span class="sxs-lookup"><span data-stu-id="98248-137">Client Certificate</span></span>
* <span data-ttu-id="98248-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="98248-138">IdentityServer</span></span>
* <span data-ttu-id="98248-139">Token JWT</span><span class="sxs-lookup"><span data-stu-id="98248-139">JWT Token</span></span>
* <span data-ttu-id="98248-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="98248-140">OAuth 2.0</span></span>
* <span data-ttu-id="98248-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="98248-141">OpenID Connect</span></span>
* <span data-ttu-id="98248-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="98248-142">WS-Federation</span></span>

<span data-ttu-id="98248-143">Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania na serwerze, zobacz [ASP.NET Core Authentication](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="98248-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="98248-144">Skonfigurowanie klienta gRPC do korzystania z uwierzytelniania będzie zależeć od używanego mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="98248-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="98248-145">W poprzednim tokenie okaziciela i certyfikatach klienta przedstawiono kilka sposobów skonfigurowania klienta gRPC do wysyłania metadanych uwierzytelniania z wywołaniami gRPC:</span><span class="sxs-lookup"><span data-stu-id="98248-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="98248-146">Klienci z jednoznacznie określonym typem gRPC używają `HttpClient` wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="98248-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="98248-147">Uwierzytelnianie można skonfigurować w usłudze [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)lub poprzez dodanie wystąpień [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do programu `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="98248-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="98248-148">Każde wywołanie gRPC ma opcjonalny `CallOptions` argument.</span><span class="sxs-lookup"><span data-stu-id="98248-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="98248-149">Nagłówki niestandardowe można wysyłać przy użyciu kolekcji nagłówków opcji.</span><span class="sxs-lookup"><span data-stu-id="98248-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="98248-150">Uwierzytelnianie systemu Windows (NTLM/Kerberos/Negotiate) nie może być używane z gRPC.</span><span class="sxs-lookup"><span data-stu-id="98248-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="98248-151">gRPC wymaga protokołu HTTP/2, a protokół HTTP/2 nie obsługuje uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="98248-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="98248-152">Autoryzuj użytkowników do uzyskiwania dostępu do usług i metod usług</span><span class="sxs-lookup"><span data-stu-id="98248-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="98248-153">Domyślnie wszystkie metody w usłudze mogą być wywoływane przez nieuwierzytelnionych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="98248-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="98248-154">Aby wymagać uwierzytelniania, należy zastosować [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut do usługi:</span><span class="sxs-lookup"><span data-stu-id="98248-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="98248-155">Można użyć argumentów konstruktora i właściwości `[Authorize]` atrybutu w celu ograniczenia dostępu tylko do użytkowników zgodnych z określonymi [zasadami autoryzacji](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="98248-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="98248-156">Na przykład jeśli masz zasady autoryzacji niestandardowej o nazwie `MyAuthorizationPolicy` , upewnij się, że tylko użytkownicy pasujący do tych zasad mogą uzyskać dostęp do usługi przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="98248-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="98248-157">Do poszczególnych metod usługi można `[Authorize]` zastosować atrybut.</span><span class="sxs-lookup"><span data-stu-id="98248-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="98248-158">Jeśli bieżący użytkownik nie jest zgodny z zasadami stosowanymi **zarówno** w metodzie, jak i klasie, do obiektu wywołującego jest zwracany błąd:</span><span class="sxs-lookup"><span data-stu-id="98248-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="98248-159">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="98248-159">Additional resources</span></span>

* [<span data-ttu-id="98248-160">Uwierzytelnianie tokenu okaziciela w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98248-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="98248-161">Konfigurowanie uwierzytelniania certyfikatu klienta w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98248-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
