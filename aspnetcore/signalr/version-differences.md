---
title: 'Różnice między SignalR i ASP.NET Core SignalR'
author: bradygaster
description: 'Różnice między SignalR i ASP.NET Core SignalR'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061213"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="9af28-103">Różnice między ASP.NET SignalR i ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="9af28-104">ASP.NET Core SignalR nie jest zgodny z klientami lub serwerami dla ASP.NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="9af28-105">Ten artykuł zawiera szczegółowe informacje o funkcjach, które zostały usunięte lub zmienione w programie ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="9af28-106">Jak zidentyfikować SignalR wersję</span><span class="sxs-lookup"><span data-stu-id="9af28-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="9af28-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-107">ASP.NET SignalR</span></span> | <span data-ttu-id="9af28-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="9af28-109">**Pakiet NuGet serwera**</span><span class="sxs-lookup"><span data-stu-id="9af28-109">**Server NuGet package**</span></span> | <span data-ttu-id="9af28-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="9af28-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="9af28-111">Brak.</span><span class="sxs-lookup"><span data-stu-id="9af28-111">None.</span></span> <span data-ttu-id="9af28-112">Uwzględnione w udostępnionej platformie [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="9af28-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="9af28-113">**Pakiety NuGet klienta**</span><span class="sxs-lookup"><span data-stu-id="9af28-113">**Client NuGet packages**</span></span> | <span data-ttu-id="9af28-114">[Microsoft. AspNet. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="9af28-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="9af28-115">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="9af28-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="9af28-116">[Microsoft. AspNetCore. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="9af28-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="9af28-117">**Pakiet npm klienta języka JavaScript**</span><span class="sxs-lookup"><span data-stu-id="9af28-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="9af28-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="9af28-119">**Klient Java**</span><span class="sxs-lookup"><span data-stu-id="9af28-119">**Java client**</span></span> | <span data-ttu-id="9af28-120">[Repozytorium GitHub](https://github.com/SignalR/java-client) (przestarzałe)</span><span class="sxs-lookup"><span data-stu-id="9af28-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="9af28-121">Maven pakietu [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="9af28-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="9af28-122">**Typ aplikacji serwera**</span><span class="sxs-lookup"><span data-stu-id="9af28-122">**Server app type**</span></span> | <span data-ttu-id="9af28-123">ASP.NET (System. Web) lub OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="9af28-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="9af28-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9af28-124">ASP.NET Core</span></span> |
| <span data-ttu-id="9af28-125">**Obsługiwane platformy serwera**</span><span class="sxs-lookup"><span data-stu-id="9af28-125">**Supported server platforms**</span></span> | <span data-ttu-id="9af28-126">.NET Framework 4,5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9af28-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="9af28-127">.NET Core 3,0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9af28-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="9af28-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-128">ASP.NET SignalR</span></span> | <span data-ttu-id="9af28-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="9af28-130">**Pakiet NuGet serwera**</span><span class="sxs-lookup"><span data-stu-id="9af28-130">**Server NuGet package**</span></span> | <span data-ttu-id="9af28-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="9af28-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="9af28-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="9af28-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="9af28-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="9af28-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="9af28-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="9af28-134">(.NET Framework)</span></span> |
| <span data-ttu-id="9af28-135">**Pakiety NuGet klienta**</span><span class="sxs-lookup"><span data-stu-id="9af28-135">**Client NuGet packages**</span></span> | <span data-ttu-id="9af28-136">[Microsoft. AspNet. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="9af28-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="9af28-137">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="9af28-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="9af28-138">[Microsoft. AspNetCore. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="9af28-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="9af28-139">**Pakiet npm klienta języka JavaScript**</span><span class="sxs-lookup"><span data-stu-id="9af28-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="9af28-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="9af28-141">**Klient Java**</span><span class="sxs-lookup"><span data-stu-id="9af28-141">**Java client**</span></span> | <span data-ttu-id="9af28-142">[Repozytorium GitHub](https://github.com/SignalR/java-client) (przestarzałe)</span><span class="sxs-lookup"><span data-stu-id="9af28-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="9af28-143">Maven pakietu [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="9af28-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="9af28-144">**Typ aplikacji serwera**</span><span class="sxs-lookup"><span data-stu-id="9af28-144">**Server app type**</span></span> | <span data-ttu-id="9af28-145">ASP.NET (System. Web) lub OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="9af28-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="9af28-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9af28-146">ASP.NET Core</span></span> |
| <span data-ttu-id="9af28-147">**Obsługiwane platformy serwera**</span><span class="sxs-lookup"><span data-stu-id="9af28-147">**Supported server platforms**</span></span> | <span data-ttu-id="9af28-148">.NET Framework 4,5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9af28-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="9af28-149">.NET Framework 4.6.1 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="9af28-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="9af28-150">.NET Core 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9af28-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="9af28-151">Różnice w funkcjach</span><span class="sxs-lookup"><span data-stu-id="9af28-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="9af28-152">Automatyczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="9af28-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9af28-153">W ASP.NET SignalR :</span><span class="sxs-lookup"><span data-stu-id="9af28-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="9af28-154">Domyślnie program SignalR próbuje ponownie nawiązać połączenie z serwerem, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="9af28-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="9af28-155">W ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="9af28-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="9af28-156">Automatyczne ponowne łączenie jest zgodą na [klienta .NET](xref:signalr/dotnet-client#automatically-reconnect) i [klienta JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="9af28-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9af28-157">Przed ASP.NET Core 3,0 program SignalR nie obsługuje automatycznego ponownego nawiązywania połączeń.</span><span class="sxs-lookup"><span data-stu-id="9af28-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="9af28-158">Jeśli klient zostanie odłączony, użytkownik musi jawnie rozpocząć nowe połączenie, aby ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="9af28-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="9af28-159">W programie SignalR ASP.NET SignalR próbuje ponownie nawiązać połączenie z serwerem, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="9af28-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="9af28-160">Obsługa protokołów</span><span class="sxs-lookup"><span data-stu-id="9af28-160">Protocol support</span></span>

<span data-ttu-id="9af28-161">ASP.NET Core SignalR obsługuje kod JSON, a także nowy protokół binarny na podstawie [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="9af28-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="9af28-162">Dodatkowo można utworzyć niestandardowe protokoły.</span><span class="sxs-lookup"><span data-stu-id="9af28-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="9af28-163">Transporty</span><span class="sxs-lookup"><span data-stu-id="9af28-163">Transports</span></span>

<span data-ttu-id="9af28-164">Transport ramki bez ograniczeń nie jest obsługiwany w ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="9af28-165">Różnice na serwerze</span><span class="sxs-lookup"><span data-stu-id="9af28-165">Differences on the server</span></span>

<span data-ttu-id="9af28-166">ASP.NET Core SignalR biblioteki po stronie serwera są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), który jest używany w szablonie **ASP.NET Core aplikacji sieci Web** dla Razor projektów obu i MVC.</span><span class="sxs-lookup"><span data-stu-id="9af28-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="9af28-167">ASP.NET Core SignalR to ASP.NET Core oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="9af28-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="9af28-168">Należy ją skonfigurować przez wywołanie metody <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9af28-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9af28-169">Aby skonfigurować Routing, Mapuj trasy do centrów wewnątrz <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> wywołania metody w `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="9af28-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9af28-170">Aby skonfigurować Routing, Mapuj trasy do centrów wewnątrz <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> wywołania metody w `Startup.Configure` metodzie.</span><span class="sxs-lookup"><span data-stu-id="9af28-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="9af28-171">Sesje programu Sticky</span><span class="sxs-lookup"><span data-stu-id="9af28-171">Sticky sessions</span></span>

<span data-ttu-id="9af28-172">Model skalowania dla ASP.NET SignalR umożliwia klientom Ponowne nawiązywanie połączenia i wysyłanie komunikatów do dowolnego serwera w farmie.</span><span class="sxs-lookup"><span data-stu-id="9af28-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="9af28-173">W ASP.NET Core SignalR Klient musi korzystać z tego samego serwera na czas trwania połączenia.</span><span class="sxs-lookup"><span data-stu-id="9af28-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="9af28-174">W przypadku skalowania przy użyciu Redis, oznacza to, że są wymagane sesje programu Sticky.</span><span class="sxs-lookup"><span data-stu-id="9af28-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="9af28-175">W przypadku skalowania przy użyciu [ SignalR usługi platformy Azure](/azure/azure-signalr/)sesje nie są wymagane, ponieważ usługa obsługuje połączenia z klientami.</span><span class="sxs-lookup"><span data-stu-id="9af28-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="9af28-176">Jedno centrum na połączenie</span><span class="sxs-lookup"><span data-stu-id="9af28-176">Single hub per connection</span></span>

<span data-ttu-id="9af28-177">W ASP.NET Core SignalR model połączenia został uproszczony.</span><span class="sxs-lookup"><span data-stu-id="9af28-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="9af28-178">Połączenia są nawiązywane bezpośrednio w jednym centrum, a nie za pomocą jednego połączenia, które jest używane do udostępniania dostępu do wielu centrów.</span><span class="sxs-lookup"><span data-stu-id="9af28-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="9af28-179">Przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="9af28-179">Streaming</span></span>

<span data-ttu-id="9af28-180">ASP.NET Core SignalR teraz obsługuje [przesyłanie strumieniowe danych](xref:signalr/streaming) z centrum do klienta programu.</span><span class="sxs-lookup"><span data-stu-id="9af28-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="9af28-181">Stan</span><span class="sxs-lookup"><span data-stu-id="9af28-181">State</span></span>

<span data-ttu-id="9af28-182">Możliwość przekazania dowolnego stanu między klientami a centrum (często wywoływanymi `HubState` ) została usunięta, a także do obsługi komunikatów o postępie.</span><span class="sxs-lookup"><span data-stu-id="9af28-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="9af28-183">W tej chwili nie ma żadnego odpowiednika serwerów proxy centrum.</span><span class="sxs-lookup"><span data-stu-id="9af28-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="9af28-184">Usuwanie PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="9af28-184">PersistentConnection removal</span></span>

<span data-ttu-id="9af28-185">W ASP.NET Core SignalR Klasa [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) została usunięta.</span><span class="sxs-lookup"><span data-stu-id="9af28-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="9af28-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="9af28-186">GlobalHost</span></span>

<span data-ttu-id="9af28-187">ASP.NET Core ma iniekcję zależności (DI) wbudowaną w strukturę.</span><span class="sxs-lookup"><span data-stu-id="9af28-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="9af28-188">Usługi mogą używać DI do uzyskiwania dostępu do [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="9af28-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="9af28-189">`GlobalHost`Obiekt, który jest używany w ASP.NET SignalR do pobrania, `HubContext` nie istnieje w ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="9af28-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="9af28-190">HubPipeline</span></span>

<span data-ttu-id="9af28-191">ASP.NET Core SignalR nie obsługuje `HubPipeline` modułów.</span><span class="sxs-lookup"><span data-stu-id="9af28-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="9af28-192">Różnice dotyczące klienta</span><span class="sxs-lookup"><span data-stu-id="9af28-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="9af28-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="9af28-193">TypeScript</span></span>

<span data-ttu-id="9af28-194">Klient ASP.NET Core SignalR jest zapisywana w języku [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="9af28-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="9af28-195">Podczas korzystania z [klienta JavaScript](xref:signalr/javascript-client)można pisać w języku JavaScript lub TypeScript.</span><span class="sxs-lookup"><span data-stu-id="9af28-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="9af28-196">Klient JavaScript jest hostowany w npm</span><span class="sxs-lookup"><span data-stu-id="9af28-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9af28-197">W wersjach ASP.NET klient JavaScript został uzyskany za pomocą pakietu NuGet w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9af28-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="9af28-198">W wersjach ASP.NET Core [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pakiet npm zawiera biblioteki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9af28-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="9af28-199">Ten pakiet nie jest uwzględniony w szablonie **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="9af28-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="9af28-200">Użyj npm, aby uzyskać i zainstalować `@microsoft/signalr` pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="9af28-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9af28-201">W wersjach ASP.NET klient JavaScript został uzyskany za pomocą pakietu NuGet w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9af28-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="9af28-202">W wersjach ASP.NET Core [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pakiet npm zawiera biblioteki JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9af28-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="9af28-203">Ten pakiet nie jest uwzględniony w szablonie **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="9af28-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="9af28-204">Użyj npm, aby uzyskać i zainstalować `@aspnet/signalr` pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="9af28-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="9af28-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="9af28-205">jQuery</span></span>

<span data-ttu-id="9af28-206">Zależność od jQuery została usunięta, jednak projekty nadal mogą korzystać z jQuery.</span><span class="sxs-lookup"><span data-stu-id="9af28-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="9af28-207">Obsługa programu Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="9af28-207">Internet Explorer support</span></span>

<span data-ttu-id="9af28-208">ASP.NET Core SignalR obsługuje program Microsoft Internet Explorer 11 lub nowszy, natomiast ASP.NET SignalR obsługuje program Microsoft Internet Explorer 8 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="9af28-208">ASP.NET Core SignalR supports Microsoft Internet Explorer 11 or later, whereas ASP.NET SignalR supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="9af28-209">Więcej informacji na temat obsługi przeglądarki można znaleźć na [obsługiwanych platformach](xref:signalr/supported-platforms#javascript-client).</span><span class="sxs-lookup"><span data-stu-id="9af28-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="9af28-210">Składnia metody klienta JavaScript</span><span class="sxs-lookup"><span data-stu-id="9af28-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9af28-211">Składnia języka JavaScript została zmieniona z wersji ASP.NET systemu SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-211">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="9af28-212">Zamiast używać `$connection` obiektu, Utwórz połączenie przy użyciu interfejsu API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="9af28-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="9af28-213">Użyj metody [on](/javascript/api/@microsoft/signalr/HubConnection#on) , aby określić metody klienta, które mogą być wywoływane przez centrum.</span><span class="sxs-lookup"><span data-stu-id="9af28-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9af28-214">Składnia języka JavaScript została zmieniona z wersji ASP.NET systemu SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-214">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="9af28-215">Zamiast używać `$connection` obiektu, Utwórz połączenie przy użyciu interfejsu API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="9af28-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="9af28-216">Użyj metody [on](/javascript/api/@aspnet/signalr/HubConnection#on) , aby określić metody klienta, które mogą być wywoływane przez centrum.</span><span class="sxs-lookup"><span data-stu-id="9af28-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="9af28-217">Po utworzeniu metody klienta należy uruchomić połączenie z centrum.</span><span class="sxs-lookup"><span data-stu-id="9af28-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="9af28-218">Tworzenie łańcucha metod [przechwytywania](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) w celu rejestrowania lub obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="9af28-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="9af28-219">Serwery proxy centrum</span><span class="sxs-lookup"><span data-stu-id="9af28-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9af28-220">Serwery proxy centrum nie są już generowane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="9af28-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="9af28-221">Zamiast tego nazwa metody jest przenoszona do [wywoływania](/javascript/api/@microsoft/signalr/hubconnection#invoke) API jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="9af28-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="9af28-222">Serwery proxy centrum nie są już generowane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="9af28-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="9af28-223">Zamiast tego nazwa metody jest przenoszona do [wywoływania](/javascript/api/@aspnet/signalr/hubconnection#invoke) API jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="9af28-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="9af28-224">.NET i inni klienci</span><span class="sxs-lookup"><span data-stu-id="9af28-224">.NET and other clients</span></span>

<span data-ttu-id="9af28-225">[Microsoft. AspNetCore. SignalR .. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Pakiet NuGet klienta zawiera biblioteki klienckie platformy .NET dla ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="9af28-225">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="9af28-226">Użyj, <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> Aby utworzyć i skompilować wystąpienie połączenia z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="9af28-226">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="9af28-227">Różnice skalowania</span><span class="sxs-lookup"><span data-stu-id="9af28-227">Scaleout differences</span></span>

<span data-ttu-id="9af28-228">ASP.NET SignalR obsługuje SQL Server i Redis.</span><span class="sxs-lookup"><span data-stu-id="9af28-228">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="9af28-229">ASP.NET Core SignalR obsługuje usługi platformy Azure SignalR i Redis.</span><span class="sxs-lookup"><span data-stu-id="9af28-229">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="9af28-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="9af28-230">ASP.NET</span></span>

* [<span data-ttu-id="9af28-231">SignalR skalowania z Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="9af28-231">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="9af28-232">SignalR skalowania z Redis</span><span class="sxs-lookup"><span data-stu-id="9af28-232">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="9af28-233">SignalR skalowania z SQL Server</span><span class="sxs-lookup"><span data-stu-id="9af28-233">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="9af28-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9af28-234">ASP.NET Core</span></span>

* [<span data-ttu-id="9af28-235">Usługa platformy Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="9af28-235">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="9af28-236">Redis</span><span class="sxs-lookup"><span data-stu-id="9af28-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="9af28-237">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9af28-237">Additional resources</span></span>

* [<span data-ttu-id="9af28-238">Centra</span><span class="sxs-lookup"><span data-stu-id="9af28-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="9af28-239">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="9af28-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="9af28-240">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="9af28-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9af28-241">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="9af28-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
