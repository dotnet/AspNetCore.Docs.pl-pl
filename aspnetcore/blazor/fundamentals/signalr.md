---
title: Blazor SignalR Wskazówki dotyczące ASP.NET Core
author: guardrex
description: Dowiedz się, jak konfigurować połączenia i zarządzać nimi Blazor SignalR .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/signalr
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 63dfd93fbc42a869211bc5cd481a8dbee6eb6c91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118918"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="939c9-103">Blazor SignalR Wskazówki dotyczące ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="939c9-103">ASP.NET Core Blazor SignalR guidance</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="939c9-104">W tym artykule wyjaśniono, jak konfigurować SignalR połączenia w aplikacjach i zarządzać nimi Blazor .</span><span class="sxs-lookup"><span data-stu-id="939c9-104">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="939c9-105">Ogólne wskazówki dotyczące konfiguracji ASP.NET Core SignalR można znaleźć w tematach w <xref:signalr/introduction> obszarze dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="939c9-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="939c9-106">Aby skonfigurować SignalR [dodanie do Blazor WebAssembly rozwiązania hostowanego](xref:tutorials/signalr-blazor), zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="939c9-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="939c9-107">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="939c9-107">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="939c9-108">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak cookie nagłówki uwierzytelniania s lub http:</span><span class="sxs-lookup"><span data-stu-id="939c9-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="939c9-109">Służy <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawiania <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> żądań między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) .</span><span class="sxs-lookup"><span data-stu-id="939c9-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests.</span></span>

  <span data-ttu-id="939c9-110">`IncludeRequestCredentialsMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="939c9-110">`IncludeRequestCredentialsMessageHandler.cs`:</span></span>

  ```csharp
  using System.Net.Http;
  using System.Threading;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Components.WebAssembly.Http;

  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="939c9-111">Jeśli utworzono połączenie z centrum, przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="939c9-111">Where a hub connection is built, assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  HubConnectionBuilder hubConnecton;

  ...

  hubConnecton = new HubConnectionBuilder()
      .WithUrl(new Uri(NavigationManager.ToAbsoluteUri("/chathub")), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

  <span data-ttu-id="939c9-112">Poprzedni przykład konfiguruje adres URL połączenia centrum do bezwzględnego adresu URI w `/chathub` , który jest adresem URL używanym w [ SignalR Blazor samouczku with](xref:tutorials/signalr-blazor) w `Index` składniku ( `Pages/Index.razor` ).</span><span class="sxs-lookup"><span data-stu-id="939c9-112">The preceding example configures the hub connection URL to the absolute URI address at `/chathub`, which is the URL used in the [SignalR with Blazor tutorial](xref:tutorials/signalr-blazor) in the `Index` component (`Pages/Index.razor`).</span></span> <span data-ttu-id="939c9-113">Identyfikator URI można również ustawić za pośrednictwem ciągu, na przykład `https://signalr.example.com` , lub za pomocą [konfiguracji](xref:blazor/fundamentals/configuration).</span><span class="sxs-lookup"><span data-stu-id="939c9-113">The URI can also be set via a string, for example `https://signalr.example.com`, or via [configuration](xref:blazor/fundamentals/configuration).</span></span>

<span data-ttu-id="939c9-114">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="939c9-114">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a><span data-ttu-id="939c9-115">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="939c9-115">Render mode</span></span>

<span data-ttu-id="939c9-116">Jeśli Blazor WebAssembly aplikacja, która korzysta z programu SignalR jest skonfigurowana do PreRender na serwerze, renderowanie wstępne występuje przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="939c9-116">If a Blazor WebAssembly app that uses SignalR is configured to prerender on the server, prerendering occurs before the client connection to the server is established.</span></span> <span data-ttu-id="939c9-117">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="939c9-117">For more information, see the following articles:</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="939c9-118">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="939c9-118">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="939c9-119">W tym artykule wyjaśniono, jak konfigurować SignalR połączenia w aplikacjach i zarządzać nimi Blazor .</span><span class="sxs-lookup"><span data-stu-id="939c9-119">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="939c9-120">Ogólne wskazówki dotyczące konfiguracji ASP.NET Core SignalR można znaleźć w tematach w <xref:signalr/introduction> obszarze dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="939c9-120">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="939c9-121">Aby skonfigurować SignalR [dodanie do Blazor WebAssembly rozwiązania hostowanego](xref:tutorials/signalr-blazor), zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="939c9-121">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="939c9-122">Opcje procedury obsługi obwodu</span><span class="sxs-lookup"><span data-stu-id="939c9-122">Circuit handler options</span></span>

<span data-ttu-id="939c9-123">Skonfiguruj Blazor Server obwód z <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> pokazanymi w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="939c9-123">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="939c9-124">Opcja</span><span class="sxs-lookup"><span data-stu-id="939c9-124">Option</span></span> | <span data-ttu-id="939c9-125">Domyślne</span><span class="sxs-lookup"><span data-stu-id="939c9-125">Default</span></span> | <span data-ttu-id="939c9-126">Opis</span><span class="sxs-lookup"><span data-stu-id="939c9-126">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="939c9-127">Wysyłaj szczegółowe komunikaty o wyjątkach do języka JavaScript, gdy wystąpił nieobsługiwany wyjątek w obwodzie lub gdy metoda .NET nie wywoływana za pomocą kodu JS Interop powoduje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="939c9-127">Send detailed exception messages to JavaScript when an unhandled exception occurs on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="939c9-128">100</span><span class="sxs-lookup"><span data-stu-id="939c9-128">100</span></span> | <span data-ttu-id="939c9-129">Maksymalna liczba odłączonych obwodów, które serwer przechowuje w pamięci w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="939c9-129">Maximum number of disconnected circuits that the server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="939c9-130">3 minuty</span><span class="sxs-lookup"><span data-stu-id="939c9-130">3 minutes</span></span> | <span data-ttu-id="939c9-131">Maksymalny czas przechowywania połączonego obwodu w pamięci przed jego usunięciem.</span><span class="sxs-lookup"><span data-stu-id="939c9-131">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="939c9-132">1 minuta</span><span class="sxs-lookup"><span data-stu-id="939c9-132">1 minute</span></span> | <span data-ttu-id="939c9-133">Maksymalny czas oczekiwania serwera przed upływem limitu czasu asynchronicznego wywołania funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="939c9-133">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="939c9-134">10</span><span class="sxs-lookup"><span data-stu-id="939c9-134">10</span></span> | <span data-ttu-id="939c9-135">Maksymalna liczba niepotwierdzonych partii renderowania, które serwer przechowuje w pamięci na obwód w danym momencie do obsługi niezawodnego ponownego łączenia.</span><span class="sxs-lookup"><span data-stu-id="939c9-135">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="939c9-136">Po osiągnięciu limitu serwer przestaje tworzyć nowe partie renderowania do momentu potwierdzenia, że co najmniej jedna partia zostanie potwierdzona przez klienta.</span><span class="sxs-lookup"><span data-stu-id="939c9-136">After reaching the limit, the server stops producing new render batches until one or more batches are acknowledged by the client.</span></span> |

<span data-ttu-id="939c9-137">Skonfiguruj opcje w programie `Startup.ConfigureServices` przy użyciu opcji delegat <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="939c9-137">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="939c9-138">Poniższy przykład przypisuje domyślne wartości opcji pokazanych w powyższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="939c9-138">The following example assigns the default option values shown in the preceding table.</span></span> <span data-ttu-id="939c9-139">Upewnij się, że `Startup.cs` używa <xref:System> przestrzeni nazw ( `using System;` ).</span><span class="sxs-lookup"><span data-stu-id="939c9-139">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="939c9-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="939c9-140">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="939c9-141">Aby skonfigurować <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , użyj <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> z <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="939c9-141">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="939c9-142">Aby uzyskać opisy opcji, zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="939c9-142">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="939c9-143">Poniższy przykład przypisuje domyślne wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="939c9-143">The following example assigns the default option values.</span></span> <span data-ttu-id="939c9-144">Upewnij się, że `Startup.cs` używa <xref:System> przestrzeni nazw ( `using System;` ).</span><span class="sxs-lookup"><span data-stu-id="939c9-144">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="939c9-145">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="939c9-145">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="939c9-146">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="939c9-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="939c9-147">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="939c9-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="939c9-148">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="939c9-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="939c9-149">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie.</span><span class="sxs-lookup"><span data-stu-id="939c9-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page.</span></span>

<span data-ttu-id="939c9-150">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-150">`Pages/_Host.cshtml`:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="939c9-151">Dodaj następujące style CSS do arkusza stylów witryny.</span><span class="sxs-lookup"><span data-stu-id="939c9-151">Add the following CSS styles to the site's stylesheet.</span></span>

<span data-ttu-id="939c9-152">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="939c9-152">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="939c9-153">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu przez Blazor platformę.</span><span class="sxs-lookup"><span data-stu-id="939c9-153">The following table describes the CSS classes applied to the `components-reconnect-modal` element by the Blazor framework.</span></span>

| <span data-ttu-id="939c9-154">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="939c9-154">CSS class</span></span>                       | <span data-ttu-id="939c9-155">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="939c9-155">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="939c9-156">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="939c9-156">A lost connection.</span></span> <span data-ttu-id="939c9-157">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="939c9-157">The client is attempting to reconnect.</span></span> <span data-ttu-id="939c9-158">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="939c9-158">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="939c9-159">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="939c9-159">An active connection is re-established to the server.</span></span> <span data-ttu-id="939c9-160">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="939c9-160">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="939c9-161">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="939c9-161">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="939c9-162">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` JavaScript.</span><span class="sxs-lookup"><span data-stu-id="939c9-162">To attempt reconnection, call `window.Blazor.reconnect()` in JavaScript.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="939c9-163">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="939c9-163">Reconnection rejected.</span></span> <span data-ttu-id="939c9-164">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="939c9-164">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="939c9-165">Aby ponownie załadować aplikację, zadzwoń do `location.reload()` języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="939c9-165">To reload the app, call `location.reload()` in JavaScript.</span></span> <span data-ttu-id="939c9-166">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="939c9-166">This connection state may result when:</span></span><ul><li><span data-ttu-id="939c9-167">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="939c9-167">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="939c9-168">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="939c9-168">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="939c9-169">Wystąpienia składników użytkownika są usuwane.</span><span class="sxs-lookup"><span data-stu-id="939c9-169">Instances of the user's components are disposed.</span></span></li><li><span data-ttu-id="939c9-170">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="939c9-170">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="939c9-171">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="939c9-171">Render mode</span></span>

<span data-ttu-id="939c9-172">Domyślnie aplikacje uruchamiają Blazor Server interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="939c9-172">By default, Blazor Server apps prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="939c9-173">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="939c9-173">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="939c9-174">Inicjowanie Blazor obwodu</span><span class="sxs-lookup"><span data-stu-id="939c9-174">Initialize the Blazor circuit</span></span>

<span data-ttu-id="939c9-175">Skonfiguruj ręczne uruchamianie Blazor Server [ SignalR obwodu](xref:blazor/hosting-models#circuits) aplikacji w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="939c9-175">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="939c9-176">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="939c9-176">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="939c9-177">Umieść skrypt wywołujący `Blazor.start` po `blazor.server.js` tagu skryptu i wewnątrz taga zamykającego `</body>` .</span><span class="sxs-lookup"><span data-stu-id="939c9-177">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="939c9-178">Gdy `autostart` jest wyłączone, dowolny aspekt aplikacji, która nie zależy od obwodu, zwykle działa.</span><span class="sxs-lookup"><span data-stu-id="939c9-178">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="939c9-179">Na przykład Routing po stronie klienta działa.</span><span class="sxs-lookup"><span data-stu-id="939c9-179">For example, client-side routing is operational.</span></span> <span data-ttu-id="939c9-180">Jednak każdy aspekt, który zależy od obwodu, nie działa do momentu `Blazor.start` wywołania.</span><span class="sxs-lookup"><span data-stu-id="939c9-180">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="939c9-181">Zachowanie aplikacji jest nieprzewidywalne bez ustalonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="939c9-181">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="939c9-182">Na przykład metody komponentów nie są wykonywane podczas odłączania obwodu.</span><span class="sxs-lookup"><span data-stu-id="939c9-182">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="939c9-183">Inicjuj Blazor , gdy dokument jest gotowy</span><span class="sxs-lookup"><span data-stu-id="939c9-183">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="939c9-184">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-184">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="939c9-185">Łańcuch z `Promise` wynikami ręcznego uruchamiania</span><span class="sxs-lookup"><span data-stu-id="939c9-185">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="939c9-186">Aby wykonać dodatkowe zadania, takie jak Inicjalizacja międzyoperacyjna JS, użyj [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) do łańcucha z [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) wynikami ręcznego Blazor uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="939c9-186">To perform additional tasks, such as JS interop initialization, use [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) to chain to the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) that results from a manual Blazor app start.</span></span>

<span data-ttu-id="939c9-187">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-187">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-signalr-client-logging"></a><span data-ttu-id="939c9-188">Konfigurowanie SignalR rejestrowania klientów</span><span class="sxs-lookup"><span data-stu-id="939c9-188">Configure SignalR client logging</span></span>

<span data-ttu-id="939c9-189">W konstruktorze klienta Przekaż `configureSignalR` obiekt konfiguracji, który wywołuje `configureLogging` z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="939c9-189">On the client builder, pass in the `configureSignalR` configuration object that calls `configureLogging` with the log level.</span></span>

<span data-ttu-id="939c9-190">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-190">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="939c9-191">W poprzednim przykładzie `information` jest to odpowiednik poziomu dziennika <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="939c9-191">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="939c9-192">Modyfikowanie procedury obsługi ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="939c9-192">Modify the reconnection handler</span></span>

<span data-ttu-id="939c9-193">Zdarzenia połączenia obwodu procedury obsługi ponownego połączenia można modyfikować dla zachowań niestandardowych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="939c9-193">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="939c9-194">Powiadomienie użytkownika, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="939c9-194">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="939c9-195">W celu przeprowadzenia rejestrowania (od klienta), gdy obwód jest połączony.</span><span class="sxs-lookup"><span data-stu-id="939c9-195">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="939c9-196">Aby zmodyfikować zdarzenia połączenia, zarejestruj wywołania zwrotne dla następujących zmian połączeń:</span><span class="sxs-lookup"><span data-stu-id="939c9-196">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="939c9-197">Porzucone użycie połączeń `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="939c9-197">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="939c9-198">Nawiązane/ponownie nawiązane połączenia `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="939c9-198">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="939c9-199">**`onConnectionDown`Należy określić oba elementy i `onConnectionUp` .**</span><span class="sxs-lookup"><span data-stu-id="939c9-199">**Both `onConnectionDown` and `onConnectionUp` must be specified.**</span></span>

<span data-ttu-id="939c9-200">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-200">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="939c9-201">Dostosuj liczbę ponownych prób ponownego połączenia i interwał</span><span class="sxs-lookup"><span data-stu-id="939c9-201">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="939c9-202">Aby dostosować liczbę ponownych prób ponownego połączenia i interwał, ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próbnej próby ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="939c9-202">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

<span data-ttu-id="939c9-203">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-203">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="939c9-204">Ukryj lub Zastąp ekran ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="939c9-204">Hide or replace the reconnection display</span></span>

<span data-ttu-id="939c9-205">Aby ukryć ekran ponowne połączenie, ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="939c9-205">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

<span data-ttu-id="939c9-206">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="939c9-206">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="939c9-207">Aby zastąpić ekran ponownego połączenia, ustaw `_reconnectionDisplay` w poprzednim przykładzie element do wyświetlenia:</span><span class="sxs-lookup"><span data-stu-id="939c9-207">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="939c9-208">Symbol zastępczy `{ELEMENT ID}` jest identyfikatorem elementu HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="939c9-208">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="939c9-209">Dostosuj opóźnienie przed wyświetleniem ekranu ponownego połączenia przez ustawienie `transition-delay` właściwości w CSS witryny dla elementu modalnego.</span><span class="sxs-lookup"><span data-stu-id="939c9-209">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the site's CSS for the modal element.</span></span> <span data-ttu-id="939c9-210">W poniższym przykładzie ustawiono opóźnienie przejścia z 500 ms (domyślnie) do 1 000 MS (1 sekunda).</span><span class="sxs-lookup"><span data-stu-id="939c9-210">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second).</span></span>

<span data-ttu-id="939c9-211">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="939c9-211">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="939c9-212">Odłączanie Blazor obwodu od klienta</span><span class="sxs-lookup"><span data-stu-id="939c9-212">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="939c9-213">Domyślnie Blazor obwód jest odłączony, gdy wyzwalane jest [ `unload` zdarzenie strony](https://developer.mozilla.org/docs/Web/API/Window/unload_event) .</span><span class="sxs-lookup"><span data-stu-id="939c9-213">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="939c9-214">Aby odłączyć obwód dla innych scenariuszy na kliencie, wywołaj `Blazor.disconnect` w odpowiedniej procedurze obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="939c9-214">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="939c9-215">W poniższym przykładzie obwód jest odłączony, gdy strona jest ukryta ([ `pagehide` zdarzenie](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="939c9-215">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="939c9-216">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="939c9-216">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="939c9-217">Blazor Server zdarzenia ponownego połączenia i zdarzenia cyklu życia składnika</span><span class="sxs-lookup"><span data-stu-id="939c9-217">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
