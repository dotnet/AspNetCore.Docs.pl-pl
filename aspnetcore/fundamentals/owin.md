---
title: Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core
author: ardalis
description: Dowiedz się, w jaki sposób ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN), dzięki czemu aplikacje sieci Web mają być oddzielone od serwerów sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
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
uid: fundamentals/owin
ms.openlocfilehash: 817eb652f4feedf19dd60873b480917c320272a3
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102760"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="c5e09-103">Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5e09-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="c5e09-104">[Steve Kowalski](https://ardalis.com/) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c5e09-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c5e09-105">ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="c5e09-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="c5e09-106">OWIN umożliwia oddzielenie aplikacji sieci Web od serwerów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c5e09-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="c5e09-107">Definiuje standardowy sposób użycia oprogramowania pośredniczącego w potoku do obsługi żądań i skojarzonych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c5e09-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="c5e09-108">ASP.NET Core aplikacje i oprogramowanie pośredniczące mogą współdziałać z aplikacjami opartymi na OWIN, serwerami i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="c5e09-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="c5e09-109">OWIN zapewnia warstwę odsprzęgającą, która umożliwia wspólne używanie dwóch platform z różnymi modelami obiektów.</span><span class="sxs-lookup"><span data-stu-id="c5e09-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="c5e09-110">`Microsoft.AspNetCore.Owin`Pakiet udostępnia dwie implementacje kart:</span><span class="sxs-lookup"><span data-stu-id="c5e09-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="c5e09-111">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="c5e09-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="c5e09-112">OWIN do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5e09-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="c5e09-113">Dzięki temu ASP.NET Core mogą być hostowane na serwerze/hoście zgodnym z OWIN lub dla innych składników zgodnych OWIN, które mają być uruchamiane w oparciu o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5e09-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="c5e09-114">Korzystanie z tych kart wiąże się z kosztem wydajności.</span><span class="sxs-lookup"><span data-stu-id="c5e09-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="c5e09-115">Aplikacje używające tylko składników ASP.NET Core nie powinny korzystać z `Microsoft.AspNetCore.Owin` pakietu lub kart.</span><span class="sxs-lookup"><span data-stu-id="c5e09-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="c5e09-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c5e09-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="c5e09-117">Uruchamianie oprogramowania pośredniczącego OWIN w potoku ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5e09-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="c5e09-118">Obsługa OWIN ASP.NET Core jest wdrażana w ramach `Microsoft.AspNetCore.Owin` pakietu.</span><span class="sxs-lookup"><span data-stu-id="c5e09-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="c5e09-119">Możesz zaimportować obsługę OWIN do projektu, instalując ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="c5e09-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="c5e09-120">Oprogramowanie pośredniczące OWIN jest zgodne ze [specyfikacją Owin](https://owin.org/spec/spec/owin-1.0.0.html), która wymaga `Func<IDictionary<string, object>, Task>` interfejsu, i należy ustawić określone klucze (takie jak `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="c5e09-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="c5e09-121">Następujące proste oprogramowanie pośredniczące OWIN wyświetla "Hello world":</span><span class="sxs-lookup"><span data-stu-id="c5e09-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="c5e09-122">Przykładowa sygnatura zwraca `Task` i akceptuje `IDictionary<string, object>` jako wymagane przez Owin.</span><span class="sxs-lookup"><span data-stu-id="c5e09-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="c5e09-123">Poniższy kod pokazuje, jak dodać `OwinHello` oprogramowanie pośredniczące (pokazane powyżej) do potoku ASP.NET Core przy użyciu `UseOwin` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="c5e09-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="c5e09-124">Można skonfigurować inne akcje, które mają być wykonywane w potoku OWIN.</span><span class="sxs-lookup"><span data-stu-id="c5e09-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="c5e09-125">Nagłówki odpowiedzi należy modyfikować tylko przed pierwszym zapisem w strumieniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c5e09-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="c5e09-126">Wiele wywołań `UseOwin` jest niezalecane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="c5e09-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="c5e09-127">Składniki OWIN działają najlepiej, jeśli są zgrupowane razem.</span><span class="sxs-lookup"><span data-stu-id="c5e09-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="c5e09-128">Uruchom ASP.NET Core na serwerze z systemem OWIN i Użyj obsługi jego gniazd WebSockets</span><span class="sxs-lookup"><span data-stu-id="c5e09-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="c5e09-129">Innym przykładem sposobu korzystania z funkcji serwerów opartych na OWIN można użyć ASP.NET Core jest dostęp do funkcji, takich jak WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c5e09-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="c5e09-130">Serwer sieci Web programu .NET OWIN używany w poprzednim przykładzie obsługuje wbudowane w sieć Web Sockets, które mogą być używane przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5e09-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="c5e09-131">W poniższym przykładzie przedstawiono prostą aplikację sieci Web, która obsługuje gniazda sieci Web i zwraca wszystkie dane wysyłane do serwera za pomocą elementów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c5e09-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="c5e09-132">Środowisko OWIN</span><span class="sxs-lookup"><span data-stu-id="c5e09-132">OWIN environment</span></span>

<span data-ttu-id="c5e09-133">Środowisko OWIN można utworzyć przy użyciu `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="c5e09-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="c5e09-134">Klucze OWIN</span><span class="sxs-lookup"><span data-stu-id="c5e09-134">OWIN keys</span></span>

<span data-ttu-id="c5e09-135">OWIN zależy od `IDictionary<string,object>` obiektu do przekazywania informacji w trakcie wymiany żądania lub odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5e09-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="c5e09-136">ASP.NET Core implementuje klucze wymienione poniżej.</span><span class="sxs-lookup"><span data-stu-id="c5e09-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="c5e09-137">Zobacz [podstawowe specyfikacje, rozszerzenia](https://owin.org/#spec)i [wskazówki dotyczące klucza Owin oraz wspólne klucze](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="c5e09-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="c5e09-138">Dane żądania (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="c5e09-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="c5e09-139">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-139">Key</span></span>               | <span data-ttu-id="c5e09-140">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-140">Value (type)</span></span> | <span data-ttu-id="c5e09-141">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-142">Owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="c5e09-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="c5e09-143">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="c5e09-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-144">Owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="c5e09-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-145">Owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="c5e09-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="c5e09-146">Owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="c5e09-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-147">Owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="c5e09-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-148">Owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="c5e09-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="c5e09-149">Owin. Elemencie requestbody</span><span class="sxs-lookup"><span data-stu-id="c5e09-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="c5e09-150">Dane żądania (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="c5e09-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="c5e09-151">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-151">Key</span></span>               | <span data-ttu-id="c5e09-152">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-152">Value (type)</span></span> | <span data-ttu-id="c5e09-153">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-154">Owin. IdentyfikatorŻądania</span><span class="sxs-lookup"><span data-stu-id="c5e09-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="c5e09-155">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="c5e09-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="c5e09-156">Dane odpowiedzi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="c5e09-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="c5e09-157">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-157">Key</span></span>               | <span data-ttu-id="c5e09-158">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-158">Value (type)</span></span> | <span data-ttu-id="c5e09-159">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-160">Owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="c5e09-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="c5e09-161">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="c5e09-161">Optional</span></span> |
| <span data-ttu-id="c5e09-162">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="c5e09-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="c5e09-163">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="c5e09-163">Optional</span></span> |
| <span data-ttu-id="c5e09-164">Owin. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="c5e09-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="c5e09-165">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="c5e09-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="c5e09-166">Inne dane (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="c5e09-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="c5e09-167">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-167">Key</span></span>               | <span data-ttu-id="c5e09-168">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-168">Value (type)</span></span> | <span data-ttu-id="c5e09-169">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-170">Owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="c5e09-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="c5e09-171">Owin. Wersja</span><span class="sxs-lookup"><span data-stu-id="c5e09-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="c5e09-172">Klucze wspólne</span><span class="sxs-lookup"><span data-stu-id="c5e09-172">Common keys</span></span>

| <span data-ttu-id="c5e09-173">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-173">Key</span></span>               | <span data-ttu-id="c5e09-174">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-174">Value (type)</span></span> | <span data-ttu-id="c5e09-175">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-176">zastosowania. Kolekcja</span><span class="sxs-lookup"><span data-stu-id="c5e09-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="c5e09-177">zastosowania. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="c5e09-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="c5e09-178">Server. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="c5e09-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-179">Server. RemotePort</span><span class="sxs-lookup"><span data-stu-id="c5e09-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="c5e09-180">Server. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="c5e09-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-181">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="c5e09-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="c5e09-182">Server. IsLocal</span><span class="sxs-lookup"><span data-stu-id="c5e09-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="c5e09-183">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="c5e09-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="c5e09-184">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="c5e09-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="c5e09-185">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-185">Key</span></span>               | <span data-ttu-id="c5e09-186">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-186">Value (type)</span></span> | <span data-ttu-id="c5e09-187">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-188">sendfile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="c5e09-188">sendfile.SendAsync</span></span> | <span data-ttu-id="c5e09-189">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c5e09-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="c5e09-190">Na żądanie</span><span class="sxs-lookup"><span data-stu-id="c5e09-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="c5e09-191">Nieprzezroczysty 0.3.0 v</span><span class="sxs-lookup"><span data-stu-id="c5e09-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="c5e09-192">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-192">Key</span></span>               | <span data-ttu-id="c5e09-193">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-193">Value (type)</span></span> | <span data-ttu-id="c5e09-194">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-195">zakrywa. Wersja</span><span class="sxs-lookup"><span data-stu-id="c5e09-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="c5e09-196">zakrywa. Uaktualniony</span><span class="sxs-lookup"><span data-stu-id="c5e09-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="c5e09-197">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c5e09-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="c5e09-198">zakrywa. Produkcyjne</span><span class="sxs-lookup"><span data-stu-id="c5e09-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="c5e09-199">zakrywa. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="c5e09-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="c5e09-200">0.3.0 protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="c5e09-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="c5e09-201">Klucz</span><span class="sxs-lookup"><span data-stu-id="c5e09-201">Key</span></span>               | <span data-ttu-id="c5e09-202">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="c5e09-202">Value (type)</span></span> | <span data-ttu-id="c5e09-203">Opis</span><span class="sxs-lookup"><span data-stu-id="c5e09-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c5e09-204">akceptowan. Wersja</span><span class="sxs-lookup"><span data-stu-id="c5e09-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="c5e09-205">akceptowan. Odebrać</span><span class="sxs-lookup"><span data-stu-id="c5e09-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="c5e09-206">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c5e09-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="c5e09-207">akceptowan. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="c5e09-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="c5e09-208">Nie-spec</span><span class="sxs-lookup"><span data-stu-id="c5e09-208">Non-spec</span></span> |
| <span data-ttu-id="c5e09-209">akceptowan. Podprotokół</span><span class="sxs-lookup"><span data-stu-id="c5e09-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="c5e09-210">Zobacz [RFC6455 sekcja 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="c5e09-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="c5e09-211">akceptowan. SendAsync</span><span class="sxs-lookup"><span data-stu-id="c5e09-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="c5e09-212">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c5e09-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="c5e09-213">akceptowan. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="c5e09-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="c5e09-214">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c5e09-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="c5e09-215">akceptowan. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="c5e09-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="c5e09-216">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c5e09-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="c5e09-217">akceptowan. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="c5e09-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="c5e09-218">akceptowan. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="c5e09-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="c5e09-219">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="c5e09-219">Optional</span></span> |
| <span data-ttu-id="c5e09-220">akceptowan. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="c5e09-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="c5e09-221">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="c5e09-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c5e09-222">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c5e09-222">Additional resources</span></span>

* [<span data-ttu-id="c5e09-223">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="c5e09-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c5e09-224">Serwery</span><span class="sxs-lookup"><span data-stu-id="c5e09-224">Servers</span></span>](xref:fundamentals/servers/index)
