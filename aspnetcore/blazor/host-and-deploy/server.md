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
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "94595444"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="b6e00-103">Hostowanie i wdrażanie Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b6e00-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="b6e00-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b6e00-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b6e00-105">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="b6e00-105">Host configuration values</span></span>

<span data-ttu-id="b6e00-106">[ Blazor Server aplikacje](xref:blazor/hosting-models#blazor-server) mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="b6e00-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="b6e00-107">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="b6e00-107">Deployment</span></span>

<span data-ttu-id="b6e00-108">Przy użyciu [ Blazor Server modelu hostingu](xref:blazor/hosting-models#blazor-server)program Blazor jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6e00-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="b6e00-109">Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.</span><span class="sxs-lookup"><span data-stu-id="b6e00-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b6e00-110">Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6e00-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b6e00-111">Program Visual Studio zawiera szablon projektu **Blazor Server aplikacji** ( `blazorserverside` szablon podczas korzystania z [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia).</span><span class="sxs-lookup"><span data-stu-id="b6e00-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="b6e00-112">Skalowalność</span><span class="sxs-lookup"><span data-stu-id="b6e00-112">Scalability</span></span>

<span data-ttu-id="b6e00-113">Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6e00-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="b6e00-114">Aby rozwiązać skalowalność aplikacji, zobacz następujące zasoby Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="b6e00-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="b6e00-115">Podstawowe Blazor Server aplikacje</span><span class="sxs-lookup"><span data-stu-id="b6e00-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="b6e00-116">Serwer wdrażania</span><span class="sxs-lookup"><span data-stu-id="b6e00-116">Deployment server</span></span>

<span data-ttu-id="b6e00-117">Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b6e00-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="b6e00-118">Dostępna pamięć na serwerze ma wpływ na:</span><span class="sxs-lookup"><span data-stu-id="b6e00-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="b6e00-119">Liczba aktywnych obwodów obsługiwanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="b6e00-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="b6e00-120">Opóźnienie interfejsu użytkownika na kliencie.</span><span class="sxs-lookup"><span data-stu-id="b6e00-120">UI latency on the client.</span></span>

<span data-ttu-id="b6e00-121">Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i skalowalnych Blazor aplikacji serwerowych, zobacz <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="b6e00-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="b6e00-122">Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*.</span><span class="sxs-lookup"><span data-stu-id="b6e00-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="b6e00-123">Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="b6e00-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="b6e00-124">Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 współbieżnych użytkowników, należy rozważyć budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).</span><span class="sxs-lookup"><span data-stu-id="b6e00-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="b6e00-125">SignalR skonfigurować</span><span class="sxs-lookup"><span data-stu-id="b6e00-125">SignalR configuration</span></span>

<span data-ttu-id="b6e00-126">Blazor Server aplikacje używają ASP.NET Core SignalR do komunikowania się z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="b6e00-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="b6e00-127">[ SignalR warunki hostingu i skalowania](xref:signalr/publish-to-azure-web-app) mają zastosowanie do Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6e00-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="b6e00-128">Blazor najlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako SignalR transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="b6e00-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="b6e00-129">Długie sondowanie jest używane przez, SignalR gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długotrwałego sondowania.</span><span class="sxs-lookup"><span data-stu-id="b6e00-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="b6e00-130">Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi.</span><span class="sxs-lookup"><span data-stu-id="b6e00-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="b6e00-131">Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="b6e00-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="b6e00-132">Usługa platformy Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="b6e00-132">Azure SignalR Service</span></span>

<span data-ttu-id="b6e00-133">Zalecamy korzystanie z [ SignalR usługi platformy Azure](xref:signalr/scale#azure-signalr-service) dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6e00-133">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="b6e00-134">Usługa umożliwia skalowanie Blazor Server aplikacji w górę do dużej liczby jednoczesnych SignalR połączeń.</span><span class="sxs-lookup"><span data-stu-id="b6e00-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="b6e00-135">Ponadto SignalR globalne zasięgi i wysokiej wydajności centra danych usługi znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.</span><span class="sxs-lookup"><span data-stu-id="b6e00-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6e00-136">Gdy usługi [WebSockets](https://wikipedia.org/wiki/WebSocket) są wyłączone, Azure App Service symuluje połączenie w czasie rzeczywistym przy użyciu długich sondowania protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b6e00-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="b6e00-137">Długotrwałe sondowanie HTTP jest znacznie wolniejsze niż uruchamianie z włączonymi składnikami WebSockets, które nie używają sondowania w celu symulowania połączenia klienta z serwerem.</span><span class="sxs-lookup"><span data-stu-id="b6e00-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="b6e00-138">Zalecamy używanie funkcji WebSockets dla Blazor Server aplikacji wdrożonych w programie Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b6e00-138">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="b6e00-139">[ SignalR Usługa platformy Azure](xref:signalr/scale#azure-signalr-service) domyślnie używa obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b6e00-139">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="b6e00-140">Jeśli aplikacja nie korzysta z usługi platformy Azure SignalR , zobacz <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .</span><span class="sxs-lookup"><span data-stu-id="b6e00-140">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="b6e00-141">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="b6e00-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="b6e00-142">Co to jest SignalR usługa Azure?</span><span class="sxs-lookup"><span data-stu-id="b6e00-142">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="b6e00-143">Przewodnik dotyczący wydajności SignalR usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b6e00-143">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="b6e00-144">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="b6e00-144">Configuration</span></span>

<span data-ttu-id="b6e00-145">Aby skonfigurować aplikację dla usługi platformy Azure SignalR , aplikacja musi obsługiwać sesje programu *Sticky Notes*, w przypadku których klienci są [przekierowywani z powrotem do tego samego serwera podczas renderowania](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="b6e00-145">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="b6e00-146">`ServerStickyMode`Wartość opcji lub konfiguracji jest ustawiona na `Required` .</span><span class="sxs-lookup"><span data-stu-id="b6e00-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="b6e00-147">Zazwyczaj aplikacja tworzy konfigurację przy użyciu **_jednej_** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="b6e00-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="b6e00-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b6e00-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="b6e00-149">Konfiguracja (Użyj **_jednej_** z następujących metod):</span><span class="sxs-lookup"><span data-stu-id="b6e00-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="b6e00-150">W pliku `appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b6e00-150">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="b6e00-151">  >  **Ustawienia aplikacji** konfiguracji usługi App Service w Azure Portal (**Nazwa**: `Azure__SignalR__StickyServerMode` , **wartość**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="b6e00-151">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="b6e00-152">Takie podejście jest stosowane automatycznie w przypadku aprowizacji [ SignalR usługi platformy Azure](#provision-the-azure-signalr-service).</span><span class="sxs-lookup"><span data-stu-id="b6e00-152">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-signalr-service"></a><span data-ttu-id="b6e00-153">Inicjowanie obsługi administracyjnej SignalR usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b6e00-153">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="b6e00-154">Aby zainicjować obsługę administracyjną usługi platformy Azure SignalR dla aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b6e00-154">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="b6e00-155">Utwórz profil publikowania aplikacji platformy Azure w programie Visual Studio dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b6e00-155">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="b6e00-156">Dodaj zależność **SignalR usługi platformy Azure** do profilu.</span><span class="sxs-lookup"><span data-stu-id="b6e00-156">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="b6e00-157">Jeśli subskrypcja platformy Azure nie ma istniejącego wystąpienia usługi platformy Azure SignalR , które ma zostać przypisane do aplikacji, wybierz opcję **Utwórz nowe SignalR wystąpienie usługi platformy Azure** , aby udostępnić nowe wystąpienie usługi.</span><span class="sxs-lookup"><span data-stu-id="b6e00-157">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="b6e00-158">Publikowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b6e00-158">Publish the app to Azure.</span></span>

<span data-ttu-id="b6e00-159">Inicjowanie obsługi administracyjnej usługi platformy Azure SignalR w programie Visual Studio powoduje automatyczne [włączenie *sesji programu Sticky Notes*](#configuration) i dodanie SignalR parametrów połączenia do konfiguracji usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="b6e00-159">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="b6e00-160">IIS</span><span class="sxs-lookup"><span data-stu-id="b6e00-160">IIS</span></span>

<span data-ttu-id="b6e00-161">W przypadku korzystania z usług IIS Włącz:</span><span class="sxs-lookup"><span data-stu-id="b6e00-161">When using IIS, enable:</span></span>

* <span data-ttu-id="b6e00-162">Obiekty [WebSockets w usługach IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="b6e00-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="b6e00-163">[Sesje programu Sticky w ramach routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="b6e00-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="b6e00-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="b6e00-164">Kubernetes</span></span>

<span data-ttu-id="b6e00-165">Utwórz definicję transferu danych przychodzących z następującymi [adnotacjami Kubernetes dla sesji programu Sticky Notes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="b6e00-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="b6e00-166">System Linux z serwerem Nginx</span><span class="sxs-lookup"><span data-stu-id="b6e00-166">Linux with Nginx</span></span>

<span data-ttu-id="b6e00-167">Aby SignalR elementy WebSockets działały prawidłowo, upewnij się, że na serwerze proxy `Upgrade` i w `Connection` nagłówkach zostały ustawione następujące wartości, które `$connection_upgrade` są mapowane na:</span><span class="sxs-lookup"><span data-stu-id="b6e00-167">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="b6e00-168">Domyślnie wartość nagłówka uaktualnienia.</span><span class="sxs-lookup"><span data-stu-id="b6e00-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="b6e00-169">`close` gdy brakuje nagłówka uaktualnienia lub jest on pusty.</span><span class="sxs-lookup"><span data-stu-id="b6e00-169">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="b6e00-170">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="b6e00-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="b6e00-171">NGINX jako serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="b6e00-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="b6e00-172">Serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="b6e00-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="b6e00-173">System Linux z serwerem Apache</span><span class="sxs-lookup"><span data-stu-id="b6e00-173">Linux with Apache</span></span>

<span data-ttu-id="b6e00-174">Aby hostować Blazor aplikację za Apache w systemie Linux, skonfiguruj `ProxyPass` dla ruchu HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b6e00-174">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="b6e00-175">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b6e00-175">In the following example:</span></span>

* <span data-ttu-id="b6e00-176">Serwer Kestrel jest uruchomiony na komputerze-hoście.</span><span class="sxs-lookup"><span data-stu-id="b6e00-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="b6e00-177">Aplikacja nasłuchuje ruchu na porcie 5000.</span><span class="sxs-lookup"><span data-stu-id="b6e00-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="b6e00-178">Włącz następujące moduły:</span><span class="sxs-lookup"><span data-stu-id="b6e00-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="b6e00-179">Sprawdź, czy w konsoli przeglądarki występują błędy usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b6e00-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="b6e00-180">Przykładowe błędy:</span><span class="sxs-lookup"><span data-stu-id="b6e00-180">Example errors:</span></span>

* <span data-ttu-id="b6e00-181">Firefox nie może nawiązać połączenia z serwerem w ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="b6e00-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="b6e00-182">Błąd: nie można uruchomić transportu "WebSockets": błąd: Wystąpił błąd podczas transportu.</span><span class="sxs-lookup"><span data-stu-id="b6e00-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="b6e00-183">Błąd: nie można uruchomić transportu "LongPolling": TypeError: this. transport jest niezdefiniowany</span><span class="sxs-lookup"><span data-stu-id="b6e00-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="b6e00-184">Błąd: nie można nawiązać połączenia z serwerem za pomocą dowolnych dostępnych transportów.</span><span class="sxs-lookup"><span data-stu-id="b6e00-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="b6e00-185">Niepowodzenie obiektów WebSockets</span><span class="sxs-lookup"><span data-stu-id="b6e00-185">WebSockets failed</span></span>
* <span data-ttu-id="b6e00-186">Błąd: nie można wysłać danych, jeśli połączenie nie jest w stanie "podłączone".</span><span class="sxs-lookup"><span data-stu-id="b6e00-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="b6e00-187">Aby uzyskać więcej informacji, zobacz [dokumentację platformy Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="b6e00-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="b6e00-188">Mierzenie opóźnienia sieci</span><span class="sxs-lookup"><span data-stu-id="b6e00-188">Measure network latency</span></span>

<span data-ttu-id="b6e00-189">Przy użyciu kodu [js Interop](xref:blazor/call-javascript-from-dotnet) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b6e00-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="b6e00-190">W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.</span><span class="sxs-lookup"><span data-stu-id="b6e00-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
