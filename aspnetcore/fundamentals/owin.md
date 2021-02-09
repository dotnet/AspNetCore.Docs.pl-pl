---
title: Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core
author: ardalis
description: Dowiedz się, w jaki sposób ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN), dzięki czemu aplikacje sieci Web mają być oddzielone od serwerów sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975277"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="14ee9-103">Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14ee9-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="14ee9-104">[Steve Kowalski](https://ardalis.com/) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="14ee9-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="14ee9-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="14ee9-105">ASP.NET Core:</span></span>

* <span data-ttu-id="14ee9-106">Obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="14ee9-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="14ee9-107">Program ma zgodne z platformą .NET Core zamienników dla `Microsoft.Owin.*` bibliotek ([Katana](/aspnet/aspnet/overview/owin-and-katana/)).</span><span class="sxs-lookup"><span data-stu-id="14ee9-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="14ee9-108">OWIN umożliwia oddzielenie aplikacji sieci Web od serwerów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="14ee9-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="14ee9-109">Definiuje standardowy sposób użycia oprogramowania pośredniczącego w potoku do obsługi żądań i skojarzonych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="14ee9-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="14ee9-110">ASP.NET Core aplikacje i oprogramowanie pośredniczące mogą współdziałać z aplikacjami opartymi na OWIN, serwerami i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14ee9-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="14ee9-111">OWIN zapewnia warstwę odsprzęgającą, która umożliwia wspólne używanie dwóch platform z różnymi modelami obiektów.</span><span class="sxs-lookup"><span data-stu-id="14ee9-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="14ee9-112">`Microsoft.AspNetCore.Owin`Pakiet udostępnia dwie implementacje kart:</span><span class="sxs-lookup"><span data-stu-id="14ee9-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="14ee9-113">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="14ee9-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="14ee9-114">OWIN do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14ee9-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="14ee9-115">Dzięki temu ASP.NET Core mogą być hostowane na serwerze/hoście zgodnym z OWIN lub dla innych składników zgodnych OWIN, które mają być uruchamiane w oparciu o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14ee9-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="14ee9-116">Korzystanie z tych kart wiąże się z kosztem wydajności.</span><span class="sxs-lookup"><span data-stu-id="14ee9-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="14ee9-117">Aplikacje używające tylko składników ASP.NET Core nie powinny korzystać z `Microsoft.AspNetCore.Owin` pakietu lub kart.</span><span class="sxs-lookup"><span data-stu-id="14ee9-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="14ee9-118">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14ee9-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="14ee9-119">Uruchamianie oprogramowania pośredniczącego OWIN w potoku ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14ee9-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="14ee9-120">Obsługa OWIN ASP.NET Core jest wdrażana w ramach `Microsoft.AspNetCore.Owin` pakietu.</span><span class="sxs-lookup"><span data-stu-id="14ee9-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="14ee9-121">Możesz zaimportować obsługę OWIN do projektu, instalując ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="14ee9-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="14ee9-122">Oprogramowanie pośredniczące OWIN jest zgodne ze [specyfikacją Owin](https://owin.org/spec/spec/owin-1.0.0.html), która wymaga `Func<IDictionary<string, object>, Task>` interfejsu, i należy ustawić określone klucze (takie jak `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="14ee9-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="14ee9-123">Następujące proste oprogramowanie pośredniczące OWIN wyświetla "Hello world":</span><span class="sxs-lookup"><span data-stu-id="14ee9-123">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="14ee9-124">Przykładowa sygnatura zwraca `Task` i akceptuje `IDictionary<string, object>` jako wymagane przez Owin.</span><span class="sxs-lookup"><span data-stu-id="14ee9-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="14ee9-125">Poniższy kod pokazuje, jak dodać `OwinHello` oprogramowanie pośredniczące (pokazane powyżej) do potoku ASP.NET Core przy użyciu `UseOwin` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="14ee9-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="14ee9-126">Można skonfigurować inne akcje, które mają być wykonywane w potoku OWIN.</span><span class="sxs-lookup"><span data-stu-id="14ee9-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="14ee9-127">Nagłówki odpowiedzi należy modyfikować tylko przed pierwszym zapisem w strumieniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="14ee9-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="14ee9-128">Wiele wywołań `UseOwin` jest niezalecane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="14ee9-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="14ee9-129">Składniki OWIN działają najlepiej, jeśli są zgrupowane razem.</span><span class="sxs-lookup"><span data-stu-id="14ee9-129">OWIN components will operate best if grouped together.</span></span>

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="14ee9-130">Uruchom ASP.NET Core na serwerze z systemem OWIN i Użyj obsługi jego gniazd WebSockets</span><span class="sxs-lookup"><span data-stu-id="14ee9-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="14ee9-131">Innym przykładem sposobu korzystania z funkcji serwerów opartych na OWIN można użyć ASP.NET Core jest dostęp do funkcji, takich jak WebSockets.</span><span class="sxs-lookup"><span data-stu-id="14ee9-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="14ee9-132">Serwer sieci Web programu .NET OWIN używany w poprzednim przykładzie obsługuje wbudowane w sieć Web Sockets, które mogą być używane przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14ee9-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="14ee9-133">W poniższym przykładzie przedstawiono prostą aplikację sieci Web, która obsługuje gniazda sieci Web i zwraca wszystkie dane wysyłane do serwera za pomocą elementów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="14ee9-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

