---
<span data-ttu-id="9b62c-101">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-103">'Blazor'</span></span>
- <span data-ttu-id="9b62c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-104">'Identity'</span></span>
- <span data-ttu-id="9b62c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-106">'Razor'</span></span>
- <span data-ttu-id="9b62c-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="9b62c-108">Hostowanie i wdrażanie ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="9b62c-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="9b62c-109">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="9b62c-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="9b62c-110">Z [ Blazor modelem hostingu zestawu webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="9b62c-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="9b62c-111">BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9b62c-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="9b62c-112">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9b62c-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="9b62c-113">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="9b62c-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="9b62c-114">BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b62c-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="9b62c-115">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="9b62c-116">BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="9b62c-117">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor aplikacji sieci webassembly jako aplikacji podrzędnej IIS.</span><span class="sxs-lookup"><span data-stu-id="9b62c-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="9b62c-118">Precompression Brotli</span><span class="sxs-lookup"><span data-stu-id="9b62c-118">Brotli precompression</span></span>

<span data-ttu-id="9b62c-119">Po Blazor opublikowaniu aplikacji webassembly dane wyjściowe są najpierw kompresowane przy użyciu [algorytmu kompresji Brotli](https://tools.ietf.org/html/rfc7932) na najwyższym poziomie w celu zmniejszenia rozmiaru aplikacji i usunięcia potrzeby kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9b62c-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="9b62c-120">Aby poznać konfigurację kompresji *Web. config* usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="9b62c-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="9b62c-121">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="9b62c-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="9b62c-122">Żądania routingu dla składników strony w Blazor aplikacji webassembly nie są tak proste jak żądania routingu na Blazor serwerze hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="9b62c-123">Weź pod uwagę Blazor aplikację webassembly z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="9b62c-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="9b62c-124">*Main. Razor*: ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="9b62c-124">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="9b62c-125">*Informacje o. Razor*: `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="9b62c-125">*About.razor*: `About` component.</span></span>

<span data-ttu-id="9b62c-126">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="9b62c-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="9b62c-127">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="9b62c-127">The browser makes a request.</span></span>
1. <span data-ttu-id="9b62c-128">Zostanie zwrócona strona domyślna, która jest zwykle *index. html*.</span><span class="sxs-lookup"><span data-stu-id="9b62c-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="9b62c-129">*index. html* Bootstrap aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b62c-129">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="9b62c-130">ładuje router, a Razor `Main` składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="9b62c-130">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="9b62c-131">Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="9b62c-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="9b62c-132">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor aplikacji webassembly* działają w ten sam sposób: żądania nie wyzwalają żądań opartych na przeglądarce do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="9b62c-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="9b62c-133">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="9b62c-133">The router handles the requests internally.</span></span>

<span data-ttu-id="9b62c-134">Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="9b62c-135">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="9b62c-136">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze, na stronie *index. html* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="9b62c-137">Po zwróceniu elementu *index. html* Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="9b62c-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="9b62c-138">Podczas wdrażania na serwerze IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym plikiem *Web. config* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="9b62c-139">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="9b62c-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="9b62c-140">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b62c-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="9b62c-141">*Wdrożenie hostowane* obsługuje Blazor aplikację webassembly dla przeglądarek z [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9b62c-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="9b62c-142">BlazorAplikacja webassembly klienta jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* aplikacji serwera, wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="9b62c-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="9b62c-143">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="9b62c-143">The two apps are deployed together.</span></span> <span data-ttu-id="9b62c-144">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b62c-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="9b62c-145">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu \*\* Blazor aplikacji webassembly\*\* ( `blazorwasm` szablon używany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) ) z wybraną opcją **hostowaną** ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="9b62c-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="9b62c-146">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="9b62c-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="9b62c-147">Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="9b62c-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="9b62c-148">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="9b62c-148">Standalone deployment</span></span>

<span data-ttu-id="9b62c-149">*Wdrożenie autonomiczne* obsługuje Blazor aplikację webassembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="9b62c-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="9b62c-150">Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b62c-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="9b62c-151">Zasoby wdrażania autonomicznego są publikowane w folderze */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="9b62c-152">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9b62c-152">Azure App Service</span></span>

Blazor<span data-ttu-id="9b62c-153">Aplikacje webassembly można wdrożyć na platformie Azure App Services w systemie Windows, który hostuje aplikację w [usługach IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="9b62c-153"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="9b62c-154">Wdrożenie autonomicznej Blazor aplikacji webassembly do Azure App Service dla systemu Linux nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9b62c-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="9b62c-155">Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny.</span><span class="sxs-lookup"><span data-stu-id="9b62c-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="9b62c-156">Trwa wykonywanie pracy, aby włączyć ten scenariusz.</span><span class="sxs-lookup"><span data-stu-id="9b62c-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="9b62c-157">IIS</span><span class="sxs-lookup"><span data-stu-id="9b62c-157">IIS</span></span>

<span data-ttu-id="9b62c-158">Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="9b62c-159">Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="9b62c-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="9b62c-160">Opublikowane zasoby są tworzone w folderze */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="9b62c-161">Hostowanie zawartości folderu *publikowania* na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="9b62c-162">plik Web. config</span><span class="sxs-lookup"><span data-stu-id="9b62c-162">web.config</span></span>

<span data-ttu-id="9b62c-163">Po Blazor opublikowaniu projektu plik *Web. config* jest tworzony z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="9b62c-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="9b62c-164">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="9b62c-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="9b62c-165">*. dll*:`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="9b62c-165">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="9b62c-166">*. JSON*:`application/json`</span><span class="sxs-lookup"><span data-stu-id="9b62c-166">*.json*: `application/json`</span></span>
  * <span data-ttu-id="9b62c-167">*. wasm*:`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="9b62c-167">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="9b62c-168">*. WOFF*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9b62c-168">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="9b62c-169">*. woff2*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9b62c-169">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="9b62c-170">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="9b62c-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="9b62c-171">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="9b62c-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="9b62c-172">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji (*wwwroot/{ŻĄDANA ścieżka}*).</span><span class="sxs-lookup"><span data-stu-id="9b62c-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="9b62c-173">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="9b62c-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="9b62c-174">Użyj niestandardowego pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="9b62c-174">Use a custom web.config</span></span>

<span data-ttu-id="9b62c-175">Aby użyć niestandardowego pliku *Web. config* , umieść niestandardowy plik *Web. config* w katalogu głównym folderu projektu i Opublikuj projekt.</span><span class="sxs-lookup"><span data-stu-id="9b62c-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="9b62c-176">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="9b62c-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="9b62c-177">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="9b62c-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="9b62c-178">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="9b62c-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="9b62c-179">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9b62c-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="9b62c-180">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="9b62c-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="9b62c-181">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="9b62c-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="9b62c-182">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="9b62c-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="9b62c-183">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="9b62c-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="9b62c-184">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="9b62c-184">Copy the installer to the server.</span></span> <span data-ttu-id="9b62c-185">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="9b62c-185">Run the installer.</span></span> <span data-ttu-id="9b62c-186">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="9b62c-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="9b62c-187">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="9b62c-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="9b62c-188">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="9b62c-188">Configure the website</span></span>

<span data-ttu-id="9b62c-189">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="9b62c-190">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="9b62c-190">The folder contains:</span></span>

* <span data-ttu-id="9b62c-191">Plik *Web. config* , za pomocą którego usługi IIS konfigurują witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="9b62c-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="9b62c-192">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="9b62c-193">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="9b62c-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="9b62c-194">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="9b62c-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="9b62c-195">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="9b62c-196">Usuń program obsługi w Blazor opublikowanym pliku *Web. config* aplikacji, dodając `<handlers>` sekcję do pliku:</span><span class="sxs-lookup"><span data-stu-id="9b62c-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="9b62c-197">Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :</span><span class="sxs-lookup"><span data-stu-id="9b62c-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="9b62c-198">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="9b62c-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="9b62c-199">Ustaw ścieżkę bazową aplikacji w pliku *index. html* aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="9b62c-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="9b62c-200">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="9b62c-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="9b62c-201">Usługi IIS można konfigurować za pomocą *pliku Web. config* w celu obsłużenia zasobów Brotli lub gzip skompresowanych Blazor .</span><span class="sxs-lookup"><span data-stu-id="9b62c-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="9b62c-202">Aby zapoznać się z przykładową konfiguracją, zobacz [plik Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="9b62c-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="9b62c-203">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="9b62c-203">Troubleshooting</span></span>

<span data-ttu-id="9b62c-204">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9b62c-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="9b62c-205">Gdy moduł nie jest zainstalowany, nie można przeanalizować pliku *Web. config* przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="9b62c-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="9b62c-206">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="9b62c-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="9b62c-207">Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="9b62c-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="9b62c-208">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="9b62c-208">Azure Storage</span></span>

<span data-ttu-id="9b62c-209">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="9b62c-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="9b62c-210">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b62c-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="9b62c-211">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="9b62c-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="9b62c-212">Ustaw **nazwę dokumentu indeksu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="9b62c-213">Ustaw **ścieżkę do dokumentu błędu** `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-213">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="9b62c-214">składniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="9b62c-214"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="9b62c-215">Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**.</span><span class="sxs-lookup"><span data-stu-id="9b62c-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="9b62c-216">Zwracany jest obiekt BLOB *index. html* , a Blazor router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="9b62c-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="9b62c-217">Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="9b62c-217">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="9b62c-218">Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.</span><span class="sxs-lookup"><span data-stu-id="9b62c-218">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="9b62c-219">Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-219">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="9b62c-220">W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:</span><span class="sxs-lookup"><span data-stu-id="9b62c-220">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="9b62c-221">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="9b62c-221">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="9b62c-222">Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="9b62c-222">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="9b62c-223">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="9b62c-223">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="9b62c-224">Nginx</span><span class="sxs-lookup"><span data-stu-id="9b62c-224">Nginx</span></span>

<span data-ttu-id="9b62c-225">Następujący plik *Nginx. conf* został uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania pliku *index. html* za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="9b62c-225">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="9b62c-226">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="9b62c-226">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="9b62c-227">Nginx w Docker</span><span class="sxs-lookup"><span data-stu-id="9b62c-227">Nginx in Docker</span></span>

<span data-ttu-id="9b62c-228">Aby hostować Blazor w Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine.</span><span class="sxs-lookup"><span data-stu-id="9b62c-228">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="9b62c-229">Zaktualizuj pliku dockerfile, aby skopiować plik *Nginx. config* do kontenera.</span><span class="sxs-lookup"><span data-stu-id="9b62c-229">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="9b62c-230">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-230">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="9b62c-231">Apache</span><span class="sxs-lookup"><span data-stu-id="9b62c-231">Apache</span></span>

<span data-ttu-id="9b62c-232">Aby wdrożyć Blazor aplikację webassembly w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="9b62c-232">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="9b62c-233">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="9b62c-233">Create the Apache configuration file.</span></span> <span data-ttu-id="9b62c-234">Poniższy przykład to uproszczony plik konfiguracji (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="9b62c-234">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="9b62c-235">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="9b62c-235">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="9b62c-236">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="9b62c-236">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="9b62c-237">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="9b62c-237">Restart the Apache service.</span></span>

<span data-ttu-id="9b62c-238">Aby uzyskać więcej informacji, zobacz [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="9b62c-238">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="9b62c-239">Strony serwisu GitHub</span><span class="sxs-lookup"><span data-stu-id="9b62c-239">GitHub Pages</span></span>

<span data-ttu-id="9b62c-240">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj plik *404. html* ze skryptem, który obsługuje przekierowywanie żądania do strony *index. html* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-240">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="9b62c-241">Aby zapoznać się z przykładową implementacją dostarczoną przez społeczność, zobacz [aplikacje jednostronicowe dla stron usługi GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/Spa-GitHub-Pages w witrynie GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="9b62c-241">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="9b62c-242">Przykład użycia podejścia społecznościowego można znaleźć[w witrynie](https://blazor-demo.github.io/) [GitHub (blazor — Demonstracja/blazor-Demonstracja](https://github.com/blazor-demo/blazor-demo.github.io) ).</span><span class="sxs-lookup"><span data-stu-id="9b62c-242">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="9b62c-243">W przypadku korzystania z witryny projektu zamiast witryny organizacji Dodaj lub zaktualizuj `<base>` tag w *pliku index. html*.</span><span class="sxs-lookup"><span data-stu-id="9b62c-243">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="9b62c-244">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `my-repository/` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-244">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="9b62c-245">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="9b62c-245">Host configuration values</span></span>

<span data-ttu-id="9b62c-246">[ Blazor Aplikacje webassembly](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="9b62c-246">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="9b62c-247">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="9b62c-247">Content root</span></span>

<span data-ttu-id="9b62c-248">`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="9b62c-248">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="9b62c-249">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-249">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="9b62c-250">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="9b62c-250">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9b62c-251">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-251">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="9b62c-252">Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9b62c-252">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="9b62c-253">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-253">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="9b62c-254">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="9b62c-254">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9b62c-255">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-255">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="9b62c-256">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="9b62c-256">Path base</span></span>

<span data-ttu-id="9b62c-257">`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="9b62c-257">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="9b62c-258">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-258">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="9b62c-259">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="9b62c-259">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9b62c-260">W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-260">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="9b62c-261">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="9b62c-261">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="9b62c-262">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="9b62c-262">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9b62c-263">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-263">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="9b62c-264">Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9b62c-264">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="9b62c-265">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-265">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="9b62c-266">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="9b62c-266">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9b62c-267">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-267">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="9b62c-268">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="9b62c-268">URLs</span></span>

<span data-ttu-id="9b62c-269">`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="9b62c-269">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="9b62c-270">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="9b62c-270">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9b62c-271">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-271">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="9b62c-272">Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9b62c-272">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="9b62c-273">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9b62c-273">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="9b62c-274">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="9b62c-274">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9b62c-275">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-275">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="9b62c-276">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="9b62c-276">Configure the Linker</span></span>

Blazor<span data-ttu-id="9b62c-277">wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="9b62c-277"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="9b62c-278">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="9b62c-278">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="9b62c-279">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="9b62c-279">Custom boot resource loading</span></span>

<span data-ttu-id="9b62c-280">BlazorAplikację webassembly można zainicjować przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="9b62c-280">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="9b62c-281">Należy używać `loadBootResource` w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="9b62c-281">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="9b62c-282">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane strefy czasowej lub *dotnet. wasm* z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="9b62c-282">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="9b62c-283">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="9b62c-283">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="9b62c-284">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="9b62c-284">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="9b62c-285">`loadBootResource`Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9b62c-285">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="9b62c-286">Parametr</span><span class="sxs-lookup"><span data-stu-id="9b62c-286">Parameter</span></span>    | <span data-ttu-id="9b62c-287">Opis</span><span class="sxs-lookup"><span data-stu-id="9b62c-287">Description</span></span> |
| ---
<span data-ttu-id="9b62c-288">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-288">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-290">'Blazor'</span></span>
- <span data-ttu-id="9b62c-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-291">'Identity'</span></span>
- <span data-ttu-id="9b62c-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-293">'Razor'</span></span>
- <span data-ttu-id="9b62c-294">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b62c-295">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-295">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-297">'Blazor'</span></span>
- <span data-ttu-id="9b62c-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-298">'Identity'</span></span>
- <span data-ttu-id="9b62c-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-300">'Razor'</span></span>
- <span data-ttu-id="9b62c-301">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b62c-302">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-302">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-304">'Blazor'</span></span>
- <span data-ttu-id="9b62c-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-305">'Identity'</span></span>
- <span data-ttu-id="9b62c-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-307">'Razor'</span></span>
- <span data-ttu-id="9b62c-308">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b62c-309">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-309">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-310">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-311">'Blazor'</span></span>
- <span data-ttu-id="9b62c-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-312">'Identity'</span></span>
- <span data-ttu-id="9b62c-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-314">'Razor'</span></span>
- <span data-ttu-id="9b62c-315">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-315">'SignalR' uid:</span></span> 

<span data-ttu-id="9b62c-316">------ | ---title: "host i Deploy ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-316">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-317">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-318">'Blazor'</span></span>
- <span data-ttu-id="9b62c-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-319">'Identity'</span></span>
- <span data-ttu-id="9b62c-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-321">'Razor'</span></span>
- <span data-ttu-id="9b62c-322">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b62c-323">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-323">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-324">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-325">'Blazor'</span></span>
- <span data-ttu-id="9b62c-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-326">'Identity'</span></span>
- <span data-ttu-id="9b62c-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-328">'Razor'</span></span>
- <span data-ttu-id="9b62c-329">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9b62c-330">title: "host i wdrażanie ASP.NET Core Blazor webassembly" Author: Description: "Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b62c-330">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="9b62c-331">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9b62c-331">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9b62c-332">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-332">'Blazor'</span></span>
- <span data-ttu-id="9b62c-333">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9b62c-333">'Identity'</span></span>
- <span data-ttu-id="9b62c-334">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9b62c-334">'Let's Encrypt'</span></span>
- <span data-ttu-id="9b62c-335">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9b62c-335">'Razor'</span></span>
- <span data-ttu-id="9b62c-336">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="9b62c-336">'SignalR' uid:</span></span> 

<span data-ttu-id="9b62c-337">------ | | `type`       | Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-337">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="9b62c-338">Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` | | `name`       | Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-338">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="9b62c-339">| | `defaultUri` | Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-339">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="9b62c-340">| | `integrity`  | Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9b62c-340">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="9b62c-341">`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="9b62c-341">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="9b62c-342">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="9b62c-342">URI string.</span></span> <span data-ttu-id="9b62c-343">W poniższym przykładzie (*wwwroot/index.html*) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="9b62c-343">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="9b62c-344">*dotnet. \* . JS*</span><span class="sxs-lookup"><span data-stu-id="9b62c-344">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="9b62c-345">*dotnet. wasm*</span><span class="sxs-lookup"><span data-stu-id="9b62c-345">*dotnet.wasm*</span></span>
  * <span data-ttu-id="9b62c-346">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="9b62c-346">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="9b62c-347">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="9b62c-347">`Promise<Response>`.</span></span> <span data-ttu-id="9b62c-348">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="9b62c-348">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="9b62c-349">Poniższy przykład (*wwwroot/index.html*) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="9b62c-349">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="9b62c-350">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="9b62c-350">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="9b62c-351">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="9b62c-351">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="9b62c-352">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="9b62c-352">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="9b62c-353">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="9b62c-353">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="9b62c-354">Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="9b62c-354">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="9b62c-355">Zmień rozszerzenie nazwy pliku DLL</span><span class="sxs-lookup"><span data-stu-id="9b62c-355">Change the filename extension of DLL files</span></span>

<span data-ttu-id="9b62c-356">W przypadku konieczności zmiany rozszerzeń nazw plików *dll* aplikacji, postępuj zgodnie ze wskazówkami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="9b62c-356">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="9b62c-357">Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy plików *. dll* , aby użyć innego rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="9b62c-357">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="9b62c-358">Docelowo pliki *. dll* w katalogu *wwwroot* w opublikowanym danych wyjściowych aplikacji (na przykład *{Content root}/bin/Release/netstandard2.1/Publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="9b62c-358">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="9b62c-359">W poniższych przykładach pliki *. dll* są nazwy, aby użyć rozszerzenia pliku *bin* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-359">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="9b62c-360">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="9b62c-360">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="9b62c-361">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-361">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="9b62c-362">W systemie Linux lub macOS:</span><span class="sxs-lookup"><span data-stu-id="9b62c-362">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="9b62c-363">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-363">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="9b62c-364">Aby użyć innego rozszerzenia pliku niż *. bin*, należy zastąpić *. bin* w poprzednich poleceniach.</span><span class="sxs-lookup"><span data-stu-id="9b62c-364">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="9b62c-365">Aby rozwiązać skompresowane pliki *blazor. boot. JSON. gz* i *blazor.boot.JSON.br* , należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9b62c-365">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="9b62c-366">Usuń skompresowane pliki *blazor. boot. JSON. gz* i *blazor.boot.JSON.br* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-366">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="9b62c-367">Kompresja jest wyłączona w tym podejściu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-367">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="9b62c-368">Ponownie skompresuje zaktualizowany plik *blazor. boot. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9b62c-368">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="9b62c-369">Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi.</span><span class="sxs-lookup"><span data-stu-id="9b62c-369">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="9b62c-370">Usuń lub rekompresuj plik *wwwroot/Service-Worker-Assets. js. br* i *wwwroot/Service-Worker-Assets. js. gz*.</span><span class="sxs-lookup"><span data-stu-id="9b62c-370">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="9b62c-371">W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="9b62c-371">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="9b62c-372">Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="9b62c-372">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="9b62c-373">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="9b62c-373">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="9b62c-374">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9b62c-374">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="9b62c-375">W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9b62c-375">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="9b62c-376">Aby przekazać opinię, odwiedź stronę [aspnetcore/problemy #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="9b62c-376">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
