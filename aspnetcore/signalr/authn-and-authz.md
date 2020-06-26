---
title: Uwierzytelnianie i autoryzacja w ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, jak używać uwierzytelniania i autoryzacji w programie ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 794465ceb69f47ee3d5cc8c100b321cb958d9cfe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407138"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="1479c-103">Uwierzytelnianie i autoryzacja w ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="1479c-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="1479c-104">Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="1479c-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="1479c-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1479c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="1479c-106">Uwierzytelnianie użytkowników łączących się z SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1479c-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="1479c-107">można go używać z [uwierzytelnianiem ASP.NET Core](xref:security/authentication/identity) , aby skojarzyć użytkownika z każdym połączeniem.</span><span class="sxs-lookup"><span data-stu-id="1479c-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="1479c-108">W centrum dane uwierzytelniania są dostępne z poziomu właściwości [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="1479c-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="1479c-109">Uwierzytelnianie umożliwia centrum wywoływanie metod we wszystkich połączeniach skojarzonych z użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="1479c-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="1479c-110">Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i grupami w programie SignalR ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="1479c-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="1479c-111">Wiele połączeń może być skojarzonych z pojedynczym użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="1479c-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="1479c-112">Poniżej przedstawiono przykład `Startup.Configure` użycia SignalR i ASP.NET Core uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="1479c-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="1479c-113">Kolejność, w której rejestrowane są SignalR zagadnienia dotyczące oprogramowania pośredniczącego uwierzytelniania i ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1479c-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="1479c-114">Zawsze wywołuj `UseAuthentication` przed `UseSignalR` tym, że SignalR ma użytkownika na `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="1479c-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="1479c-115">Uwierzytelnianie plików cookie</span><span class="sxs-lookup"><span data-stu-id="1479c-115">Cookie authentication</span></span>

<span data-ttu-id="1479c-116">W aplikacji opartej na przeglądarce uwierzytelnianie plików cookie umożliwia istniejące poświadczenia użytkownika w celu automatycznego przepływania do SignalR połączeń.</span><span class="sxs-lookup"><span data-stu-id="1479c-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="1479c-117">W przypadku korzystania z klienta przeglądarki nie jest wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="1479c-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="1479c-118">Jeśli użytkownik jest zalogowany do aplikacji, SignalR połączenie automatycznie dziedziczy to uwierzytelnianie.</span><span class="sxs-lookup"><span data-stu-id="1479c-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="1479c-119">Pliki cookie to specyficzny dla przeglądarki sposób wysyłania tokenów dostępu, ale Klienci niebędący przeglądarkami mogą je wysyłać.</span><span class="sxs-lookup"><span data-stu-id="1479c-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="1479c-120">W przypadku korzystania z [klienta platformy .NET](xref:signalr/dotnet-client) `Cookies` Właściwość można skonfigurować w wywołaniu, `.WithUrl` Aby dostarczyć plik cookie.</span><span class="sxs-lookup"><span data-stu-id="1479c-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="1479c-121">Jednak używanie uwierzytelniania plików cookie z poziomu klienta platformy .NET wymaga, aby aplikacja zapewniała interfejs API do wymiany danych uwierzytelniania dla plików cookie.</span><span class="sxs-lookup"><span data-stu-id="1479c-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="1479c-122">Uwierzytelnianie tokenu okaziciela</span><span class="sxs-lookup"><span data-stu-id="1479c-122">Bearer token authentication</span></span>

<span data-ttu-id="1479c-123">Klient może dostarczyć token dostępu zamiast korzystać z pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="1479c-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="1479c-124">Serwer sprawdza token i używa go do identyfikowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1479c-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="1479c-125">Sprawdzanie poprawności jest wykonywane tylko wtedy, gdy połączenie zostało nawiązane.</span><span class="sxs-lookup"><span data-stu-id="1479c-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="1479c-126">W trakcie trwania połączenia serwer nie zostanie automatycznie ponownie sprawdzony w celu sprawdzenia odwołania tokenu.</span><span class="sxs-lookup"><span data-stu-id="1479c-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="1479c-127">Na serwerze uwierzytelnianie tokenu okaziciela jest konfigurowane przy użyciu [oprogramowania pośredniczącego okaziciela JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="1479c-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="1479c-128">W kliencie JavaScript token może być dostarczony przy użyciu opcji [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="1479c-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="1479c-129">W kliencie .NET jest podobna Właściwość [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) , która może służyć do konfigurowania tokenu:</span><span class="sxs-lookup"><span data-stu-id="1479c-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="1479c-130">Funkcja tokenu dostępu dostarczana jest wywoływana przed **każdym** żądaniem http wykonywanym przez SignalR .</span><span class="sxs-lookup"><span data-stu-id="1479c-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="1479c-131">Jeśli musisz odnowić token, aby zachować aktywne połączenie (ponieważ może ono wygasnąć podczas połączenia), zrób to w ramach tej funkcji i zwróć zaktualizowany token.</span><span class="sxs-lookup"><span data-stu-id="1479c-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="1479c-132">W standardowym interfejsie API sieci Web tokeny okaziciela są wysyłane w nagłówku HTTP.</span><span class="sxs-lookup"><span data-stu-id="1479c-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="1479c-133">Jednak program SignalR nie może ustawić tych nagłówków w przeglądarkach w przypadku korzystania z niektórych transportów.</span><span class="sxs-lookup"><span data-stu-id="1479c-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="1479c-134">W przypadku korzystania z obiektów WebSockets i zdarzeń wysyłanych przez serwer token jest przekazywany jako parametr ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="1479c-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="1479c-135">Aby można było obsługiwać ten serwer na serwerze, wymagana jest dodatkowa konfiguracja:</span><span class="sxs-lookup"><span data-stu-id="1479c-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="1479c-136">Ciąg zapytania jest używany w przeglądarkach w przypadku nawiązywania połączenia z usługą WebSockets i zdarzeniami wysłanymi przez serwer z powodu ograniczeń interfejsu API przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1479c-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="1479c-137">W przypadku korzystania z protokołu HTTPS wartości ciągu zapytania są zabezpieczane przez połączenie TLS.</span><span class="sxs-lookup"><span data-stu-id="1479c-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="1479c-138">Jednak wiele serwerów rejestruje wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="1479c-138">However, many servers log query string values.</span></span> <span data-ttu-id="1479c-139">Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące SignalR zabezpieczeń w ASP.NET Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="1479c-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="1479c-140">używa nagłówków do przesyłania tokenów w środowiskach, które je obsługują (takich jak klienci .NET i Java).</span><span class="sxs-lookup"><span data-stu-id="1479c-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="1479c-141">Pliki cookie a tokeny okaziciela</span><span class="sxs-lookup"><span data-stu-id="1479c-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="1479c-142">Pliki cookie są specyficzne dla przeglądarek.</span><span class="sxs-lookup"><span data-stu-id="1479c-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="1479c-143">Wysyłanie ich z innych rodzajów klientów zwiększa złożoność w porównaniu do wysyłania tokenów okaziciela.</span><span class="sxs-lookup"><span data-stu-id="1479c-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="1479c-144">W związku z tym uwierzytelnianie plików cookie nie jest zalecane, chyba że aplikacja wymaga tylko uwierzytelnienia użytkowników z poziomu klienta przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1479c-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="1479c-145">Uwierzytelnianie tokenów okaziciela jest zalecanym rozwiązaniem w przypadku korzystania z klientów innych niż klient przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1479c-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="1479c-146">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="1479c-146">Windows authentication</span></span>

<span data-ttu-id="1479c-147">Jeśli w aplikacji skonfigurowano [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) , program SignalR może używać tej tożsamości do zabezpieczania centrów.</span><span class="sxs-lookup"><span data-stu-id="1479c-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="1479c-148">Aby jednak wysyłać komunikaty do poszczególnych użytkowników, należy dodać niestandardowego dostawcę identyfikatora użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1479c-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="1479c-149">System uwierzytelniania systemu Windows nie zapewnia żądania "name identifier".</span><span class="sxs-lookup"><span data-stu-id="1479c-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="1479c-150">używa tego żądania, aby określić nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1479c-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="1479c-151">Dodaj nową klasę, która implementuje `IUserIdProvider` i pobiera jedno z oświadczeń od użytkownika do użycia jako identyfikator.</span><span class="sxs-lookup"><span data-stu-id="1479c-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="1479c-152">Aby na przykład użyć żądania "name" (czyli nazwy użytkownika systemu Windows w formularzu `[Domain]\[Username]` ), Utwórz następującą klasę:</span><span class="sxs-lookup"><span data-stu-id="1479c-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="1479c-153">Zamiast programu `ClaimTypes.Name` można użyć dowolnej wartości z (na przykład `User` identyfikatora SID systemu Windows itd.).</span><span class="sxs-lookup"><span data-stu-id="1479c-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="1479c-154">Wybrana wartość musi być unikatowa wśród wszystkich użytkowników w systemie.</span><span class="sxs-lookup"><span data-stu-id="1479c-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="1479c-155">W przeciwnym razie komunikat przeznaczony dla jednego użytkownika może zostać zakończony przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1479c-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="1479c-156">Zarejestruj ten składnik w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="1479c-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="1479c-157">W kliencie .NET należy włączyć uwierzytelnianie systemu Windows, ustawiając właściwość [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="1479c-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="1479c-158">Uwierzytelnianie systemu Windows jest obsługiwane w programach Internet Explorer i Microsoft Edge, ale nie we wszystkich przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="1479c-158">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="1479c-159">Na przykład w przeglądarce Chrome i Safari próba użycia uwierzytelniania systemu Windows i gniazd sieciowych kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="1479c-159">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="1479c-160">W przypadku niepowodzenia uwierzytelniania systemu Windows klient próbuje wrócić do innych transportów, które mogą działać.</span><span class="sxs-lookup"><span data-stu-id="1479c-160">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="1479c-161">Używanie oświadczeń do dostosowywania obsługi tożsamości</span><span class="sxs-lookup"><span data-stu-id="1479c-161">Use claims to customize identity handling</span></span>

<span data-ttu-id="1479c-162">Aplikacja, która uwierzytelnia użytkowników, może wyprowadzać SignalR identyfikatory użytkowników z oświadczeń użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1479c-162">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="1479c-163">Aby określić sposób SignalR tworzenia identyfikatorów użytkowników, implementowania `IUserIdProvider` i rejestrowania implementacji.</span><span class="sxs-lookup"><span data-stu-id="1479c-163">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="1479c-164">Przykładowy kod demonstruje, jak używać oświadczeń do wybierania adresu e-mail użytkownika jako właściwości identyfikującej.</span><span class="sxs-lookup"><span data-stu-id="1479c-164">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="1479c-165">Wybrana wartość musi być unikatowa wśród wszystkich użytkowników w systemie.</span><span class="sxs-lookup"><span data-stu-id="1479c-165">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="1479c-166">W przeciwnym razie komunikat przeznaczony dla jednego użytkownika może zostać zakończony przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1479c-166">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="1479c-167">Rejestracja konta dodaje `ClaimsTypes.Email` do bazy danych tożsamości ASP.NET zgłoszenie typu.</span><span class="sxs-lookup"><span data-stu-id="1479c-167">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="1479c-168">Zarejestruj ten składnik w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1479c-168">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="1479c-169">Autoryzuj użytkowników do uzyskiwania dostępu do centrów i metod centrów</span><span class="sxs-lookup"><span data-stu-id="1479c-169">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="1479c-170">Domyślnie wszystkie metody w koncentratorze mogą być wywoływane przez nieuwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="1479c-170">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="1479c-171">Aby wymagać uwierzytelniania, zastosuj atrybut [Autoryzuj](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) do centrum:</span><span class="sxs-lookup"><span data-stu-id="1479c-171">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="1479c-172">Można użyć argumentów konstruktora i właściwości `[Authorize]` atrybutu w celu ograniczenia dostępu tylko do użytkowników zgodnych z określonymi [zasadami autoryzacji](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="1479c-172">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="1479c-173">Na przykład jeśli masz niestandardowe zasady autoryzacji o nazwie, możesz `MyAuthorizationPolicy` upewnić się, że tylko użytkownicy pasujący do tych zasad mogą uzyskać dostęp do centrum przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="1479c-173">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="1479c-174">Do poszczególnych metod centrów można `[Authorize]` również zastosować atrybut.</span><span class="sxs-lookup"><span data-stu-id="1479c-174">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="1479c-175">Jeśli bieżący użytkownik nie jest zgodny z zasadami zastosowanymi do metody, zwracany jest błąd do obiektu wywołującego:</span><span class="sxs-lookup"><span data-stu-id="1479c-175">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="1479c-176">Używanie programów obsługi autoryzacji do dostosowywania autoryzacji metody centrum</span><span class="sxs-lookup"><span data-stu-id="1479c-176">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="1479c-177">udostępnia zasób niestandardowy do obsługi autoryzacji, gdy metoda centrum wymaga autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1479c-177"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="1479c-178">Zasób jest wystąpieniem `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="1479c-178">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="1479c-179">`HubInvocationContext`Obejmuje `HubCallerContext` , nazwę wywoływanej metody centrum oraz argumenty metody centrum.</span><span class="sxs-lookup"><span data-stu-id="1479c-179">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="1479c-180">Rozważmy przykład pokoju czatu umożliwiającego logowanie do wielu organizacji za pośrednictwem Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="1479c-180">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="1479c-181">Każda osoba mająca konto Microsoft może zalogować się do programu chat, ale tylko członkowie organizacji będącej właścicielem będą mogli uniemożliwić użytkownikom lub wyświetlać historie rozmów użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1479c-181">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="1479c-182">Ponadto możemy chcieć ograniczyć niektóre funkcje do określonych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="1479c-182">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="1479c-183">Korzystanie z zaktualizowanych funkcji w ASP.NET Core 3,0 jest to w całości możliwe.</span><span class="sxs-lookup"><span data-stu-id="1479c-183">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="1479c-184">Zwróć uwagę na to, jak `DomainRestrictedRequirement` służy jako niestandardowy `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="1479c-184">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="1479c-185">Teraz, gdy `HubInvocationContext` parametr zasobu jest przesyłany, wewnętrzna logika może sprawdzić kontekst, w którym jest wywoływana centrum, i podjąć decyzje dotyczące umożliwienia użytkownikowi wykonywania poszczególnych metod centrów.</span><span class="sxs-lookup"><span data-stu-id="1479c-185">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="1479c-186">W programie `Startup.ConfigureServices` Dodaj nowe zasady, dostarczając niestandardowe `DomainRestrictedRequirement` wymagania jako parametr do tworzenia `DomainRestricted` zasad.</span><span class="sxs-lookup"><span data-stu-id="1479c-186">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="1479c-187">W poprzednim przykładzie `DomainRestrictedRequirement` Klasa jest zarówno, `IAuthorizationRequirement` jak i `AuthorizationHandler` dla tego wymagania.</span><span class="sxs-lookup"><span data-stu-id="1479c-187">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="1479c-188">Można podzielić te dwa składniki na osobne klasy w celu oddzielenia obaw.</span><span class="sxs-lookup"><span data-stu-id="1479c-188">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="1479c-189">Zaletą podejścia przykładowego jest brak konieczności wstrzykiwania `AuthorizationHandler` podczas uruchamiania, ponieważ wymaganie i procedura obsługi są takie same.</span><span class="sxs-lookup"><span data-stu-id="1479c-189">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1479c-190">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1479c-190">Additional resources</span></span>

* [<span data-ttu-id="1479c-191">Uwierzytelnianie tokenu okaziciela w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1479c-191">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="1479c-192">Autoryzacja na podstawie zasobów</span><span class="sxs-lookup"><span data-stu-id="1479c-192">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
