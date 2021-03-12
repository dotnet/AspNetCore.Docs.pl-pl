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
ms.openlocfilehash: e476f3f62a250d960c809e5062b3bd8ca3a1940a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587240"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="21536-103">Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21536-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="21536-104">[Steve Kowalski](https://ardalis.com/) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21536-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="21536-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="21536-105">ASP.NET Core:</span></span>

* <span data-ttu-id="21536-106">Obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="21536-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="21536-107">Program ma zgodne z platformą .NET Core zamienników dla `Microsoft.Owin.*` bibliotek ([Katana](/aspnet/aspnet/overview/owin-and-katana/)).</span><span class="sxs-lookup"><span data-stu-id="21536-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="21536-108">OWIN umożliwia oddzielenie aplikacji sieci Web od serwerów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="21536-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="21536-109">Definiuje standardowy sposób użycia oprogramowania pośredniczącego w potoku do obsługi żądań i skojarzonych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="21536-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="21536-110">ASP.NET Core aplikacje i oprogramowanie pośredniczące mogą współdziałać z aplikacjami opartymi na OWIN, serwerami i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="21536-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="21536-111">OWIN zapewnia warstwę odsprzęgającą, która umożliwia wspólne używanie dwóch platform z różnymi modelami obiektów.</span><span class="sxs-lookup"><span data-stu-id="21536-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="21536-112">`Microsoft.AspNetCore.Owin`Pakiet udostępnia dwie implementacje kart:</span><span class="sxs-lookup"><span data-stu-id="21536-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="21536-113">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="21536-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="21536-114">OWIN do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21536-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="21536-115">Dzięki temu ASP.NET Core mogą być hostowane na serwerze/hoście zgodnym z OWIN lub dla innych składników zgodnych OWIN, które mają być uruchamiane w oparciu o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21536-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="21536-116">Korzystanie z tych kart wiąże się z kosztem wydajności.</span><span class="sxs-lookup"><span data-stu-id="21536-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="21536-117">Aplikacje używające tylko składników ASP.NET Core nie powinny korzystać z `Microsoft.AspNetCore.Owin` pakietu lub kart.</span><span class="sxs-lookup"><span data-stu-id="21536-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="21536-118">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="21536-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="21536-119">Uruchamianie oprogramowania pośredniczącego OWIN w potoku ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21536-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="21536-120">Obsługa OWIN ASP.NET Core jest wdrażana w ramach `Microsoft.AspNetCore.Owin` pakietu.</span><span class="sxs-lookup"><span data-stu-id="21536-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="21536-121">Możesz zaimportować obsługę OWIN do projektu, instalując ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="21536-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="21536-122">Oprogramowanie pośredniczące OWIN jest zgodne ze [specyfikacją Owin](https://owin.org/spec/spec/owin-1.0.0.html), która wymaga `Func<IDictionary<string, object>, Task>` interfejsu, i należy ustawić określone klucze (takie jak `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="21536-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="21536-123">Następujące proste oprogramowanie pośredniczące OWIN wyświetla "Hello world":</span><span class="sxs-lookup"><span data-stu-id="21536-123">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="21536-124">Przykładowa sygnatura zwraca `Task` i akceptuje `IDictionary<string, object>` jako wymagane przez Owin.</span><span class="sxs-lookup"><span data-stu-id="21536-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="21536-125">Poniższy kod pokazuje, jak dodać `OwinHello` oprogramowanie pośredniczące (pokazane powyżej) do potoku ASP.NET Core przy użyciu `UseOwin` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="21536-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="21536-126">Można skonfigurować inne akcje, które mają być wykonywane w potoku OWIN.</span><span class="sxs-lookup"><span data-stu-id="21536-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="21536-127">Nagłówki odpowiedzi należy modyfikować tylko przed pierwszym zapisem w strumieniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="21536-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="21536-128">Wiele wywołań `UseOwin` jest niezalecane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="21536-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="21536-129">Składniki OWIN działają najlepiej, jeśli są zgrupowane razem.</span><span class="sxs-lookup"><span data-stu-id="21536-129">OWIN components will operate best if grouped together.</span></span>

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="21536-130">Uruchom ASP.NET Core na serwerze z systemem OWIN i Użyj obsługi jego gniazd WebSockets</span><span class="sxs-lookup"><span data-stu-id="21536-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="21536-131">Innym przykładem sposobu korzystania z funkcji serwerów opartych na OWIN można użyć ASP.NET Core jest dostęp do funkcji, takich jak WebSockets.</span><span class="sxs-lookup"><span data-stu-id="21536-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="21536-132">Serwer sieci Web programu .NET OWIN używany w poprzednim przykładzie obsługuje wbudowane w sieć Web Sockets, które mogą być używane przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21536-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="21536-133">W poniższym przykładzie przedstawiono prostą aplikację sieci Web, która obsługuje gniazda sieci Web i zwraca wszystkie dane wysyłane do serwera za pomocą elementów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="21536-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

## <a name="owin-environment"></a><span data-ttu-id="21536-134">Środowisko OWIN</span><span class="sxs-lookup"><span data-stu-id="21536-134">OWIN environment</span></span>

