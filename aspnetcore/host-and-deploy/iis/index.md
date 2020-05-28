---
<span data-ttu-id="ca09b-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-102">'Blazor'</span></span>
- <span data-ttu-id="ca09b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-103">'Identity'</span></span>
- <span data-ttu-id="ca09b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-105">'Razor'</span></span>
- <span data-ttu-id="ca09b-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="ca09b-107">ASP.NET Core hosta w systemie Windows z usługami IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca09b-108">Aby zapoznać się z samouczkiem dotyczącym publikowania aplikacji ASP.NET Core na serwerze usług IIS, zobacz <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="ca09b-109">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="ca09b-110">Obsługiwane systemy operacyjne</span><span class="sxs-lookup"><span data-stu-id="ca09b-110">Supported operating systems</span></span>

<span data-ttu-id="ca09b-111">Obsługiwane są następujące systemy operacyjne:</span><span class="sxs-lookup"><span data-stu-id="ca09b-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="ca09b-112">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-112">Windows 7 or later</span></span>
* <span data-ttu-id="ca09b-113">System Windows Server 2012 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="ca09b-114">[Serwer http. sys](xref:fundamentals/servers/httpsys) (znany wcześniej jako webListener) nie działa w konfiguracji zwrotnego serwera proxy z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="ca09b-115">Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ca09b-116">Aby uzyskać informacje na temat hostingu na platformie Azure, zobacz <xref:host-and-deploy/azure-apps/index> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="ca09b-117">Aby uzyskać wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="ca09b-118">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="ca09b-118">Supported platforms</span></span>

<span data-ttu-id="ca09b-119">Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64).</span><span class="sxs-lookup"><span data-stu-id="ca09b-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="ca09b-120">Wdróż aplikację 32-bitową z 32-bitową (x86) zestaw .NET Core SDK, chyba że aplikacja:</span><span class="sxs-lookup"><span data-stu-id="ca09b-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="ca09b-121">Wymagana jest większa przestrzeń adresów pamięci wirtualnej dla aplikacji 64-bitowej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="ca09b-122">Wymaga większego rozmiaru stosu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="ca09b-123">Ma 64-bitowe zależności natywne.</span><span class="sxs-lookup"><span data-stu-id="ca09b-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="ca09b-124">Aplikacje opublikowane dla 32-bitowej (x86) muszą mieć włączone 32-bitowe dla ich pul aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="ca09b-125">Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie witryny usług IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="ca09b-126">Aby opublikować aplikację 64-bitową, należy użyć 64-bitowej (x64) zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ca09b-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="ca09b-127">64-bitowy środowisko uruchomieniowe musi być obecny w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="ca09b-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="ca09b-128">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="ca09b-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="ca09b-129">Model hostingu w procesie</span><span class="sxs-lookup"><span data-stu-id="ca09b-129">In-process hosting model</span></span>

