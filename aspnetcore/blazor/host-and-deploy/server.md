---
title: Hostowanie i wdrażanie ASP.NET Core Blazor Server
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor Server aplikację przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: 75682171a59a610a24364778616774c49257d2ad
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279850"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="43914-103">Hostowanie i wdrażanie Blazor Server</span><span class="sxs-lookup"><span data-stu-id="43914-103">Host and deploy Blazor Server</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="43914-104">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="43914-104">Host configuration values</span></span>

<span data-ttu-id="43914-105">[ Blazor Server aplikacje](xref:blazor/hosting-models#blazor-server) mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="43914-105">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="43914-106">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="43914-106">Deployment</span></span>

<span data-ttu-id="43914-107">Przy użyciu [ Blazor Server modelu hostingu](xref:blazor/hosting-models#blazor-server)program Blazor jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43914-107">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="43914-108">Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.</span><span class="sxs-lookup"><span data-stu-id="43914-108">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="43914-109">Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43914-109">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="43914-110">Program Visual Studio zawiera szablon projektu **Blazor Server aplikacji** ( `blazorserverside` szablon podczas korzystania z [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia).</span><span class="sxs-lookup"><span data-stu-id="43914-110">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="43914-111">Skalowalność</span><span class="sxs-lookup"><span data-stu-id="43914-111">Scalability</span></span>

<span data-ttu-id="43914-112">Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43914-112">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="43914-113">Aby rozwiązać skalowalność aplikacji, zobacz następujące zasoby Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="43914-113">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="43914-114">Podstawowe Blazor Server aplikacje</span><span class="sxs-lookup"><span data-stu-id="43914-114">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="43914-115">Serwer wdrażania</span><span class="sxs-lookup"><span data-stu-id="43914-115">Deployment server</span></span>

<span data-ttu-id="43914-116">Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43914-116">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="43914-117">Dostępna pamięć na serwerze ma wpływ na:</span><span class="sxs-lookup"><span data-stu-id="43914-117">The available memory on the server affects the:</span></span>

* <span data-ttu-id="43914-118">Liczba aktywnych obwodów obsługiwanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="43914-118">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="43914-119">Opóźnienie interfejsu użytkownika na kliencie.</span><span class="sxs-lookup"><span data-stu-id="43914-119">UI latency on the client.</span></span>

<span data-ttu-id="43914-120">Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i skalowalnych Blazor aplikacji serwerowych, zobacz <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="43914-120">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="43914-121">Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*.</span><span class="sxs-lookup"><span data-stu-id="43914-121">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="43914-122">Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="43914-122">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="43914-123">Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 współbieżnych użytkowników, należy rozważyć budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).</span><span class="sxs-lookup"><span data-stu-id="43914-123">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="43914-124">SignalR skonfigurować</span><span class="sxs-lookup"><span data-stu-id="43914-124">SignalR configuration</span></span>

<span data-ttu-id="43914-125">Blazor Server aplikacje używają ASP.NET Core SignalR do komunikowania się z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="43914-125">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="43914-126">[ SignalR warunki hostingu i skalowania](xref:signalr/publish-to-azure-web-app) mają zastosowanie do Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43914-126">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="43914-127">Blazor najlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako SignalR transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="43914-127">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="43914-128">Długie sondowanie jest używane przez, SignalR gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długotrwałego sondowania.</span><span class="sxs-lookup"><span data-stu-id="43914-128">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="43914-129">Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi.</span><span class="sxs-lookup"><span data-stu-id="43914-129">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="43914-130">Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="43914-130">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="43914-131">Usługa platformy Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="43914-131">Azure SignalR Service</span></span>

<span data-ttu-id="43914-132">Zalecamy korzystanie z [ SignalR usługi platformy Azure](xref:signalr/scale#azure-signalr-service) dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43914-132">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="43914-133">Usługa umożliwia skalowanie Blazor Server aplikacji w górę do dużej liczby jednoczesnych SignalR połączeń.</span><span class="sxs-lookup"><span data-stu-id="43914-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="43914-134">Ponadto SignalR globalne zasięgi i wysokiej wydajności centra danych usługi znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.</span><span class="sxs-lookup"><span data-stu-id="43914-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="43914-135">Gdy usługi [WebSockets](https://wikipedia.org/wiki/WebSocket) są wyłączone, Azure App Service symuluje połączenie w czasie rzeczywistym przy użyciu długich sondowania protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="43914-135">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="43914-136">Długotrwałe sondowanie HTTP jest znacznie wolniejsze niż uruchamianie z włączonymi składnikami WebSockets, które nie używają sondowania w celu symulowania połączenia klienta z serwerem.</span><span class="sxs-lookup"><span data-stu-id="43914-136">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="43914-137">Zalecamy używanie funkcji WebSockets dla Blazor Server aplikacji wdrożonych w programie Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="43914-137">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="43914-138">[ SignalR Usługa platformy Azure](xref:signalr/scale#azure-signalr-service) domyślnie używa obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="43914-138">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="43914-139">Jeśli aplikacja nie korzysta z usługi platformy Azure SignalR , zobacz <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .</span><span class="sxs-lookup"><span data-stu-id="43914-139">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="43914-140">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="43914-140">For more information, see:</span></span>
>
> * [<span data-ttu-id="43914-141">Co to jest SignalR usługa Azure?</span><span class="sxs-lookup"><span data-stu-id="43914-141">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="43914-142">Przewodnik dotyczący wydajności SignalR usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="43914-142">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="43914-143">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="43914-143">Configuration</span></span>

<span data-ttu-id="43914-144">Aby skonfigurować aplikację dla usługi platformy Azure SignalR , aplikacja musi obsługiwać sesje programu *Sticky Notes*, w przypadku których klienci są [przekierowywani z powrotem do tego samego serwera podczas renderowania](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="43914-144">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="43914-145">`ServerStickyMode`Wartość opcji lub konfiguracji jest ustawiona na `Required` .</span><span class="sxs-lookup"><span data-stu-id="43914-145">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="43914-146">Zazwyczaj aplikacja tworzy konfigurację przy użyciu **_jednej_** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="43914-146">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="43914-147">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="43914-147">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="43914-148">Konfiguracja (Użyj **_jednej_** z następujących metod):</span><span class="sxs-lookup"><span data-stu-id="43914-148">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="43914-149">W pliku `appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="43914-149">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="43914-150">  >  **Ustawienia aplikacji** konfiguracji usługi App Service w Azure Portal (**Nazwa**: `Azure__SignalR__StickyServerMode` , **wartość**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="43914-150">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="43914-151">Takie podejście jest stosowane automatycznie w przypadku aprowizacji [ SignalR usługi platformy Azure](#provision-the-azure-signalr-service).</span><span class="sxs-lookup"><span data-stu-id="43914-151">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-signalr-service"></a><span data-ttu-id="43914-152">Inicjowanie obsługi administracyjnej SignalR usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="43914-152">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="43914-153">Aby zainicjować obsługę administracyjną usługi platformy Azure SignalR dla aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="43914-153">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="43914-154">Utwórz profil publikowania aplikacji platformy Azure w programie Visual Studio dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43914-154">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="43914-155">Dodaj zależność **SignalR usługi platformy Azure** do profilu.</span><span class="sxs-lookup"><span data-stu-id="43914-155">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="43914-156">Jeśli subskrypcja platformy Azure nie ma istniejącego wystąpienia usługi platformy Azure SignalR , które ma zostać przypisane do aplikacji, wybierz opcję **Utwórz nowe SignalR wystąpienie usługi platformy Azure** , aby udostępnić nowe wystąpienie usługi.</span><span class="sxs-lookup"><span data-stu-id="43914-156">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="43914-157">Publikowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="43914-157">Publish the app to Azure.</span></span>

<span data-ttu-id="43914-158">Inicjowanie obsługi administracyjnej usługi platformy Azure SignalR w programie Visual Studio powoduje automatyczne [włączenie *sesji programu Sticky Notes*](#configuration) i dodanie SignalR parametrów połączenia do konfiguracji usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="43914-158">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="43914-159">IIS</span><span class="sxs-lookup"><span data-stu-id="43914-159">IIS</span></span>

<span data-ttu-id="43914-160">W przypadku korzystania z usług IIS Włącz:</span><span class="sxs-lookup"><span data-stu-id="43914-160">When using IIS, enable:</span></span>

* <span data-ttu-id="43914-161">Obiekty [WebSockets w usługach IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="43914-161">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="43914-162">[Sesje programu Sticky w ramach routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="43914-162">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="43914-163">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="43914-163">Kubernetes</span></span>

<span data-ttu-id="43914-164">Utwórz definicję transferu danych przychodzących z następującymi [adnotacjami Kubernetes dla sesji programu Sticky Notes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="43914-164">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="43914-165">System Linux z serwerem Nginx</span><span class="sxs-lookup"><span data-stu-id="43914-165">Linux with Nginx</span></span>

<span data-ttu-id="43914-166">Aby SignalR elementy WebSockets działały prawidłowo, upewnij się, że na serwerze proxy `Upgrade` i w `Connection` nagłówkach zostały ustawione następujące wartości, które `$connection_upgrade` są mapowane na:</span><span class="sxs-lookup"><span data-stu-id="43914-166">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="43914-167">Domyślnie wartość nagłówka uaktualnienia.</span><span class="sxs-lookup"><span data-stu-id="43914-167">The Upgrade header value by default.</span></span>
* <span data-ttu-id="43914-168">`close` gdy brakuje nagłówka uaktualnienia lub jest on pusty.</span><span class="sxs-lookup"><span data-stu-id="43914-168">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="43914-169">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="43914-169">For more information, see the following articles:</span></span>

* [<span data-ttu-id="43914-170">NGINX jako serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="43914-170">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="43914-171">Serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="43914-171">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="43914-172">System Linux z serwerem Apache</span><span class="sxs-lookup"><span data-stu-id="43914-172">Linux with Apache</span></span>

<span data-ttu-id="43914-173">Aby hostować Blazor aplikację za Apache w systemie Linux, skonfiguruj `ProxyPass` dla ruchu HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="43914-173">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="43914-174">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="43914-174">In the following example:</span></span>

* <span data-ttu-id="43914-175">Serwer Kestrel jest uruchomiony na komputerze-hoście.</span><span class="sxs-lookup"><span data-stu-id="43914-175">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="43914-176">Aplikacja nasłuchuje ruchu na porcie 5000.</span><span class="sxs-lookup"><span data-stu-id="43914-176">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="43914-177">Włącz następujące moduły:</span><span class="sxs-lookup"><span data-stu-id="43914-177">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="43914-178">Sprawdź, czy w konsoli przeglądarki występują błędy usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="43914-178">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="43914-179">Przykładowe błędy:</span><span class="sxs-lookup"><span data-stu-id="43914-179">Example errors:</span></span>

* <span data-ttu-id="43914-180">Firefox nie może nawiązać połączenia z serwerem w ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="43914-180">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="43914-181">Błąd: nie można uruchomić transportu "WebSockets": błąd: Wystąpił błąd podczas transportu.</span><span class="sxs-lookup"><span data-stu-id="43914-181">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="43914-182">Błąd: nie można uruchomić transportu "LongPolling": TypeError: this. transport jest niezdefiniowany</span><span class="sxs-lookup"><span data-stu-id="43914-182">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="43914-183">Błąd: nie można nawiązać połączenia z serwerem za pomocą dowolnych dostępnych transportów.</span><span class="sxs-lookup"><span data-stu-id="43914-183">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="43914-184">Niepowodzenie obiektów WebSockets</span><span class="sxs-lookup"><span data-stu-id="43914-184">WebSockets failed</span></span>
* <span data-ttu-id="43914-185">Błąd: nie można wysłać danych, jeśli połączenie nie jest w stanie "podłączone".</span><span class="sxs-lookup"><span data-stu-id="43914-185">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="43914-186">Aby uzyskać więcej informacji, zobacz [dokumentację platformy Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="43914-186">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="43914-187">Mierzenie opóźnienia sieci</span><span class="sxs-lookup"><span data-stu-id="43914-187">Measure network latency</span></span>

<span data-ttu-id="43914-188">Przy użyciu kodu [js Interop](xref:blazor/call-javascript-from-dotnet) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="43914-188">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="43914-189">W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.</span><span class="sxs-lookup"><span data-stu-id="43914-189">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