<span data-ttu-id="21536-135">Środowisko OWIN można utworzyć przy użyciu `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="21536-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="21536-136">Klucze OWIN</span><span class="sxs-lookup"><span data-stu-id="21536-136">OWIN keys</span></span>

<span data-ttu-id="21536-137">OWIN zależy od `IDictionary<string,object>` obiektu do przekazywania informacji w trakcie wymiany żądania lub odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="21536-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="21536-138">ASP.NET Core implementuje klucze wymienione poniżej.</span><span class="sxs-lookup"><span data-stu-id="21536-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="21536-139">Zobacz [podstawowe specyfikacje, rozszerzenia](https://owin.org/#spec)i [wskazówki dotyczące klucza Owin oraz wspólne klucze](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="21536-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="21536-140">Dane żądania (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="21536-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="21536-141">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-141">Key</span></span>               | <span data-ttu-id="21536-142">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-142">Value (type)</span></span> | <span data-ttu-id="21536-143">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-144">Owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="21536-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="21536-145">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="21536-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="21536-146">Owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="21536-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="21536-147">Owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="21536-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="21536-148">Owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="21536-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="21536-149">Owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="21536-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="21536-150">Owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="21536-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="21536-151">Owin. Elemencie requestbody</span><span class="sxs-lookup"><span data-stu-id="21536-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="21536-152">Dane żądania (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="21536-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="21536-153">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-153">Key</span></span>               | <span data-ttu-id="21536-154">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-154">Value (type)</span></span> | <span data-ttu-id="21536-155">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-156">Owin. IdentyfikatorŻądania</span><span class="sxs-lookup"><span data-stu-id="21536-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="21536-157">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="21536-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="21536-158">Dane odpowiedzi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="21536-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="21536-159">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-159">Key</span></span>               | <span data-ttu-id="21536-160">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-160">Value (type)</span></span> | <span data-ttu-id="21536-161">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-162">Owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="21536-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="21536-163">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="21536-163">Optional</span></span> |
| <span data-ttu-id="21536-164">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="21536-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="21536-165">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="21536-165">Optional</span></span> |
| <span data-ttu-id="21536-166">Owin. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="21536-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="21536-167">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="21536-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="21536-168">Inne dane (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="21536-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="21536-169">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-169">Key</span></span>               | <span data-ttu-id="21536-170">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-170">Value (type)</span></span> | <span data-ttu-id="21536-171">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-172">Owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="21536-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="21536-173">Owin. Wersja</span><span class="sxs-lookup"><span data-stu-id="21536-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="21536-174">Klucze wspólne</span><span class="sxs-lookup"><span data-stu-id="21536-174">Common keys</span></span>

| <span data-ttu-id="21536-175">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-175">Key</span></span>               | <span data-ttu-id="21536-176">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-176">Value (type)</span></span> | <span data-ttu-id="21536-177">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-178">zastosowania. Kolekcja</span><span class="sxs-lookup"><span data-stu-id="21536-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="21536-179">zastosowania. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="21536-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="21536-180">Server. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="21536-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="21536-181">Server. RemotePort</span><span class="sxs-lookup"><span data-stu-id="21536-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="21536-182">Server. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="21536-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="21536-183">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="21536-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="21536-184">Server. IsLocal</span><span class="sxs-lookup"><span data-stu-id="21536-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="21536-185">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="21536-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="21536-186">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="21536-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="21536-187">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-187">Key</span></span>               | <span data-ttu-id="21536-188">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-188">Value (type)</span></span> | <span data-ttu-id="21536-189">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-190">sendfile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="21536-190">sendfile.SendAsync</span></span> | <span data-ttu-id="21536-191">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21536-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="21536-192">Na żądanie</span><span class="sxs-lookup"><span data-stu-id="21536-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="21536-193">Nieprzezroczysty 0.3.0 v</span><span class="sxs-lookup"><span data-stu-id="21536-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="21536-194">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-194">Key</span></span>               | <span data-ttu-id="21536-195">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-195">Value (type)</span></span> | <span data-ttu-id="21536-196">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-197">zakrywa. Wersja</span><span class="sxs-lookup"><span data-stu-id="21536-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="21536-198">zakrywa. Uaktualniony</span><span class="sxs-lookup"><span data-stu-id="21536-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="21536-199">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21536-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="21536-200">zakrywa. Produkcyjne</span><span class="sxs-lookup"><span data-stu-id="21536-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="21536-201">zakrywa. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="21536-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="21536-202">0.3.0 protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="21536-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="21536-203">Klucz</span><span class="sxs-lookup"><span data-stu-id="21536-203">Key</span></span>               | <span data-ttu-id="21536-204">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="21536-204">Value (type)</span></span> | <span data-ttu-id="21536-205">Opis</span><span class="sxs-lookup"><span data-stu-id="21536-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21536-206">akceptowan. Wersja</span><span class="sxs-lookup"><span data-stu-id="21536-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="21536-207">akceptowan. Odebrać</span><span class="sxs-lookup"><span data-stu-id="21536-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="21536-208">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21536-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="21536-209">akceptowan. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="21536-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="21536-210">Nie-spec</span><span class="sxs-lookup"><span data-stu-id="21536-210">Non-spec</span></span> |
| <span data-ttu-id="21536-211">akceptowan. Podprotokół</span><span class="sxs-lookup"><span data-stu-id="21536-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="21536-212">Zobacz [RFC6455 sekcja 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="21536-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="21536-213">akceptowan. SendAsync</span><span class="sxs-lookup"><span data-stu-id="21536-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="21536-214">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21536-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="21536-215">akceptowan. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="21536-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="21536-216">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21536-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="21536-217">akceptowan. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="21536-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="21536-218">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21536-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="21536-219">akceptowan. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="21536-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="21536-220">akceptowan. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="21536-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="21536-221">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="21536-221">Optional</span></span> |
| <span data-ttu-id="21536-222">akceptowan. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="21536-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="21536-223">Opcjonalne</span><span class="sxs-lookup"><span data-stu-id="21536-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="21536-224">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="21536-224">Additional resources</span></span>

* [<span data-ttu-id="21536-225">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="21536-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="21536-226">Serwery</span><span class="sxs-lookup"><span data-stu-id="21536-226">Servers</span></span>](xref:fundamentals/servers/index)
