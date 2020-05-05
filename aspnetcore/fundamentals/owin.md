---
title: Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core
author: ardalis
description: Dowiedz się, w jaki sposób ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN), dzięki czemu aplikacje sieci Web mają być oddzielone od serwerów sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: defbf428634a04e6fa3862926136dc98b88da589
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775963"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="18b09-103">Otwórz interfejs sieci Web dla platformy .NET (OWIN) z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18b09-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="18b09-104">[Steve Kowalski](https://ardalis.com/) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18b09-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="18b09-105">ASP.NET Core obsługuje otwarty interfejs sieci Web dla platformy .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="18b09-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="18b09-106">OWIN umożliwia oddzielenie aplikacji sieci Web od serwerów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="18b09-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="18b09-107">Definiuje standardowy sposób użycia oprogramowania pośredniczącego w potoku do obsługi żądań i skojarzonych odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="18b09-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="18b09-108">ASP.NET Core aplikacje i oprogramowanie pośredniczące mogą współdziałać z aplikacjami opartymi na OWIN, serwerami i oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="18b09-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="18b09-109">OWIN zapewnia warstwę odsprzęgającą, która umożliwia wspólne używanie dwóch platform z różnymi modelami obiektów.</span><span class="sxs-lookup"><span data-stu-id="18b09-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="18b09-110">`Microsoft.AspNetCore.Owin` Pakiet udostępnia dwie implementacje kart:</span><span class="sxs-lookup"><span data-stu-id="18b09-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="18b09-111">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="18b09-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="18b09-112">OWIN do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18b09-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="18b09-113">Dzięki temu ASP.NET Core mogą być hostowane na serwerze/hoście zgodnym z OWIN lub dla innych składników zgodnych OWIN, które mają być uruchamiane w oparciu o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18b09-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="18b09-114">Korzystanie z tych kart wiąże się z kosztem wydajności.</span><span class="sxs-lookup"><span data-stu-id="18b09-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="18b09-115">Aplikacje używające tylko składników ASP.NET Core nie powinny `Microsoft.AspNetCore.Owin` korzystać z pakietu lub kart.</span><span class="sxs-lookup"><span data-stu-id="18b09-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="18b09-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18b09-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="18b09-117">Uruchamianie oprogramowania pośredniczącego OWIN w potoku ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18b09-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="18b09-118">Obsługa OWIN ASP.NET Core jest wdrażana w ramach `Microsoft.AspNetCore.Owin` pakietu.</span><span class="sxs-lookup"><span data-stu-id="18b09-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="18b09-119">Możesz zaimportować obsługę OWIN do projektu, instalując ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="18b09-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="18b09-120">Oprogramowanie pośredniczące OWIN jest zgodne ze [specyfikacją Owin](https://owin.org/spec/spec/owin-1.0.0.html), która wymaga `Func<IDictionary<string, object>, Task>` interfejsu, i należy ustawić określone klucze (takie jak `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="18b09-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="18b09-121">Następujące proste oprogramowanie pośredniczące OWIN wyświetla "Hello world":</span><span class="sxs-lookup"><span data-stu-id="18b09-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="18b09-122">Przykładowa sygnatura zwraca `Task` i akceptuje `IDictionary<string, object>` jako wymagane przez Owin.</span><span class="sxs-lookup"><span data-stu-id="18b09-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="18b09-123">Poniższy kod pokazuje, jak dodać `OwinHello` oprogramowanie pośredniczące (pokazane powyżej) do potoku ASP.NET Core przy użyciu `UseOwin` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="18b09-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="18b09-124">Można skonfigurować inne akcje, które mają być wykonywane w potoku OWIN.</span><span class="sxs-lookup"><span data-stu-id="18b09-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="18b09-125">Nagłówki odpowiedzi należy modyfikować tylko przed pierwszym zapisem w strumieniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="18b09-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="18b09-126">Wiele wywołań `UseOwin` jest niezalecane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="18b09-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="18b09-127">Składniki OWIN działają najlepiej, jeśli są zgrupowane razem.</span><span class="sxs-lookup"><span data-stu-id="18b09-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="18b09-128">Korzystanie z ASP.NET Core hosting na serwerze z systemem OWIN</span><span class="sxs-lookup"><span data-stu-id="18b09-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="18b09-129">Serwery z systemem OWIN mogą hostować aplikacje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18b09-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="18b09-130">Jeden taki serwer to [nowin](https://github.com/Bobris/Nowin), serwer sieci Web programu .NET Owin.</span><span class="sxs-lookup"><span data-stu-id="18b09-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="18b09-131">W przykładzie w tym artykule dodaliśmy projekt, który odwołuje się do nowin i używa go do utworzenia `IServer` możliwości samodzielnego udostępniania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18b09-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="18b09-132">`IServer`jest interfejsem, który wymaga `Features` właściwości i `Start` metody.</span><span class="sxs-lookup"><span data-stu-id="18b09-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="18b09-133">`Start`jest odpowiedzialny za konfigurowanie i uruchamianie serwera, co w tym przypadku jest realizowane za pośrednictwem szeregu wywołań interfejsu API Fluent, które ustawiają adresy przeanalizowane z IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="18b09-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="18b09-134">Należy zauważyć, że konfiguracja Fluent `_builder` zmiennej określa, że żądania będą obsługiwane przez `appFunc` zdefiniowane wcześniej w metodzie.</span><span class="sxs-lookup"><span data-stu-id="18b09-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="18b09-135">Jest `Func` on wywoływany dla każdego żądania w celu przetworzenia żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="18b09-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="18b09-136">Dodamy również `IWebHostBuilder` rozszerzenie, aby ułatwić Dodawanie i Konfigurowanie serwera nowin.</span><span class="sxs-lookup"><span data-stu-id="18b09-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

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

<span data-ttu-id="18b09-137">W tym miejscu Wywołaj rozszerzenie *program.cs* , aby uruchomić aplikację ASP.NET Core przy użyciu tego serwera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="18b09-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

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

<span data-ttu-id="18b09-138">Dowiedz się więcej o [serwerach ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="18b09-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="18b09-139">Uruchom ASP.NET Core na serwerze z systemem OWIN i Użyj obsługi jego gniazd WebSockets</span><span class="sxs-lookup"><span data-stu-id="18b09-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="18b09-140">Innym przykładem sposobu korzystania z funkcji serwerów opartych na OWIN można użyć ASP.NET Core jest dostęp do funkcji, takich jak WebSockets.</span><span class="sxs-lookup"><span data-stu-id="18b09-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="18b09-141">Serwer sieci Web programu .NET OWIN używany w poprzednim przykładzie obsługuje wbudowane w sieć Web Sockets, które mogą być używane przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18b09-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="18b09-142">W poniższym przykładzie przedstawiono prostą aplikację sieci Web, która obsługuje gniazda sieci Web i zwraca wszystkie dane wysyłane do serwera za pomocą elementów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="18b09-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="18b09-143">Ten [przykład](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) jest konfigurowany przy użyciu takiego `NowinServer` samego, jak poprzedni — jedyną różnicą jest to, w jaki sposób aplikacja jest skonfigurowana `Configure` w metodzie.</span><span class="sxs-lookup"><span data-stu-id="18b09-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="18b09-144">Test korzystający z [prostego klienta protokołu WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) pokazuje aplikację:</span><span class="sxs-lookup"><span data-stu-id="18b09-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Klient testowy gniazda sieci Web](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="18b09-146">Środowisko OWIN</span><span class="sxs-lookup"><span data-stu-id="18b09-146">OWIN environment</span></span>

<span data-ttu-id="18b09-147">Środowisko OWIN można utworzyć przy użyciu `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="18b09-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="18b09-148">Klucze OWIN</span><span class="sxs-lookup"><span data-stu-id="18b09-148">OWIN keys</span></span>

<span data-ttu-id="18b09-149">OWIN zależy od `IDictionary<string,object>` obiektu do przekazywania informacji w trakcie wymiany żądania lub odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="18b09-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="18b09-150">ASP.NET Core implementuje klucze wymienione poniżej.</span><span class="sxs-lookup"><span data-stu-id="18b09-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="18b09-151">Zobacz [podstawowe specyfikacje, rozszerzenia](https://owin.org/#spec)i [wskazówki dotyczące klucza Owin oraz wspólne klucze](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="18b09-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="18b09-152">Dane żądania (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="18b09-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="18b09-153">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-153">Key</span></span>               | <span data-ttu-id="18b09-154">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-154">Value (type)</span></span> | <span data-ttu-id="18b09-155">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-156">Owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="18b09-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="18b09-157">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="18b09-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="18b09-158">Owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="18b09-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="18b09-159">Owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="18b09-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="18b09-160">Owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="18b09-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="18b09-161">Owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="18b09-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="18b09-162">Owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="18b09-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="18b09-163">Owin. Elemencie requestbody</span><span class="sxs-lookup"><span data-stu-id="18b09-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="18b09-164">Dane żądania (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="18b09-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="18b09-165">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-165">Key</span></span>               | <span data-ttu-id="18b09-166">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-166">Value (type)</span></span> | <span data-ttu-id="18b09-167">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-168">Owin. IdentyfikatorŻądania</span><span class="sxs-lookup"><span data-stu-id="18b09-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="18b09-169">Optional</span><span class="sxs-lookup"><span data-stu-id="18b09-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="18b09-170">Dane odpowiedzi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="18b09-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="18b09-171">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-171">Key</span></span>               | <span data-ttu-id="18b09-172">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-172">Value (type)</span></span> | <span data-ttu-id="18b09-173">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-174">Owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="18b09-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="18b09-175">Optional</span><span class="sxs-lookup"><span data-stu-id="18b09-175">Optional</span></span> |
| <span data-ttu-id="18b09-176">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="18b09-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="18b09-177">Optional</span><span class="sxs-lookup"><span data-stu-id="18b09-177">Optional</span></span> |
| <span data-ttu-id="18b09-178">Owin. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="18b09-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="18b09-179">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="18b09-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="18b09-180">Inne dane (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="18b09-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="18b09-181">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-181">Key</span></span>               | <span data-ttu-id="18b09-182">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-182">Value (type)</span></span> | <span data-ttu-id="18b09-183">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-184">Owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="18b09-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="18b09-185">Owin. Wersja</span><span class="sxs-lookup"><span data-stu-id="18b09-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="18b09-186">Klucze wspólne</span><span class="sxs-lookup"><span data-stu-id="18b09-186">Common keys</span></span>

| <span data-ttu-id="18b09-187">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-187">Key</span></span>               | <span data-ttu-id="18b09-188">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-188">Value (type)</span></span> | <span data-ttu-id="18b09-189">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-190">zastosowania. Kolekcja</span><span class="sxs-lookup"><span data-stu-id="18b09-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="18b09-191">zastosowania. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="18b09-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="18b09-192">Server. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="18b09-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="18b09-193">Server. RemotePort</span><span class="sxs-lookup"><span data-stu-id="18b09-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="18b09-194">Server. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="18b09-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="18b09-195">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="18b09-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="18b09-196">Server. IsLocal</span><span class="sxs-lookup"><span data-stu-id="18b09-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="18b09-197">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="18b09-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="18b09-198">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="18b09-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="18b09-199">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-199">Key</span></span>               | <span data-ttu-id="18b09-200">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-200">Value (type)</span></span> | <span data-ttu-id="18b09-201">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-202">sendfile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="18b09-202">sendfile.SendAsync</span></span> | <span data-ttu-id="18b09-203">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="18b09-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="18b09-204">Na żądanie</span><span class="sxs-lookup"><span data-stu-id="18b09-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="18b09-205">Nieprzezroczysty 0.3.0 v</span><span class="sxs-lookup"><span data-stu-id="18b09-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="18b09-206">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-206">Key</span></span>               | <span data-ttu-id="18b09-207">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-207">Value (type)</span></span> | <span data-ttu-id="18b09-208">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-209">zakrywa. Wersja</span><span class="sxs-lookup"><span data-stu-id="18b09-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="18b09-210">zakrywa. Uaktualniony</span><span class="sxs-lookup"><span data-stu-id="18b09-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="18b09-211">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="18b09-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="18b09-212">zakrywa. Produkcyjne</span><span class="sxs-lookup"><span data-stu-id="18b09-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="18b09-213">zakrywa. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="18b09-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="18b09-214">0.3.0 protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="18b09-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="18b09-215">Klucz</span><span class="sxs-lookup"><span data-stu-id="18b09-215">Key</span></span>               | <span data-ttu-id="18b09-216">Wartość (typ)</span><span class="sxs-lookup"><span data-stu-id="18b09-216">Value (type)</span></span> | <span data-ttu-id="18b09-217">Opis</span><span class="sxs-lookup"><span data-stu-id="18b09-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="18b09-218">akceptowan. Wersja</span><span class="sxs-lookup"><span data-stu-id="18b09-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="18b09-219">akceptowan. Odebrać</span><span class="sxs-lookup"><span data-stu-id="18b09-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="18b09-220">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="18b09-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="18b09-221">akceptowan. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="18b09-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="18b09-222">Nie-spec</span><span class="sxs-lookup"><span data-stu-id="18b09-222">Non-spec</span></span> |
| <span data-ttu-id="18b09-223">akceptowan. Podprotokół</span><span class="sxs-lookup"><span data-stu-id="18b09-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="18b09-224">Zobacz [RFC6455 sekcja 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="18b09-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="18b09-225">akceptowan. SendAsync</span><span class="sxs-lookup"><span data-stu-id="18b09-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="18b09-226">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="18b09-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="18b09-227">akceptowan. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="18b09-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="18b09-228">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="18b09-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="18b09-229">akceptowan. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="18b09-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="18b09-230">Zobacz [podpis delegata](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="18b09-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="18b09-231">akceptowan. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="18b09-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="18b09-232">akceptowan. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="18b09-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="18b09-233">Optional</span><span class="sxs-lookup"><span data-stu-id="18b09-233">Optional</span></span> |
| <span data-ttu-id="18b09-234">akceptowan. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="18b09-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="18b09-235">Optional</span><span class="sxs-lookup"><span data-stu-id="18b09-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="18b09-236">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="18b09-236">Additional resources</span></span>

* [<span data-ttu-id="18b09-237">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="18b09-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="18b09-238">Serwery</span><span class="sxs-lookup"><span data-stu-id="18b09-238">Servers</span></span>](xref:fundamentals/servers/index)
