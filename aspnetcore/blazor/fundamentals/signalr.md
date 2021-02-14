---
title: Blazor SignalR Wskazówki dotyczące ASP.NET Core
author: guardrex
description: Dowiedz się, jak konfigurować połączenia i zarządzać nimi Blazor SignalR .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279855"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="910de-103">Blazor SignalR Wskazówki dotyczące ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="910de-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="910de-104">Ogólne wskazówki dotyczące konfiguracji ASP.NET Core SignalR można znaleźć w tematach w <xref:signalr/introduction> obszarze dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="910de-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="910de-105">Aby skonfigurować SignalR [dodanie do Blazor WebAssembly rozwiązania hostowanego](xref:tutorials/signalr-blazor), zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="910de-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="910de-106">Opcje procedury obsługi obwodu</span><span class="sxs-lookup"><span data-stu-id="910de-106">Circuit handler options</span></span>

<span data-ttu-id="910de-107">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="910de-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="910de-108">Skonfiguruj Blazor Server obwód z <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> pokazanymi w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="910de-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="910de-109">Opcja</span><span class="sxs-lookup"><span data-stu-id="910de-109">Option</span></span> | <span data-ttu-id="910de-110">Domyślne</span><span class="sxs-lookup"><span data-stu-id="910de-110">Default</span></span> | <span data-ttu-id="910de-111">Opis</span><span class="sxs-lookup"><span data-stu-id="910de-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="910de-112">Wysyłaj szczegółowe komunikaty o wyjątkach do języka JavaScript, gdy wystąpił nieobsługiwany wyjątek w obwodzie lub kiedy wywołanie metody .NET za pomocą kodu JS Interop spowoduje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="910de-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="910de-113">100</span><span class="sxs-lookup"><span data-stu-id="910de-113">100</span></span> | <span data-ttu-id="910de-114">Maksymalna liczba odłączonych obwodów, które dany serwer przechowuje w pamięci w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="910de-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="910de-115">3 minuty</span><span class="sxs-lookup"><span data-stu-id="910de-115">3 minutes</span></span> | <span data-ttu-id="910de-116">Maksymalny czas przechowywania połączonego obwodu w pamięci przed jego usunięciem.</span><span class="sxs-lookup"><span data-stu-id="910de-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="910de-117">1 minuta</span><span class="sxs-lookup"><span data-stu-id="910de-117">1 minute</span></span> | <span data-ttu-id="910de-118">Maksymalny czas oczekiwania serwera przed upływem limitu czasu asynchronicznego wywołania funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="910de-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="910de-119">10</span><span class="sxs-lookup"><span data-stu-id="910de-119">10</span></span> | <span data-ttu-id="910de-120">Maksymalna liczba niepotwierdzonych partii renderowania, które serwer przechowuje w pamięci na obwód w danym momencie do obsługi niezawodnego ponownego łączenia.</span><span class="sxs-lookup"><span data-stu-id="910de-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="910de-121">Po osiągnięciu limitu serwer przestaje tworzyć nowe partie renderowania do momentu potwierdzenia co najmniej jednej partii przez klienta.</span><span class="sxs-lookup"><span data-stu-id="910de-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="910de-122">Skonfiguruj opcje w programie `Startup.ConfigureServices` przy użyciu opcji delegat <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> .</span><span class="sxs-lookup"><span data-stu-id="910de-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="910de-123">Poniższy przykład przypisuje domyślne wartości opcji pokazanych w powyższej tabeli:</span><span class="sxs-lookup"><span data-stu-id="910de-123">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="910de-124">Aby skonfigurować <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> , użyj <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> z <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> .</span><span class="sxs-lookup"><span data-stu-id="910de-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="910de-125">Aby uzyskać opisy opcji, zobacz <xref:signalr/configuration#configure-server-options> .</span><span class="sxs-lookup"><span data-stu-id="910de-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="910de-126">Poniższy przykład przypisuje domyślne wartości opcji:</span><span class="sxs-lookup"><span data-stu-id="910de-126">The following example assigns the default option values:</span></span>