## <a name="owin-environment"></a><span data-ttu-id="14ee9-134">Środowisko OWIN</span><span class="sxs-lookup"><span data-stu-id="14ee9-134">OWIN environment</span></span>

<span data-ttu-id="14ee9-135">Środowisko OWIN można utworzyć przy użyciu `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="14ee9-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="14ee9-136">Klucze OWIN</span><span class="sxs-lookup"><span data-stu-id="14ee9-136">OWIN keys</span></span>

<span data-ttu-id="14ee9-137">OWIN zależy od `IDictionary<string,object>` obiektu do przekazywania informacji w trakcie wymiany żądania lub odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="14ee9-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="14ee9-138">ASP.NET Core implementuje klucze wymienione poniżej.</span><span class="sxs-lookup"><span data-stu-id="14ee9-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="14ee9-139">Zobacz [podstawowe specyfikacje, rozszerzenia](https://owin.org/#spec)i [wskazówki dotyczące klucza Owin oraz wspólne klucze](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="14ee9-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="14ee9-140">Dane żądania (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="14ee9-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="14ee9-141">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-141">Key</span></span>               | <span data-ttu-id="14ee9-142">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-142">Value (type)</span></span> | <span data-ttu-id="14ee9-143">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-144">Owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="14ee9-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="14ee9-145">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="14ee9-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-146">Owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="14ee9-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-147">Owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="14ee9-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="14ee9-148">Owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="14ee9-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-149">Owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="14ee9-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-150">Owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="14ee9-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="14ee9-151">Owin. Elemencie requestbody</span><span class="sxs-lookup"><span data-stu-id="14ee9-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="14ee9-152">Dane żądania (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="14ee9-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="14ee9-153">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-153">Key</span></span>               | <span data-ttu-id="14ee9-154">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-154">Value (type)</span></span> | <span data-ttu-id="14ee9-155">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-156">Owin. IdentyfikatorŻądania</span><span class="sxs-lookup"><span data-stu-id="14ee9-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="14ee9-157">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="14ee9-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="14ee9-158">Dane odpowiedzi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="14ee9-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="14ee9-159">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-159">Key</span></span>               | <span data-ttu-id="14ee9-160">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-160">Value (type)</span></span> | <span data-ttu-id="14ee9-161">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-162">Owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="14ee9-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="14ee9-163">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="14ee9-163">Optional</span></span> |
| <span data-ttu-id="14ee9-164">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="14ee9-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="14ee9-165">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="14ee9-165">Optional</span></span> |
| <span data-ttu-id="14ee9-166">Owin. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="14ee9-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="14ee9-167">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="14ee9-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="14ee9-168">Inne dane (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="14ee9-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="14ee9-169">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-169">Key</span></span>               | <span data-ttu-id="14ee9-170">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-170">Value (type)</span></span> | <span data-ttu-id="14ee9-171">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-172">Owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="14ee9-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="14ee9-173">Owin. Wersja</span><span class="sxs-lookup"><span data-stu-id="14ee9-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="14ee9-174">Klucze wspólne</span><span class="sxs-lookup"><span data-stu-id="14ee9-174">Common keys</span></span>

| <span data-ttu-id="14ee9-175">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-175">Key</span></span>               | <span data-ttu-id="14ee9-176">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-176">Value (type)</span></span> | <span data-ttu-id="14ee9-177">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-178">zastosowania. Kolekcja</span><span class="sxs-lookup"><span data-stu-id="14ee9-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="14ee9-179">zastosowania. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="14ee9-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="14ee9-180">Server. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="14ee9-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-181">Server. RemotePort</span><span class="sxs-lookup"><span data-stu-id="14ee9-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="14ee9-182">Server. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="14ee9-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-183">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="14ee9-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="14ee9-184">Server. IsLocal</span><span class="sxs-lookup"><span data-stu-id="14ee9-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="14ee9-185">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="14ee9-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="14ee9-186">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="14ee9-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="14ee9-187">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-187">Key</span></span>               | <span data-ttu-id="14ee9-188">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-188">Value (type)</span></span> | <span data-ttu-id="14ee9-189">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-190">sendfile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="14ee9-190">sendfile.SendAsync</span></span> | <span data-ttu-id="14ee9-191">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="14ee9-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="14ee9-192">Na żądanie</span><span class="sxs-lookup"><span data-stu-id="14ee9-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="14ee9-193">Nieprzezroczysty 0.3.0 v</span><span class="sxs-lookup"><span data-stu-id="14ee9-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="14ee9-194">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-194">Key</span></span>               | <span data-ttu-id="14ee9-195">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-195">Value (type)</span></span> | <span data-ttu-id="14ee9-196">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-197">zakrywa. Wersja</span><span class="sxs-lookup"><span data-stu-id="14ee9-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="14ee9-198">zakrywa. Uaktualniony</span><span class="sxs-lookup"><span data-stu-id="14ee9-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="14ee9-199">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="14ee9-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="14ee9-200">zakrywa. Produkcyjne</span><span class="sxs-lookup"><span data-stu-id="14ee9-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="14ee9-201">zakrywa. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="14ee9-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="14ee9-202">0.3.0 protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="14ee9-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="14ee9-203">Klucz</span><span class="sxs-lookup"><span data-stu-id="14ee9-203">Key</span></span>               | <span data-ttu-id="14ee9-204">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="14ee9-204">Value (type)</span></span> | <span data-ttu-id="14ee9-205">Opis</span><span class="sxs-lookup"><span data-stu-id="14ee9-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="14ee9-206">akceptowan. Wersja</span><span class="sxs-lookup"><span data-stu-id="14ee9-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="14ee9-207">akceptowan. Odebrać</span><span class="sxs-lookup"><span data-stu-id="14ee9-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="14ee9-208">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="14ee9-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="14ee9-209">akceptowan. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="14ee9-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="14ee9-210">Nie-spec</span><span class="sxs-lookup"><span data-stu-id="14ee9-210">Non-spec</span></span> |
| <span data-ttu-id="14ee9-211">akceptowan. Podprotokół</span><span class="sxs-lookup"><span data-stu-id="14ee9-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="14ee9-212">Zobacz [RFC6455 sekcja 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="14ee9-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="14ee9-213">akceptowan. SendAsync</span><span class="sxs-lookup"><span data-stu-id="14ee9-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="14ee9-214">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="14ee9-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="14ee9-215">akceptowan. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="14ee9-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="14ee9-216">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="14ee9-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="14ee9-217">akceptowan. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="14ee9-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="14ee9-218">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="14ee9-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="14ee9-219">akceptowan. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="14ee9-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="14ee9-220">akceptowan. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="14ee9-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="14ee9-221">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="14ee9-221">Optional</span></span> |
| <span data-ttu-id="14ee9-222">akceptowan. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="14ee9-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="14ee9-223">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="14ee9-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="14ee9-224">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="14ee9-224">Additional resources</span></span>

* [<span data-ttu-id="14ee9-225">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="14ee9-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="14ee9-226">Serwery</span><span class="sxs-lookup"><span data-stu-id="14ee9-226">Servers</span></span>](xref:fundamentals/servers/index)
