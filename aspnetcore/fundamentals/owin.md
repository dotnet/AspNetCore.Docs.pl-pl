---
title: Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core
author: ardalis
description: Dowiedz się, jak ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN), który umożliwia oddzielenie aplikacji sieci Web od serwerów sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666685"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="3e48d-103">Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e48d-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="3e48d-104">Przez [Steve Smith](https://ardalis.com/) i Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3e48d-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3e48d-105">ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="3e48d-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="3e48d-106">OWIN umożliwia oddzielenie aplikacji internetowych od serwerów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e48d-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="3e48d-107">Definiuje standardowy sposób oprogramowania pośredniczącego, który ma być używany w potoku do obsługi żądań i skojarzonych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3e48d-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="3e48d-108">ASP.NET Aplikacje Core i oprogramowanie pośredniczące mogą współpracować z aplikacjami, serwerami i oprogramowaniem pośredniczącym opartym na OWIN.</span><span class="sxs-lookup"><span data-stu-id="3e48d-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="3e48d-109">OWIN zapewnia warstwę oddzielenia, która umożliwia dwie struktury z różnych modeli obiektów, które mają być używane razem.</span><span class="sxs-lookup"><span data-stu-id="3e48d-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="3e48d-110">Pakiet `Microsoft.AspNetCore.Owin` zawiera dwie implementacje kart:</span><span class="sxs-lookup"><span data-stu-id="3e48d-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="3e48d-111">ASP.NET Core do OWIN</span><span class="sxs-lookup"><span data-stu-id="3e48d-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="3e48d-112">OWIN do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e48d-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="3e48d-113">Dzięki temu ASP.NET Core mogą być hostowane na serwerze/hoście zgodnym z OWIN lub na innych komponentach zgodnych z OWIN, które mają być uruchamiane na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e48d-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="3e48d-114">Korzystanie z tych kart wiąże się z kosztami wydajności.</span><span class="sxs-lookup"><span data-stu-id="3e48d-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="3e48d-115">Aplikacje używające tylko składników ASP.NET Core `Microsoft.AspNetCore.Owin` nie powinny używać pakietu ani kart.</span><span class="sxs-lookup"><span data-stu-id="3e48d-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="3e48d-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e48d-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="3e48d-117">Uruchamianie oprogramowania pośredniczącego OWIN w potoku ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e48d-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="3e48d-118">ASP.NET Core OWIN wsparcie jest wdrażany jako część `Microsoft.AspNetCore.Owin` pakietu.</span><span class="sxs-lookup"><span data-stu-id="3e48d-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="3e48d-119">Można zaimportować obsługę OWIN do projektu, instalując ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="3e48d-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="3e48d-120">Oprogramowanie pośredniczące OWIN jest zgodne ze `Func<IDictionary<string, object>, Task>` [specyfikacją OWIN,](https://owin.org/spec/spec/owin-1.0.0.html)która wymaga `owin.ResponseBody`interfejsu i ustawionymi określonymi klawiszami (takimi jak ).</span><span class="sxs-lookup"><span data-stu-id="3e48d-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="3e48d-121">Następujące proste oprogramowanie pośredniczące OWIN wyświetla "Hello World":</span><span class="sxs-lookup"><span data-stu-id="3e48d-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="3e48d-122">Przykładowy podpis `Task` zwraca i `IDictionary<string, object>` akceptuje zgodnie z wymaganiami OWIN.</span><span class="sxs-lookup"><span data-stu-id="3e48d-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="3e48d-123">Poniższy kod pokazuje, `OwinHello` jak dodać oprogramowanie pośredniczące (pokazane powyżej) `UseOwin` do potoku ASP.NET Core z metodą rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="3e48d-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="3e48d-124">Można skonfigurować inne akcje, które mają być podejmowane w potoku OWIN.</span><span class="sxs-lookup"><span data-stu-id="3e48d-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="3e48d-125">Nagłówki odpowiedzi powinny być modyfikowane tylko przed pierwszym zapisem w strumieniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3e48d-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="3e48d-126">Wiele wywołań `UseOwin` jest odradzane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="3e48d-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="3e48d-127">Składniki OWIN będą działać najlepiej, jeśli są zgrupowane razem.</span><span class="sxs-lookup"><span data-stu-id="3e48d-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="3e48d-128">Korzystanie z ASP.NET Core Hosting na serwerze opartym na OWIN</span><span class="sxs-lookup"><span data-stu-id="3e48d-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="3e48d-129">Serwery oparte na OWIN mogą obsługiwać aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e48d-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="3e48d-130">Jednym z takich serwerów jest [Nowin](https://github.com/Bobris/Nowin), serwer www .NET OWIN.</span><span class="sxs-lookup"><span data-stu-id="3e48d-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="3e48d-131">W przykładzie do tego artykułu, mam włączone projektu, który odwołuje się nowin i używa go do tworzenia `IServer` zdolnego do samodzielnego hostingu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e48d-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="3e48d-132">`IServer`jest interfejsem, `Features` który wymaga `Start` właściwości i metody.</span><span class="sxs-lookup"><span data-stu-id="3e48d-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="3e48d-133">`Start`jest odpowiedzialny za konfigurowanie i uruchamianie serwera, co w tym przypadku odbywa się za pośrednictwem serii płynnych wywołań interfejsu API, które ustawiają adresy analizowane z iServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="3e48d-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="3e48d-134">Należy zauważyć, że płynna konfiguracja zmiennej `_builder` określa, `appFunc` że żądania będą obsługiwane przez zdefiniowane wcześniej w metodzie.</span><span class="sxs-lookup"><span data-stu-id="3e48d-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="3e48d-135">Jest `Func` to wywoływane w każdym żądaniu do przetwarzania żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="3e48d-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="3e48d-136">Dodamy również `IWebHostBuilder` rozszerzenie, aby ułatwić dodawanie i konfigurowanie serwera Nowin.</span><span class="sxs-lookup"><span data-stu-id="3e48d-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="3e48d-137">Mając to na miejscu, wywołaj rozszerzenie w *Program.cs,* aby uruchomić aplikację ASP.NET Core przy użyciu tego serwera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="3e48d-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="3e48d-138">Dowiedz się więcej o [ASP.NET Serwery podstawowe](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="3e48d-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="3e48d-139">Uruchom ASP.NET Core na serwerze opartym na OWIN i korzystaj z obsługi websockets</span><span class="sxs-lookup"><span data-stu-id="3e48d-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="3e48d-140">Innym przykładem tego, jak funkcje serwerów opartych na OWIN mogą być wykorzystywane przez ASP.NET Core jest dostęp do funkcji, takich jak WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3e48d-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="3e48d-141">Serwer sieci web owin .NET używany w poprzednim przykładzie ma obsługę wbudowanych gniazd sieci Web, które mogą być wykorzystywane przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e48d-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="3e48d-142">Poniższy przykład przedstawia prostą aplikację internetową, która obsługuje gniazda sieci Web i echa z powrotem wszystko wysyłane do serwera za pośrednictwem WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3e48d-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="3e48d-143">Ten [przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) jest skonfigurowany `NowinServer` przy użyciu tego samego, co poprzedni — jedyną `Configure` różnicą jest sposób konfigurowania aplikacji w swojej metodzie.</span><span class="sxs-lookup"><span data-stu-id="3e48d-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="3e48d-144">Test przy użyciu [prostego klienta websocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) pokazuje aplikacji:</span><span class="sxs-lookup"><span data-stu-id="3e48d-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Klient testowy gniazda sieci Web](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="3e48d-146">Środowisko OWIN</span><span class="sxs-lookup"><span data-stu-id="3e48d-146">OWIN environment</span></span>

<span data-ttu-id="3e48d-147">Środowisko OWIN można skonstruować `HttpContext`za pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="3e48d-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="3e48d-148">Klucze OWIN</span><span class="sxs-lookup"><span data-stu-id="3e48d-148">OWIN keys</span></span>

<span data-ttu-id="3e48d-149">OWIN zależy `IDictionary<string,object>` od obiektu do przekazywania informacji w całej wymiany żądania/odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="3e48d-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="3e48d-150">ASP.NET Core implementuje klucze wymienione poniżej.</span><span class="sxs-lookup"><span data-stu-id="3e48d-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="3e48d-151">Zobacz [podstawową specyfikację, rozszerzenia](https://owin.org/#spec)i [wytyczne dotyczące kluczy OWIN i klucze wspólne](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="3e48d-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="3e48d-152">Żądania danych (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="3e48d-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="3e48d-153">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-153">Key</span></span>               | <span data-ttu-id="3e48d-154">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-154">Value (type)</span></span> | <span data-ttu-id="3e48d-155">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-156">owin. ProśbaScheme</span><span class="sxs-lookup"><span data-stu-id="3e48d-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="3e48d-157">owin. RequestMetoda</span><span class="sxs-lookup"><span data-stu-id="3e48d-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-158">owin. Baza żądań</span><span class="sxs-lookup"><span data-stu-id="3e48d-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-159">owin. Ścieżka żądań</span><span class="sxs-lookup"><span data-stu-id="3e48d-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="3e48d-160">owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="3e48d-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-161">owin. WniosekProtocol</span><span class="sxs-lookup"><span data-stu-id="3e48d-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-162">owin. RequestHeaders (Żądaj y)</span><span class="sxs-lookup"><span data-stu-id="3e48d-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="3e48d-163">owin. RequestBody (Ciało żądania)</span><span class="sxs-lookup"><span data-stu-id="3e48d-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="3e48d-164">Żądania danych (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="3e48d-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="3e48d-165">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-165">Key</span></span>               | <span data-ttu-id="3e48d-166">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-166">Value (type)</span></span> | <span data-ttu-id="3e48d-167">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-168">owin. Requestid</span><span class="sxs-lookup"><span data-stu-id="3e48d-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="3e48d-169">Optional (Opcjonalność)</span><span class="sxs-lookup"><span data-stu-id="3e48d-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="3e48d-170">Dane odpowiedzi (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="3e48d-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="3e48d-171">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-171">Key</span></span>               | <span data-ttu-id="3e48d-172">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-172">Value (type)</span></span> | <span data-ttu-id="3e48d-173">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-174">owin. Kod statususdus odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="3e48d-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="3e48d-175">Optional (Opcjonalność)</span><span class="sxs-lookup"><span data-stu-id="3e48d-175">Optional</span></span> |
| <span data-ttu-id="3e48d-176">owin. OdpowiedźReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="3e48d-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="3e48d-177">Optional (Opcjonalność)</span><span class="sxs-lookup"><span data-stu-id="3e48d-177">Optional</span></span> |
| <span data-ttu-id="3e48d-178">owin. ResponseHeaders (Głowice odpowiedzi)</span><span class="sxs-lookup"><span data-stu-id="3e48d-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="3e48d-179">owin. Ciało odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="3e48d-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="3e48d-180">Inne dane (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="3e48d-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="3e48d-181">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-181">Key</span></span>               | <span data-ttu-id="3e48d-182">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-182">Value (type)</span></span> | <span data-ttu-id="3e48d-183">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-184">owin. CallCancelled ( CallCancelled )</span><span class="sxs-lookup"><span data-stu-id="3e48d-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="3e48d-185">owin. Wersja</span><span class="sxs-lookup"><span data-stu-id="3e48d-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="3e48d-186">Wspólne klawisze</span><span class="sxs-lookup"><span data-stu-id="3e48d-186">Common keys</span></span>

| <span data-ttu-id="3e48d-187">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-187">Key</span></span>               | <span data-ttu-id="3e48d-188">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-188">Value (type)</span></span> | <span data-ttu-id="3e48d-189">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-190">Ssl. Clientcertificate</span><span class="sxs-lookup"><span data-stu-id="3e48d-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="3e48d-191">Ssl. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="3e48d-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="3e48d-192">Serwera. RemoteIpAddress (RemoteIpAddress)</span><span class="sxs-lookup"><span data-stu-id="3e48d-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-193">Serwera. Usługa RemotePort</span><span class="sxs-lookup"><span data-stu-id="3e48d-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="3e48d-194">Serwera. LocalIpAddress (LocalIpAddress)</span><span class="sxs-lookup"><span data-stu-id="3e48d-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-195">Serwera. Port_lokalny</span><span class="sxs-lookup"><span data-stu-id="3e48d-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="3e48d-196">Serwera. IsLocal (Lokalizacja)</span><span class="sxs-lookup"><span data-stu-id="3e48d-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="3e48d-197">Serwera. OnSendingHeaders (Głowice onsendingheaders)</span><span class="sxs-lookup"><span data-stu-id="3e48d-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="3e48d-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="3e48d-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="3e48d-199">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-199">Key</span></span>               | <span data-ttu-id="3e48d-200">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-200">Value (type)</span></span> | <span data-ttu-id="3e48d-201">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-202">Sendfile. Sendasync</span><span class="sxs-lookup"><span data-stu-id="3e48d-202">sendfile.SendAsync</span></span> | <span data-ttu-id="3e48d-203">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="3e48d-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="3e48d-204">Na żądanie</span><span class="sxs-lookup"><span data-stu-id="3e48d-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="3e48d-205">Nieprzezroczyste v0.3.0</span><span class="sxs-lookup"><span data-stu-id="3e48d-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="3e48d-206">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-206">Key</span></span>               | <span data-ttu-id="3e48d-207">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-207">Value (type)</span></span> | <span data-ttu-id="3e48d-208">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-209">Nieprzezroczyste. Wersja</span><span class="sxs-lookup"><span data-stu-id="3e48d-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="3e48d-210">Nieprzezroczyste. Uaktualnienia</span><span class="sxs-lookup"><span data-stu-id="3e48d-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="3e48d-211">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="3e48d-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="3e48d-212">Nieprzezroczyste. Strumienia</span><span class="sxs-lookup"><span data-stu-id="3e48d-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="3e48d-213">Nieprzezroczyste. CallCancelled ( CallCancelled )</span><span class="sxs-lookup"><span data-stu-id="3e48d-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="3e48d-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="3e48d-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="3e48d-215">Klucz</span><span class="sxs-lookup"><span data-stu-id="3e48d-215">Key</span></span>               | <span data-ttu-id="3e48d-216">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="3e48d-216">Value (type)</span></span> | <span data-ttu-id="3e48d-217">Opis</span><span class="sxs-lookup"><span data-stu-id="3e48d-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="3e48d-218">Websocket. Wersja</span><span class="sxs-lookup"><span data-stu-id="3e48d-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="3e48d-219">Websocket. Zaakceptować</span><span class="sxs-lookup"><span data-stu-id="3e48d-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="3e48d-220">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="3e48d-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="3e48d-221">Websocket. AcceptAlt (AcceptAlt)</span><span class="sxs-lookup"><span data-stu-id="3e48d-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="3e48d-222">Non-spec</span><span class="sxs-lookup"><span data-stu-id="3e48d-222">Non-spec</span></span> |
| <span data-ttu-id="3e48d-223">Websocket. SubProtocol</span><span class="sxs-lookup"><span data-stu-id="3e48d-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="3e48d-224">Patrz [RFC6455 Sekcja 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Krok 5.5</span><span class="sxs-lookup"><span data-stu-id="3e48d-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="3e48d-225">Websocket. Sendasync</span><span class="sxs-lookup"><span data-stu-id="3e48d-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="3e48d-226">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="3e48d-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="3e48d-227">Websocket. OdbiórAsync</span><span class="sxs-lookup"><span data-stu-id="3e48d-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="3e48d-228">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="3e48d-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="3e48d-229">Websocket. Zamknijasync</span><span class="sxs-lookup"><span data-stu-id="3e48d-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="3e48d-230">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="3e48d-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="3e48d-231">Websocket. CallCancelled ( CallCancelled )</span><span class="sxs-lookup"><span data-stu-id="3e48d-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="3e48d-232">Websocket. Stan klimowy</span><span class="sxs-lookup"><span data-stu-id="3e48d-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="3e48d-233">Optional (Opcjonalność)</span><span class="sxs-lookup"><span data-stu-id="3e48d-233">Optional</span></span> |
| <span data-ttu-id="3e48d-234">Websocket. ClientCloseDescription (Opis klienta)</span><span class="sxs-lookup"><span data-stu-id="3e48d-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="3e48d-235">Optional (Opcjonalność)</span><span class="sxs-lookup"><span data-stu-id="3e48d-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="3e48d-236">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3e48d-236">Additional resources</span></span>

* [<span data-ttu-id="3e48d-237">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="3e48d-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="3e48d-238">Serwery</span><span class="sxs-lookup"><span data-stu-id="3e48d-238">Servers</span></span>](xref:fundamentals/servers/index)
