---
title: Hostowanie i wdrażanie Blazor ASP.NET Core WebAssembly
author: guardrex
description: Dowiedz się, jak Blazor hostować i wdrażać aplikację przy użyciu ASP.NET Core, Content Delivery Networks (CDN), serwerów plików i stron GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751130"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="5fb47-103">Hostowanie i wdrażanie Blazor ASP.NET Core WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5fb47-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="5fb47-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5fb47-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="5fb47-105">Z [ Blazor webassembly hosting modelu:](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="5fb47-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="5fb47-106">Aplikacja, Blazor jej zależności i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5fb47-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="5fb47-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5fb47-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="5fb47-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="5fb47-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="5fb47-109">Aplikacja Blazor jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fb47-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="5fb47-110">Ta strategia jest omówiona w [hostowane wdrożenie z ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="5fb47-111">Aplikacja Blazor jest umieszczana na statycznym serwerze www hostingu, gdzie .NET nie jest używana do obsługi Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="5fb47-112">Ta strategia jest omówiona w sekcji [samodzielne wdrażanie,](#standalone-deployment) która zawiera informacje na temat hostingu Blazor aplikacji WebAssembly jako podkoszuli IIS.</span><span class="sxs-lookup"><span data-stu-id="5fb47-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="5fb47-113">Przepisz adresy URL w celu prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="5fb47-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="5fb47-114">Żądania routingu składników Blazor strony w aplikacji WebAssembly nie jest tak Blazor proste, jak żądania routingu w aplikacji serwerowej, hostowane.</span><span class="sxs-lookup"><span data-stu-id="5fb47-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="5fb47-115">Należy Blazor wziąć pod uwagę webassembly aplikacji z dwóch składników:</span><span class="sxs-lookup"><span data-stu-id="5fb47-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="5fb47-116">*Main.brzytwa* &ndash; Ładuje się w katalogu głównym `About` aplikacji`href="About"`i zawiera link do składnika ( ).</span><span class="sxs-lookup"><span data-stu-id="5fb47-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="5fb47-117">*Składnik About.brzytwa.* &ndash; `About`</span><span class="sxs-lookup"><span data-stu-id="5fb47-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="5fb47-118">Gdy wymagany jest domyślny dokument aplikacji przy użyciu paska adresu `https://www.contoso.com/`przeglądarki (na przykład):</span><span class="sxs-lookup"><span data-stu-id="5fb47-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="5fb47-119">Przeglądarka składa żądanie.</span><span class="sxs-lookup"><span data-stu-id="5fb47-119">The browser makes a request.</span></span>
1. <span data-ttu-id="5fb47-120">Zwracana jest strona domyślna, która jest zwykle *index.html*.</span><span class="sxs-lookup"><span data-stu-id="5fb47-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="5fb47-121">*index.html* bootstraps aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-121">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="5fb47-122">'s ładuje się, a `Main` komponent Razor jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="5fb47-122">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="5fb47-123">Na stronie głównej wybranie łącza `About` do składnika działa Blazor na kliencie, ponieważ router uniemożliwia `www.contoso.com` przeglądarce składanie żądania w Internecie do i `About` służy renderowane składnika. `About`</span><span class="sxs-lookup"><span data-stu-id="5fb47-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="5fb47-124">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w aplikacji Blazor WebAssembly* działają w ten sam sposób: żądania nie wyzwalają żądań opartych na przeglądarce do zasobów hostowanych na serwerze w Internecie.</span><span class="sxs-lookup"><span data-stu-id="5fb47-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="5fb47-125">Router obsługuje żądania wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="5fb47-125">The router handles the requests internally.</span></span>

<span data-ttu-id="5fb47-126">Jeśli żądanie zostanie złożone przy użyciu paska adresu przeglądarki dla `www.contoso.com/About`, żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="5fb47-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="5fb47-127">Nie istnieje taki zasób na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="5fb47-128">Ponieważ przeglądarki żądają hostów internetowych dla stron po stronie klienta, serwery sieci web i usługi hostingowe muszą przepisać wszystkie żądania dotyczące zasobów, które nie są fizycznie na serwerze, na stronie *index.html.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="5fb47-129">Po powrocie *pliku index.html* Blazor router aplikacji przejmuje kontrolę nad poprawnym zasobem i odpowiada za pomocą odpowiedniego zasobu.</span><span class="sxs-lookup"><span data-stu-id="5fb47-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="5fb47-130">Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisu adresu URL z opublikowanym *plikiem web.config* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="5fb47-131">Aby uzyskać więcej informacji, zobacz sekcję [IIS.](#iis)</span><span class="sxs-lookup"><span data-stu-id="5fb47-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="5fb47-132">Hostowane wdrażanie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fb47-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="5fb47-133">*Wdrożenie hostowane* obsługuje Blazor aplikację WebAssembly do przeglądarek z [aplikacji ASP.NET Core,](xref:index) która działa na serwerze sieci web.</span><span class="sxs-lookup"><span data-stu-id="5fb47-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="5fb47-134">Aplikacja Blazor WebAssembly klienta jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* aplikacji serwera wraz z innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="5fb47-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="5fb47-135">Dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="5fb47-135">The two apps are deployed together.</span></span> <span data-ttu-id="5fb47-136">Wymagany jest serwer sieci web, który jest w stanie hostowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5fb47-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="5fb47-137">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon`blazorwasm` projektu \*\* Blazor aplikacji WebAssembly\*\* (szablon podczas korzystania`-ho|--hosted` z nowego `dotnet new` polecenia [dotnet)](/dotnet/core/tools/dotnet-new) z wybraną opcją **Hosted** ( podczas korzystania z polecenia).</span><span class="sxs-lookup"><span data-stu-id="5fb47-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="5fb47-138">Aby uzyskać więcej informacji na temat hostingu <xref:host-and-deploy/index>i wdrażania aplikacji core ASP.NET, zobacz .</span><span class="sxs-lookup"><span data-stu-id="5fb47-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="5fb47-139">Aby uzyskać informacje na temat wdrażania <xref:tutorials/publish-to-azure-webapp-using-vs>w usłudze Azure App Service, zobacz .</span><span class="sxs-lookup"><span data-stu-id="5fb47-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="5fb47-140">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="5fb47-140">Standalone deployment</span></span>

<span data-ttu-id="5fb47-141">*Wdrożenie autonomiczne* służy Blazor aplikacji WebAssembly jako zestaw plików statycznych, które są wymagane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="5fb47-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="5fb47-142">Każdy statyczny serwer plików jest Blazor w stanie obsługiwać aplikację.</span><span class="sxs-lookup"><span data-stu-id="5fb47-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="5fb47-143">Autonomiczne zasoby wdrażania są publikowane w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="5fb47-144">IIS</span><span class="sxs-lookup"><span data-stu-id="5fb47-144">IIS</span></span>

<span data-ttu-id="5fb47-145">Usługi IIS to zdolny statyczny serwer plików dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="5fb47-146">Aby skonfigurować programy IIS do hosta, Blazorzobacz Tworzenie [statycznej witryny sieci Web w serwisie IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="5fb47-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="5fb47-147">Opublikowane zasoby są tworzone w folderze */bin/Release/{TARGET FRAMEWORK}/publish.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="5fb47-148">Hostuj zawartość folderu *publikowania* na serwerze www lub usłudze hostingowej.</span><span class="sxs-lookup"><span data-stu-id="5fb47-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="5fb47-149">Web.config</span><span class="sxs-lookup"><span data-stu-id="5fb47-149">web.config</span></span>

<span data-ttu-id="5fb47-150">Po Blazor opublikowaniu projektu tworzony jest plik *web.config* z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="5fb47-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="5fb47-151">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="5fb47-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="5fb47-152">*.dll (dll)* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="5fb47-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="5fb47-153">*.json (json)* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="5fb47-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="5fb47-154">*.wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="5fb47-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="5fb47-155">*.woff (woff)* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="5fb47-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="5fb47-156">*.woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="5fb47-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="5fb47-157">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="5fb47-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="5fb47-158">Reguły modułu zapisywania adresów URL są ustanawiane:</span><span class="sxs-lookup"><span data-stu-id="5fb47-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="5fb47-159">Służy podkatarenie, w którym znajdują się zasoby statyczne aplikacji (*wwwroot/{PATH REQUESTED}*).</span><span class="sxs-lookup"><span data-stu-id="5fb47-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="5fb47-160">Utwórz routing rezerwowy SPA, aby żądania dotyczące zasobów innych niż pliki były przekierowywane do domyślnego dokumentu aplikacji w folderze zasobów statycznych (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="5fb47-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="5fb47-161">Korzystanie z niestandardowego pliku web.config</span><span class="sxs-lookup"><span data-stu-id="5fb47-161">Use a custom web.config</span></span>

<span data-ttu-id="5fb47-162">Aby użyć niestandardowego pliku *web.config:*</span><span class="sxs-lookup"><span data-stu-id="5fb47-162">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="5fb47-163">Umieść niestandardowy plik *web.config* w katalogu głównym folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="5fb47-163">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="5fb47-164">Dodaj następujący obiekt docelowy do pliku projektu (*.csproj*):</span><span class="sxs-lookup"><span data-stu-id="5fb47-164">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="5fb47-165">`<IsWebConfigTransformDisabled>` Użycie właściwości MSBuild ustawionej `true` na nie Blazor jest obsługiwane w aplikacjach WebAssembly, [tak jak w przypadku aplikacji ASP.NET Core wdrożonych w serwisach IIS](xref:host-and-deploy/iis/index#webconfig-file).</span><span class="sxs-lookup"><span data-stu-id="5fb47-165">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="5fb47-166">Aby uzyskać więcej informacji, zobacz [Kopiowanie obiektu docelowego Blazor wymaganego do podania niestandardowego pliku WEB.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span><span class="sxs-lookup"><span data-stu-id="5fb47-166">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="5fb47-167">Instalowanie modułu przepisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="5fb47-167">Install the URL Rewrite Module</span></span>

<span data-ttu-id="5fb47-168">[Moduł przepisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisu adresów URL.</span><span class="sxs-lookup"><span data-stu-id="5fb47-168">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="5fb47-169">Moduł nie jest zainstalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli serwera sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="5fb47-169">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="5fb47-170">Moduł należy pobrać ze strony internetowej IIS.</span><span class="sxs-lookup"><span data-stu-id="5fb47-170">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="5fb47-171">Użyj Instalatora platformy sieci Web, aby zainstalować moduł:</span><span class="sxs-lookup"><span data-stu-id="5fb47-171">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="5fb47-172">Lokalnie przejdź do [strony Pliki do pobrania modułu ponownego zapisu adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="5fb47-172">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="5fb47-173">W przypadku wersji angielskiej wybierz pozycję **WebPI,** aby pobrać instalatora webpi.</span><span class="sxs-lookup"><span data-stu-id="5fb47-173">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="5fb47-174">W przypadku innych języków wybierz odpowiednią architekturę serwera (x86/x64), aby pobrać instalator.</span><span class="sxs-lookup"><span data-stu-id="5fb47-174">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="5fb47-175">Skopiuj instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="5fb47-175">Copy the installer to the server.</span></span> <span data-ttu-id="5fb47-176">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="5fb47-176">Run the installer.</span></span> <span data-ttu-id="5fb47-177">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="5fb47-177">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="5fb47-178">Ponowne uruchomienie serwera nie jest wymagane po zakończeniu instalacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-178">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="5fb47-179">Konfigurowanie witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="5fb47-179">Configure the website</span></span>

<span data-ttu-id="5fb47-180">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-180">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="5fb47-181">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="5fb47-181">The folder contains:</span></span>

* <span data-ttu-id="5fb47-182">Plik *web.config* używany przez usługi IIS do konfigurowania witryny sieci Web, w tym wymagane reguły przekierowania i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="5fb47-182">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="5fb47-183">Statyczny folder zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-183">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="5fb47-184">Host jako podnauka IIS</span><span class="sxs-lookup"><span data-stu-id="5fb47-184">Host as an IIS sub-app</span></span>

<span data-ttu-id="5fb47-185">Jeśli autonomiczna aplikacja jest hostowana jako podnauka IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="5fb47-185">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="5fb47-186">Wyłącz program obsługi dziedziczonego modułu ASP.NET core.</span><span class="sxs-lookup"><span data-stu-id="5fb47-186">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="5fb47-187">Usuń program obsługi Blazor w opublikowanym pliku *web.config* aplikacji, dodając sekcję `<handlers>` do pliku:</span><span class="sxs-lookup"><span data-stu-id="5fb47-187">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="5fb47-188">Wyłącz dziedziczenie sekcji głównej `<system.webServer>` (nadrzędnej) `<location>` aplikacji `inheritInChildApplications` przy `false`użyciu elementu z ustawionym na:</span><span class="sxs-lookup"><span data-stu-id="5fb47-188">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="5fb47-189">Usunięcie programu obsługi lub wyłączenie dziedziczenia jest wykonywane oprócz [konfigurowania ścieżki podstawowej aplikacji](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="5fb47-189">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="5fb47-190">Ustaw ścieżkę podstawową aplikacji w pliku *index.html* aplikacji na alias IIS używany podczas konfigurowania poddołty w programach IIS.</span><span class="sxs-lookup"><span data-stu-id="5fb47-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="5fb47-191">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5fb47-191">Troubleshooting</span></span>

<span data-ttu-id="5fb47-192">Jeśli zostanie odebrany *błąd serwera 500 — wewnętrzny,* a menedżer usług IIS zgłasza błędy podczas próby uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że jest zainstalowany moduł przepisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="5fb47-192">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="5fb47-193">Gdy moduł nie jest zainstalowany, plik *web.config* nie może być analizowany przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="5fb47-193">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="5fb47-194">Uniemożliwia to menedżerowi usług IIS ładowanie konfiguracji witryny sieci BlazorWeb, a witryny sieci Web wyświetlania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="5fb47-194">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="5fb47-195">Aby uzyskać więcej informacji na temat rozwiązywania <xref:test/troubleshoot-azure-iis>problemów z wdrożeniami w usługach IIS, zobacz .</span><span class="sxs-lookup"><span data-stu-id="5fb47-195">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="5fb47-196">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="5fb47-196">Azure Storage</span></span>

<span data-ttu-id="5fb47-197">[Hosting](/azure/storage/) plików statycznych usługi Blazor Azure Storage umożliwia hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="5fb47-197">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="5fb47-198">Obsługiwane są niestandardowe nazwy domen, sieć dostarczania zawartości platformy Azure (CDN) i https.</span><span class="sxs-lookup"><span data-stu-id="5fb47-198">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="5fb47-199">Gdy usługa obiektu blob jest włączona dla statycznego hostingu witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="5fb47-199">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="5fb47-200">Ustaw **nazwę dokumentu** `index.html`Indeks na .</span><span class="sxs-lookup"><span data-stu-id="5fb47-200">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="5fb47-201">Ustaw **ścieżkę** dokumentu `index.html`Błąd na .</span><span class="sxs-lookup"><span data-stu-id="5fb47-201">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="5fb47-202">Składniki maszynki do golenia i inne punkty końcowe niebędące plikami nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę obiektu blob.</span><span class="sxs-lookup"><span data-stu-id="5fb47-202">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="5fb47-203">Po odebraniu żądania jednego z Blazor tych zasobów, które router powinien obsłużyć, błąd *404 - Nie znaleziono* wygenerowany przez usługę obiektu blob kieruje żądanie do **ścieżki dokumentu błąd**.</span><span class="sxs-lookup"><span data-stu-id="5fb47-203">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="5fb47-204">Obiekt blob *index.html* jest Blazor zwracany, a router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="5fb47-204">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="5fb47-205">Aby uzyskać więcej informacji, zobacz [Hosting statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="5fb47-205">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="5fb47-206">Nginx</span><span class="sxs-lookup"><span data-stu-id="5fb47-206">Nginx</span></span>

<span data-ttu-id="5fb47-207">Następujący plik *nginx.conf* jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania pliku *index.html,* gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="5fb47-207">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="5fb47-208">Aby uzyskać więcej informacji na temat produkcji konfiguracji serwera sieci Web Nginx, zobacz [Tworzenie plików konfiguracyjnych NGINX Plus i NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="5fb47-208">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="5fb47-209">Nginx w mieście Docker</span><span class="sxs-lookup"><span data-stu-id="5fb47-209">Nginx in Docker</span></span>

<span data-ttu-id="5fb47-210">Aby Blazor hostować w utoke za pomocą platformy Nginx, skonfiguruj plik Dockerfile, aby użyć obrazu Nginx opartego na masie.</span><span class="sxs-lookup"><span data-stu-id="5fb47-210">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="5fb47-211">Zaktualizuj plik Dockerfile, aby skopiować plik *nginx.config* do kontenera.</span><span class="sxs-lookup"><span data-stu-id="5fb47-211">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="5fb47-212">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5fb47-212">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="5fb47-213">Apache</span><span class="sxs-lookup"><span data-stu-id="5fb47-213">Apache</span></span>

<span data-ttu-id="5fb47-214">Aby wdrożyć aplikację Blazor WebAssembly w centos 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="5fb47-214">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="5fb47-215">Utwórz plik konfiguracyjny Apache.</span><span class="sxs-lookup"><span data-stu-id="5fb47-215">Create the Apache configuration file.</span></span> <span data-ttu-id="5fb47-216">Poniższy przykład to uproszczony plik konfiguracyjny (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="5fb47-216">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="5fb47-217">Umieść plik konfiguracyjny `/etc/httpd/conf.d/` Apache w katalogu, który jest domyślnym katalogiem konfiguracyjnym Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="5fb47-217">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="5fb47-218">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="5fb47-218">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="5fb47-219">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="5fb47-219">Restart the Apache service.</span></span>

<span data-ttu-id="5fb47-220">Aby uzyskać więcej informacji, zobacz [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="5fb47-220">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="5fb47-221">Strony GitHub</span><span class="sxs-lookup"><span data-stu-id="5fb47-221">GitHub Pages</span></span>

<span data-ttu-id="5fb47-222">Aby obsłużyć przepisywanie adresów URL, dodaj plik *404.html* ze skryptem obsługującym przekierowanie żądania do strony *index.html.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-222">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="5fb47-223">Aby uzyskać przykładową implementację udostępnianą przez społeczność, zobacz [Aplikacje jednostronicowe dla stron GitHub](https://spa-github-pages.rafrex.com/) [(rafrex/spa-github-pages w usłudze GitHub).](https://github.com/rafrex/spa-github-pages#readme)</span><span class="sxs-lookup"><span data-stu-id="5fb47-223">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="5fb47-224">Przykład przy użyciu podejścia społeczności można zobaczyć na [blazor-demo/blazor-demo.github.io na GitHub](https://github.com/blazor-demo/blazor-demo.github.io) [(live site).](https://blazor-demo.github.io/)</span><span class="sxs-lookup"><span data-stu-id="5fb47-224">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="5fb47-225">W przypadku korzystania z witryny projektu zamiast witryny organizacji dodaj lub zaktualizuj `<base>` znacznik w pliku *index.html*.</span><span class="sxs-lookup"><span data-stu-id="5fb47-225">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="5fb47-226">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub za pomocą `my-repository/`końcowego ukośnika (na przykład .</span><span class="sxs-lookup"><span data-stu-id="5fb47-226">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="5fb47-227">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="5fb47-227">Host configuration values</span></span>

<span data-ttu-id="5fb47-228">Aplikacje WebAssembly mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="5fb47-228">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="5fb47-229">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="5fb47-229">Content root</span></span>

<span data-ttu-id="5fb47-230">Argument `--contentroot` ustawia ścieżkę bezwzględną do katalogu zawierającego pliki zawartości aplikacji[(katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="5fb47-230">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="5fb47-231">W poniższych przykładach `/content-root-path` jest ścieżka katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-231">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="5fb47-232">Przekaż argument podczas uruchamiania aplikacji lokalnie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="5fb47-232">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="5fb47-233">Z katalogu aplikacji wykonaj następujące wykonanie:</span><span class="sxs-lookup"><span data-stu-id="5fb47-233">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="5fb47-234">Dodaj wpis do pliku *launchSettings.json* aplikacji w profilu **IIS Express.**</span><span class="sxs-lookup"><span data-stu-id="5fb47-234">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="5fb47-235">To ustawienie jest używane, gdy aplikacja jest uruchamiana za pomocą `dotnet run`debugera programu Visual Studio i z wiersza polecenia z programem .</span><span class="sxs-lookup"><span data-stu-id="5fb47-235">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="5fb47-236">W programie Visual Studio określ argument w **argumentach** > Właściwości**debugowania** > **aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="5fb47-236">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="5fb47-237">Ustawienie argumentu na stronie właściwości programu Visual Studio dodaje argument do pliku *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-237">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="5fb47-238">Podstawa ścieżki</span><span class="sxs-lookup"><span data-stu-id="5fb47-238">Path base</span></span>

<span data-ttu-id="5fb47-239">Argument `--pathbase` ustawia ścieżkę podstawową aplikacji dla aplikacji uruchamianej lokalnie ze `<base>` `href` ścieżką względnego adresu `/` URL niebędącego głównym (tag jest ustawiony na ścieżkę inną niż dla przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="5fb47-239">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="5fb47-240">W poniższych przykładach `/relative-URL-path` jest baza ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5fb47-240">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="5fb47-241">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="5fb47-241">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5fb47-242">W przeciwieństwie do `href` ścieżki `<base>` podanej do znacznika, nie`/`należy dołączać końcowego ukośnika ( ) podczas przekazywania wartości argumentu. `--pathbase`</span><span class="sxs-lookup"><span data-stu-id="5fb47-242">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="5fb47-243">Jeśli ścieżka podstawowa aplikacji `<base>` znajduje `<base href="/CoolApp/">` się w tagu jako (zawiera ukośnik końcowy), przekaż wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez końcowego ukośnika).</span><span class="sxs-lookup"><span data-stu-id="5fb47-243">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="5fb47-244">Przekaż argument podczas uruchamiania aplikacji lokalnie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="5fb47-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="5fb47-245">Z katalogu aplikacji wykonaj następujące wykonanie:</span><span class="sxs-lookup"><span data-stu-id="5fb47-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="5fb47-246">Dodaj wpis do pliku *launchSettings.json* aplikacji w profilu **IIS Express.**</span><span class="sxs-lookup"><span data-stu-id="5fb47-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="5fb47-247">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i wiersza polecenia z programem `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="5fb47-247">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="5fb47-248">W programie Visual Studio określ argument w **argumentach** > Właściwości**debugowania** > **aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="5fb47-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="5fb47-249">Ustawienie argumentu na stronie właściwości programu Visual Studio dodaje argument do pliku *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="5fb47-250">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="5fb47-250">URLs</span></span>

<span data-ttu-id="5fb47-251">Argument `--urls` ustawia adresy IP lub adresy hostów z portami i protokołami do nasłuchiwania żądań.</span><span class="sxs-lookup"><span data-stu-id="5fb47-251">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="5fb47-252">Przekaż argument podczas uruchamiania aplikacji lokalnie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="5fb47-252">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="5fb47-253">Z katalogu aplikacji wykonaj następujące wykonanie:</span><span class="sxs-lookup"><span data-stu-id="5fb47-253">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="5fb47-254">Dodaj wpis do pliku *launchSettings.json* aplikacji w profilu **IIS Express.**</span><span class="sxs-lookup"><span data-stu-id="5fb47-254">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="5fb47-255">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i wiersza polecenia z programem `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="5fb47-255">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="5fb47-256">W programie Visual Studio określ argument w **argumentach** > Właściwości**debugowania** > **aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="5fb47-256">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="5fb47-257">Ustawienie argumentu na stronie właściwości programu Visual Studio dodaje argument do pliku *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5fb47-257">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="5fb47-258">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="5fb47-258">Configure the Linker</span></span>

Blazor<span data-ttu-id="5fb47-259">wykonuje łącze języka pośredniego (IL) na każdej kompilacji release, aby usunąć niepotrzebne IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="5fb47-259"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="5fb47-260">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="5fb47-260">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