```csharp
using System;

...

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="910de-127">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="910de-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="910de-128">*Ta sekcja ma zastosowanie do Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="910de-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="910de-129">Aby skonfigurować SignalR klienta podstawowego do wysyłania poświadczeń, takich jak cookie nagłówki uwierzytelniania s lub http:</span><span class="sxs-lookup"><span data-stu-id="910de-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="910de-130">Użyj <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> do ustawienia <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> na żądania między źródłami [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="910de-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
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

* <span data-ttu-id="910de-131">Przypisz <xref:System.Net.Http.HttpMessageHandler> do <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opcji:</span><span class="sxs-lookup"><span data-stu-id="910de-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="910de-132">Aby uzyskać więcej informacji, zobacz <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="910de-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="910de-133">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="910de-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="910de-134">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="910de-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="910de-135">Gdy klient wykryje, że połączenie zostało utracone, do użytkownika jest wyświetlany domyślny interfejs użytkownika, podczas gdy klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="910de-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="910de-136">Jeśli ponowne połączenie nie powiedzie się, użytkownik otrzymuje opcję ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="910de-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="910de-137">Aby dostosować interfejs użytkownika, zdefiniuj element z elementu `id` `components-reconnect-modal` na `<body>` `_Host.cshtml` Razor stronie:</span><span class="sxs-lookup"><span data-stu-id="910de-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="910de-138">Dodaj następujący do arkusza stylów aplikacji ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="910de-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="910de-139">W poniższej tabeli opisano klasy CSS stosowane do `components-reconnect-modal` elementu.</span><span class="sxs-lookup"><span data-stu-id="910de-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="910de-140">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="910de-140">CSS class</span></span>                       | <span data-ttu-id="910de-141">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="910de-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="910de-142">Utracono połączenie.</span><span class="sxs-lookup"><span data-stu-id="910de-142">A lost connection.</span></span> <span data-ttu-id="910de-143">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="910de-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="910de-144">Pokaż modalne.</span><span class="sxs-lookup"><span data-stu-id="910de-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="910de-145">Aktywne połączenie zostanie ponownie nawiązane z serwerem.</span><span class="sxs-lookup"><span data-stu-id="910de-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="910de-146">Ukryj modalne.</span><span class="sxs-lookup"><span data-stu-id="910de-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="910de-147">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu błędu sieci.</span><span class="sxs-lookup"><span data-stu-id="910de-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="910de-148">Aby spróbować ponownie nawiązać połączenie, wywołaj polecenie `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="910de-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="910de-149">Odrzucono ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="910de-149">Reconnection rejected.</span></span> <span data-ttu-id="910de-150">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze został utracony.</span><span class="sxs-lookup"><span data-stu-id="910de-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="910de-151">Aby ponownie załadować aplikację, wywołaj polecenie `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="910de-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="910de-152">Ten stan połączenia może skutkować tym, że:</span><span class="sxs-lookup"><span data-stu-id="910de-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="910de-153">Wystąpił awaria w obwodzie po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="910de-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="910de-154">Klient jest odłączony wystarczająco długo, aby serwer mógł porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="910de-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="910de-155">Wystąpienia składników, z którymi łączy się użytkownik, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="910de-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="910de-156">Serwer zostanie uruchomiony ponownie lub proces roboczy aplikacji zostanie odtworzony.</span><span class="sxs-lookup"><span data-stu-id="910de-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="910de-157">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="910de-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="910de-158">*Ta sekcja ma zastosowanie do hostowanych Blazor WebAssembly i Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="910de-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="910de-159">Blazor aplikacje są domyślnie skonfigurowane w taki sposób, aby wyprerender interfejs użytkownika na serwerze.</span><span class="sxs-lookup"><span data-stu-id="910de-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="910de-160">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="910de-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="910de-161">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="910de-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="910de-162">Blazor Server aplikacje są domyślnie skonfigurowane, aby skonfigurować interfejs użytkownika na serwerze przed nawiązaniem połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="910de-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="910de-163">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="910de-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="910de-164">Inicjowanie Blazor obwodu</span><span class="sxs-lookup"><span data-stu-id="910de-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="910de-165">*Ta sekcja ma zastosowanie do Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="910de-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="910de-166">Skonfiguruj ręczne uruchamianie Blazor Server [ SignalR obwodu](xref:blazor/hosting-models#circuits) aplikacji w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="910de-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="910de-167">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="910de-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="910de-168">Umieść skrypt wywołujący `Blazor.start` po `blazor.server.js` tagu skryptu i wewnątrz taga zamykającego `</body>` .</span><span class="sxs-lookup"><span data-stu-id="910de-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="910de-169">Gdy `autostart` jest wyłączone, dowolny aspekt aplikacji, która nie zależy od obwodu, zwykle działa.</span><span class="sxs-lookup"><span data-stu-id="910de-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="910de-170">Na przykład Routing po stronie klienta działa.</span><span class="sxs-lookup"><span data-stu-id="910de-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="910de-171">Jednak każdy aspekt, który zależy od obwodu, nie działa do momentu `Blazor.start` wywołania.</span><span class="sxs-lookup"><span data-stu-id="910de-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="910de-172">Zachowanie aplikacji jest nieprzewidywalne bez ustalonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="910de-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="910de-173">Na przykład metody komponentów nie są wykonywane podczas odłączania obwodu.</span><span class="sxs-lookup"><span data-stu-id="910de-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="910de-174">Inicjuj Blazor , gdy dokument jest gotowy</span><span class="sxs-lookup"><span data-stu-id="910de-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="910de-175">Aby zainicjować Blazor aplikację, gdy dokument jest gotowy:</span><span class="sxs-lookup"><span data-stu-id="910de-175">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="910de-176">Łańcuch z `Promise` wynikami ręcznego uruchamiania</span><span class="sxs-lookup"><span data-stu-id="910de-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="910de-177">Aby wykonać dodatkowe zadania, takie jak Inicjalizacja międzyoperacyjna JS, użyj `then` do łańcucha z `Promise` wynikami ręcznego Blazor uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="910de-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="910de-178">Konfigurowanie SignalR klienta programu</span><span class="sxs-lookup"><span data-stu-id="910de-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="910de-179">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="910de-179">Logging</span></span>

<span data-ttu-id="910de-180">Aby skonfigurować SignalR Rejestrowanie klienta, należy przekazać obiekt konfiguracji ( `configureSignalR` ), który wywołuje `configureLogging` z poziomem rejestrowania w konstruktorze klienta:</span><span class="sxs-lookup"><span data-stu-id="910de-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="910de-181">W poprzednim przykładzie `information` jest to odpowiednik poziomu dziennika <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="910de-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="910de-182">Modyfikowanie procedury obsługi ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="910de-182">Modify the reconnection handler</span></span>

<span data-ttu-id="910de-183">Zdarzenia połączenia obwodu procedury obsługi ponownego połączenia można modyfikować dla zachowań niestandardowych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="910de-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="910de-184">Powiadomienie użytkownika, jeśli połączenie zostało porzucone.</span><span class="sxs-lookup"><span data-stu-id="910de-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="910de-185">W celu przeprowadzenia rejestrowania (od klienta), gdy obwód jest połączony.</span><span class="sxs-lookup"><span data-stu-id="910de-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="910de-186">Aby zmodyfikować zdarzenia połączenia, zarejestruj wywołania zwrotne dla następujących zmian połączeń:</span><span class="sxs-lookup"><span data-stu-id="910de-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="910de-187">Porzucone użycie połączeń `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="910de-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="910de-188">Nawiązane/ponownie nawiązane połączenia `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="910de-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="910de-189">**Oba** `onConnectionDown` elementy i `onConnectionUp` muszą być określone:</span><span class="sxs-lookup"><span data-stu-id="910de-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="910de-190">Dostosuj liczbę ponownych prób ponownego połączenia i interwał</span><span class="sxs-lookup"><span data-stu-id="910de-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="910de-191">Aby dostosować liczbę ponownych prób ponownego połączenia i interwał, ustaw liczbę ponownych prób ( `maxRetries` ) i okres w milisekundach dozwolony dla każdej próbnej próby ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="910de-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="910de-192">Ukryj lub Zastąp ekran ponownego połączenia</span><span class="sxs-lookup"><span data-stu-id="910de-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="910de-193">Aby ukryć ekran ponowne połączenie, ustaw dla programu obsługi ponownego połączenia `_reconnectionDisplay` pusty obiekt ( `{}` lub `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="910de-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="910de-194">Aby zastąpić ekran ponownego połączenia, ustaw `_reconnectionDisplay` w poprzednim przykładzie element do wyświetlenia:</span><span class="sxs-lookup"><span data-stu-id="910de-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="910de-195">Symbol zastępczy `{ELEMENT ID}` jest identyfikatorem elementu HTML do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="910de-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="910de-196">Dostosuj opóźnienie przed wyświetleniem ekranu ponownego połączenia przez ustawienie `transition-delay` właściwości w kodzie CSS aplikacji ( `wwwroot/css/site.css` ) dla elementu modalnego.</span><span class="sxs-lookup"><span data-stu-id="910de-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="910de-197">W poniższym przykładzie ustawiono opóźnienie przejścia z 500 ms (domyślnie) do 1 000 MS (1 sekunda):</span><span class="sxs-lookup"><span data-stu-id="910de-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="910de-198">Odłączanie Blazor obwodu od klienta</span><span class="sxs-lookup"><span data-stu-id="910de-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="910de-199">Domyślnie Blazor obwód jest odłączony, gdy wyzwalane jest [ `unload` zdarzenie strony](https://developer.mozilla.org/docs/Web/API/Window/unload_event) .</span><span class="sxs-lookup"><span data-stu-id="910de-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="910de-200">Aby odłączyć obwód dla innych scenariuszy na kliencie, wywołaj `Blazor.disconnect` w odpowiedniej procedurze obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="910de-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="910de-201">W poniższym przykładzie obwód jest odłączony, gdy strona jest ukryta ([ `pagehide` zdarzenie](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="910de-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="910de-202">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="910de-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="910de-203">Blazor Server zdarzenia ponownego połączenia i zdarzenia cyklu życia składnika</span><span class="sxs-lookup"><span data-stu-id="910de-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