<span data-ttu-id="ca09b-130">Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="ca09b-131">Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="ca09b-132">Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca09b-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca09b-133">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="ca09b-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="ca09b-134">Wykonuje inicjalizację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-134">Performs app initialization.</span></span>
  * <span data-ttu-id="ca09b-135">Ładuje [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="ca09b-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="ca09b-136">Wywołania `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="ca09b-137">Obsługuje okres istnienia żądania natywnego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="ca09b-138">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowaną w procesie aplikacją:</span><span class="sxs-lookup"><span data-stu-id="ca09b-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Moduł ASP.NET Core w scenariuszu hostingu w procesie](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="ca09b-140">Żądanie dociera do sieci Web do sterownika HTTP. sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="ca09b-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="ca09b-141">Sterownik kieruje natywne żądanie do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="ca09b-142">Moduł ASP.NET Core odbiera żądanie natywne i przekazuje go do serwera HTTP usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="ca09b-143">Serwer HTTP IIS jest implementacją serwera w procesie dla usług IIS, która konwertuje żądanie z natywnego na zarządzane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="ca09b-144">Po przetworzeniu żądania przez serwer HTTP IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="ca09b-145">Żądanie jest wysyłane do ASP.NET Core potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="ca09b-146">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="ca09b-147">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS za pośrednictwem serwera HTTP IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="ca09b-148">Program IIS wysyła odpowiedź do klienta, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="ca09b-149">Hosting w procesie jest zgodą na istniejące aplikacje.</span><span class="sxs-lookup"><span data-stu-id="ca09b-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="ca09b-150">Szablony sieci Web ASP.NET Core korzystają z modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="ca09b-151">`CreateDefaultBuilder`dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie przez wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> metody w celu uruchomienia [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji w procesie roboczym usług IIS (*w3wp. exe* lub *iisexpress. exe*).</span><span class="sxs-lookup"><span data-stu-id="ca09b-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="ca09b-152">Testy wydajności wskazują, że hostowanie aplikacji platformy .NET Core w procesie zapewnia znacznie wyższą przepływność żądań w porównaniu z obsługą żądań proxy poza procesem i [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ca09b-153">Aplikacje publikowane jako pojedynczy plik wykonywalny nie mogą zostać załadowane przez model hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="ca09b-154">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="ca09b-154">Out-of-process hosting model</span></span>

<span data-ttu-id="ca09b-155">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="ca09b-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="ca09b-156">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="ca09b-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ca09b-157">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca09b-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca09b-158">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="ca09b-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="ca09b-160">Żądania docierają do sieci Web do sterownika HTTP. sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="ca09b-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="ca09b-161">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="ca09b-162">Skonfigurowany port jest zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="ca09b-163">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="ca09b-164">Port losowy nie jest 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="ca09b-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="ca09b-165">Moduł ASP.NET Core określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="ca09b-166"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Rozszerzenie konfiguruje serwer do nasłuchiwania `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="ca09b-167">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ca09b-168">Moduł nie obsługuje przekazywania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="ca09b-169">Żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługi IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ca09b-170">Po poKestrel przez program żądania z modułu żądanie zostanie przekazane do ASP.NET Core potoku programu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca09b-171">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca09b-172">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca09b-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ca09b-173">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która przekazuje ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ca09b-174">Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ca09b-175">Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ca09b-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ca09b-176">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="ca09b-177">Włącz składniki IISIntegration</span><span class="sxs-lookup"><span data-stu-id="ca09b-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="ca09b-178">Podczas kompilowania hosta w `CreateHostBuilder` (*program.cs*), wywołaj, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Aby włączyć integrację usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="ca09b-179">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/generic-host#default-builder-settings> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="ca09b-180">Opcje usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-180">IIS options</span></span>

<span data-ttu-id="ca09b-181">**Model hostingu w procesie**</span><span class="sxs-lookup"><span data-stu-id="ca09b-181">**In-process hosting model**</span></span>

<span data-ttu-id="ca09b-182">Aby skonfigurować opcje serwera usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISServerOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="ca09b-183">Poniższy przykład wyłącza AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="ca09b-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="ca09b-184">Opcja</span><span class="sxs-lookup"><span data-stu-id="ca09b-184">Option</span></span>                         | <span data-ttu-id="ca09b-185">Domyślne</span><span class="sxs-lookup"><span data-stu-id="ca09b-185">Default</span></span> | <span data-ttu-id="ca09b-186">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="ca09b-186">Setting</span></span> |
| ---
<span data-ttu-id="ca09b-187">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-188">'Blazor'</span></span>
- <span data-ttu-id="ca09b-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-189">'Identity'</span></span>
- <span data-ttu-id="ca09b-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-191">'Razor'</span></span>
- <span data-ttu-id="ca09b-192">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-193">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-194">'Blazor'</span></span>
- <span data-ttu-id="ca09b-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-195">'Identity'</span></span>
- <span data-ttu-id="ca09b-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-197">'Razor'</span></span>
- <span data-ttu-id="ca09b-198">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-199">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-200">'Blazor'</span></span>
- <span data-ttu-id="ca09b-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-201">'Identity'</span></span>
- <span data-ttu-id="ca09b-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-203">'Razor'</span></span>
- <span data-ttu-id="ca09b-204">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-205">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-206">'Blazor'</span></span>
- <span data-ttu-id="ca09b-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-207">'Identity'</span></span>
- <span data-ttu-id="ca09b-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-209">'Razor'</span></span>
- <span data-ttu-id="ca09b-210">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-211">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-212">'Blazor'</span></span>
- <span data-ttu-id="ca09b-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-213">'Identity'</span></span>
- <span data-ttu-id="ca09b-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-215">'Razor'</span></span>
- <span data-ttu-id="ca09b-216">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-217">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-218">'Blazor'</span></span>
- <span data-ttu-id="ca09b-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-219">'Identity'</span></span>
- <span data-ttu-id="ca09b-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-221">'Razor'</span></span>
- <span data-ttu-id="ca09b-222">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-223">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-224">'Blazor'</span></span>
- <span data-ttu-id="ca09b-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-225">'Identity'</span></span>
- <span data-ttu-id="ca09b-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-227">'Razor'</span></span>
- <span data-ttu-id="ca09b-228">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-229">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-230">'Blazor'</span></span>
- <span data-ttu-id="ca09b-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-231">'Identity'</span></span>
- <span data-ttu-id="ca09b-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-233">'Razor'</span></span>
- <span data-ttu-id="ca09b-234">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-235">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-236">'Blazor'</span></span>
- <span data-ttu-id="ca09b-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-237">'Identity'</span></span>
- <span data-ttu-id="ca09b-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-239">'Razor'</span></span>
- <span data-ttu-id="ca09b-240">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-241">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-242">'Blazor'</span></span>
- <span data-ttu-id="ca09b-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-243">'Identity'</span></span>
- <span data-ttu-id="ca09b-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-245">'Razor'</span></span>
- <span data-ttu-id="ca09b-246">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-247">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-248">'Blazor'</span></span>
- <span data-ttu-id="ca09b-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-249">'Identity'</span></span>
- <span data-ttu-id="ca09b-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-251">'Razor'</span></span>
- <span data-ttu-id="ca09b-252">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-253">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-254">'Blazor'</span></span>
- <span data-ttu-id="ca09b-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-255">'Identity'</span></span>
- <span data-ttu-id="ca09b-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-257">'Razor'</span></span>
- <span data-ttu-id="ca09b-258">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-259">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-260">'Blazor'</span></span>
- <span data-ttu-id="ca09b-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-261">'Identity'</span></span>
- <span data-ttu-id="ca09b-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-263">'Razor'</span></span>
- <span data-ttu-id="ca09b-264">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-264">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-265">--------------- | :-----: | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-266">'Blazor'</span></span>
- <span data-ttu-id="ca09b-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-267">'Identity'</span></span>
- <span data-ttu-id="ca09b-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-269">'Razor'</span></span>
- <span data-ttu-id="ca09b-270">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-270">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-271">---- | | `AutomaticAuthentication`      | `true`  | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-272">Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ca09b-273">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ca09b-274">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-275">| | `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="ca09b-276">| | `AllowSynchronousIO`           | `false` | Czy synchroniczna operacja we/wy jest dozwolona dla `HttpContext.Request` i `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="ca09b-277">| | `MaxRequestBodySize`           | `30000000`  | Pobiera lub ustawia maksymalny rozmiar treści żądania dla `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="ca09b-278">Należy zauważyć, że same usługi IIS mają limit, `maxAllowedContentLength` który zostanie przetworzony przed `MaxRequestBodySize` zestawem w `IISServerOptions` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="ca09b-279">Zmiana `MaxRequestBodySize` nie wpłynie na `maxAllowedContentLength` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="ca09b-280">Aby zwiększyć `maxAllowedContentLength` , Dodaj wpis w *pliku Web. config* , aby ustawić `maxAllowedContentLength` wyższą wartość.</span><span class="sxs-lookup"><span data-stu-id="ca09b-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="ca09b-281">Aby uzyskać więcej informacji, zobacz [Konfiguracja](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="ca09b-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="ca09b-282">**Model hostingu poza procesem**</span><span class="sxs-lookup"><span data-stu-id="ca09b-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="ca09b-283">Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="ca09b-284">Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="ca09b-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="ca09b-285">Opcja</span><span class="sxs-lookup"><span data-stu-id="ca09b-285">Option</span></span>                         | <span data-ttu-id="ca09b-286">Domyślne</span><span class="sxs-lookup"><span data-stu-id="ca09b-286">Default</span></span> | <span data-ttu-id="ca09b-287">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="ca09b-287">Setting</span></span> |
| ---
<span data-ttu-id="ca09b-288">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-289">'Blazor'</span></span>
- <span data-ttu-id="ca09b-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-290">'Identity'</span></span>
- <span data-ttu-id="ca09b-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-292">'Razor'</span></span>
- <span data-ttu-id="ca09b-293">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-294">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-295">'Blazor'</span></span>
- <span data-ttu-id="ca09b-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-296">'Identity'</span></span>
- <span data-ttu-id="ca09b-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-298">'Razor'</span></span>
- <span data-ttu-id="ca09b-299">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-300">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-301">'Blazor'</span></span>
- <span data-ttu-id="ca09b-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-302">'Identity'</span></span>
- <span data-ttu-id="ca09b-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-304">'Razor'</span></span>
- <span data-ttu-id="ca09b-305">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-306">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-307">'Blazor'</span></span>
- <span data-ttu-id="ca09b-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-308">'Identity'</span></span>
- <span data-ttu-id="ca09b-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-310">'Razor'</span></span>
- <span data-ttu-id="ca09b-311">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-312">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-313">'Blazor'</span></span>
- <span data-ttu-id="ca09b-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-314">'Identity'</span></span>
- <span data-ttu-id="ca09b-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-316">'Razor'</span></span>
- <span data-ttu-id="ca09b-317">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-318">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-319">'Blazor'</span></span>
- <span data-ttu-id="ca09b-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-320">'Identity'</span></span>
- <span data-ttu-id="ca09b-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-322">'Razor'</span></span>
- <span data-ttu-id="ca09b-323">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-324">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-325">'Blazor'</span></span>
- <span data-ttu-id="ca09b-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-326">'Identity'</span></span>
- <span data-ttu-id="ca09b-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-328">'Razor'</span></span>
- <span data-ttu-id="ca09b-329">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-330">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-331">'Blazor'</span></span>
- <span data-ttu-id="ca09b-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-332">'Identity'</span></span>
- <span data-ttu-id="ca09b-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-334">'Razor'</span></span>
- <span data-ttu-id="ca09b-335">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-336">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-337">'Blazor'</span></span>
- <span data-ttu-id="ca09b-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-338">'Identity'</span></span>
- <span data-ttu-id="ca09b-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-340">'Razor'</span></span>
- <span data-ttu-id="ca09b-341">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-342">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-343">'Blazor'</span></span>
- <span data-ttu-id="ca09b-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-344">'Identity'</span></span>
- <span data-ttu-id="ca09b-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-346">'Razor'</span></span>
- <span data-ttu-id="ca09b-347">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-348">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-349">'Blazor'</span></span>
- <span data-ttu-id="ca09b-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-350">'Identity'</span></span>
- <span data-ttu-id="ca09b-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-352">'Razor'</span></span>
- <span data-ttu-id="ca09b-353">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-354">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-355">'Blazor'</span></span>
- <span data-ttu-id="ca09b-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-356">'Identity'</span></span>
- <span data-ttu-id="ca09b-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-358">'Razor'</span></span>
- <span data-ttu-id="ca09b-359">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-360">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-361">'Blazor'</span></span>
- <span data-ttu-id="ca09b-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-362">'Identity'</span></span>
- <span data-ttu-id="ca09b-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-364">'Razor'</span></span>
- <span data-ttu-id="ca09b-365">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-365">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-366">--------------- | :-----: | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-367">'Blazor'</span></span>
- <span data-ttu-id="ca09b-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-368">'Identity'</span></span>
- <span data-ttu-id="ca09b-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-370">'Razor'</span></span>
- <span data-ttu-id="ca09b-371">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-371">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-372">---- | | `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-373">Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ca09b-374">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ca09b-375">Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="ca09b-376">| | `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="ca09b-377">| | `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ca09b-378">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="ca09b-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ca09b-379">[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) i moduł ASP.NET Core są skonfigurowane do przesyłania dalej:</span><span class="sxs-lookup"><span data-stu-id="ca09b-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="ca09b-380">Schemat (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="ca09b-381">Zdalny adres IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="ca09b-382">[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) konfiguruje przekazane nagłówki pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="ca09b-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="ca09b-383">W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ca09b-384">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="ca09b-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="ca09b-385">plik Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-385">web.config file</span></span>

<span data-ttu-id="ca09b-386">Plik *Web. config* konfiguruje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="ca09b-387">Tworzenie, przekształcanie i publikowanie pliku *Web. config* jest obsługiwane przez obiekt docelowy MSBuild ( `_TransformWebConfig` ), gdy projekt jest publikowany.</span><span class="sxs-lookup"><span data-stu-id="ca09b-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="ca09b-388">Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="ca09b-389">Zestaw SDK jest ustawiany u góry pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="ca09b-390">Jeśli plik *Web. config* nie jest obecny w projekcie, plik zostanie utworzony przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przesunięcia do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="ca09b-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="ca09b-391">Jeśli plik *Web. config* znajduje się w projekcie, plik jest przekształcany przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przesunięcia do publikowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="ca09b-392">Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.</span><span class="sxs-lookup"><span data-stu-id="ca09b-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="ca09b-393">Plik *Web. config* może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="ca09b-394">Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="ca09b-395">Aby zapobiec transformacje pliku *Web. config* przez zestaw SDK sieci Web, należy użyć **\<IsTransformWebConfigDisabled>** właściwości w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="ca09b-396">Podczas wyłączania pliku zestawu SDK sieci Web, *processPath* i *argumenty* powinny być ustawiane ręcznie przez dewelopera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="ca09b-397">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="ca09b-398">Lokalizacja pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-398">web.config file location</span></span>

<span data-ttu-id="ca09b-399">Aby poprawnie skonfigurować [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , plik *Web. config* musi znajdować się w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="ca09b-400">Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="ca09b-401">Plik *Web. config* jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="ca09b-402">Poufne pliki znajdują się w ścieżce fizycznej aplikacji, takiej jak \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (Komentarze dokumentacji XML) i \* \<assembly> . deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="ca09b-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="ca09b-403">Gdy plik *Web. config* jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="ca09b-404">Jeśli brakuje pliku *Web. config* o nazwie lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie obsłużyć poufne pliki.</span><span class="sxs-lookup"><span data-stu-id="ca09b-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="ca09b-405">**Plik *Web. config* musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie należy usuwać pliku *Web. config* z wdrożenia produkcyjnego.**</span><span class="sxs-lookup"><span data-stu-id="ca09b-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="ca09b-406">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="ca09b-406">Transform web.config</span></span>

<span data-ttu-id="ca09b-407">Jeśli musisz przekształcić *plik Web. config* przy publikowaniu, zobacz <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="ca09b-408">Może być konieczne przekształcenie *pliku Web. config* przy publikowaniu w celu ustawienia zmiennych środowiskowych na podstawie konfiguracji, profilu lub środowiska.</span><span class="sxs-lookup"><span data-stu-id="ca09b-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="ca09b-409">Konfiguracja usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-409">IIS configuration</span></span>

<span data-ttu-id="ca09b-410">**Systemy operacyjne Windows Server**</span><span class="sxs-lookup"><span data-stu-id="ca09b-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="ca09b-411">Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.</span><span class="sxs-lookup"><span data-stu-id="ca09b-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="ca09b-412">Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="ca09b-413">W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="ca09b-415">Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="ca09b-416">Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.</span><span class="sxs-lookup"><span data-stu-id="ca09b-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="ca09b-418">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ca09b-419">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="ca09b-420">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ca09b-421">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ca09b-422">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ca09b-423">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ca09b-424">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="ca09b-425">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ca09b-426">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="ca09b-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ca09b-427">Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="ca09b-428">Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="ca09b-429">**Systemy operacyjne Windows dla komputerów stacjonarnych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="ca09b-430">Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ca09b-431">Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="ca09b-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="ca09b-432">Otwórz węzeł **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="ca09b-433">Otwórz węzeł **Narzędzia do zarządzania siecią Web** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="ca09b-434">Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="ca09b-435">Zaznacz pole wyboru **usług World Wide Web Services**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ca09b-436">Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="ca09b-437">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ca09b-438">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="ca09b-439">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ca09b-440">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ca09b-441">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ca09b-442">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ca09b-443">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="ca09b-444">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ca09b-445">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="ca09b-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ca09b-446">Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.</span><span class="sxs-lookup"><span data-stu-id="ca09b-446">If the IIS installation requires a restart, restart the system.</span></span>

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="ca09b-448">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="ca09b-449">Zainstaluj *pakiet hostingu platformy .NET Core* w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="ca09b-450">Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="ca09b-451">Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ca09b-452">Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny.</span><span class="sxs-lookup"><span data-stu-id="ca09b-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="ca09b-453">Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="ca09b-454">Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="ca09b-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="ca09b-455">Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="ca09b-456">Pobieranie bezpośrednie (aktualna wersja)</span><span class="sxs-lookup"><span data-stu-id="ca09b-456">Direct download (current version)</span></span>

<span data-ttu-id="ca09b-457">Pobierz instalatora przy użyciu następującego linku:</span><span class="sxs-lookup"><span data-stu-id="ca09b-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="ca09b-458">Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)</span><span class="sxs-lookup"><span data-stu-id="ca09b-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="ca09b-459">Starsze wersje Instalatora</span><span class="sxs-lookup"><span data-stu-id="ca09b-459">Earlier versions of the installer</span></span>

<span data-ttu-id="ca09b-460">Aby uzyskać wcześniejszą wersję Instalatora:</span><span class="sxs-lookup"><span data-stu-id="ca09b-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="ca09b-461">Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="ca09b-462">Wybierz żądaną wersję programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="ca09b-463">W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="ca09b-464">Pobierz instalatora przy użyciu linku do **pakietu hostingu** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="ca09b-465">Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ca09b-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="ca09b-466">Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="ca09b-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="ca09b-467">Zainstaluj pakiet hostingu</span><span class="sxs-lookup"><span data-stu-id="ca09b-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="ca09b-468">Uruchom Instalatora na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-468">Run the installer on the server.</span></span> <span data-ttu-id="ca09b-469">Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:</span><span class="sxs-lookup"><span data-stu-id="ca09b-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="ca09b-470">`OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="ca09b-471">`OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="ca09b-472">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca09b-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="ca09b-473">`OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="ca09b-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="ca09b-474">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca09b-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="ca09b-475">`OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86.</span><span class="sxs-lookup"><span data-stu-id="ca09b-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="ca09b-476">Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="ca09b-477">Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="ca09b-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="ca09b-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona (*ApplicationHost. config*) znajduje się na tym samym komputerze, na którym zainstalowano program IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="ca09b-479">*Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="ca09b-480">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="ca09b-481">Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ca09b-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="ca09b-482">Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.</span><span class="sxs-lookup"><span data-stu-id="ca09b-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="ca09b-483">ASP.NET Core nie przyjmuje zachowania z przekazaniem do przodu dla wersji poprawki współużytkowanych pakietów platformy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="ca09b-484">Po uaktualnieniu udostępnionej platformy, instalując nowy pakiet hostingu, ponownie uruchom system lub wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ca09b-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="ca09b-485">Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca09b-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="ca09b-486">Zainstaluj Web Deploy przy publikowaniu w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="ca09b-487">Podczas wdrażania aplikacji na serwerach z [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)Zainstaluj najnowszą wersję Web Deploy na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="ca09b-488">Aby zainstalować Web Deploy, należy użyć [Instalatora platformy sieci Web (Instalatora WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskać instalatora bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="ca09b-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="ca09b-489">Preferowaną metodą jest użycie Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="ca09b-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="ca09b-490">Instalatora WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="ca09b-491">Tworzenie witryny usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-491">Create the IIS site</span></span>

1. <span data-ttu-id="ca09b-492">W systemie hostingu Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="ca09b-493">W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="ca09b-494">Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="ca09b-495">W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="ca09b-496">Kliknij prawym przyciskiem myszy folder **sitess** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="ca09b-497">Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="ca09b-498">Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** folderu wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="ca09b-499">Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Podaj nazwę lokacji, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="ca09b-501">`http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ca09b-502">Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="ca09b-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="ca09b-503">Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ca09b-504">Używaj jawnych nazw hostów zamiast symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="ca09b-505">Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona).</span><span class="sxs-lookup"><span data-stu-id="ca09b-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ca09b-506">Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="ca09b-507">W węźle serwera wybierz pozycję **Pule aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="ca09b-508">Kliknij prawym przyciskiem myszy pulę aplikacji lokacji i wybierz pozycję **Ustawienia podstawowe** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="ca09b-509">W oknie **Edytowanie puli aplikacji** Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Nie ustawiaj kodu zarządzanego dla wersji środowiska .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="ca09b-511">ASP.NET Core działa w osobnym procesie i zarządza środowiskiem uruchomieniowym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="ca09b-512">ASP.NET Core nie polega na ładowaniu programu Desktop CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="ca09b-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="ca09b-513">Podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchamiane w celu hostowania aplikacji w procesie roboczym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="ca09b-514">Ustawienie **wersji środowiska .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="ca09b-515">*ASP.NET Core 2,2 lub nowszy*:</span><span class="sxs-lookup"><span data-stu-id="ca09b-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="ca09b-516">W przypadku 32-bitowego (x86) [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) opublikowanego z 32-bitowym zestawem SDK, który korzysta z [modelu hostingu w procesie](#in-process-hosting-model), należy włączyć pulę aplikacji dla 32-bitowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="ca09b-517">W Menedżerze usług IIS przejdź do **pul aplikacji** na pasku bocznym **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="ca09b-518">Wybierz pulę aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-518">Select the app's Application Pool.</span></span> <span data-ttu-id="ca09b-519">Na pasku bocznym **Akcje** wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="ca09b-520">Ustaw dla opcji **Włącz aplikacje 32-bitowe** `True` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="ca09b-521">W przypadku [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) z systemem 64-bitowym (x64), które używa [modelu hostingu w procesie](#in-process-hosting-model), wyłącz pulę aplikacji dla procesów 32-bitowych (x86).</span><span class="sxs-lookup"><span data-stu-id="ca09b-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="ca09b-522">W Menedżerze usług IIS przejdź do **pul aplikacji** na pasku bocznym **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="ca09b-523">Wybierz pulę aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-523">Select the app's Application Pool.</span></span> <span data-ttu-id="ca09b-524">Na pasku bocznym **Akcje** wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="ca09b-525">Ustaw dla opcji **Włącz aplikacje 32-bitowe** `False` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="ca09b-526">Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="ca09b-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="ca09b-527">Jeśli domyślna tożsamość puli aplikacji (**model procesów**  >  **Identity** ) została zmieniona z **ApplicationPoolIdentity** na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="ca09b-528">Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.</span><span class="sxs-lookup"><span data-stu-id="ca09b-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="ca09b-529">**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="ca09b-530">Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="ca09b-531">Wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-531">Deploy the app</span></span>

<span data-ttu-id="ca09b-532">Wdróż aplikację w folderze **ścieżki fizycznej** usług IIS, który został ustanowiony w sekcji [Tworzenie witryny usług IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="ca09b-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przeniesienia aplikacji z folderu *publikowania* projektu do folderu wdrożenia systemu hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="ca09b-534">Web Deploy z programem Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="ca09b-535">Aby dowiedzieć się, jak utworzyć profil publikowania do użytku z usługą Web Deploy, zobacz temat [Profile publikacji programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="ca09b-536">Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia aplikacji, pobierz swój profil i zaimportuj go za pomocą okna dialogowego **publikowania** programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ca09b-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Strona okna dialogowego publikowanie](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="ca09b-538">Web Deploy poza programem Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="ca09b-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) można również użyć poza programem Visual Studio z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ca09b-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="ca09b-540">Aby uzyskać więcej informacji, zobacz [Narzędzie do wdrażania w sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="ca09b-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="ca09b-541">Alternatywy dla Web Deploy</span><span class="sxs-lookup"><span data-stu-id="ca09b-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="ca09b-542">Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingu, takich jak ręczna kopia, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="ca09b-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="ca09b-543">Aby uzyskać więcej informacji na ASP.NET Core wdrażania usług IIS, zobacz sekcję [zasoby wdrażania dla administratorów usług IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="ca09b-544">Przeglądanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="ca09b-544">Browse the website</span></span>

<span data-ttu-id="ca09b-545">Po wdrożeniu aplikacji w systemie hostingu należy wysłać żądanie do jednej z publicznych punktów końcowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="ca09b-546">W poniższym przykładzie lokacja jest powiązana z **nazwą hosta** usług IIS `www.mysite.com` na **porcie** `80` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="ca09b-547">Zostanie wysłane żądanie `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="ca09b-547">A request is made to `http://www.mysite.com`:</span></span>

![Przeglądarka Microsoft Edge załadowała stronę startową usług IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="ca09b-549">Zablokowane pliki wdrożenia</span><span class="sxs-lookup"><span data-stu-id="ca09b-549">Locked deployment files</span></span>

<span data-ttu-id="ca09b-550">Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="ca09b-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="ca09b-551">Nie można zastąpić zablokowanych plików podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="ca09b-552">Aby zwolnić zablokowane pliki we wdrożeniu, Zatrzymaj pulę aplikacji, korzystając z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="ca09b-553">Użyj Web Deploy i Reference `Microsoft.NET.Sdk.Web` w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="ca09b-554">Plik *app_offline. htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="ca09b-555">Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline. htm* podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="ca09b-556">Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="ca09b-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="ca09b-557">Ręcznie Zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="ca09b-558">Porzuć *app_offline. htm* przy użyciu programu PowerShell (wymaga programu PowerShell 5 lub nowszego):</span><span class="sxs-lookup"><span data-stu-id="ca09b-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="ca09b-559">Ochrona danych</span><span class="sxs-lookup"><span data-stu-id="ca09b-559">Data protection</span></span>

<span data-ttu-id="ca09b-560">[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="ca09b-561">Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="ca09b-562">Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="ca09b-563">Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="ca09b-564">Wszystkie tokeny uwierzytelniania na podstawie plików cookie są unieważnione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="ca09b-565">Użytkownicy muszą ponownie zalogować się przy następnym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="ca09b-566">Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza.</span><span class="sxs-lookup"><span data-stu-id="ca09b-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="ca09b-567">Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) [ASP.NET Core i pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="ca09b-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="ca09b-568">Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="ca09b-569">**Tworzenie kluczy rejestru ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="ca09b-570">Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="ca09b-571">Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="ca09b-572">W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt programu PowerShell provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) dla każdej puli aplikacji używanej z aplikacją ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="ca09b-573">Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="ca09b-574">Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="ca09b-575">W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="ca09b-576">Domyślnie klucze ochrony danych nie są szyfrowane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="ca09b-577">Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym jest uruchamiana aplikacja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="ca09b-578">Certyfikat x509 może służyć do ochrony kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="ca09b-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="ca09b-579">Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca09b-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="ca09b-580">Szczegóły można znaleźć w temacie [Konfigurowanie ochrony danych ASP.NET Core](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="ca09b-581">**Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**</span><span class="sxs-lookup"><span data-stu-id="ca09b-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="ca09b-582">To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="ca09b-583">Ustaw **Załaduj profil użytkownika** na `True` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="ca09b-584">Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca09b-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="ca09b-585">Klucze są utrwalane w folderze *% LocalAppData%/ASP.NET/dataprotection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="ca09b-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="ca09b-586">[Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="ca09b-587">Wartość domyślna `setProfileEnvironment` to `true` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="ca09b-588">W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="ca09b-589">Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:</span><span class="sxs-lookup"><span data-stu-id="ca09b-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="ca09b-590">Przejdź do folderu *% windir%/system32/inetsrv/config*</span><span class="sxs-lookup"><span data-stu-id="ca09b-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="ca09b-591">Otwórz plik *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="ca09b-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="ca09b-592">Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span><span class="sxs-lookup"><span data-stu-id="ca09b-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="ca09b-593">Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="ca09b-594">**Używanie systemu plików jako magazynu kluczy**</span><span class="sxs-lookup"><span data-stu-id="ca09b-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="ca09b-595">Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="ca09b-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="ca09b-596">Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="ca09b-597">Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="ca09b-598">W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:</span><span class="sxs-lookup"><span data-stu-id="ca09b-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="ca09b-599">Użyj udziału plików, do którego mają dostęp wszystkie maszyny.</span><span class="sxs-lookup"><span data-stu-id="ca09b-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="ca09b-600">Wdróż certyfikat x509 na każdym komputerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="ca09b-601">Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="ca09b-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="ca09b-602">**Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="ca09b-603">System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="ca09b-604">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="ca09b-605">Katalogi wirtualne</span><span class="sxs-lookup"><span data-stu-id="ca09b-605">Virtual Directories</span></span>

<span data-ttu-id="ca09b-606">[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="ca09b-607">Aplikacja może być hostowana jako [podaplikacja](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="ca09b-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="ca09b-608">Aplikacje podrzędne</span><span class="sxs-lookup"><span data-stu-id="ca09b-608">Sub-applications</span></span>

<span data-ttu-id="ca09b-609">Aplikacja ASP.NET Core może być hostowana jako [podaplikacja usług IIS (podrzędna)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="ca09b-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="ca09b-610">Ścieżka aplikacji podrzędnej jest częścią adresu URL aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="ca09b-611">Linki do zasobów statycznych w aplikacji podrzędnej powinny używać notacji z ukośnikiem ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="ca09b-612">Notacja "ukośnik" wyzwala [pomocnika tagów](xref:mvc/views/tag-helpers/intro) , aby dołączyć pathbase podaplikacji do renderowanego linku względnego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="ca09b-613">W przypadku aplikacji podrzędnej w `/subapp_path` programie obraz połączony z programem `src="~/image.png"` jest renderowany jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="ca09b-614">Oprogramowanie pośredniczące aplikacji głównej nie przetwarza żądania pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="ca09b-615">Żądanie jest przetwarzane przez oprogramowanie pośredniczące plików statycznych aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="ca09b-616">Jeśli atrybut statycznego zasobu `src` jest ustawiony na ścieżkę bezwzględną (na przykład `src="/image.png"` ), link jest renderowany bez pathbase podaplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="ca09b-617">Oprogramowanie pośredniczące pliku statycznego aplikacji głównej próbuje obsłużyć zasób z poziomu [głównego katalogu sieci Web](xref:fundamentals/index#web-root)aplikacji głównej, co spowoduje, że odpowiedź na *404 nie zostanie znaleziona* , chyba że statyczny zasób jest dostępny z poziomu aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="ca09b-618">Aby hostować aplikację ASP.NET Core jako aplikację podrzędną w innej aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca09b-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="ca09b-619">Ustanów pulę aplikacji dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="ca09b-620">Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego** , ponieważ podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchomione w celu hostowania aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="ca09b-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="ca09b-621">Dodaj lokację główną w Menedżerze usług IIS przy użyciu aplikacji podrzędnej w folderze w lokacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="ca09b-622">Kliknij prawym przyciskiem myszy folder subapp w Menedżerze usług IIS, a następnie wybierz polecenie **Konwertuj na aplikację**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="ca09b-623">W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="ca09b-624">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-624">Select **OK**.</span></span>

<span data-ttu-id="ca09b-625">Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="ca09b-626">Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="ca09b-627">Konfiguracja usług IIS z pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="ca09b-628">Na konfigurację usług IIS wpływa `<system.webServer>` sekcja *pliku Web. config* dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji z modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="ca09b-629">Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="ca09b-630">Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w pliku *Web. config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="ca09b-631">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="ca09b-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="ca09b-632">Dokumentacja konfiguracyjna programu\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="ca09b-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="ca09b-633">Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwanych w przypadku usług IIS 10,0 lub nowszych), zobacz sekcję *polecenie Appcmd. exe* w temacie [ \<environmentVariables> zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej programu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="ca09b-634">Sekcje konfiguracji pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-634">Configuration sections of web.config</span></span>

<span data-ttu-id="ca09b-635">Sekcje konfiguracji aplikacji ASP.NET 4. x w *pliku Web. config* nie są używane przez aplikacje ASP.NET Core na potrzeby konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="ca09b-636">Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="ca09b-637">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ca09b-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="ca09b-638">Pule aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-638">Application Pools</span></span>

<span data-ttu-id="ca09b-639">Izolacja puli aplikacji jest określana przez model hostingu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="ca09b-640">Hosting w procesie: aplikacje muszą być uruchamiane w oddzielnych pulach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="ca09b-641">Hosting poza procesem: zalecamy izolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="ca09b-642">Okno dialogowe **Dodaj witrynę sieci Web** usług IIS domyślnie umożliwia pojedynczej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="ca09b-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="ca09b-643">Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="ca09b-644">Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="ca09b-645">Pula aplikacjiIdentity</span><span class="sxs-lookup"><span data-stu-id="ca09b-645">Application Pool Identity</span></span>

<span data-ttu-id="ca09b-646">Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi.</span><span class="sxs-lookup"><span data-stu-id="ca09b-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="ca09b-647">W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta.</span><span class="sxs-lookup"><span data-stu-id="ca09b-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="ca09b-648">W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

<span data-ttu-id="ca09b-650">Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ca09b-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="ca09b-651">Zasoby mogą być zabezpieczone przy użyciu tej tożsamości.</span><span class="sxs-lookup"><span data-stu-id="ca09b-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="ca09b-652">Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ca09b-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="ca09b-653">Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:</span><span class="sxs-lookup"><span data-stu-id="ca09b-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="ca09b-654">Otwórz Eksploratora Windows i przejdź do katalogu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="ca09b-655">Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="ca09b-656">Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="ca09b-657">Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.</span><span class="sxs-lookup"><span data-stu-id="ca09b-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="ca09b-658">Wprowadź **<usług IIS puli aplikacji \\ app_pool_name>** w polu **Wprowadź nazwy obiektów do wybrania** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="ca09b-659">Wybierz przycisk **Sprawdź nazwy** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-659">Select the **Check Names** button.</span></span> <span data-ttu-id="ca09b-660">W obszarze *Domyślna pula aplikacji* Sprawdź nazwy za pomocą **usług IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="ca09b-661">Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów zostanie wykazana wartość **Domyślna pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="ca09b-662">Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów.</span><span class="sxs-lookup"><span data-stu-id="ca09b-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="ca09b-663">Podczas sprawdzania nazwy obiektu Użyj **<IIS puli aplikacji \\ app_pool_name>** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="ca09b-665">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-665">Select **OK**.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: po wybraniu pozycji "Sprawdź nazwy" w obszarze nazwy obiektów zostanie wyświetlona nazwa obiektu "domyślna pula aplikacji".](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="ca09b-667">&amp;Uprawnienia do wykonywania odczytu powinny być przyznawane domyślnie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="ca09b-668">Podaj dodatkowe uprawnienia zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="ca09b-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="ca09b-669">Dostęp można także udzielić w wierszu polecenia przy użyciu narzędzia **icacls** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="ca09b-670">Użycie funkcji *Domyślna pula aplikacji* jako przykładu powoduje użycie następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="ca09b-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="ca09b-671">Aby uzyskać więcej informacji, zobacz temat [icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ca09b-672">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ca09b-672">HTTP/2 support</span></span>

<span data-ttu-id="ca09b-673">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="ca09b-674">W procesie</span><span class="sxs-lookup"><span data-stu-id="ca09b-674">In-process</span></span>
  * <span data-ttu-id="ca09b-675">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca09b-676">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ca09b-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="ca09b-677">Pozaprocesowe</span><span class="sxs-lookup"><span data-stu-id="ca09b-677">Out-of-process</span></span>
  * <span data-ttu-id="ca09b-678">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca09b-679">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ca09b-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ca09b-680">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ca09b-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ca09b-681">W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 w raportach [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="ca09b-682">W przypadku wdrożenia poza procesem, gdy jest nawiązywane połączenie HTTP/2, raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="ca09b-683">Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ca09b-684">Protokół HTTP/2 jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ca09b-685">Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="ca09b-686">Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ca09b-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="ca09b-687">Żądania inspekcji wstępnej CORS</span><span class="sxs-lookup"><span data-stu-id="ca09b-687">CORS preflight requests</span></span>

<span data-ttu-id="ca09b-688">*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="ca09b-689">W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="ca09b-690">Aby dowiedzieć się, jak skonfigurować obsługę usług IIS aplikacji w *pliku Web. config* w celu przekazywania żądań dotyczących opcji, zobacz [Włączanie żądań między źródłami w programie ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="ca09b-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="ca09b-691">Moduł inicjalizacji aplikacji i limit czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="ca09b-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="ca09b-692">Hostowane w usługach IIS przez moduł ASP.NET Core w wersji 2:</span><span class="sxs-lookup"><span data-stu-id="ca09b-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="ca09b-693">[Moduł inicjalizacji aplikacji](#application-initialization-module): hostowana [w procesie](#in-process-hosting-model) lub [poza procesem](#out-of-process-hosting-model) aplikacja może zostać skonfigurowana do automatycznego uruchamiania na potrzeby ponownego uruchomienia procesu roboczego lub ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="ca09b-694">[Limit czasu bezczynności](#idle-timeout): hostowana [w trakcie](#in-process-hosting-model) działania aplikacji można skonfigurować w taki sposób, aby nie przekroczyć limitu czasu podczas okresów braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="ca09b-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="ca09b-695">Moduł inicjalizacji aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-695">Application Initialization Module</span></span>

<span data-ttu-id="ca09b-696">*Dotyczy aplikacji hostowanych w procesie i pozaprocesowe.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="ca09b-697">[Inicjowanie aplikacji IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła do aplikacji żądanie HTTP, gdy pula aplikacji zostanie uruchomiona lub zostanie odtworzona.</span><span class="sxs-lookup"><span data-stu-id="ca09b-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="ca09b-698">Żądanie wyzwala uruchomienie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-698">The request triggers the app to start.</span></span> <span data-ttu-id="ca09b-699">Domyślnie usługi IIS wysyłają żądanie do głównego adresu URL aplikacji ( `/` ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby](#application-initialization-module-and-idle-timeout-additional-resources) , aby uzyskać więcej informacji na temat konfiguracji).</span><span class="sxs-lookup"><span data-stu-id="ca09b-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="ca09b-700">Upewnij się, że funkcja inicjowania roli inicjalizacji aplikacji IIS jest włączona:</span><span class="sxs-lookup"><span data-stu-id="ca09b-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="ca09b-701">Na komputerach z systemem Windows 7 lub nowszym w przypadku lokalnego korzystania z usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="ca09b-702">Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="ca09b-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ca09b-703">Otwórz **Internet Information Services** > **World Wide Web Services** > **funkcje projektowania aplikacji**Internet Information Services World Wide Web Services.</span><span class="sxs-lookup"><span data-stu-id="ca09b-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="ca09b-704">Zaznacz pole wyboru dla **inicjowania aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="ca09b-705">W systemie Windows Server 2008 R2 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="ca09b-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="ca09b-706">Otwórz **Kreatora dodawania ról i funkcji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="ca09b-707">W panelu **Wybierz usługi ról** Otwórz węzeł **Programowanie aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="ca09b-708">Zaznacz pole wyboru dla **inicjowania aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="ca09b-709">Użyj jednego z poniższych metod, aby włączyć moduł inicjowania aplikacji dla lokacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="ca09b-710">Za pomocą Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="ca09b-711">W panelu **połączenia** wybierz pozycję **Pule aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="ca09b-712">Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="ca09b-713">Domyślny **tryb uruchamiania** to **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="ca09b-714">Ustaw **tryb uruchamiania** na **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="ca09b-715">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-715">Select **OK**.</span></span>
  1. <span data-ttu-id="ca09b-716">Otwórz węzeł **Lokacje** w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="ca09b-717">Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję Zarządzaj ustawieniami zaawansowanymi **witryny sieci Web** > **Advanced Settings**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="ca09b-718">Domyślnym ustawieniem **wstępnego ładowania** jest **wartość false**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="ca09b-719">Ustaw dla opcji **wstępnego ładowania** **wartość true**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="ca09b-720">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-720">Select **OK**.</span></span>

* <span data-ttu-id="ca09b-721">Korzystając z pliku *Web. config*, Dodaj `<applicationInitialization>` element z `doAppInitAfterRestart` ustawionym do `true` `<system.webServer>` elementów w plik *Web. config* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="ca09b-722">Limit czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="ca09b-722">Idle Timeout</span></span>

<span data-ttu-id="ca09b-723">*Dotyczy tylko aplikacji hostowanych w procesie.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="ca09b-724">Aby zapobiec przekroczeniu przez aplikację, należy ustawić limit czasu bezczynności puli aplikacji przy użyciu Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="ca09b-725">W panelu **połączenia** wybierz pozycję **Pule aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="ca09b-726">Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="ca09b-727">Domyślny **limit czasu bezczynności (w minutach)** wynosi **20** minut.</span><span class="sxs-lookup"><span data-stu-id="ca09b-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="ca09b-728">Ustaw **limit czasu bezczynności (w minutach)** na **0** (zero).</span><span class="sxs-lookup"><span data-stu-id="ca09b-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="ca09b-729">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-729">Select **OK**.</span></span>
1. <span data-ttu-id="ca09b-730">Odtwórz proces roboczy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-730">Recycle the worker process.</span></span>

<span data-ttu-id="ca09b-731">Aby zapobiec przekroczeniu limitu [czasu hostowanych przez aplikacje](#out-of-process-hosting-model) aplikacji, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="ca09b-732">Wyślij polecenie ping do aplikacji z zewnętrznej usługi, aby było ono uruchomione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="ca09b-733">Jeśli aplikacja obsługuje tylko usługi w tle, należy unikać hostingu usług IIS i używać [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="ca09b-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="ca09b-734">Dodatkowe zasoby dotyczące modułu inicjalizacji aplikacji i limitu czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="ca09b-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="ca09b-735">Inicjowanie aplikacji usług IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="ca09b-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="ca09b-736">[Inicjowanie \<applicationInitialization> aplikacji ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="ca09b-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="ca09b-737">[Ustawienia modelu procesów dla \<processModel> puli aplikacji ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="ca09b-738">Zasoby wdrażania dla administratorów usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="ca09b-739">Dokumentacja usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="ca09b-740">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="ca09b-741">Wdrażanie aplikacji .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="ca09b-742">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ca09b-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="ca09b-743">Oficjalna witryna programu Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="ca09b-744">Biblioteka zawartości technicznej systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="ca09b-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="ca09b-745">Protokół HTTP/2 w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ca09b-746">Aby zapoznać się z samouczkiem dotyczącym publikowania aplikacji ASP.NET Core na serwerze usług IIS, zobacz <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="ca09b-747">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="ca09b-748">Obsługiwane systemy operacyjne</span><span class="sxs-lookup"><span data-stu-id="ca09b-748">Supported operating systems</span></span>

<span data-ttu-id="ca09b-749">Obsługiwane są następujące systemy operacyjne:</span><span class="sxs-lookup"><span data-stu-id="ca09b-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="ca09b-750">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-750">Windows 7 or later</span></span>
* <span data-ttu-id="ca09b-751">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ca09b-752">[Serwer http. sys](xref:fundamentals/servers/httpsys) (znany wcześniej jako webListener) nie działa w konfiguracji zwrotnego serwera proxy z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="ca09b-753">Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ca09b-754">Aby uzyskać informacje na temat hostingu na platformie Azure, zobacz <xref:host-and-deploy/azure-apps/index> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="ca09b-755">Aby uzyskać wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="ca09b-756">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="ca09b-756">Supported platforms</span></span>

<span data-ttu-id="ca09b-757">Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64).</span><span class="sxs-lookup"><span data-stu-id="ca09b-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="ca09b-758">Wdróż aplikację 32-bitową z 32-bitową (x86) zestaw .NET Core SDK, chyba że aplikacja:</span><span class="sxs-lookup"><span data-stu-id="ca09b-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="ca09b-759">Wymagana jest większa przestrzeń adresów pamięci wirtualnej dla aplikacji 64-bitowej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="ca09b-760">Wymaga większego rozmiaru stosu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="ca09b-761">Ma 64-bitowe zależności natywne.</span><span class="sxs-lookup"><span data-stu-id="ca09b-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="ca09b-762">Aby opublikować aplikację 64-bitową, należy użyć 64-bitowej (x64) zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ca09b-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="ca09b-763">64-bitowy środowisko uruchomieniowe musi być obecny w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="ca09b-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="ca09b-764">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="ca09b-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="ca09b-765">Model hostingu w procesie</span><span class="sxs-lookup"><span data-stu-id="ca09b-765">In-process hosting model</span></span>

<span data-ttu-id="ca09b-766">Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="ca09b-767">Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu poza procesem, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="ca09b-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="ca09b-768">Żądania nie są przekazywane za pośrednictwem karty sprzężenia zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="ca09b-769">Karta sprzężenia zwrotnego jest interfejsem sieciowym, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="ca09b-770">Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca09b-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca09b-771">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="ca09b-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="ca09b-772">Wykonuje inicjalizację aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-772">Performs app initialization.</span></span>
  * <span data-ttu-id="ca09b-773">Ładuje [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="ca09b-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="ca09b-774">Wywołania `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="ca09b-775">Obsługuje okres istnienia żądania natywnego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="ca09b-776">Model hostingu w procesie nie jest obsługiwany w przypadku aplikacji ASP.NET Core przeznaczonych dla .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ca09b-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="ca09b-777">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowaną w procesie aplikacją:</span><span class="sxs-lookup"><span data-stu-id="ca09b-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Moduł ASP.NET Core w scenariuszu hostingu w procesie](index/_static/ancm-inprocess.png)

<span data-ttu-id="ca09b-779">Żądanie dociera do sieci Web do sterownika HTTP. sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="ca09b-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ca09b-780">Sterownik kieruje natywne żądanie do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ca09b-781">Moduł ASP.NET Core odbiera żądanie natywne i przekazuje go do serwera HTTP usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="ca09b-782">Serwer HTTP IIS jest implementacją serwera w procesie dla usług IIS, która konwertuje żądanie z natywnego na zarządzane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="ca09b-783">Po przetworzeniu żądania przez serwer HTTP IIS żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca09b-784">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca09b-785">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS za pośrednictwem serwera HTTP IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="ca09b-786">Program IIS wysyła odpowiedź do klienta, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="ca09b-787">Hosting w procesie jest nieobecny w przypadku istniejących aplikacji, ale w przypadku wszystkich scenariuszy z usługami w ramach programu z obsługą IIS Express usług w toku są domyślnie obsługiwane [nowe](/dotnet/core/tools/dotnet-new) szablony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="ca09b-788">`CreateDefaultBuilder`dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie przez wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> metody w celu uruchomienia [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji w procesie roboczym usług IIS (*w3wp. exe* lub *iisexpress. exe*).</span><span class="sxs-lookup"><span data-stu-id="ca09b-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="ca09b-789">Testy wydajności wskazują, że hostowanie aplikacji platformy .NET Core w procesie zapewnia znacznie wyższą przepływność żądań w porównaniu z obsługą żądań serwera proxy poza procesem i serwerem [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="ca09b-790">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="ca09b-790">Out-of-process hosting model</span></span>

<span data-ttu-id="ca09b-791">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="ca09b-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="ca09b-792">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="ca09b-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ca09b-793">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca09b-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca09b-794">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="ca09b-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

<span data-ttu-id="ca09b-796">Żądania docierają do sieci Web do sterownika HTTP. sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="ca09b-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ca09b-797">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ca09b-798">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="ca09b-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="ca09b-799">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> rozszerzenie konfiguruje serwer do nasłuchiwania `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="ca09b-800">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ca09b-801">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ca09b-802">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca09b-803">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca09b-804">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca09b-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ca09b-805">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ca09b-806">Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ca09b-807">Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ca09b-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ca09b-808">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="ca09b-809">Włącz składniki IISIntegration</span><span class="sxs-lookup"><span data-stu-id="ca09b-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="ca09b-810">Podczas kompilowania hosta w `CreateWebHostBuilder` (*program.cs*), wywołaj, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> Aby włączyć integrację usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="ca09b-811">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="ca09b-812">Opcje usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-812">IIS options</span></span>

<span data-ttu-id="ca09b-813">**Model hostingu w procesie**</span><span class="sxs-lookup"><span data-stu-id="ca09b-813">**In-process hosting model**</span></span>

<span data-ttu-id="ca09b-814">Aby skonfigurować opcje serwera usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISServerOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="ca09b-815">Poniższy przykład wyłącza AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="ca09b-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="ca09b-816">Opcja</span><span class="sxs-lookup"><span data-stu-id="ca09b-816">Option</span></span>                         | <span data-ttu-id="ca09b-817">Domyślne</span><span class="sxs-lookup"><span data-stu-id="ca09b-817">Default</span></span> | <span data-ttu-id="ca09b-818">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="ca09b-818">Setting</span></span> |
| ---
<span data-ttu-id="ca09b-819">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-820">'Blazor'</span></span>
- <span data-ttu-id="ca09b-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-821">'Identity'</span></span>
- <span data-ttu-id="ca09b-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-823">'Razor'</span></span>
- <span data-ttu-id="ca09b-824">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-825">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-826">'Blazor'</span></span>
- <span data-ttu-id="ca09b-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-827">'Identity'</span></span>
- <span data-ttu-id="ca09b-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-829">'Razor'</span></span>
- <span data-ttu-id="ca09b-830">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-831">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-832">'Blazor'</span></span>
- <span data-ttu-id="ca09b-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-833">'Identity'</span></span>
- <span data-ttu-id="ca09b-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-835">'Razor'</span></span>
- <span data-ttu-id="ca09b-836">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-837">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-838">'Blazor'</span></span>
- <span data-ttu-id="ca09b-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-839">'Identity'</span></span>
- <span data-ttu-id="ca09b-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-841">'Razor'</span></span>
- <span data-ttu-id="ca09b-842">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-843">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-844">'Blazor'</span></span>
- <span data-ttu-id="ca09b-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-845">'Identity'</span></span>
- <span data-ttu-id="ca09b-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-847">'Razor'</span></span>
- <span data-ttu-id="ca09b-848">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-849">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-850">'Blazor'</span></span>
- <span data-ttu-id="ca09b-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-851">'Identity'</span></span>
- <span data-ttu-id="ca09b-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-853">'Razor'</span></span>
- <span data-ttu-id="ca09b-854">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-855">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-856">'Blazor'</span></span>
- <span data-ttu-id="ca09b-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-857">'Identity'</span></span>
- <span data-ttu-id="ca09b-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-859">'Razor'</span></span>
- <span data-ttu-id="ca09b-860">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-861">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-862">'Blazor'</span></span>
- <span data-ttu-id="ca09b-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-863">'Identity'</span></span>
- <span data-ttu-id="ca09b-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-865">'Razor'</span></span>
- <span data-ttu-id="ca09b-866">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-867">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-868">'Blazor'</span></span>
- <span data-ttu-id="ca09b-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-869">'Identity'</span></span>
- <span data-ttu-id="ca09b-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-871">'Razor'</span></span>
- <span data-ttu-id="ca09b-872">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-873">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-874">'Blazor'</span></span>
- <span data-ttu-id="ca09b-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-875">'Identity'</span></span>
- <span data-ttu-id="ca09b-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-877">'Razor'</span></span>
- <span data-ttu-id="ca09b-878">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-879">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-880">'Blazor'</span></span>
- <span data-ttu-id="ca09b-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-881">'Identity'</span></span>
- <span data-ttu-id="ca09b-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-883">'Razor'</span></span>
- <span data-ttu-id="ca09b-884">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-885">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-886">'Blazor'</span></span>
- <span data-ttu-id="ca09b-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-887">'Identity'</span></span>
- <span data-ttu-id="ca09b-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-889">'Razor'</span></span>
- <span data-ttu-id="ca09b-890">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-891">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-892">'Blazor'</span></span>
- <span data-ttu-id="ca09b-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-893">'Identity'</span></span>
- <span data-ttu-id="ca09b-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-895">'Razor'</span></span>
- <span data-ttu-id="ca09b-896">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-896">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-897">--------------- | :-----: | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-898">'Blazor'</span></span>
- <span data-ttu-id="ca09b-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-899">'Identity'</span></span>
- <span data-ttu-id="ca09b-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-901">'Razor'</span></span>
- <span data-ttu-id="ca09b-902">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-902">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-903">---- | | `AutomaticAuthentication`      | `true`  | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-904">Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ca09b-905">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ca09b-906">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-907">| | `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="ca09b-908">**Model hostingu poza procesem**</span><span class="sxs-lookup"><span data-stu-id="ca09b-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="ca09b-909">Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="ca09b-910">Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="ca09b-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="ca09b-911">Opcja</span><span class="sxs-lookup"><span data-stu-id="ca09b-911">Option</span></span>                         | <span data-ttu-id="ca09b-912">Domyślne</span><span class="sxs-lookup"><span data-stu-id="ca09b-912">Default</span></span> | <span data-ttu-id="ca09b-913">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="ca09b-913">Setting</span></span> |
| ---
<span data-ttu-id="ca09b-914">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-915">'Blazor'</span></span>
- <span data-ttu-id="ca09b-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-916">'Identity'</span></span>
- <span data-ttu-id="ca09b-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-918">'Razor'</span></span>
- <span data-ttu-id="ca09b-919">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-920">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-921">'Blazor'</span></span>
- <span data-ttu-id="ca09b-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-922">'Identity'</span></span>
- <span data-ttu-id="ca09b-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-924">'Razor'</span></span>
- <span data-ttu-id="ca09b-925">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-926">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-927">'Blazor'</span></span>
- <span data-ttu-id="ca09b-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-928">'Identity'</span></span>
- <span data-ttu-id="ca09b-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-930">'Razor'</span></span>
- <span data-ttu-id="ca09b-931">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-932">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-933">'Blazor'</span></span>
- <span data-ttu-id="ca09b-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-934">'Identity'</span></span>
- <span data-ttu-id="ca09b-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-936">'Razor'</span></span>
- <span data-ttu-id="ca09b-937">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-938">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-939">'Blazor'</span></span>
- <span data-ttu-id="ca09b-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-940">'Identity'</span></span>
- <span data-ttu-id="ca09b-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-942">'Razor'</span></span>
- <span data-ttu-id="ca09b-943">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-944">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-945">'Blazor'</span></span>
- <span data-ttu-id="ca09b-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-946">'Identity'</span></span>
- <span data-ttu-id="ca09b-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-948">'Razor'</span></span>
- <span data-ttu-id="ca09b-949">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-950">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-951">'Blazor'</span></span>
- <span data-ttu-id="ca09b-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-952">'Identity'</span></span>
- <span data-ttu-id="ca09b-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-954">'Razor'</span></span>
- <span data-ttu-id="ca09b-955">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-956">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-957">'Blazor'</span></span>
- <span data-ttu-id="ca09b-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-958">'Identity'</span></span>
- <span data-ttu-id="ca09b-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-960">'Razor'</span></span>
- <span data-ttu-id="ca09b-961">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-962">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-963">'Blazor'</span></span>
- <span data-ttu-id="ca09b-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-964">'Identity'</span></span>
- <span data-ttu-id="ca09b-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-966">'Razor'</span></span>
- <span data-ttu-id="ca09b-967">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-968">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-969">'Blazor'</span></span>
- <span data-ttu-id="ca09b-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-970">'Identity'</span></span>
- <span data-ttu-id="ca09b-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-972">'Razor'</span></span>
- <span data-ttu-id="ca09b-973">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-974">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-975">'Blazor'</span></span>
- <span data-ttu-id="ca09b-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-976">'Identity'</span></span>
- <span data-ttu-id="ca09b-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-978">'Razor'</span></span>
- <span data-ttu-id="ca09b-979">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-980">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-981">'Blazor'</span></span>
- <span data-ttu-id="ca09b-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-982">'Identity'</span></span>
- <span data-ttu-id="ca09b-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-984">'Razor'</span></span>
- <span data-ttu-id="ca09b-985">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-986">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-987">'Blazor'</span></span>
- <span data-ttu-id="ca09b-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-988">'Identity'</span></span>
- <span data-ttu-id="ca09b-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-990">'Razor'</span></span>
- <span data-ttu-id="ca09b-991">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-991">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-992">--------------- | :-----: | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-993">'Blazor'</span></span>
- <span data-ttu-id="ca09b-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-994">'Identity'</span></span>
- <span data-ttu-id="ca09b-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-996">'Razor'</span></span>
- <span data-ttu-id="ca09b-997">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-997">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-998">---- | | `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-999">Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ca09b-1000">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ca09b-1001">Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="ca09b-1002">| | `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="ca09b-1003">| | `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ca09b-1004">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="ca09b-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ca09b-1005">[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje przekazane nagłówki oprogramowania pośredniczącego, a moduł ASP.NET Core jest skonfigurowany do przesyłania dalej schematu (http/https) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="ca09b-1006">W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ca09b-1007">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="ca09b-1008">plik Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1008">web.config file</span></span>

<span data-ttu-id="ca09b-1009">Plik *Web. config* konfiguruje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="ca09b-1010">Tworzenie, przekształcanie i publikowanie pliku *Web. config* jest obsługiwane przez obiekt docelowy MSBuild ( `_TransformWebConfig` ), gdy projekt jest publikowany.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="ca09b-1011">Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="ca09b-1012">Zestaw SDK jest ustawiany u góry pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="ca09b-1013">Jeśli plik *Web. config* nie jest obecny w projekcie, plik zostanie utworzony przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przesunięcia do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="ca09b-1014">Jeśli plik *Web. config* znajduje się w projekcie, plik jest przekształcany przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przesunięcia do publikowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="ca09b-1015">Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="ca09b-1016">Plik *Web. config* może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="ca09b-1017">Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="ca09b-1018">Aby zapobiec transformacje pliku *Web. config* przez zestaw SDK sieci Web, należy użyć **\<IsTransformWebConfigDisabled>** właściwości w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="ca09b-1019">Podczas wyłączania pliku zestawu SDK sieci Web, *processPath* i *argumenty* powinny być ustawiane ręcznie przez dewelopera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="ca09b-1020">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="ca09b-1021">Lokalizacja pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1021">web.config file location</span></span>

<span data-ttu-id="ca09b-1022">Aby poprawnie skonfigurować [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , plik *Web. config* musi znajdować się w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="ca09b-1023">Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="ca09b-1024">Plik *Web. config* jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="ca09b-1025">Poufne pliki znajdują się w ścieżce fizycznej aplikacji, takiej jak \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (Komentarze dokumentacji XML) i \* \<assembly> . deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="ca09b-1026">Gdy plik *Web. config* jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="ca09b-1027">Jeśli brakuje pliku *Web. config* o nazwie lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie obsłużyć poufne pliki.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="ca09b-1028">**Plik *Web. config* musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie należy usuwać pliku *Web. config* z wdrożenia produkcyjnego.**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="ca09b-1029">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1029">Transform web.config</span></span>

<span data-ttu-id="ca09b-1030">Jeśli musisz przekształcić *plik Web. config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na podstawie konfiguracji, profilu lub środowiska), zobacz <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="ca09b-1031">Konfiguracja usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1031">IIS configuration</span></span>

<span data-ttu-id="ca09b-1032">**Systemy operacyjne Windows Server**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="ca09b-1033">Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="ca09b-1034">Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="ca09b-1035">W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="ca09b-1037">Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="ca09b-1038">Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="ca09b-1040">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1041">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="ca09b-1042">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ca09b-1043">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ca09b-1044">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1045">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ca09b-1046">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="ca09b-1047">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ca09b-1048">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ca09b-1049">Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="ca09b-1050">Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="ca09b-1051">**Systemy operacyjne Windows dla komputerów stacjonarnych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="ca09b-1052">Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ca09b-1053">Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="ca09b-1054">Otwórz węzeł **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="ca09b-1055">Otwórz węzeł **Narzędzia do zarządzania siecią Web** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="ca09b-1056">Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="ca09b-1057">Zaznacz pole wyboru **usług World Wide Web Services**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ca09b-1058">Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="ca09b-1059">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1060">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="ca09b-1061">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ca09b-1062">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ca09b-1063">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1064">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ca09b-1065">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="ca09b-1066">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ca09b-1067">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ca09b-1068">Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1068">If the IIS installation requires a restart, restart the system.</span></span>

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="ca09b-1070">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="ca09b-1071">Zainstaluj *pakiet hostingu platformy .NET Core* w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="ca09b-1072">Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="ca09b-1073">Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ca09b-1074">Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="ca09b-1075">Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="ca09b-1076">Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="ca09b-1077">Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="ca09b-1078">Pobierz</span><span class="sxs-lookup"><span data-stu-id="ca09b-1078">Download</span></span>

1. <span data-ttu-id="ca09b-1079">Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="ca09b-1080">Wybierz żądaną wersję programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="ca09b-1081">W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="ca09b-1082">Pobierz instalatora przy użyciu linku do **pakietu hostingu** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="ca09b-1083">Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="ca09b-1084">Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="ca09b-1085">Zainstaluj pakiet hostingu</span><span class="sxs-lookup"><span data-stu-id="ca09b-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="ca09b-1086">Uruchom Instalatora na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1086">Run the installer on the server.</span></span> <span data-ttu-id="ca09b-1087">Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="ca09b-1088">`OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="ca09b-1089">`OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="ca09b-1090">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="ca09b-1091">`OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="ca09b-1092">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="ca09b-1093">`OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="ca09b-1094">Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="ca09b-1095">Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="ca09b-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona (*ApplicationHost. config*) znajduje się na tym samym komputerze, na którym zainstalowano program IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="ca09b-1097">*Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="ca09b-1098">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="ca09b-1099">Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="ca09b-1100">Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="ca09b-1101">Nie jest konieczne ręczne zatrzymanie poszczególnych lokacji w usługach IIS podczas instalowania pakietu hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="ca09b-1102">Aplikacje hostowane (lokacje IIS) są ponownie uruchamiane po ponownym uruchomieniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="ca09b-1103">Aplikacje są uruchamiane ponownie po otrzymaniu pierwszego żądania, w tym od [modułu inicjalizacji aplikacji](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="ca09b-1104">ASP.NET Core przyjmuje zachowanie funkcji przekazywania do przodu dla wydań poprawek udostępnionych pakietów platformy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="ca09b-1105">Po ponownym uruchomieniu aplikacji hostowanych przez usługi IIS przy użyciu usług IIS aplikacje są ładowane z najnowszymi wersjami poprawki pakietów, do których się odwołują, gdy otrzymają swoje pierwsze żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="ca09b-1106">Jeśli usługi IIS nie zostaną ponownie uruchomione, aplikacje ponownie uruchamiają się i wykazują zachowanie przekazujące, gdy procesy robocze są odtwarzane i otrzymują swoje pierwsze żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="ca09b-1107">Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="ca09b-1108">Zainstaluj Web Deploy przy publikowaniu w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="ca09b-1109">Podczas wdrażania aplikacji na serwerach z [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)Zainstaluj najnowszą wersję Web Deploy na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="ca09b-1110">Aby zainstalować Web Deploy, należy użyć [Instalatora platformy sieci Web (Instalatora WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskać instalatora bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="ca09b-1111">Preferowaną metodą jest użycie Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="ca09b-1112">Instalatora WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="ca09b-1113">Tworzenie witryny usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1113">Create the IIS site</span></span>

1. <span data-ttu-id="ca09b-1114">W systemie hostingu Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="ca09b-1115">W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="ca09b-1116">Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="ca09b-1117">W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="ca09b-1118">Kliknij prawym przyciskiem myszy folder **sitess** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="ca09b-1119">Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="ca09b-1120">Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** folderu wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="ca09b-1121">Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Podaj nazwę lokacji, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="ca09b-1123">`http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ca09b-1124">Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="ca09b-1125">Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ca09b-1126">Używaj jawnych nazw hostów zamiast symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="ca09b-1127">Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ca09b-1128">Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="ca09b-1129">W węźle serwera wybierz pozycję **Pule aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="ca09b-1130">Kliknij prawym przyciskiem myszy pulę aplikacji lokacji i wybierz pozycję **Ustawienia podstawowe** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="ca09b-1131">W oknie **Edytowanie puli aplikacji** Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Nie ustawiaj kodu zarządzanego dla wersji środowiska .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="ca09b-1133">ASP.NET Core działa w osobnym procesie i zarządza środowiskiem uruchomieniowym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="ca09b-1134">ASP.NET Core nie polega na ładowaniu środowiska CLR (.NET CLR) &mdash; podstawowego środowiska uruchomieniowego języka wspólnego (CoreCLR) dla platformy .NET Core w celu hostowania aplikacji w procesie roboczym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="ca09b-1135">Ustawienie **wersji środowiska .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="ca09b-1136">*ASP.NET Core 2,2 lub nowsza*: w przypadku [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) z systemem 64-bitowym (x64), które korzysta z [modelu hostingu w procesie](#in-process-hosting-model), wyłączaj pulę aplikacji dla procesów 32-bit (x86).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="ca09b-1137">Na pasku bocznym **Akcje** Menedżera usług IIS > **Pule aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="ca09b-1138">Znajdź **opcję Włącz aplikacje 32-bitowe** i ustaw wartość na `False` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="ca09b-1139">To ustawienie nie ma wpływu na aplikacje wdrożone na potrzeby [hostingu poza procesem](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="ca09b-1140">Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="ca09b-1141">Jeśli domyślna tożsamość puli aplikacji (**model procesów**  >  **Identity** ) została zmieniona z **ApplicationPoolIdentity** na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="ca09b-1142">Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="ca09b-1143">**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="ca09b-1144">Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="ca09b-1145">Wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-1145">Deploy the app</span></span>

<span data-ttu-id="ca09b-1146">Wdróż aplikację w folderze **ścieżki fizycznej** usług IIS, który został ustanowiony w sekcji [Tworzenie witryny usług IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="ca09b-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przeniesienia aplikacji z folderu *publikowania* projektu do folderu wdrożenia systemu hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="ca09b-1148">Web Deploy z programem Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="ca09b-1149">Aby dowiedzieć się, jak utworzyć profil publikowania do użytku z usługą Web Deploy, zobacz temat [Profile publikacji programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="ca09b-1150">Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia aplikacji, pobierz swój profil i zaimportuj go za pomocą okna dialogowego **publikowania** programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Strona okna dialogowego publikowanie](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="ca09b-1152">Web Deploy poza programem Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="ca09b-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) można również użyć poza programem Visual Studio z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="ca09b-1154">Aby uzyskać więcej informacji, zobacz [Narzędzie do wdrażania w sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="ca09b-1155">Alternatywy dla Web Deploy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="ca09b-1156">Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingu, takich jak ręczna kopia, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="ca09b-1157">Aby uzyskać więcej informacji na ASP.NET Core wdrażania usług IIS, zobacz sekcję [zasoby wdrażania dla administratorów usług IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="ca09b-1158">Przeglądanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="ca09b-1158">Browse the website</span></span>

<span data-ttu-id="ca09b-1159">Po wdrożeniu aplikacji w systemie hostingu należy wysłać żądanie do jednej z publicznych punktów końcowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="ca09b-1160">W poniższym przykładzie lokacja jest powiązana z **nazwą hosta** usług IIS `www.mysite.com` na **porcie** `80` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="ca09b-1161">Zostanie wysłane żądanie `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="ca09b-1161">A request is made to `http://www.mysite.com`:</span></span>

![Przeglądarka Microsoft Edge załadowała stronę startową usług IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="ca09b-1163">Zablokowane pliki wdrożenia</span><span class="sxs-lookup"><span data-stu-id="ca09b-1163">Locked deployment files</span></span>

<span data-ttu-id="ca09b-1164">Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="ca09b-1165">Nie można zastąpić zablokowanych plików podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="ca09b-1166">Aby zwolnić zablokowane pliki we wdrożeniu, Zatrzymaj pulę aplikacji, korzystając z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="ca09b-1167">Użyj Web Deploy i Reference `Microsoft.NET.Sdk.Web` w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="ca09b-1168">Plik *app_offline. htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="ca09b-1169">Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline. htm* podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="ca09b-1170">Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="ca09b-1171">Ręcznie Zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="ca09b-1172">Porzuć *app_offline. htm* przy użyciu programu PowerShell (wymaga programu PowerShell 5 lub nowszego):</span><span class="sxs-lookup"><span data-stu-id="ca09b-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="ca09b-1173">Ochrona danych</span><span class="sxs-lookup"><span data-stu-id="ca09b-1173">Data protection</span></span>

<span data-ttu-id="ca09b-1174">[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="ca09b-1175">Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="ca09b-1176">Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="ca09b-1177">Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="ca09b-1178">Wszystkie tokeny uwierzytelniania na podstawie plików cookie są unieważnione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="ca09b-1179">Użytkownicy muszą ponownie zalogować się przy następnym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="ca09b-1180">Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="ca09b-1181">Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) [ASP.NET Core i pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="ca09b-1182">Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="ca09b-1183">**Tworzenie kluczy rejestru ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="ca09b-1184">Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="ca09b-1185">Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="ca09b-1186">W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt programu PowerShell provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) dla każdej puli aplikacji używanej z aplikacją ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="ca09b-1187">Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="ca09b-1188">Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="ca09b-1189">W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="ca09b-1190">Domyślnie klucze ochrony danych nie są szyfrowane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="ca09b-1191">Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym jest uruchamiana aplikacja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="ca09b-1192">Certyfikat x509 może służyć do ochrony kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="ca09b-1193">Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="ca09b-1194">Szczegóły można znaleźć w temacie [Konfigurowanie ochrony danych ASP.NET Core](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="ca09b-1195">**Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="ca09b-1196">To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="ca09b-1197">Ustaw **Załaduj profil użytkownika** na `True` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="ca09b-1198">Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="ca09b-1199">Klucze są utrwalane w folderze *% LocalAppData%/ASP.NET/dataprotection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="ca09b-1200">[Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="ca09b-1201">Wartość domyślna `setProfileEnvironment` to `true` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="ca09b-1202">W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="ca09b-1203">Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="ca09b-1204">Przejdź do folderu *% windir%/system32/inetsrv/config*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="ca09b-1205">Otwórz plik *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="ca09b-1206">Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="ca09b-1207">Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="ca09b-1208">**Używanie systemu plików jako magazynu kluczy**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="ca09b-1209">Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="ca09b-1210">Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="ca09b-1211">Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="ca09b-1212">W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="ca09b-1213">Użyj udziału plików, do którego mają dostęp wszystkie maszyny.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="ca09b-1214">Wdróż certyfikat x509 na każdym komputerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="ca09b-1215">Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="ca09b-1216">**Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="ca09b-1217">System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="ca09b-1218">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="ca09b-1219">Katalogi wirtualne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1219">Virtual Directories</span></span>

<span data-ttu-id="ca09b-1220">[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="ca09b-1221">Aplikacja może być hostowana jako [podaplikacja](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="ca09b-1222">Aplikacje podrzędne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1222">Sub-applications</span></span>

<span data-ttu-id="ca09b-1223">Aplikacja ASP.NET Core może być hostowana jako [podaplikacja usług IIS (podrzędna)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="ca09b-1224">Ścieżka aplikacji podrzędnej jest częścią adresu URL aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="ca09b-1225">Linki do zasobów statycznych w aplikacji podrzędnej powinny używać notacji z ukośnikiem ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="ca09b-1226">Notacja "ukośnik" wyzwala [pomocnika tagów](xref:mvc/views/tag-helpers/intro) , aby dołączyć pathbase podaplikacji do renderowanego linku względnego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="ca09b-1227">W przypadku aplikacji podrzędnej w `/subapp_path` programie obraz połączony z programem `src="~/image.png"` jest renderowany jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="ca09b-1228">Oprogramowanie pośredniczące aplikacji głównej nie przetwarza żądania pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="ca09b-1229">Żądanie jest przetwarzane przez oprogramowanie pośredniczące plików statycznych aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="ca09b-1230">Jeśli atrybut statycznego zasobu `src` jest ustawiony na ścieżkę bezwzględną (na przykład `src="/image.png"` ), link jest renderowany bez pathbase podaplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="ca09b-1231">Oprogramowanie pośredniczące pliku statycznego aplikacji głównej próbuje obsłużyć zasób z poziomu [głównego katalogu sieci Web](xref:fundamentals/index#web-root)aplikacji głównej, co spowoduje, że odpowiedź na *404 nie zostanie znaleziona* , chyba że statyczny zasób jest dostępny z poziomu aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="ca09b-1232">Aby hostować aplikację ASP.NET Core jako aplikację podrzędną w innej aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="ca09b-1233">Ustanów pulę aplikacji dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="ca09b-1234">Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego** , ponieważ podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchomione w celu hostowania aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="ca09b-1235">Dodaj lokację główną w Menedżerze usług IIS przy użyciu aplikacji podrzędnej w folderze w lokacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="ca09b-1236">Kliknij prawym przyciskiem myszy folder subapp w Menedżerze usług IIS, a następnie wybierz polecenie **Konwertuj na aplikację**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="ca09b-1237">W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="ca09b-1238">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1238">Select **OK**.</span></span>

<span data-ttu-id="ca09b-1239">Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="ca09b-1240">Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="ca09b-1241">Konfiguracja usług IIS z pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="ca09b-1242">Na konfigurację usług IIS wpływa `<system.webServer>` sekcja *pliku Web. config* dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji z modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="ca09b-1243">Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="ca09b-1244">Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w pliku *Web. config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="ca09b-1245">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="ca09b-1246">Dokumentacja konfiguracyjna programu\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="ca09b-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="ca09b-1247">Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwanych w przypadku usług IIS 10,0 lub nowszych), zobacz sekcję *polecenie Appcmd. exe* w temacie [ \<environmentVariables> zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej programu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="ca09b-1248">Sekcje konfiguracji pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="ca09b-1249">Sekcje konfiguracji aplikacji ASP.NET 4. x w *pliku Web. config* nie są używane przez aplikacje ASP.NET Core na potrzeby konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="ca09b-1250">Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="ca09b-1251">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="ca09b-1252">Pule aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-1252">Application Pools</span></span>

<span data-ttu-id="ca09b-1253">Izolacja puli aplikacji jest określana przez model hostingu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="ca09b-1254">Hosting w procesie: aplikacje muszą być uruchamiane w oddzielnych pulach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="ca09b-1255">Hosting poza procesem: zalecamy izolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="ca09b-1256">Okno dialogowe **Dodaj witrynę sieci Web** usług IIS domyślnie umożliwia pojedynczej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="ca09b-1257">Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="ca09b-1258">Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="ca09b-1259">Pula aplikacjiIdentity</span><span class="sxs-lookup"><span data-stu-id="ca09b-1259">Application Pool Identity</span></span>

<span data-ttu-id="ca09b-1260">Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="ca09b-1261">W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="ca09b-1262">W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

<span data-ttu-id="ca09b-1264">Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="ca09b-1265">Zasoby mogą być zabezpieczone przy użyciu tej tożsamości.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="ca09b-1266">Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="ca09b-1267">Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="ca09b-1268">Otwórz Eksploratora Windows i przejdź do katalogu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="ca09b-1269">Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="ca09b-1270">Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="ca09b-1271">Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="ca09b-1272">Wprowadź **<usług IIS puli aplikacji \\ app_pool_name>** w polu **Wprowadź nazwy obiektów do wybrania** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="ca09b-1273">Wybierz przycisk **Sprawdź nazwy** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="ca09b-1274">W obszarze *Domyślna pula aplikacji* Sprawdź nazwy za pomocą **usług IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="ca09b-1275">Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów zostanie wykazana wartość **Domyślna pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="ca09b-1276">Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="ca09b-1277">Podczas sprawdzania nazwy obiektu Użyj **<IIS puli aplikacji \\ app_pool_name>** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="ca09b-1279">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1279">Select **OK**.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: po wybraniu pozycji "Sprawdź nazwy" w obszarze nazwy obiektów zostanie wyświetlona nazwa obiektu "domyślna pula aplikacji".](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="ca09b-1281">&amp;Uprawnienia do wykonywania odczytu powinny być przyznawane domyślnie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="ca09b-1282">Podaj dodatkowe uprawnienia zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="ca09b-1283">Dostęp można także udzielić w wierszu polecenia przy użyciu narzędzia **icacls** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="ca09b-1284">Użycie funkcji *Domyślna pula aplikacji* jako przykładu powoduje użycie następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="ca09b-1285">Aby uzyskać więcej informacji, zobacz temat [icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ca09b-1286">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ca09b-1286">HTTP/2 support</span></span>

<span data-ttu-id="ca09b-1287">[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="ca09b-1288">W procesie</span><span class="sxs-lookup"><span data-stu-id="ca09b-1288">In-process</span></span>
  * <span data-ttu-id="ca09b-1289">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca09b-1290">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ca09b-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="ca09b-1291">Pozaprocesowe</span><span class="sxs-lookup"><span data-stu-id="ca09b-1291">Out-of-process</span></span>
  * <span data-ttu-id="ca09b-1292">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca09b-1293">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ca09b-1294">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ca09b-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ca09b-1295">W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 w raportach [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="ca09b-1296">W przypadku wdrożenia poza procesem, gdy jest nawiązywane połączenie HTTP/2, raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="ca09b-1297">Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ca09b-1298">Protokół HTTP/2 jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ca09b-1299">Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="ca09b-1300">Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="ca09b-1301">Żądania inspekcji wstępnej CORS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1301">CORS preflight requests</span></span>

<span data-ttu-id="ca09b-1302">*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="ca09b-1303">W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="ca09b-1304">Aby dowiedzieć się, jak skonfigurować obsługę usług IIS aplikacji w *pliku Web. config* w celu przekazywania żądań dotyczących opcji, zobacz [Włączanie żądań między źródłami w programie ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="ca09b-1305">Moduł inicjalizacji aplikacji i limit czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="ca09b-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="ca09b-1306">Hostowane w usługach IIS przez moduł ASP.NET Core w wersji 2:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="ca09b-1307">[Moduł inicjalizacji aplikacji](#application-initialization-module): hostowana [w procesie](#in-process-hosting-model) lub [poza procesem](#out-of-process-hosting-model) aplikacja może zostać skonfigurowana do automatycznego uruchamiania na potrzeby ponownego uruchomienia procesu roboczego lub ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="ca09b-1308">[Limit czasu bezczynności](#idle-timeout): hostowana [w trakcie](#in-process-hosting-model) działania aplikacji można skonfigurować w taki sposób, aby nie przekroczyć limitu czasu podczas okresów braku aktywności.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="ca09b-1309">Moduł inicjalizacji aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-1309">Application Initialization Module</span></span>

<span data-ttu-id="ca09b-1310">*Dotyczy aplikacji hostowanych w procesie i pozaprocesowe.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="ca09b-1311">[Inicjowanie aplikacji IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła do aplikacji żądanie HTTP, gdy pula aplikacji zostanie uruchomiona lub zostanie odtworzona.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="ca09b-1312">Żądanie wyzwala uruchomienie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1312">The request triggers the app to start.</span></span> <span data-ttu-id="ca09b-1313">Domyślnie usługi IIS wysyłają żądanie do głównego adresu URL aplikacji ( `/` ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby](#application-initialization-module-and-idle-timeout-additional-resources) , aby uzyskać więcej informacji na temat konfiguracji).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="ca09b-1314">Upewnij się, że funkcja inicjowania roli inicjalizacji aplikacji IIS jest włączona:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="ca09b-1315">Na komputerach z systemem Windows 7 lub nowszym w przypadku lokalnego korzystania z usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="ca09b-1316">Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ca09b-1317">Otwórz **Internet Information Services** > **World Wide Web Services** > **funkcje projektowania aplikacji**Internet Information Services World Wide Web Services.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="ca09b-1318">Zaznacz pole wyboru dla **inicjowania aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="ca09b-1319">W systemie Windows Server 2008 R2 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="ca09b-1320">Otwórz **Kreatora dodawania ról i funkcji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="ca09b-1321">W panelu **Wybierz usługi ról** Otwórz węzeł **Programowanie aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="ca09b-1322">Zaznacz pole wyboru dla **inicjowania aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="ca09b-1323">Użyj jednego z poniższych metod, aby włączyć moduł inicjowania aplikacji dla lokacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="ca09b-1324">Za pomocą Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="ca09b-1325">W panelu **połączenia** wybierz pozycję **Pule aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="ca09b-1326">Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="ca09b-1327">Domyślny **tryb uruchamiania** to **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="ca09b-1328">Ustaw **tryb uruchamiania** na **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="ca09b-1329">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="ca09b-1330">Otwórz węzeł **Lokacje** w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="ca09b-1331">Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję Zarządzaj ustawieniami zaawansowanymi **witryny sieci Web** > **Advanced Settings**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="ca09b-1332">Domyślnym ustawieniem **wstępnego ładowania** jest **wartość false**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="ca09b-1333">Ustaw dla opcji **wstępnego ładowania** **wartość true**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="ca09b-1334">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1334">Select **OK**.</span></span>

* <span data-ttu-id="ca09b-1335">Korzystając z pliku *Web. config*, Dodaj `<applicationInitialization>` element z `doAppInitAfterRestart` ustawionym do `true` `<system.webServer>` elementów w plik *Web. config* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="ca09b-1336">Limit czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="ca09b-1336">Idle Timeout</span></span>

<span data-ttu-id="ca09b-1337">*Dotyczy tylko aplikacji hostowanych w procesie.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="ca09b-1338">Aby zapobiec przekroczeniu przez aplikację, należy ustawić limit czasu bezczynności puli aplikacji przy użyciu Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="ca09b-1339">W panelu **połączenia** wybierz pozycję **Pule aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="ca09b-1340">Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="ca09b-1341">Domyślny **limit czasu bezczynności (w minutach)** wynosi **20** minut.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="ca09b-1342">Ustaw **limit czasu bezczynności (w minutach)** na **0** (zero).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="ca09b-1343">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1343">Select **OK**.</span></span>
1. <span data-ttu-id="ca09b-1344">Odtwórz proces roboczy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1344">Recycle the worker process.</span></span>

<span data-ttu-id="ca09b-1345">Aby zapobiec przekroczeniu limitu [czasu hostowanych przez aplikacje](#out-of-process-hosting-model) aplikacji, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="ca09b-1346">Wyślij polecenie ping do aplikacji z zewnętrznej usługi, aby było ono uruchomione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="ca09b-1347">Jeśli aplikacja obsługuje tylko usługi w tle, należy unikać hostingu usług IIS i używać [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="ca09b-1348">Dodatkowe zasoby dotyczące modułu inicjalizacji aplikacji i limitu czasu bezczynności</span><span class="sxs-lookup"><span data-stu-id="ca09b-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="ca09b-1349">Inicjowanie aplikacji usług IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="ca09b-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="ca09b-1350">[Inicjowanie \<applicationInitialization> aplikacji ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="ca09b-1351">[Ustawienia modelu procesów dla \<processModel> puli aplikacji ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="ca09b-1352">Zasoby wdrażania dla administratorów usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="ca09b-1353">Dokumentacja usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="ca09b-1354">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="ca09b-1355">Wdrażanie aplikacji .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="ca09b-1356">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ca09b-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="ca09b-1357">Oficjalna witryna programu Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="ca09b-1358">Biblioteka zawartości technicznej systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="ca09b-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="ca09b-1359">Protokół HTTP/2 w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ca09b-1360">Aby zapoznać się z samouczkiem dotyczącym publikowania aplikacji ASP.NET Core na serwerze usług IIS, zobacz <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="ca09b-1361">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="ca09b-1362">Obsługiwane systemy operacyjne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1362">Supported operating systems</span></span>

<span data-ttu-id="ca09b-1363">Obsługiwane są następujące systemy operacyjne:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="ca09b-1364">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1364">Windows 7 or later</span></span>
* <span data-ttu-id="ca09b-1365">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ca09b-1366">[Serwer http. sys](xref:fundamentals/servers/httpsys) (znany wcześniej jako webListener) nie działa w konfiguracji zwrotnego serwera proxy z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="ca09b-1367">Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ca09b-1368">Aby uzyskać informacje na temat hostingu na platformie Azure, zobacz <xref:host-and-deploy/azure-apps/index> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="ca09b-1369">Aby uzyskać wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="ca09b-1370">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1370">Supported platforms</span></span>

<span data-ttu-id="ca09b-1371">Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="ca09b-1372">Wdróż aplikację 32-bitową z 32-bitową (x86) zestaw .NET Core SDK, chyba że aplikacja:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="ca09b-1373">Wymagana jest większa przestrzeń adresów pamięci wirtualnej dla aplikacji 64-bitowej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="ca09b-1374">Wymaga większego rozmiaru stosu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="ca09b-1375">Ma 64-bitowe zależności natywne.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="ca09b-1376">Aby opublikować aplikację 64-bitową, należy użyć 64-bitowej (x64) zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="ca09b-1377">64-bitowy środowisko uruchomieniowe musi być obecny w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="ca09b-1378">ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), a domyślnym serwerem HTTP na wielu platformach.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="ca09b-1379">W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja działa w procesie innym niż proces roboczy usług IIS (*poza procesem*) z [serwerem Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="ca09b-1380">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="ca09b-1381">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ca09b-1382">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca09b-1383">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Moduł ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="ca09b-1385">Żądania docierają do sieci Web do sterownika HTTP. sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ca09b-1386">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ca09b-1387">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="ca09b-1388">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="ca09b-1389">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ca09b-1390">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ca09b-1391">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca09b-1392">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca09b-1393">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ca09b-1394">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ca09b-1395">`CreateDefaultBuilder`Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i włącza INTEGRACJĘ usług IIS przez skonfigurowanie ścieżki podstawowej i portu dla [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="ca09b-1396">Moduł ASP.NET Core generuje port dynamiczny do przypisania do procesu zaplecza.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="ca09b-1397">`CreateDefaultBuilder`wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metodę.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="ca09b-1398">`UseIISIntegration`konfiguruje Kestrel do nasłuchiwania na porcie dynamicznym przy użyciu adresu IP hosta lokalnego ( `127.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="ca09b-1399">Jeśli port dynamiczny to 1234, Kestrel nasłuchuje na `127.0.0.1:1234` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="ca09b-1400">Ta konfiguracja zastępuje inne konfiguracje URL dostarczone przez:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="ca09b-1401">Interfejs API Listen Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca09b-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="ca09b-1402">[Konfiguracja](xref:fundamentals/configuration/index) (lub [Opcja wiersza polecenia--URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="ca09b-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="ca09b-1403">Wywołania `UseUrls` interfejsu API lub Kestrel `Listen` nie są wymagane w przypadku korzystania z modułu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="ca09b-1404">Jeśli `UseUrls` lub `Listen` jest wywoływana, Kestrel nasłuchuje na porcie określonym tylko podczas uruchamiania aplikacji bez usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="ca09b-1405">Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ca09b-1406">Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ca09b-1407">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="ca09b-1408">Włącz składniki IISIntegration</span><span class="sxs-lookup"><span data-stu-id="ca09b-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="ca09b-1409">Podczas kompilowania hosta w `CreateWebHostBuilder` (*program.cs*), wywołaj, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> Aby włączyć integrację usług IIS:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="ca09b-1410">Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="ca09b-1411">Opcje usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1411">IIS options</span></span>

| <span data-ttu-id="ca09b-1412">Opcja</span><span class="sxs-lookup"><span data-stu-id="ca09b-1412">Option</span></span>                         | <span data-ttu-id="ca09b-1413">Domyślne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1413">Default</span></span> | <span data-ttu-id="ca09b-1414">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="ca09b-1414">Setting</span></span> |
| ---
<span data-ttu-id="ca09b-1415">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1416">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1417">'Identity'</span></span>
- <span data-ttu-id="ca09b-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1419">'Razor'</span></span>
- <span data-ttu-id="ca09b-1420">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1421">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1422">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1423">'Identity'</span></span>
- <span data-ttu-id="ca09b-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1425">'Razor'</span></span>
- <span data-ttu-id="ca09b-1426">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1427">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1428">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1429">'Identity'</span></span>
- <span data-ttu-id="ca09b-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1431">'Razor'</span></span>
- <span data-ttu-id="ca09b-1432">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1433">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1434">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1435">'Identity'</span></span>
- <span data-ttu-id="ca09b-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1437">'Razor'</span></span>
- <span data-ttu-id="ca09b-1438">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1439">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1440">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1441">'Identity'</span></span>
- <span data-ttu-id="ca09b-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1443">'Razor'</span></span>
- <span data-ttu-id="ca09b-1444">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1445">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1446">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1447">'Identity'</span></span>
- <span data-ttu-id="ca09b-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1449">'Razor'</span></span>
- <span data-ttu-id="ca09b-1450">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1451">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1452">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1453">'Identity'</span></span>
- <span data-ttu-id="ca09b-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1455">'Razor'</span></span>
- <span data-ttu-id="ca09b-1456">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1457">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1458">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1459">'Identity'</span></span>
- <span data-ttu-id="ca09b-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1461">'Razor'</span></span>
- <span data-ttu-id="ca09b-1462">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1463">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1464">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1465">'Identity'</span></span>
- <span data-ttu-id="ca09b-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1467">'Razor'</span></span>
- <span data-ttu-id="ca09b-1468">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1469">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1470">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1471">'Identity'</span></span>
- <span data-ttu-id="ca09b-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1473">'Razor'</span></span>
- <span data-ttu-id="ca09b-1474">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1475">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1476">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1477">'Identity'</span></span>
- <span data-ttu-id="ca09b-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1479">'Razor'</span></span>
- <span data-ttu-id="ca09b-1480">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1481">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1482">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1483">'Identity'</span></span>
- <span data-ttu-id="ca09b-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1485">'Razor'</span></span>
- <span data-ttu-id="ca09b-1486">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1487">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1488">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1489">'Identity'</span></span>
- <span data-ttu-id="ca09b-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1491">'Razor'</span></span>
- <span data-ttu-id="ca09b-1492">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-1493">--------------- | :-----: | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1494">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1495">'Identity'</span></span>
- <span data-ttu-id="ca09b-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1497">'Razor'</span></span>
- <span data-ttu-id="ca09b-1498">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-1499">---- | | `AutomaticAuthentication`      | `true`  | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-1500">Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ca09b-1501">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ca09b-1502">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-1503">| | `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="ca09b-1504">Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="ca09b-1505">Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="ca09b-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="ca09b-1506">Opcja</span><span class="sxs-lookup"><span data-stu-id="ca09b-1506">Option</span></span>                         | <span data-ttu-id="ca09b-1507">Domyślne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1507">Default</span></span> | <span data-ttu-id="ca09b-1508">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="ca09b-1508">Setting</span></span> |
| ---
<span data-ttu-id="ca09b-1509">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1510">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1511">'Identity'</span></span>
- <span data-ttu-id="ca09b-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1513">'Razor'</span></span>
- <span data-ttu-id="ca09b-1514">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1515">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1516">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1517">'Identity'</span></span>
- <span data-ttu-id="ca09b-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1519">'Razor'</span></span>
- <span data-ttu-id="ca09b-1520">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1521">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1522">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1523">'Identity'</span></span>
- <span data-ttu-id="ca09b-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1525">'Razor'</span></span>
- <span data-ttu-id="ca09b-1526">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1527">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1528">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1529">'Identity'</span></span>
- <span data-ttu-id="ca09b-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1531">'Razor'</span></span>
- <span data-ttu-id="ca09b-1532">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1533">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1534">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1535">'Identity'</span></span>
- <span data-ttu-id="ca09b-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1537">'Razor'</span></span>
- <span data-ttu-id="ca09b-1538">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1539">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1540">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1541">'Identity'</span></span>
- <span data-ttu-id="ca09b-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1543">'Razor'</span></span>
- <span data-ttu-id="ca09b-1544">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1545">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1546">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1547">'Identity'</span></span>
- <span data-ttu-id="ca09b-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1549">'Razor'</span></span>
- <span data-ttu-id="ca09b-1550">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1551">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1552">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1553">'Identity'</span></span>
- <span data-ttu-id="ca09b-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1555">'Razor'</span></span>
- <span data-ttu-id="ca09b-1556">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1557">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1558">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1559">'Identity'</span></span>
- <span data-ttu-id="ca09b-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1561">'Razor'</span></span>
- <span data-ttu-id="ca09b-1562">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1563">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1564">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1565">'Identity'</span></span>
- <span data-ttu-id="ca09b-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1567">'Razor'</span></span>
- <span data-ttu-id="ca09b-1568">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1569">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1570">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1571">'Identity'</span></span>
- <span data-ttu-id="ca09b-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1573">'Razor'</span></span>
- <span data-ttu-id="ca09b-1574">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1575">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1576">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1577">'Identity'</span></span>
- <span data-ttu-id="ca09b-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1579">'Razor'</span></span>
- <span data-ttu-id="ca09b-1580">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca09b-1581">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1582">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1583">'Identity'</span></span>
- <span data-ttu-id="ca09b-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1585">'Razor'</span></span>
- <span data-ttu-id="ca09b-1586">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-1587">--------------- | :-----: | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca09b-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1588">'Blazor'</span></span>
- <span data-ttu-id="ca09b-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1589">'Identity'</span></span>
- <span data-ttu-id="ca09b-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca09b-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca09b-1591">'Razor'</span></span>
- <span data-ttu-id="ca09b-1592">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="ca09b-1593">---- | | `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ca09b-1594">Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ca09b-1595">Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ca09b-1596">Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="ca09b-1597">| | `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="ca09b-1598">| | `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ca09b-1599">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="ca09b-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ca09b-1600">[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje przekazane nagłówki oprogramowania pośredniczącego, a moduł ASP.NET Core jest skonfigurowany do przesyłania dalej schematu (http/https) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="ca09b-1601">W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ca09b-1602">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="ca09b-1603">plik Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1603">web.config file</span></span>

<span data-ttu-id="ca09b-1604">Plik *Web. config* konfiguruje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="ca09b-1605">Tworzenie, przekształcanie i publikowanie pliku *Web. config* jest obsługiwane przez obiekt docelowy MSBuild ( `_TransformWebConfig` ), gdy projekt jest publikowany.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="ca09b-1606">Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="ca09b-1607">Zestaw SDK jest ustawiany u góry pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="ca09b-1608">Jeśli plik *Web. config* nie jest obecny w projekcie, plik zostanie utworzony przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przesunięcia do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="ca09b-1609">Jeśli plik *Web. config* znajduje się w projekcie, plik jest przekształcany przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przesunięcia do publikowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="ca09b-1610">Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="ca09b-1611">Plik *Web. config* może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="ca09b-1612">Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="ca09b-1613">Aby zapobiec transformacje pliku *Web. config* przez zestaw SDK sieci Web, należy użyć **\<IsTransformWebConfigDisabled>** właściwości w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="ca09b-1614">Podczas wyłączania pliku zestawu SDK sieci Web, *processPath* i *argumenty* powinny być ustawiane ręcznie przez dewelopera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="ca09b-1615">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="ca09b-1616">Lokalizacja pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1616">web.config file location</span></span>

<span data-ttu-id="ca09b-1617">Aby poprawnie skonfigurować [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , plik *Web. config* musi znajdować się w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="ca09b-1618">Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="ca09b-1619">Plik *Web. config* jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="ca09b-1620">Poufne pliki znajdują się w ścieżce fizycznej aplikacji, takiej jak \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (Komentarze dokumentacji XML) i \* \<assembly> . deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="ca09b-1621">Gdy plik *Web. config* jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="ca09b-1622">Jeśli brakuje pliku *Web. config* o nazwie lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie obsłużyć poufne pliki.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="ca09b-1623">**Plik *Web. config* musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie należy usuwać pliku *Web. config* z wdrożenia produkcyjnego.**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="ca09b-1624">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1624">Transform web.config</span></span>

<span data-ttu-id="ca09b-1625">Jeśli musisz przekształcić *plik Web. config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na podstawie konfiguracji, profilu lub środowiska), zobacz <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="ca09b-1626">Konfiguracja usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1626">IIS configuration</span></span>

<span data-ttu-id="ca09b-1627">**Systemy operacyjne Windows Server**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="ca09b-1628">Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="ca09b-1629">Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="ca09b-1630">W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="ca09b-1632">Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="ca09b-1633">Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="ca09b-1635">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1636">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="ca09b-1637">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ca09b-1638">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ca09b-1639">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1640">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ca09b-1641">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="ca09b-1642">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ca09b-1643">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ca09b-1644">Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="ca09b-1645">Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="ca09b-1646">**Systemy operacyjne Windows dla komputerów stacjonarnych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="ca09b-1647">Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ca09b-1648">Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="ca09b-1649">Otwórz węzeł **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="ca09b-1650">Otwórz węzeł **Narzędzia do zarządzania siecią Web** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="ca09b-1651">Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="ca09b-1652">Zaznacz pole wyboru **usług World Wide Web Services**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ca09b-1653">Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="ca09b-1654">**Uwierzytelnianie systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1655">Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="ca09b-1656">Wybierz funkcję **uwierzytelniania systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ca09b-1657">Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ca09b-1658">**Obiekty WebSockets (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ca09b-1659">Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ca09b-1660">Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="ca09b-1661">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ca09b-1662">Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ca09b-1663">Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1663">If the IIS installation requires a restart, restart the system.</span></span>

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="ca09b-1665">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="ca09b-1666">Zainstaluj *pakiet hostingu platformy .NET Core* w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="ca09b-1667">Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="ca09b-1668">Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ca09b-1669">Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="ca09b-1670">Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="ca09b-1671">Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="ca09b-1672">Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="ca09b-1673">Pobierz</span><span class="sxs-lookup"><span data-stu-id="ca09b-1673">Download</span></span>

1. <span data-ttu-id="ca09b-1674">Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="ca09b-1675">Wybierz żądaną wersję programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="ca09b-1676">W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="ca09b-1677">Pobierz instalatora przy użyciu linku do **pakietu hostingu** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="ca09b-1678">Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="ca09b-1679">Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="ca09b-1680">Zainstaluj pakiet hostingu</span><span class="sxs-lookup"><span data-stu-id="ca09b-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="ca09b-1681">Uruchom Instalatora na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1681">Run the installer on the server.</span></span> <span data-ttu-id="ca09b-1682">Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="ca09b-1683">`OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="ca09b-1684">`OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="ca09b-1685">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="ca09b-1686">`OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="ca09b-1687">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="ca09b-1688">`OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="ca09b-1689">Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="ca09b-1690">Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="ca09b-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona (*ApplicationHost. config*) znajduje się na tym samym komputerze, na którym zainstalowano program IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="ca09b-1692">*Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="ca09b-1693">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="ca09b-1694">Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="ca09b-1695">Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="ca09b-1696">Nie jest konieczne ręczne zatrzymanie poszczególnych lokacji w usługach IIS podczas instalowania pakietu hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="ca09b-1697">Aplikacje hostowane (lokacje IIS) są ponownie uruchamiane po ponownym uruchomieniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="ca09b-1698">Aplikacje są uruchamiane ponownie po otrzymaniu pierwszego żądania, w tym od [modułu inicjalizacji aplikacji](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="ca09b-1699">ASP.NET Core przyjmuje zachowanie funkcji przekazywania do przodu dla wydań poprawek udostępnionych pakietów platformy.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="ca09b-1700">Po ponownym uruchomieniu aplikacji hostowanych przez usługi IIS przy użyciu usług IIS aplikacje są ładowane z najnowszymi wersjami poprawki pakietów, do których się odwołują, gdy otrzymają swoje pierwsze żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="ca09b-1701">Jeśli usługi IIS nie zostaną ponownie uruchomione, aplikacje ponownie uruchamiają się i wykazują zachowanie przekazujące, gdy procesy robocze są odtwarzane i otrzymują swoje pierwsze żądanie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="ca09b-1702">Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="ca09b-1703">Zainstaluj Web Deploy przy publikowaniu w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="ca09b-1704">Podczas wdrażania aplikacji na serwerach z [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)Zainstaluj najnowszą wersję Web Deploy na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="ca09b-1705">Aby zainstalować Web Deploy, należy użyć [Instalatora platformy sieci Web (Instalatora WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskać instalatora bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="ca09b-1706">Preferowaną metodą jest użycie Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="ca09b-1707">Instalatora WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="ca09b-1708">Tworzenie witryny usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1708">Create the IIS site</span></span>

1. <span data-ttu-id="ca09b-1709">W systemie hostingu Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="ca09b-1710">W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="ca09b-1711">Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="ca09b-1712">W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="ca09b-1713">Kliknij prawym przyciskiem myszy folder **sitess** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="ca09b-1714">Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="ca09b-1715">Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** folderu wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="ca09b-1716">Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Podaj nazwę lokacji, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="ca09b-1718">`http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ca09b-1719">Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="ca09b-1720">Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ca09b-1721">Używaj jawnych nazw hostów zamiast symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="ca09b-1722">Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ca09b-1723">Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="ca09b-1724">W węźle serwera wybierz pozycję **Pule aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="ca09b-1725">Kliknij prawym przyciskiem myszy pulę aplikacji lokacji i wybierz pozycję **Ustawienia podstawowe** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="ca09b-1726">W oknie **Edytowanie puli aplikacji** Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Nie ustawiaj kodu zarządzanego dla wersji środowiska .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="ca09b-1728">ASP.NET Core działa w osobnym procesie i zarządza środowiskiem uruchomieniowym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="ca09b-1729">ASP.NET Core nie polega na ładowaniu środowiska CLR (.NET CLR) &mdash; podstawowego środowiska uruchomieniowego języka wspólnego (CoreCLR) dla platformy .NET Core w celu hostowania aplikacji w procesie roboczym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="ca09b-1730">Ustawienie **wersji środowiska .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="ca09b-1731">*ASP.NET Core 2,2 lub nowsza*: w przypadku [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) z systemem 64-bitowym (x64), które korzysta z [modelu hostingu w procesie](#in-process-hosting-model), wyłączaj pulę aplikacji dla procesów 32-bit (x86).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="ca09b-1732">Na pasku bocznym **Akcje** Menedżera usług IIS > **Pule aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="ca09b-1733">Znajdź **opcję Włącz aplikacje 32-bitowe** i ustaw wartość na `False` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="ca09b-1734">To ustawienie nie ma wpływu na aplikacje wdrożone na potrzeby [hostingu poza procesem](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="ca09b-1735">Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="ca09b-1736">Jeśli domyślna tożsamość puli aplikacji (**model procesów**  >  **Identity** ) została zmieniona z **ApplicationPoolIdentity** na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="ca09b-1737">Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="ca09b-1738">**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="ca09b-1739">Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="ca09b-1740">Wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-1740">Deploy the app</span></span>

<span data-ttu-id="ca09b-1741">Wdróż aplikację w folderze **ścieżki fizycznej** usług IIS, który został ustanowiony w sekcji [Tworzenie witryny usług IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="ca09b-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przeniesienia aplikacji z folderu *publikowania* projektu do folderu wdrożenia systemu hostingu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="ca09b-1743">Web Deploy z programem Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="ca09b-1744">Aby dowiedzieć się, jak utworzyć profil publikowania do użytku z usługą Web Deploy, zobacz temat [Profile publikacji programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="ca09b-1745">Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia aplikacji, pobierz swój profil i zaimportuj go za pomocą okna dialogowego **publikowania** programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Strona okna dialogowego publikowanie](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="ca09b-1747">Web Deploy poza programem Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca09b-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="ca09b-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) można również użyć poza programem Visual Studio z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="ca09b-1749">Aby uzyskać więcej informacji, zobacz [Narzędzie do wdrażania w sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="ca09b-1750">Alternatywy dla Web Deploy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="ca09b-1751">Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingu, takich jak ręczna kopia, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="ca09b-1752">Aby uzyskać więcej informacji na ASP.NET Core wdrażania usług IIS, zobacz sekcję [zasoby wdrażania dla administratorów usług IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="ca09b-1753">Przeglądanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="ca09b-1753">Browse the website</span></span>

<span data-ttu-id="ca09b-1754">Po wdrożeniu aplikacji w systemie hostingu należy wysłać żądanie do jednej z publicznych punktów końcowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="ca09b-1755">W poniższym przykładzie lokacja jest powiązana z **nazwą hosta** usług IIS `www.mysite.com` na **porcie** `80` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="ca09b-1756">Zostanie wysłane żądanie `http://www.mysite.com` :</span><span class="sxs-lookup"><span data-stu-id="ca09b-1756">A request is made to `http://www.mysite.com`:</span></span>

![Przeglądarka Microsoft Edge załadowała stronę startową usług IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="ca09b-1758">Zablokowane pliki wdrożenia</span><span class="sxs-lookup"><span data-stu-id="ca09b-1758">Locked deployment files</span></span>

<span data-ttu-id="ca09b-1759">Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="ca09b-1760">Nie można zastąpić zablokowanych plików podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="ca09b-1761">Aby zwolnić zablokowane pliki we wdrożeniu, Zatrzymaj pulę aplikacji, korzystając z **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="ca09b-1762">Użyj Web Deploy i Reference `Microsoft.NET.Sdk.Web` w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="ca09b-1763">Plik *app_offline. htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="ca09b-1764">Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline. htm* podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="ca09b-1765">Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="ca09b-1766">Ręcznie Zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="ca09b-1767">Porzuć *app_offline. htm* przy użyciu programu PowerShell (wymaga programu PowerShell 5 lub nowszego):</span><span class="sxs-lookup"><span data-stu-id="ca09b-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="ca09b-1768">Ochrona danych</span><span class="sxs-lookup"><span data-stu-id="ca09b-1768">Data protection</span></span>

<span data-ttu-id="ca09b-1769">[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="ca09b-1770">Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="ca09b-1771">Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="ca09b-1772">Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="ca09b-1773">Wszystkie tokeny uwierzytelniania na podstawie plików cookie są unieważnione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="ca09b-1774">Użytkownicy muszą ponownie zalogować się przy następnym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="ca09b-1775">Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="ca09b-1776">Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) [ASP.NET Core i pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="ca09b-1777">Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="ca09b-1778">**Tworzenie kluczy rejestru ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="ca09b-1779">Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="ca09b-1780">Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="ca09b-1781">W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt programu PowerShell provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) dla każdej puli aplikacji używanej z aplikacją ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="ca09b-1782">Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="ca09b-1783">Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="ca09b-1784">W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="ca09b-1785">Domyślnie klucze ochrony danych nie są szyfrowane.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="ca09b-1786">Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym jest uruchamiana aplikacja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="ca09b-1787">Certyfikat x509 może służyć do ochrony kluczy w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="ca09b-1788">Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="ca09b-1789">Szczegóły można znaleźć w temacie [Konfigurowanie ochrony danych ASP.NET Core](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="ca09b-1790">**Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="ca09b-1791">To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="ca09b-1792">Ustaw **Załaduj profil użytkownika** na `True` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="ca09b-1793">Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="ca09b-1794">Klucze są utrwalane w folderze *% LocalAppData%/ASP.NET/dataprotection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="ca09b-1795">[Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="ca09b-1796">Wartość domyślna `setProfileEnvironment` to `true` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="ca09b-1797">W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="ca09b-1798">Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="ca09b-1799">Przejdź do folderu *% windir%/system32/inetsrv/config*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="ca09b-1800">Otwórz plik *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="ca09b-1801">Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="ca09b-1802">Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="ca09b-1803">**Używanie systemu plików jako magazynu kluczy**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="ca09b-1804">Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="ca09b-1805">Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="ca09b-1806">Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="ca09b-1807">W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="ca09b-1808">Użyj udziału plików, do którego mają dostęp wszystkie maszyny.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="ca09b-1809">Wdróż certyfikat x509 na każdym komputerze.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="ca09b-1810">Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="ca09b-1811">**Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**</span><span class="sxs-lookup"><span data-stu-id="ca09b-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="ca09b-1812">System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="ca09b-1813">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="ca09b-1814">Katalogi wirtualne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1814">Virtual Directories</span></span>

<span data-ttu-id="ca09b-1815">[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="ca09b-1816">Aplikacja może być hostowana jako [podaplikacja](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="ca09b-1817">Aplikacje podrzędne</span><span class="sxs-lookup"><span data-stu-id="ca09b-1817">Sub-applications</span></span>

<span data-ttu-id="ca09b-1818">Aplikacja ASP.NET Core może być hostowana jako [podaplikacja usług IIS (podrzędna)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="ca09b-1819">Ścieżka aplikacji podrzędnej jest częścią adresu URL aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="ca09b-1820">Aplikacja podrzędna nie powinna zawierać modułu ASP.NET Core jako programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="ca09b-1821">Jeśli moduł zostanie dodany jako program obsługi w pliku *Web. config* aplikacji podrzędnej, podczas próby przeglądania aplikacji podrzędnej *Wystąpił błąd wewnętrzny serwera 500,19* odwołujący się do uszkodzonego pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="ca09b-1822">Poniższy przykład przedstawia opublikowany plik *Web. config* dla aplikacji podrzędnej ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="ca09b-1823">W przypadku hostowania aplikacji podrzędnej non-ASP.NET Core w aplikacji ASP.NET Core jawnie Usuń dziedziczoną procedurę obsługi w pliku *Web. config* aplikacji podrzędnej:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="ca09b-1824">Linki do zasobów statycznych w aplikacji podrzędnej powinny używać notacji z ukośnikiem ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="ca09b-1825">Notacja "ukośnik" wyzwala [pomocnika tagów](xref:mvc/views/tag-helpers/intro) , aby dołączyć pathbase podaplikacji do renderowanego linku względnego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="ca09b-1826">W przypadku aplikacji podrzędnej w `/subapp_path` programie obraz połączony z programem `src="~/image.png"` jest renderowany jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="ca09b-1827">Oprogramowanie pośredniczące aplikacji głównej nie przetwarza żądania pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="ca09b-1828">Żądanie jest przetwarzane przez oprogramowanie pośredniczące plików statycznych aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="ca09b-1829">Jeśli atrybut statycznego zasobu `src` jest ustawiony na ścieżkę bezwzględną (na przykład `src="/image.png"` ), link jest renderowany bez pathbase podaplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="ca09b-1830">Oprogramowanie pośredniczące pliku statycznego aplikacji głównej próbuje obsłużyć zasób z poziomu [głównego katalogu sieci Web](xref:fundamentals/index#web-root)aplikacji głównej, co spowoduje, że odpowiedź na *404 nie zostanie znaleziona* , chyba że statyczny zasób jest dostępny z poziomu aplikacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="ca09b-1831">Aby hostować aplikację ASP.NET Core jako aplikację podrzędną w innej aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="ca09b-1832">Ustanów pulę aplikacji dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="ca09b-1833">Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego** , ponieważ podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchomione w celu hostowania aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="ca09b-1834">Dodaj lokację główną w Menedżerze usług IIS przy użyciu aplikacji podrzędnej w folderze w lokacji głównej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="ca09b-1835">Kliknij prawym przyciskiem myszy folder subapp w Menedżerze usług IIS, a następnie wybierz polecenie **Konwertuj na aplikację**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="ca09b-1836">W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="ca09b-1837">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1837">Select **OK**.</span></span>

<span data-ttu-id="ca09b-1838">Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="ca09b-1839">Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="ca09b-1840">Konfiguracja usług IIS z pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="ca09b-1841">Na konfigurację usług IIS wpływa `<system.webServer>` sekcja *pliku Web. config* dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji z modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="ca09b-1842">Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="ca09b-1843">Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w pliku *Web. config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="ca09b-1844">Aby uzyskać więcej informacji, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="ca09b-1845">Dokumentacja konfiguracyjna programu\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="ca09b-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="ca09b-1846">Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwanych w przypadku usług IIS 10,0 lub nowszych), zobacz sekcję *polecenie Appcmd. exe* w temacie [ \<environmentVariables> zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej programu IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="ca09b-1847">Sekcje konfiguracji pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="ca09b-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="ca09b-1848">Sekcje konfiguracji aplikacji ASP.NET 4. x w *pliku Web. config* nie są używane przez aplikacje ASP.NET Core na potrzeby konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="ca09b-1849">Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="ca09b-1850">Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="ca09b-1851">Pule aplikacji</span><span class="sxs-lookup"><span data-stu-id="ca09b-1851">Application Pools</span></span>

<span data-ttu-id="ca09b-1852">W przypadku hostowania wielu witryn sieci Web na serwerze zalecamy odizolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="ca09b-1853">W oknie dialogowym **Dodaj witrynę sieci Web** programu IIS domyślnie zostanie wydana konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="ca09b-1854">Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="ca09b-1855">Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="ca09b-1856">Pula aplikacjiIdentity</span><span class="sxs-lookup"><span data-stu-id="ca09b-1856">Application Pool Identity</span></span>

<span data-ttu-id="ca09b-1857">Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="ca09b-1858">W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="ca09b-1859">W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

<span data-ttu-id="ca09b-1861">Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="ca09b-1862">Zasoby mogą być zabezpieczone przy użyciu tej tożsamości.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="ca09b-1863">Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="ca09b-1864">Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="ca09b-1865">Otwórz Eksploratora Windows i przejdź do katalogu.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="ca09b-1866">Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="ca09b-1867">Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="ca09b-1868">Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="ca09b-1869">Wprowadź **<usług IIS puli aplikacji \\ app_pool_name>** w polu **Wprowadź nazwy obiektów do wybrania** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="ca09b-1870">Wybierz przycisk **Sprawdź nazwy** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="ca09b-1871">W obszarze *Domyślna pula aplikacji* Sprawdź nazwy za pomocą **usług IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="ca09b-1872">Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów zostanie wykazana wartość **Domyślna pula aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="ca09b-1873">Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="ca09b-1874">Podczas sprawdzania nazwy obiektu Użyj **<IIS puli aplikacji \\ app_pool_name>** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="ca09b-1876">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1876">Select **OK**.</span></span>

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: po wybraniu pozycji "Sprawdź nazwy" w obszarze nazwy obiektów zostanie wyświetlona nazwa obiektu "domyślna pula aplikacji".](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="ca09b-1878">&amp;Uprawnienia do wykonywania odczytu powinny być przyznawane domyślnie.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="ca09b-1879">Podaj dodatkowe uprawnienia zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="ca09b-1880">Dostęp można także udzielić w wierszu polecenia przy użyciu narzędzia **icacls** .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="ca09b-1881">Użycie funkcji *Domyślna pula aplikacji* jako przykładu powoduje użycie następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="ca09b-1882">Aby uzyskać więcej informacji, zobacz temat [icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ca09b-1883">Obsługa protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ca09b-1883">HTTP/2 support</span></span>

<span data-ttu-id="ca09b-1884">Obsługa [protokołu HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwana w przypadku wdrożeń pozaprocesowych, które spełniają następujące wymagania podstawowe:</span><span class="sxs-lookup"><span data-stu-id="ca09b-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="ca09b-1885">Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="ca09b-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="ca09b-1886">Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="ca09b-1887">Struktura docelowa: nie dotyczy wdrożeń poza procesem, ponieważ połączenie HTTP/2 jest obsługiwane całkowicie przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="ca09b-1888">Połączenie TLS 1,2 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ca09b-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ca09b-1889">W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="ca09b-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="ca09b-1890">Protokół HTTP/2 jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ca09b-1891">Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="ca09b-1892">Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="ca09b-1893">Żądania inspekcji wstępnej CORS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1893">CORS preflight requests</span></span>

<span data-ttu-id="ca09b-1894">*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="ca09b-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="ca09b-1895">W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="ca09b-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="ca09b-1896">Aby dowiedzieć się, jak skonfigurować obsługę usług IIS aplikacji w *pliku Web. config* w celu przekazywania żądań dotyczących opcji, zobacz [Włączanie żądań między źródłami w programie ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="ca09b-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="ca09b-1897">Zasoby wdrażania dla administratorów usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="ca09b-1898">Dokumentacja usług IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="ca09b-1899">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="ca09b-1900">Wdrażanie aplikacji .NET Core</span><span class="sxs-lookup"><span data-stu-id="ca09b-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="ca09b-1901">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ca09b-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="ca09b-1902">Oficjalna witryna programu Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="ca09b-1903">Biblioteka zawartości technicznej systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="ca09b-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="ca09b-1904">Protokół HTTP/2 w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="ca09b-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
