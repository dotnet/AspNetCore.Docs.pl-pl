---
title: Hostuj i wdrażaj Blazor ASP.NET serwer podstawowy
author: guardrex
description: Dowiedz się, jak Blazor hostować i wdrażać aplikację serwera przy użyciu ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025423"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="962b0-103">Hostuj Blazor i wdrażaj serwer</span><span class="sxs-lookup"><span data-stu-id="962b0-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="962b0-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="962b0-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="962b0-105">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="962b0-105">Host configuration values</span></span>

<span data-ttu-id="962b0-106">Aplikacje serwera mogą akceptować [wartości konfiguracji hosta ogólnego](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="962b0-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="962b0-107">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="962b0-107">Deployment</span></span>

<span data-ttu-id="962b0-108">Za pomocą modelu Blazor [ Blazor hostingu serwera](xref:blazor/hosting-models#blazor-server), jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="962b0-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="962b0-109">Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania [SignalR](xref:signalr/introduction) JavaScript są obsługiwane przez połączenie.</span><span class="sxs-lookup"><span data-stu-id="962b0-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="962b0-110">Wymagany jest serwer www obsługujący aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="962b0-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="962b0-111">Program Visual Studio zawiera szablon`blazorserverside` projektu aplikacji \*\* Blazor serwera\*\* (szablon podczas korzystania z nowego polecenia [dotnet).](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="962b0-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="962b0-112">Skalowalność</span><span class="sxs-lookup"><span data-stu-id="962b0-112">Scalability</span></span>

<span data-ttu-id="962b0-113">Zaplanuj wdrożenie, aby jak najlepiej Blazor wykorzystać dostępną infrastrukturę dla aplikacji Server.</span><span class="sxs-lookup"><span data-stu-id="962b0-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="962b0-114">Zobacz następujące zasoby, Blazor aby rozwiązać skalowalność aplikacji serwera:</span><span class="sxs-lookup"><span data-stu-id="962b0-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="962b0-115">[Podstawy aplikacji Blazor serwera](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="962b0-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="962b0-116">Serwer wdrażania</span><span class="sxs-lookup"><span data-stu-id="962b0-116">Deployment server</span></span>

<span data-ttu-id="962b0-117">Biorąc pod uwagę skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który aplikacja wyczerpie wraz ze wzrostem wymagań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="962b0-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="962b0-118">Dostępna pamięć na serwerze wpływa na:</span><span class="sxs-lookup"><span data-stu-id="962b0-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="962b0-119">Liczba aktywnych obwodów obsługiwanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="962b0-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="962b0-120">Opóźnienie interfejsu użytkownika na kliencie.</span><span class="sxs-lookup"><span data-stu-id="962b0-120">UI latency on the client.</span></span>

<span data-ttu-id="962b0-121">Aby uzyskać wskazówki dotyczące Blazor tworzenia bezpiecznych i skalowalnych aplikacji serwerowych, zobacz <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="962b0-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="962b0-122">Każdy obwód używa około 250 KB pamięci dla minimalnej aplikacji *Hello World*-style.</span><span class="sxs-lookup"><span data-stu-id="962b0-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="962b0-123">Rozmiar obwodu zależy od kodu aplikacji i wymagań konserwacji stanu skojarzonych z każdym składnikiem.</span><span class="sxs-lookup"><span data-stu-id="962b0-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="962b0-124">Zaleca się, aby mierzyć zapotrzebowanie na zasoby podczas tworzenia aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem wyjścia w planowaniu miejsca docelowego wdrożenia: Jeśli oczekujesz, że aplikacja obsługuje 5000 równoczesnych użytkowników, należy wziąć pod uwagę budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~273 KB na użytkownika).</span><span class="sxs-lookup"><span data-stu-id="962b0-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="962b0-125">Konfiguracji</span><span class="sxs-lookup"><span data-stu-id="962b0-125"> configuration</span></span>

Blazor<span data-ttu-id="962b0-126">Aplikacje serwerowe używają SignalR ASP.NET Core do komunikowania się z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="962b0-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="962b0-127">['s hosting i skalowanie warunki mają zastosowanie do aplikacji serwera. SignalR](xref:signalr/publish-to-azure-web-app) Blazor</span><span class="sxs-lookup"><span data-stu-id="962b0-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="962b0-128">działa najlepiej, gdy używasz SignalR WebSockets jako transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo.](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="962b0-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="962b0-129">Długie sondowanie SignalR jest używany przez gdy WebSockets nie jest dostępny lub gdy aplikacja jest jawnie skonfigurowany do korzystania z długiego sondowania.</span><span class="sxs-lookup"><span data-stu-id="962b0-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="962b0-130">Podczas wdrażania w usłudze Azure App Service należy skonfigurować aplikację do używania websockets w ustawieniach witryny Azure portal dla usługi.</span><span class="sxs-lookup"><span data-stu-id="962b0-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="962b0-131">Aby uzyskać szczegółowe informacje na temat konfigurowania aplikacji dla usługi Azure App Service, zobacz [ SignalR wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="962b0-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="962b0-132">Usługa SignalR platformy Azure</span><span class="sxs-lookup"><span data-stu-id="962b0-132">Azure SignalR Service</span></span>

<span data-ttu-id="962b0-133">Zalecamy korzystanie z Blazor [usługi Azure SignalR ](/azure/azure-signalr) dla aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="962b0-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="962b0-134">Usługa umożliwia skalowanie aplikacji Blazor serwera do dużej liczby równoczesnych SignalR połączeń.</span><span class="sxs-lookup"><span data-stu-id="962b0-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="962b0-135">Ponadto globalny SignalR zasięg usługi i wysokowydajne centra danych znacznie pomagają w zmniejszaniu opóźnień ze względu na lokalizację geograficzną.</span><span class="sxs-lookup"><span data-stu-id="962b0-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="962b0-136">Aby skonfigurować aplikację (i opcjonalnie SignalR aprowizować) usługę Azure:</span><span class="sxs-lookup"><span data-stu-id="962b0-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="962b0-137">Włącz usługę do obsługi *sesji przyklejonych,* gdzie klienci są [przekierowywane z powrotem do tego samego serwera podczas prerendering](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="962b0-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="962b0-138">Ustaw `ServerStickyMode` opcję lub wartość `Required`konfiguracj na .</span><span class="sxs-lookup"><span data-stu-id="962b0-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="962b0-139">Zazwyczaj aplikacja tworzy konfigurację przy użyciu **jednego** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="962b0-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="962b0-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="962b0-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="962b0-141">Konfiguracja (użyj **jednego** z następujących podejść):</span><span class="sxs-lookup"><span data-stu-id="962b0-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="962b0-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="962b0-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="962b0-143">**Ustawienia aplikacji** **konfiguracji** > usługi aplikacji w witrynie `Azure:SignalR:ServerStickyMode`Azure portal (**Nazwa**: , **Wartość**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="962b0-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="962b0-144">Utwórz profil publikowania usługi Azure Blazor Apps w programie Visual Studio dla aplikacji server.</span><span class="sxs-lookup"><span data-stu-id="962b0-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="962b0-145">Dodaj zależność \*\*usługi Azure SignalR \*\* do profilu.</span><span class="sxs-lookup"><span data-stu-id="962b0-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="962b0-146">Jeśli subskrypcja platformy Azure nie ma SignalR wcześniej istniejącego wystąpienia usługi Azure do przypisania do aplikacji, wybierz pozycję **Utwórz nowe wystąpienie SignalR usługi Azure,** aby aprowizować nowe wystąpienie usługi.</span><span class="sxs-lookup"><span data-stu-id="962b0-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="962b0-147">Publikowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="962b0-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="962b0-148">IIS</span><span class="sxs-lookup"><span data-stu-id="962b0-148">IIS</span></span>

<span data-ttu-id="962b0-149">Podczas korzystania z usługi IIS włącz:</span><span class="sxs-lookup"><span data-stu-id="962b0-149">When using IIS, enable:</span></span>

* <span data-ttu-id="962b0-150">[WebSockets na IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="962b0-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="962b0-151">[Sesje przyklejone z routingiem żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="962b0-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="962b0-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="962b0-152">Kubernetes</span></span>

<span data-ttu-id="962b0-153">Tworzenie definicji transferu ruchu przychodzącego z [następującymi adnotacjami Kubernetes dla sesji przyklejonych:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)</span><span class="sxs-lookup"><span data-stu-id="962b0-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="962b0-154">System Linux z serwerem Nginx</span><span class="sxs-lookup"><span data-stu-id="962b0-154">Linux with Nginx</span></span>

<span data-ttu-id="962b0-155">Aby SignalR zestawy WebSockets działały poprawnie, upewnij `Upgrade` `Connection` się, że serwer proxy i `$connection_upgrade` nagłówki są ustawione na następujące wartości i które są mapowane na:</span><span class="sxs-lookup"><span data-stu-id="962b0-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="962b0-156">Wartość nagłówka uaktualnienia domyślnie.</span><span class="sxs-lookup"><span data-stu-id="962b0-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="962b0-157">`close`gdy brakuje nagłówka uaktualnienia lub jest on pusty.</span><span class="sxs-lookup"><span data-stu-id="962b0-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="962b0-158">Aby uzyskać więcej informacji zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="962b0-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="962b0-159">NGINX jako serwer proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="962b0-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="962b0-160">Proxy websocket</span><span class="sxs-lookup"><span data-stu-id="962b0-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="962b0-161">Mierzenie opóźnienia sieci</span><span class="sxs-lookup"><span data-stu-id="962b0-161">Measure network latency</span></span>

<span data-ttu-id="962b0-162">[JS interop](xref:blazor/call-javascript-from-dotnet) może służyć do pomiaru opóźnienia sieci, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="962b0-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="962b0-163">Aby uzyskać odpowiednie środowisko interfejsu użytkownika, zaleca się stałe opóźnienie interfejsu użytkownika 250ms lub mniej.</span><span class="sxs-lookup"><span data-stu-id="962b0-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
