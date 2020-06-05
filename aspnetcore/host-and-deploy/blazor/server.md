---
title: Hostowanie i wdrażanie Blazor serwera ASP.NET Core
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację serwera przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 8c06d3a4d0d75a3e2fd9f699af38a23833fa8bce
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419947"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="62c1b-103">Hostowanie i wdrażanie Blazor serwera</span><span class="sxs-lookup"><span data-stu-id="62c1b-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="62c1b-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="62c1b-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="62c1b-105">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="62c1b-105">Host configuration values</span></span>

<span data-ttu-id="62c1b-106">[ Blazor Aplikacje serwera](xref:blazor/hosting-models#blazor-server) mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="62c1b-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="62c1b-107">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="62c1b-107">Deployment</span></span>

<span data-ttu-id="62c1b-108">Przy użyciu [ Blazor modelu hostingu serwera](xref:blazor/hosting-models#blazor-server)program Blazor jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62c1b-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="62c1b-109">Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.</span><span class="sxs-lookup"><span data-stu-id="62c1b-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="62c1b-110">Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62c1b-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="62c1b-111">Program Visual Studio zawiera szablon projektu \*\* Blazor aplikacji serwera\*\* (szablon, który jest `blazorserverside` używany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="62c1b-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="62c1b-112">Skalowalność</span><span class="sxs-lookup"><span data-stu-id="62c1b-112">Scalability</span></span>

<span data-ttu-id="62c1b-113">Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla Blazor aplikacji serwerowej.</span><span class="sxs-lookup"><span data-stu-id="62c1b-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="62c1b-114">Zobacz następujące zasoby, aby rozwiązać Blazor skalowalność aplikacji serwera:</span><span class="sxs-lookup"><span data-stu-id="62c1b-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="62c1b-115">[Podstawowe Blazor aplikacje serwera](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="62c1b-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="62c1b-116">Serwer wdrażania</span><span class="sxs-lookup"><span data-stu-id="62c1b-116">Deployment server</span></span>

<span data-ttu-id="62c1b-117">Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="62c1b-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="62c1b-118">Dostępna pamięć na serwerze ma wpływ na:</span><span class="sxs-lookup"><span data-stu-id="62c1b-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="62c1b-119">Liczba aktywnych obwodów obsługiwanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="62c1b-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="62c1b-120">Opóźnienie interfejsu użytkownika na kliencie.</span><span class="sxs-lookup"><span data-stu-id="62c1b-120">UI latency on the client.</span></span>

<span data-ttu-id="62c1b-121">Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i skalowalnych Blazor aplikacji serwerowych, zobacz <xref:security/blazor/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="62c1b-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="62c1b-122">Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*.</span><span class="sxs-lookup"><span data-stu-id="62c1b-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="62c1b-123">Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="62c1b-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="62c1b-124">Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 współbieżnych użytkowników, należy rozważyć budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).</span><span class="sxs-lookup"><span data-stu-id="62c1b-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="62c1b-125">skonfigurować</span><span class="sxs-lookup"><span data-stu-id="62c1b-125"> configuration</span></span>

Blazor<span data-ttu-id="62c1b-126">Aplikacje serwera używają ASP.NET Core SignalR do komunikowania się z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="62c1b-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="62c1b-127">[ SignalR warunki hostingu i skalowania](xref:signalr/publish-to-azure-web-app) dotyczą Blazor Aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="62c1b-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="62c1b-128">najlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako SignalR transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="62c1b-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="62c1b-129">Długie sondowanie jest używane przez, SignalR gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długotrwałego sondowania.</span><span class="sxs-lookup"><span data-stu-id="62c1b-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="62c1b-130">Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi.</span><span class="sxs-lookup"><span data-stu-id="62c1b-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="62c1b-131">Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="62c1b-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="62c1b-132">Usługa platformy Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="62c1b-132">Azure SignalR Service</span></span>

<span data-ttu-id="62c1b-133">Zalecamy korzystanie z [ SignalR usługi platformy Azure](/azure/azure-signalr) dla Blazor aplikacji serwerowych.</span><span class="sxs-lookup"><span data-stu-id="62c1b-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="62c1b-134">Usługa umożliwia skalowanie Blazor aplikacji serwera do dużej liczby jednoczesnych SignalR połączeń.</span><span class="sxs-lookup"><span data-stu-id="62c1b-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="62c1b-135">Ponadto SignalR globalne zasięgi i wysokiej wydajności centra danych usługi znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.</span><span class="sxs-lookup"><span data-stu-id="62c1b-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="62c1b-136">Aby skonfigurować aplikację (i opcjonalnie zainicjować obsługę administracyjną) SignalR usługi platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="62c1b-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="62c1b-137">Włącz obsługę *sesji usługi Sticky Notes*, w przypadku których klienci są [przekierowywani z powrotem do tego samego serwera podczas renderowania](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="62c1b-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="62c1b-138">Ustaw `ServerStickyMode` opcję lub wartość konfiguracji na `Required` .</span><span class="sxs-lookup"><span data-stu-id="62c1b-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="62c1b-139">Zazwyczaj aplikacja tworzy konfigurację przy użyciu **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="62c1b-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="62c1b-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="62c1b-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="62c1b-141">Konfiguracja (Użyj **jednej** z następujących metod):</span><span class="sxs-lookup"><span data-stu-id="62c1b-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="62c1b-142">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="62c1b-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="62c1b-143">**Configuration**  >  **Ustawienia aplikacji** konfiguracji usługi App Service w Azure Portal (**Nazwa**: `Azure:SignalR:ServerStickyMode` , **wartość**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="62c1b-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="62c1b-144">Utwórz profil publikowania aplikacji platformy Azure w programie Visual Studio dla Blazor aplikacji serwerowej.</span><span class="sxs-lookup"><span data-stu-id="62c1b-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="62c1b-145">Dodaj zależność \*\* SignalR usługi platformy Azure\*\* do profilu.</span><span class="sxs-lookup"><span data-stu-id="62c1b-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="62c1b-146">Jeśli subskrypcja platformy Azure nie ma istniejącego wystąpienia usługi platformy Azure SignalR , które ma zostać przypisane do aplikacji, wybierz opcję **Utwórz nowe SignalR wystąpienie usługi platformy Azure** , aby udostępnić nowe wystąpienie usługi.</span><span class="sxs-lookup"><span data-stu-id="62c1b-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="62c1b-147">Publikowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="62c1b-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="62c1b-148">IIS</span><span class="sxs-lookup"><span data-stu-id="62c1b-148">IIS</span></span>

<span data-ttu-id="62c1b-149">W przypadku korzystania z usług IIS Włącz:</span><span class="sxs-lookup"><span data-stu-id="62c1b-149">When using IIS, enable:</span></span>

* <span data-ttu-id="62c1b-150">Obiekty [WebSockets w usługach IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="62c1b-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="62c1b-151">[Sesje programu Sticky w ramach routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="62c1b-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="62c1b-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="62c1b-152">Kubernetes</span></span>

<span data-ttu-id="62c1b-153">Utwórz definicję transferu danych przychodzących z następującymi [adnotacjami Kubernetes dla sesji programu Sticky Notes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="62c1b-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="62c1b-154">System Linux z serwerem Nginx</span><span class="sxs-lookup"><span data-stu-id="62c1b-154">Linux with Nginx</span></span>

<span data-ttu-id="62c1b-155">Aby SignalR elementy WebSockets działały prawidłowo, upewnij się, że na serwerze proxy `Upgrade` i w `Connection` nagłówkach zostały ustawione następujące wartości, które `$connection_upgrade` są mapowane na:</span><span class="sxs-lookup"><span data-stu-id="62c1b-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="62c1b-156">Domyślnie wartość nagłówka uaktualnienia.</span><span class="sxs-lookup"><span data-stu-id="62c1b-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="62c1b-157">`close`gdy brakuje nagłówka uaktualnienia lub jest on pusty.</span><span class="sxs-lookup"><span data-stu-id="62c1b-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="62c1b-158">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="62c1b-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="62c1b-159">NGINX jako serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="62c1b-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="62c1b-160">Serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="62c1b-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="62c1b-161">System Linux z serwerem Apache</span><span class="sxs-lookup"><span data-stu-id="62c1b-161">Linux with Apache</span></span>

<span data-ttu-id="62c1b-162">Aby hostować Blazor aplikację za Apache w systemie Linux, skonfiguruj `ProxyPass` dla ruchu HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="62c1b-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="62c1b-163">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="62c1b-163">In the following example:</span></span>

* <span data-ttu-id="62c1b-164">Serwer Kestrel jest uruchomiony na komputerze-hoście.</span><span class="sxs-lookup"><span data-stu-id="62c1b-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="62c1b-165">Aplikacja nasłuchuje ruchu na porcie 5000.</span><span class="sxs-lookup"><span data-stu-id="62c1b-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="62c1b-166">Włącz następujące moduły:</span><span class="sxs-lookup"><span data-stu-id="62c1b-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="62c1b-167">Sprawdź, czy w konsoli przeglądarki występują błędy usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="62c1b-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="62c1b-168">Przykładowe błędy:</span><span class="sxs-lookup"><span data-stu-id="62c1b-168">Example errors:</span></span>

* <span data-ttu-id="62c1b-169">Firefox nie może nawiązać połączenia z serwerem w ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="62c1b-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="62c1b-170">Błąd: nie można uruchomić transportu "WebSockets": błąd: Wystąpił błąd podczas transportu.</span><span class="sxs-lookup"><span data-stu-id="62c1b-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="62c1b-171">Błąd: nie można uruchomić transportu "LongPolling": TypeError: this. transport jest niezdefiniowany</span><span class="sxs-lookup"><span data-stu-id="62c1b-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="62c1b-172">Błąd: nie można nawiązać połączenia z serwerem za pomocą dowolnych dostępnych transportów.</span><span class="sxs-lookup"><span data-stu-id="62c1b-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="62c1b-173">Niepowodzenie obiektów WebSockets</span><span class="sxs-lookup"><span data-stu-id="62c1b-173">WebSockets failed</span></span>
* <span data-ttu-id="62c1b-174">Błąd: nie można wysłać danych, jeśli połączenie nie jest w stanie "podłączone".</span><span class="sxs-lookup"><span data-stu-id="62c1b-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="62c1b-175">Aby uzyskać więcej informacji, zobacz [dokumentację platformy Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="62c1b-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="62c1b-176">Mierzenie opóźnienia sieci</span><span class="sxs-lookup"><span data-stu-id="62c1b-176">Measure network latency</span></span>

<span data-ttu-id="62c1b-177">Przy użyciu kodu [js Interop](xref:blazor/call-javascript-from-dotnet) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="62c1b-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="62c1b-178">W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.</span><span class="sxs-lookup"><span data-stu-id="62c1b-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
