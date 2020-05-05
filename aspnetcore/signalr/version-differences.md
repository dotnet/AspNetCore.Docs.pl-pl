---
title: Różnice między SignalR i ASP.NET CoreSignalR
author: bradygaster
description: Różnice między SignalR i ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772582"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="5bc04-103">Różnice między sygnalizującym ASP.NET a ASP.NET Core sygnalizującym</span><span class="sxs-lookup"><span data-stu-id="5bc04-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="5bc04-104">ASP.NET Core sygnalizujący nie jest zgodny z klientami lub serwerami dla sygnalizującego ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5bc04-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="5bc04-105">Ten artykuł zawiera szczegółowe informacje o funkcjach, które zostały usunięte lub zmienione w programie ASP.NET Core Signaler.</span><span class="sxs-lookup"><span data-stu-id="5bc04-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="5bc04-106">Jak zidentyfikować wersję sygnalizującego</span><span class="sxs-lookup"><span data-stu-id="5bc04-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="5bc04-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="5bc04-107">ASP.NET SignalR</span></span> | <span data-ttu-id="5bc04-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="5bc04-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="5bc04-109">Pakiet NuGet serwera</span><span class="sxs-lookup"><span data-stu-id="5bc04-109">Server NuGet package</span></span> | [<span data-ttu-id="5bc04-110">Microsoft. AspNet. Signal</span><span class="sxs-lookup"><span data-stu-id="5bc04-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="5bc04-111">Brak.</span><span class="sxs-lookup"><span data-stu-id="5bc04-111">None.</span></span> <span data-ttu-id="5bc04-112">Uwzględnione w udostępnionej platformie [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="5bc04-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="5bc04-113">Pakiety NuGet klienta</span><span class="sxs-lookup"><span data-stu-id="5bc04-113">Client NuGet packages</span></span> | [<span data-ttu-id="5bc04-114">Microsoft. AspNet. Signal. Client</span><span class="sxs-lookup"><span data-stu-id="5bc04-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="5bc04-115">Microsoft. AspNet. Signaler. JS</span><span class="sxs-lookup"><span data-stu-id="5bc04-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="5bc04-116">Microsoft. AspNetCore. Signaler. Client</span><span class="sxs-lookup"><span data-stu-id="5bc04-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="5bc04-117">Pakiet npm klienta języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5bc04-117">JavaScript client npm package</span></span> | [<span data-ttu-id="5bc04-118">signalr</span><span class="sxs-lookup"><span data-stu-id="5bc04-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="5bc04-119">Klient Java</span><span class="sxs-lookup"><span data-stu-id="5bc04-119">Java client</span></span> | <span data-ttu-id="5bc04-120">[Repozytorium GitHub](https://github.com/SignalR/java-client) (przestarzałe)</span><span class="sxs-lookup"><span data-stu-id="5bc04-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="5bc04-121">Maven pakietu [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="5bc04-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="5bc04-122">Typ aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="5bc04-122">Server app type</span></span> | <span data-ttu-id="5bc04-123">ASP.NET (System. Web) lub samoobsługowy OWIN</span><span class="sxs-lookup"><span data-stu-id="5bc04-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="5bc04-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5bc04-124">ASP.NET Core</span></span> |
| <span data-ttu-id="5bc04-125">Obsługiwane platformy serwera</span><span class="sxs-lookup"><span data-stu-id="5bc04-125">Supported server platforms</span></span> | <span data-ttu-id="5bc04-126">.NET Framework 4,5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5bc04-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="5bc04-127">.NET Core 3,0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5bc04-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="5bc04-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="5bc04-128">ASP.NET SignalR</span></span> | <span data-ttu-id="5bc04-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="5bc04-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="5bc04-130">Pakiet NuGet serwera</span><span class="sxs-lookup"><span data-stu-id="5bc04-130">Server NuGet package</span></span> | <span data-ttu-id="5bc04-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="5bc04-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="5bc04-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="5bc04-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="5bc04-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="5bc04-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="5bc04-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="5bc04-134">(.NET Framework)</span></span> |
| <span data-ttu-id="5bc04-135">Pakiety NuGet klienta</span><span class="sxs-lookup"><span data-stu-id="5bc04-135">Client NuGet packages</span></span> | <span data-ttu-id="5bc04-136">[Microsoft. AspNet. SignalR. Klient](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="5bc04-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="5bc04-137">[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="5bc04-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="5bc04-138">[Microsoft. AspNetCore. SignalR. Klient](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="5bc04-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="5bc04-139">Pakiet npm klienta języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5bc04-139">JavaScript client npm package</span></span> | [<span data-ttu-id="5bc04-140">signalr</span><span class="sxs-lookup"><span data-stu-id="5bc04-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="5bc04-141">Klient Java</span><span class="sxs-lookup"><span data-stu-id="5bc04-141">Java client</span></span> | <span data-ttu-id="5bc04-142">[Repozytorium GitHub](https://github.com/SignalR/java-client) (przestarzałe)</span><span class="sxs-lookup"><span data-stu-id="5bc04-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="5bc04-143">Maven pakietu [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="5bc04-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="5bc04-144">Typ aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="5bc04-144">Server app type</span></span> | <span data-ttu-id="5bc04-145">ASP.NET (System. Web) lub samoobsługowy OWIN</span><span class="sxs-lookup"><span data-stu-id="5bc04-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="5bc04-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5bc04-146">ASP.NET Core</span></span> |
| <span data-ttu-id="5bc04-147">Obsługiwane platformy serwera</span><span class="sxs-lookup"><span data-stu-id="5bc04-147">Supported server platforms</span></span> | <span data-ttu-id="5bc04-148">.NET Framework 4,5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5bc04-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="5bc04-149">.NET Framework 4.6.1 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="5bc04-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="5bc04-150">.NET Core 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5bc04-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="5bc04-151">Różnice w funkcjach</span><span class="sxs-lookup"><span data-stu-id="5bc04-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="5bc04-152">Automatyczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="5bc04-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bc04-153">W ASP.NET SignalR:</span><span class="sxs-lookup"><span data-stu-id="5bc04-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="5bc04-154">Domyślnie program próbuje ponownie nawiązać połączenie z serwerem, SignalR Jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="5bc04-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="5bc04-155">W ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="5bc04-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="5bc04-156">Automatyczne ponowne łączenie jest zgodą na [klienta .NET](xref:signalr/dotnet-client#automatically-reconnect) i [klienta JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="5bc04-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5bc04-157">Przed ASP.NET Core 3,0 program SignalR nie obsługuje automatycznego ponownego nawiązywania połączeń.</span><span class="sxs-lookup"><span data-stu-id="5bc04-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="5bc04-158">Jeśli klient zostanie odłączony, użytkownik musi jawnie rozpocząć nowe połączenie, aby ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="5bc04-159">W programie SignalRASP.NET SignalR próbuje ponownie nawiązać połączenie z serwerem, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="5bc04-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="5bc04-160">Obsługa protokołów</span><span class="sxs-lookup"><span data-stu-id="5bc04-160">Protocol support</span></span>

<span data-ttu-id="5bc04-161">ASP.NET Core SignalR obsługuje kod JSON, a także nowy protokół binarny na podstawie [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="5bc04-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="5bc04-162">Dodatkowo można utworzyć niestandardowe protokoły.</span><span class="sxs-lookup"><span data-stu-id="5bc04-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="5bc04-163">Transporty</span><span class="sxs-lookup"><span data-stu-id="5bc04-163">Transports</span></span>

<span data-ttu-id="5bc04-164">Transport ramki bez ograniczeń nie jest obsługiwany w SignalRASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5bc04-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="5bc04-165">Różnice na serwerze</span><span class="sxs-lookup"><span data-stu-id="5bc04-165">Differences on the server</span></span>

<span data-ttu-id="5bc04-166">ASP.NET Core SignalR biblioteki po stronie serwera są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), który jest używany w szablonie **ASP.NET Core aplikacji sieci Web** dla projektów Razor obu i MVC.</span><span class="sxs-lookup"><span data-stu-id="5bc04-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="5bc04-167">ASP.NET Core SignalR to ASP.NET Core oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="5bc04-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="5bc04-168">Należy ją skonfigurować przez wywołanie metody <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5bc04-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bc04-169">Aby skonfigurować Routing, Mapuj trasy do centrów wewnątrz <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> wywołania metody w `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5bc04-170">Aby skonfigurować Routing, Mapuj trasy do centrów wewnątrz <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> wywołania metody w `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="5bc04-171">Sesje programu Sticky</span><span class="sxs-lookup"><span data-stu-id="5bc04-171">Sticky sessions</span></span>

<span data-ttu-id="5bc04-172">Model skalowania dla ASP.NET SignalR umożliwia klientom Ponowne nawiązywanie połączenia i wysyłanie komunikatów do dowolnego serwera w farmie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="5bc04-173">W ASP.NET Core SignalRklient musi korzystać z tego samego serwera na czas trwania połączenia.</span><span class="sxs-lookup"><span data-stu-id="5bc04-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="5bc04-174">W przypadku skalowania przy użyciu Redis, oznacza to, że są wymagane sesje programu Sticky.</span><span class="sxs-lookup"><span data-stu-id="5bc04-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="5bc04-175">W przypadku skalowania przy użyciu [usługi platformy Azure SignalR ](/azure/azure-signalr/)sesje nie są wymagane, ponieważ usługa obsługuje połączenia z klientami.</span><span class="sxs-lookup"><span data-stu-id="5bc04-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="5bc04-176">Jedno centrum na połączenie</span><span class="sxs-lookup"><span data-stu-id="5bc04-176">Single hub per connection</span></span>

<span data-ttu-id="5bc04-177">W ASP.NET Core SignalRmodel połączenia został uproszczony.</span><span class="sxs-lookup"><span data-stu-id="5bc04-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="5bc04-178">Połączenia są nawiązywane bezpośrednio w jednym centrum, a nie za pomocą jednego połączenia, które jest używane do udostępniania dostępu do wielu centrów.</span><span class="sxs-lookup"><span data-stu-id="5bc04-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="5bc04-179">Przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="5bc04-179">Streaming</span></span>

<span data-ttu-id="5bc04-180">ASP.NET Core SignalR teraz obsługuje [przesyłanie strumieniowe danych](xref:signalr/streaming) z centrum do klienta programu.</span><span class="sxs-lookup"><span data-stu-id="5bc04-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="5bc04-181">Stan</span><span class="sxs-lookup"><span data-stu-id="5bc04-181">State</span></span>

<span data-ttu-id="5bc04-182">Możliwość przekazania dowolnego stanu między klientami a centrum (często wywoływanymi `HubState`) została usunięta, a także do obsługi komunikatów o postępie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="5bc04-183">W tej chwili nie ma żadnego odpowiednika serwerów proxy centrum.</span><span class="sxs-lookup"><span data-stu-id="5bc04-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="5bc04-184">Usuwanie PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="5bc04-184">PersistentConnection removal</span></span>

<span data-ttu-id="5bc04-185">W ASP.NET Core SignalRKlasa [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) została usunięta.</span><span class="sxs-lookup"><span data-stu-id="5bc04-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="5bc04-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="5bc04-186">GlobalHost</span></span>

<span data-ttu-id="5bc04-187">ASP.NET Core ma iniekcję zależności (DI) wbudowaną w strukturę.</span><span class="sxs-lookup"><span data-stu-id="5bc04-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="5bc04-188">Usługi mogą używać DI do uzyskiwania dostępu do [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="5bc04-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="5bc04-189">`GlobalHost` Obiekt, który jest używany w ASP.NET SignalR do pobrania, `HubContext` nie istnieje w ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="5bc04-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="5bc04-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="5bc04-190">HubPipeline</span></span>

<span data-ttu-id="5bc04-191">ASP.NET Core SignalR nie obsługuje `HubPipeline` modułów.</span><span class="sxs-lookup"><span data-stu-id="5bc04-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="5bc04-192">Różnice dotyczące klienta</span><span class="sxs-lookup"><span data-stu-id="5bc04-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="5bc04-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="5bc04-193">TypeScript</span></span>

<span data-ttu-id="5bc04-194">Klient ASP.NET Core SignalR jest zapisywana w języku [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="5bc04-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="5bc04-195">Podczas korzystania z [klienta JavaScript](xref:signalr/javascript-client)można pisać w języku JavaScript lub TypeScript.</span><span class="sxs-lookup"><span data-stu-id="5bc04-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="5bc04-196">Klient JavaScript jest hostowany w npm</span><span class="sxs-lookup"><span data-stu-id="5bc04-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bc04-197">W wersjach ASP.NET klient JavaScript został uzyskany za pomocą pakietu NuGet w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5bc04-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="5bc04-198">W wersjach ASP.NET Core pakiet [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm zawiera biblioteki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bc04-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="5bc04-199">Ten pakiet nie jest uwzględniony w szablonie **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="5bc04-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="5bc04-200">Użyj npm, aby uzyskać i zainstalować `@microsoft/signalr` pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="5bc04-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5bc04-201">W wersjach ASP.NET klient JavaScript został uzyskany za pomocą pakietu NuGet w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5bc04-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="5bc04-202">W wersjach ASP.NET Core pakiet [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm zawiera biblioteki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bc04-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="5bc04-203">Ten pakiet nie jest uwzględniony w szablonie **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="5bc04-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="5bc04-204">Użyj npm, aby uzyskać i zainstalować `@aspnet/signalr` pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="5bc04-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="5bc04-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="5bc04-205">jQuery</span></span>

<span data-ttu-id="5bc04-206">Zależność od jQuery została usunięta, jednak projekty nadal mogą korzystać z jQuery.</span><span class="sxs-lookup"><span data-stu-id="5bc04-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="5bc04-207">Obsługa programu Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="5bc04-207">Internet Explorer support</span></span>

<span data-ttu-id="5bc04-208">ASP.NET Core SignalR wymaga programu Microsoft Internet Explorer 11 lub nowszego SignalR (ASP.NET obsługiwany program Microsoft Internet Explorer 8 lub nowszy).</span><span class="sxs-lookup"><span data-stu-id="5bc04-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="5bc04-209">Składnia metody klienta JavaScript</span><span class="sxs-lookup"><span data-stu-id="5bc04-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bc04-210">Składnia języka JavaScript została zmieniona z wersji ASP.NET systemu SignalR.</span><span class="sxs-lookup"><span data-stu-id="5bc04-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="5bc04-211">Zamiast używać `$connection` obiektu, Utwórz połączenie przy użyciu interfejsu API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="5bc04-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="5bc04-212">Użyj metody [on](/javascript/api/@microsoft/signalr/HubConnection#on) , aby określić metody klienta, które mogą być wywoływane przez centrum.</span><span class="sxs-lookup"><span data-stu-id="5bc04-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5bc04-213">Składnia języka JavaScript została zmieniona z wersji ASP.NET systemu SignalR.</span><span class="sxs-lookup"><span data-stu-id="5bc04-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="5bc04-214">Zamiast używać `$connection` obiektu, Utwórz połączenie przy użyciu interfejsu API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="5bc04-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="5bc04-215">Użyj metody [on](/javascript/api/@aspnet/signalr/HubConnection#on) , aby określić metody klienta, które mogą być wywoływane przez centrum.</span><span class="sxs-lookup"><span data-stu-id="5bc04-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="5bc04-216">Po utworzeniu metody klienta należy uruchomić połączenie z centrum.</span><span class="sxs-lookup"><span data-stu-id="5bc04-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="5bc04-217">Tworzenie łańcucha metod [przechwytywania](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) w celu rejestrowania lub obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="5bc04-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="5bc04-218">Serwery proxy centrum</span><span class="sxs-lookup"><span data-stu-id="5bc04-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bc04-219">Serwery proxy centrum nie są już generowane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="5bc04-220">Zamiast tego nazwa metody jest przenoszona do [wywoływania](/javascript/api/@microsoft/signalr/hubconnection#invoke) API jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="5bc04-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5bc04-221">Serwery proxy centrum nie są już generowane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="5bc04-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="5bc04-222">Zamiast tego nazwa metody jest przenoszona do [wywoływania](/javascript/api/@aspnet/signalr/hubconnection#invoke) API jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="5bc04-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="5bc04-223">.NET i inni klienci</span><span class="sxs-lookup"><span data-stu-id="5bc04-223">.NET and other clients</span></span>

<span data-ttu-id="5bc04-224">[Microsoft. AspNetCoreSignalR... ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Pakiet NuGet klienta zawiera biblioteki klienckie platformy .NET dla ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="5bc04-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="5bc04-225">Użyj, <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> aby utworzyć i skompilować wystąpienie połączenia z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="5bc04-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="5bc04-226">Różnice skalowania</span><span class="sxs-lookup"><span data-stu-id="5bc04-226">Scaleout differences</span></span>

<span data-ttu-id="5bc04-227">ASP.NET SignalR obsługuje SQL Server i Redis.</span><span class="sxs-lookup"><span data-stu-id="5bc04-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="5bc04-228">ASP.NET Core SignalR obsługuje usługi SignalR platformy Azure i Redis.</span><span class="sxs-lookup"><span data-stu-id="5bc04-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="5bc04-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="5bc04-229">ASP.NET</span></span>

* <span data-ttu-id="5bc04-230">[SignalRskalowania z Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="5bc04-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="5bc04-231">[SignalRskalowania z Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="5bc04-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="5bc04-232">[SignalRskalowania z SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="5bc04-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="5bc04-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5bc04-233">ASP.NET Core</span></span>

* <span data-ttu-id="5bc04-234">[Usługa SignalR platformy Azure](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="5bc04-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="5bc04-235">Redis</span><span class="sxs-lookup"><span data-stu-id="5bc04-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="5bc04-236">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5bc04-236">Additional resources</span></span>

* [<span data-ttu-id="5bc04-237">Centra</span><span class="sxs-lookup"><span data-stu-id="5bc04-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5bc04-238">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="5bc04-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="5bc04-239">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="5bc04-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5bc04-240">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="5bc04-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
