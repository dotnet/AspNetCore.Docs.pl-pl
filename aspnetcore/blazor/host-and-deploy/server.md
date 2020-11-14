---
title: 'Hostowanie i wdrażanie ASP.NET Core :::no-loc(Blazor Server):::'
author: guardrex
description: 'Dowiedz się, jak hostować i wdrażać :::no-loc(Blazor Server)::: aplikację przy użyciu ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595444"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="5b4b1-103">Hostowanie i wdrażanie :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="5b4b1-103">Host and deploy :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="5b4b1-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5b4b1-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="5b4b1-105">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="5b4b1-105">Host configuration values</span></span>

<span data-ttu-id="5b4b1-106">[ :::no-loc(Blazor Server)::: aplikacje](xref:blazor/hosting-models#blazor-server) mogą akceptować [ogólne wartości konfiguracji hosta](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-106">[:::no-loc(Blazor Server)::: apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="5b4b1-107">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="5b4b1-107">Deployment</span></span>

<span data-ttu-id="5b4b1-108">Przy użyciu [ :::no-loc(Blazor Server)::: modelu hostingu](xref:blazor/hosting-models#blazor-server)program :::no-loc(Blazor)::: jest wykonywany na serwerze z poziomu aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-108">Using the [:::no-loc(Blazor Server)::: hosting model](xref:blazor/hosting-models#blazor-server), :::no-loc(Blazor)::: is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="5b4b1-109">Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [:::no-loc(SignalR):::](xref:signalr/introduction) połączenie.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-109">UI updates, event handling, and JavaScript calls are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="5b4b1-110">Wymagany jest serwer sieci Web obsługujący aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="5b4b1-111">Program Visual Studio zawiera szablon projektu **:::no-loc(Blazor Server)::: aplikacji** ( `blazorserverside` szablon podczas korzystania z [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-111">Visual Studio includes the **:::no-loc(Blazor Server)::: App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="5b4b1-112">Skalowalność</span><span class="sxs-lookup"><span data-stu-id="5b4b1-112">Scalability</span></span>

<span data-ttu-id="5b4b1-113">Zaplanuj wdrożenie, aby najlepiej wykorzystać dostępną infrastrukturę dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-113">Plan a deployment to make the best use of the available infrastructure for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="5b4b1-114">Aby rozwiązać skalowalność aplikacji, zobacz następujące zasoby :::no-loc(Blazor Server)::: :</span><span class="sxs-lookup"><span data-stu-id="5b4b1-114">See the following resources to address :::no-loc(Blazor Server)::: app scalability:</span></span>

* [<span data-ttu-id="5b4b1-115">Podstawowe :::no-loc(Blazor Server)::: aplikacje</span><span class="sxs-lookup"><span data-stu-id="5b4b1-115">Fundamentals of :::no-loc(Blazor Server)::: apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="5b4b1-116">Serwer wdrażania</span><span class="sxs-lookup"><span data-stu-id="5b4b1-116">Deployment server</span></span>

<span data-ttu-id="5b4b1-117">Gdy rozważasz skalowalność pojedynczego serwera (skalowanie w górę), pamięć dostępna dla aplikacji jest prawdopodobnie pierwszym zasobem, który zostanie wyczerpany przez aplikację w miarę wzrostu wymagań użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="5b4b1-118">Dostępna pamięć na serwerze ma wpływ na:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="5b4b1-119">Liczba aktywnych obwodów obsługiwanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="5b4b1-120">Opóźnienie interfejsu użytkownika na kliencie.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-120">UI latency on the client.</span></span>

<span data-ttu-id="5b4b1-121">Aby uzyskać wskazówki dotyczące tworzenia bezpiecznych i skalowalnych :::no-loc(Blazor)::: aplikacji serwerowych, zobacz <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="5b4b1-121">For guidance on building secure and scalable :::no-loc(Blazor)::: server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="5b4b1-122">Każdy obwód wykorzystuje około 250 KB pamięci w przypadku aplikacji o minimalnej *Hello World*.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World* -style app.</span></span> <span data-ttu-id="5b4b1-123">Rozmiar obwodu zależy od kodu aplikacji i wymagań dotyczących konserwacji stanu związanych z poszczególnymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="5b4b1-124">Zalecamy mierzenie wymagań dotyczących zasobów podczas opracowywania aplikacji i infrastruktury, ale następujący punkt odniesienia może być punktem początkowym w planowaniu celu wdrożenia: jeśli oczekujesz, że aplikacja będzie obsługiwać 5 000 współbieżnych użytkowników, należy rozważyć budżetowanie co najmniej 1,3 GB pamięci serwera do aplikacji (lub ~ 273 KB na użytkownika).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="5b4b1-125">:::no-loc(SignalR)::: skonfigurować</span><span class="sxs-lookup"><span data-stu-id="5b4b1-125">:::no-loc(SignalR)::: configuration</span></span>

<span data-ttu-id="5b4b1-126">:::no-loc(Blazor Server)::: aplikacje używają ASP.NET Core :::no-loc(SignalR)::: do komunikowania się z przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-126">:::no-loc(Blazor Server)::: apps use ASP.NET Core :::no-loc(SignalR)::: to communicate with the browser.</span></span> <span data-ttu-id="5b4b1-127">[ :::no-loc(SignalR)::: warunki hostingu i skalowania](xref:signalr/publish-to-azure-web-app) mają zastosowanie do :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-127">[:::no-loc(SignalR):::'s hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to :::no-loc(Blazor Server)::: apps.</span></span>

<span data-ttu-id="5b4b1-128">:::no-loc(Blazor)::: najlepiej sprawdza się w przypadku korzystania z usługi WebSockets jako :::no-loc(SignalR)::: transportu ze względu na mniejsze opóźnienia, niezawodność i [bezpieczeństwo](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-128">:::no-loc(Blazor)::: works best when using WebSockets as the :::no-loc(SignalR)::: transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="5b4b1-129">Długie sondowanie jest używane przez, :::no-loc(SignalR)::: gdy obiekty WebSockets nie są dostępne lub gdy aplikacja jest jawnie skonfigurowana do korzystania z długotrwałego sondowania.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-129">Long Polling is used by :::no-loc(SignalR)::: when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="5b4b1-130">Podczas wdrażania programu w celu Azure App Service Skonfiguruj aplikację do używania obiektów WebSockets w ustawieniach Azure Portal dla usługi.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="5b4b1-131">Aby uzyskać szczegółowe informacje dotyczące konfigurowania aplikacji na potrzeby Azure App Service, zobacz [ :::no-loc(SignalR)::: wskazówki dotyczące publikowania](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-131">For details on configuring the app for Azure App Service, see the [:::no-loc(SignalR)::: publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="5b4b1-132">Usługa platformy Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="5b4b1-132">Azure :::no-loc(SignalR)::: Service</span></span>

<span data-ttu-id="5b4b1-133">Zalecamy korzystanie z [ :::no-loc(SignalR)::: usługi platformy Azure](xref:signalr/scale#azure-signalr-service) dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-133">We recommend using the [Azure :::no-loc(SignalR)::: Service](xref:signalr/scale#azure-signalr-service) for :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="5b4b1-134">Usługa umożliwia skalowanie :::no-loc(Blazor Server)::: aplikacji w górę do dużej liczby jednoczesnych :::no-loc(SignalR)::: połączeń.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-134">The service allows for scaling up a :::no-loc(Blazor Server)::: app to a large number of concurrent :::no-loc(SignalR)::: connections.</span></span> <span data-ttu-id="5b4b1-135">Ponadto :::no-loc(SignalR)::: globalne zasięgi i wysokiej wydajności centra danych usługi znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-135">In addition, the :::no-loc(SignalR)::: service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5b4b1-136">Gdy usługi [WebSockets](https://wikipedia.org/wiki/WebSocket) są wyłączone, Azure App Service symuluje połączenie w czasie rzeczywistym przy użyciu długich sondowania protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="5b4b1-137">Długotrwałe sondowanie HTTP jest znacznie wolniejsze niż uruchamianie z włączonymi składnikami WebSockets, które nie używają sondowania w celu symulowania połączenia klienta z serwerem.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="5b4b1-138">Zalecamy używanie funkcji WebSockets dla :::no-loc(Blazor Server)::: aplikacji wdrożonych w programie Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-138">We recommend using WebSockets for :::no-loc(Blazor Server)::: apps deployed to Azure App Service.</span></span> <span data-ttu-id="5b4b1-139">[ :::no-loc(SignalR)::: Usługa platformy Azure](xref:signalr/scale#azure-signalr-service) domyślnie używa obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-139">The [Azure :::no-loc(SignalR)::: Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="5b4b1-140">Jeśli aplikacja nie korzysta z usługi platformy Azure :::no-loc(SignalR)::: , zobacz <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .</span><span class="sxs-lookup"><span data-stu-id="5b4b1-140">If the app doesn't use the Azure :::no-loc(SignalR)::: Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="5b4b1-141">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="5b4b1-142">Co to jest :::no-loc(SignalR)::: usługa Azure?</span><span class="sxs-lookup"><span data-stu-id="5b4b1-142">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="5b4b1-143">Przewodnik dotyczący wydajności :::no-loc(SignalR)::: usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="5b4b1-143">Performance guide for Azure :::no-loc(SignalR)::: Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="5b4b1-144">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="5b4b1-144">Configuration</span></span>

<span data-ttu-id="5b4b1-145">Aby skonfigurować aplikację dla usługi platformy Azure :::no-loc(SignalR)::: , aplikacja musi obsługiwać sesje programu *Sticky Notes* , w przypadku których klienci są [przekierowywani z powrotem do tego samego serwera podczas renderowania](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-145">To configure an app for the Azure :::no-loc(SignalR)::: Service, the app must support *sticky sessions* , where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="5b4b1-146">`ServerStickyMode`Wartość opcji lub konfiguracji jest ustawiona na `Required` .</span><span class="sxs-lookup"><span data-stu-id="5b4b1-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="5b4b1-147">Zazwyczaj aplikacja tworzy konfigurację przy użyciu **_jednej_** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="5b4b1-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.Add:::no-loc(SignalR):::().AddAzure:::no-loc(SignalR):::(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.:::no-loc(SignalR):::.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="5b4b1-149">Konfiguracja (Użyj **_jednej_** z następujących metod):</span><span class="sxs-lookup"><span data-stu-id="5b4b1-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="5b4b1-150">W pliku `:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-150">In `:::no-loc(appsettings.json):::`:</span></span>

       ```json
       "Azure::::no-loc(SignalR)::::StickyServerMode": "Required"
       ```

     * <span data-ttu-id="5b4b1-151">**Configuration**  >  **Ustawienia aplikacji** konfiguracji usługi App Service w Azure Portal ( **Nazwa** : `Azure__:::no-loc(SignalR):::__StickyServerMode` , **wartość** : `Required` ).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-151">The app service's **Configuration** > **Application settings** in the Azure portal ( **Name** : `Azure__:::no-loc(SignalR):::__StickyServerMode`, **Value** : `Required`).</span></span> <span data-ttu-id="5b4b1-152">Takie podejście jest stosowane automatycznie w przypadku aprowizacji [ :::no-loc(SignalR)::: usługi platformy Azure](#provision-the-azure-signalr-service).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-152">This approach is adopted for the app automatically if you [provision the Azure :::no-loc(SignalR)::: Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-no-locsignalr-service"></a><span data-ttu-id="5b4b1-153">Inicjowanie obsługi administracyjnej :::no-loc(SignalR)::: usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="5b4b1-153">Provision the Azure :::no-loc(SignalR)::: Service</span></span>

<span data-ttu-id="5b4b1-154">Aby zainicjować obsługę administracyjną usługi platformy Azure :::no-loc(SignalR)::: dla aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-154">To provision the Azure :::no-loc(SignalR)::: Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="5b4b1-155">Utwórz profil publikowania aplikacji platformy Azure w programie Visual Studio dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-155">Create an Azure Apps publish profile in Visual Studio for the :::no-loc(Blazor Server)::: app.</span></span>
1. <span data-ttu-id="5b4b1-156">Dodaj zależność **:::no-loc(SignalR)::: usługi platformy Azure** do profilu.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-156">Add the **Azure :::no-loc(SignalR)::: Service** dependency to the profile.</span></span> <span data-ttu-id="5b4b1-157">Jeśli subskrypcja platformy Azure nie ma istniejącego wystąpienia usługi platformy Azure :::no-loc(SignalR)::: , które ma zostać przypisane do aplikacji, wybierz opcję **Utwórz nowe :::no-loc(SignalR)::: wystąpienie usługi platformy Azure** , aby udostępnić nowe wystąpienie usługi.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-157">If the Azure subscription doesn't have a pre-existing Azure :::no-loc(SignalR)::: Service instance to assign to the app, select **Create a new Azure :::no-loc(SignalR)::: Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="5b4b1-158">Publikowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="5b4b1-158">Publish the app to Azure.</span></span>

<span data-ttu-id="5b4b1-159">Inicjowanie obsługi administracyjnej usługi platformy Azure :::no-loc(SignalR)::: w programie Visual Studio powoduje automatyczne [włączenie *sesji programu Sticky Notes*](#configuration) i dodanie :::no-loc(SignalR)::: parametrów połączenia do konfiguracji usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-159">Provisioning the Azure :::no-loc(SignalR)::: Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the :::no-loc(SignalR)::: connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="5b4b1-160">IIS</span><span class="sxs-lookup"><span data-stu-id="5b4b1-160">IIS</span></span>

<span data-ttu-id="5b4b1-161">W przypadku korzystania z usług IIS Włącz:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-161">When using IIS, enable:</span></span>

* <span data-ttu-id="5b4b1-162">Obiekty [WebSockets w usługach IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="5b4b1-163">[Sesje programu Sticky w ramach routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="5b4b1-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="5b4b1-164">Kubernetes</span></span>

<span data-ttu-id="5b4b1-165">Utwórz definicję transferu danych przychodzących z następującymi [adnotacjami Kubernetes dla sesji programu Sticky Notes](https://kubernetes.github.io/ingress-nginx/examples/affinity/:::no-loc(cookie):::/):</span><span class="sxs-lookup"><span data-stu-id="5b4b1-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/:::no-loc(cookie):::/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: ":::no-loc(cookie):::"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-name: "affinity"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-expires: "14400"
    nginx.ingress.kubernetes.io/session-:::no-loc(cookie):::-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="5b4b1-166">System Linux z serwerem Nginx</span><span class="sxs-lookup"><span data-stu-id="5b4b1-166">Linux with Nginx</span></span>

<span data-ttu-id="5b4b1-167">Aby :::no-loc(SignalR)::: elementy WebSockets działały prawidłowo, upewnij się, że na serwerze proxy `Upgrade` i w `Connection` nagłówkach zostały ustawione następujące wartości, które `$connection_upgrade` są mapowane na:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-167">For :::no-loc(SignalR)::: WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="5b4b1-168">Domyślnie wartość nagłówka uaktualnienia.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="5b4b1-169">`close` gdy brakuje nagłówka uaktualnienia lub jest on pusty.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-169">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="5b4b1-170">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="5b4b1-171">NGINX jako serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="5b4b1-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="5b4b1-172">Serwer proxy protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="5b4b1-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="5b4b1-173">System Linux z serwerem Apache</span><span class="sxs-lookup"><span data-stu-id="5b4b1-173">Linux with Apache</span></span>

<span data-ttu-id="5b4b1-174">Aby hostować :::no-loc(Blazor)::: aplikację za Apache w systemie Linux, skonfiguruj `ProxyPass` dla ruchu HTTP i WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-174">To host a :::no-loc(Blazor)::: app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="5b4b1-175">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-175">In the following example:</span></span>

* <span data-ttu-id="5b4b1-176">Serwer Kestrel jest uruchomiony na komputerze-hoście.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="5b4b1-177">Aplikacja nasłuchuje ruchu na porcie 5000.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="5b4b1-178">Włącz następujące moduły:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="5b4b1-179">Sprawdź, czy w konsoli przeglądarki występują błędy usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="5b4b1-180">Przykładowe błędy:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-180">Example errors:</span></span>

* <span data-ttu-id="5b4b1-181">Firefox nie może nawiązać połączenia z serwerem w ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="5b4b1-182">Błąd: nie można uruchomić transportu "WebSockets": błąd: Wystąpił błąd podczas transportu.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="5b4b1-183">Błąd: nie można uruchomić transportu "LongPolling": TypeError: this. transport jest niezdefiniowany</span><span class="sxs-lookup"><span data-stu-id="5b4b1-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="5b4b1-184">Błąd: nie można nawiązać połączenia z serwerem za pomocą dowolnych dostępnych transportów.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="5b4b1-185">Niepowodzenie obiektów WebSockets</span><span class="sxs-lookup"><span data-stu-id="5b4b1-185">WebSockets failed</span></span>
* <span data-ttu-id="5b4b1-186">Błąd: nie można wysłać danych, jeśli połączenie nie jest w stanie "podłączone".</span><span class="sxs-lookup"><span data-stu-id="5b4b1-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="5b4b1-187">Aby uzyskać więcej informacji, zobacz [dokumentację platformy Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="5b4b1-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="5b4b1-188">Mierzenie opóźnienia sieci</span><span class="sxs-lookup"><span data-stu-id="5b4b1-188">Measure network latency</span></span>

<span data-ttu-id="5b4b1-189">Przy użyciu kodu [js Interop](xref:blazor/call-javascript-from-dotnet) można mierzyć opóźnienia sieci, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5b4b1-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="5b4b1-190">W celu uzyskania odpowiedniego środowiska interfejsu użytkownika zalecamy opóźnienia interfejsu użytkownika 250ms lub mniej.</span><span class="sxs-lookup"><span data-stu-id="5b4b1-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
