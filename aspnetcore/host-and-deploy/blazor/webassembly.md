---
title: Hostowanie i wdrażanie Blazor ASP.NET Core webassembly
author: guardrex
description: Dowiedz się, jak hostować Blazor i wdrażać aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 2472fd499128a8807b76a3cc031d466140e180f5
ms.sourcegitcommit: 23243f6d6a3100303802e4310b0634860cc0b268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619372"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="775dd-103">Hostowanie i wdrażanie ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="775dd-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="775dd-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="775dd-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="775dd-105">Z [modelem hostingu Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="775dd-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="775dd-106">Aplikacja Blazor, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="775dd-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="775dd-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="775dd-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="775dd-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="775dd-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="775dd-109">Aplikacja Blazor jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="775dd-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="775dd-110">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="775dd-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="775dd-111">Aplikacja Blazor jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="775dd-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="775dd-112">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu aplikacji Blazor webassembly jako aplikacji PODRZĘDNEJ usług IIS.</span><span class="sxs-lookup"><span data-stu-id="775dd-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="775dd-113">Precompression Brotli</span><span class="sxs-lookup"><span data-stu-id="775dd-113">Brotli precompression</span></span>

<span data-ttu-id="775dd-114">Po opublikowaniu aplikacji Blazor webassembly dane wyjściowe są dekompresowane przy użyciu [algorytmu kompresji Brotli](https://tools.ietf.org/html/rfc7932) na najwyższym poziomie w celu zmniejszenia rozmiaru aplikacji i usunięcia potrzeby kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="775dd-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="775dd-115">Aby poznać konfigurację kompresji *Web. config* usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="775dd-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="775dd-116">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="775dd-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="775dd-117">Żądania routingu dla składników strony w aplikacji Blazor webassembly nie są tak proste jak żądania routingu na serwerze Blazor, hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="775dd-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="775dd-118">Rozważmy aplikację webassembly Blazor z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="775dd-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="775dd-119">*Główny. Razor* &ndash; ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="775dd-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="775dd-120">*Informacje o* &ndash; `About` składniku Razor.</span><span class="sxs-lookup"><span data-stu-id="775dd-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="775dd-121">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="775dd-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="775dd-122">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="775dd-122">The browser makes a request.</span></span>
1. <span data-ttu-id="775dd-123">Zostanie zwrócona strona domyślna, która jest zwykle *index. html*.</span><span class="sxs-lookup"><span data-stu-id="775dd-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="775dd-124">*index. html* Bootstrap aplikację.</span><span class="sxs-lookup"><span data-stu-id="775dd-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="775dd-125">Ładowanie routera Blazor, a składnik Razor `Main` jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="775dd-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="775dd-126">Na stronie głównej `About` wybranie linku do składnika działa na kliencie, ponieważ router Blazor zatrzyma w przeglądarce żądanie połączenia z Internetem `www.contoso.com` `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="775dd-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="775dd-127">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w aplikacji Blazor webassembly* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="775dd-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="775dd-128">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="775dd-128">The router handles the requests internally.</span></span>

<span data-ttu-id="775dd-129">Żądanie kończy się niepowodzeniem `www.contoso.com/About`, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="775dd-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="775dd-130">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="775dd-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="775dd-131">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze, na stronie *index. html* .</span><span class="sxs-lookup"><span data-stu-id="775dd-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="775dd-132">Po zwróceniu elementu *index. html* router Blazor aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="775dd-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="775dd-133">Podczas wdrażania na serwerze IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym plikiem *Web. config* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="775dd-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="775dd-134">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="775dd-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="775dd-135">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="775dd-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="775dd-136">*Wdrożenie hostowane* służy do obsługi aplikacji Blazor webassembly w przeglądarkach z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="775dd-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="775dd-137">Aplikacja webassembly Blazor klienta jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* aplikacji serwerowej wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="775dd-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="775dd-138">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="775dd-138">The two apps are deployed together.</span></span> <span data-ttu-id="775dd-139">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="775dd-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="775dd-140">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu **aplikacji Webassembly Blazor** (`blazorwasm` szablon używany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) ) z wybraną opcją **hostowaną** (`-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="775dd-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="775dd-141">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji <xref:host-and-deploy/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="775dd-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="775dd-142">Aby uzyskać informacje na temat wdrażania do Azure App Service <xref:tutorials/publish-to-azure-webapp-using-vs>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="775dd-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="775dd-143">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="775dd-143">Standalone deployment</span></span>

<span data-ttu-id="775dd-144">*Wdrożenie autonomiczne* obsługuje aplikację webassembly Blazor jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="775dd-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="775dd-145">Każdy statyczny serwer plików jest w stanie obsłużyć aplikację Blazor.</span><span class="sxs-lookup"><span data-stu-id="775dd-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="775dd-146">Zasoby wdrażania autonomicznego są publikowane w folderze */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="775dd-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="775dd-147">IIS</span><span class="sxs-lookup"><span data-stu-id="775dd-147">IIS</span></span>

<span data-ttu-id="775dd-148">Usługi IIS to obsługujący statyczny serwer plików dla aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="775dd-148">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="775dd-149">Aby skonfigurować usługi IIS do hostowania Blazor, zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="775dd-149">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="775dd-150">Opublikowane zasoby są tworzone w folderze */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="775dd-150">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="775dd-151">Hostowanie zawartości folderu *publikowania* na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="775dd-151">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="775dd-152">plik Web. config</span><span class="sxs-lookup"><span data-stu-id="775dd-152">web.config</span></span>

<span data-ttu-id="775dd-153">Po opublikowaniu projektu Blazor zostanie utworzony plik *Web. config* z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="775dd-153">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="775dd-154">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="775dd-154">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="775dd-155">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="775dd-155">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="775dd-156">*. JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="775dd-156">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="775dd-157">*. wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="775dd-157">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="775dd-158">*. WOFF* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="775dd-158">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="775dd-159">*. woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="775dd-159">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="775dd-160">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="775dd-160">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="775dd-161">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="775dd-161">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="775dd-162">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji (*wwwroot/{ŻĄDANA ścieżka}*).</span><span class="sxs-lookup"><span data-stu-id="775dd-162">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="775dd-163">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="775dd-163">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="775dd-164">Użyj niestandardowego pliku Web. config</span><span class="sxs-lookup"><span data-stu-id="775dd-164">Use a custom web.config</span></span>

<span data-ttu-id="775dd-165">Aby użyć niestandardowego pliku *Web. config* , umieść niestandardowy plik *Web. config* w katalogu głównym folderu projektu i Opublikuj projekt.</span><span class="sxs-lookup"><span data-stu-id="775dd-165">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="775dd-166">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="775dd-166">Install the URL Rewrite Module</span></span>

<span data-ttu-id="775dd-167">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="775dd-167">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="775dd-168">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="775dd-168">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="775dd-169">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="775dd-169">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="775dd-170">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="775dd-170">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="775dd-171">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="775dd-171">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="775dd-172">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="775dd-172">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="775dd-173">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="775dd-173">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="775dd-174">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="775dd-174">Copy the installer to the server.</span></span> <span data-ttu-id="775dd-175">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="775dd-175">Run the installer.</span></span> <span data-ttu-id="775dd-176">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="775dd-176">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="775dd-177">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="775dd-177">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="775dd-178">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="775dd-178">Configure the website</span></span>

<span data-ttu-id="775dd-179">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="775dd-179">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="775dd-180">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="775dd-180">The folder contains:</span></span>

* <span data-ttu-id="775dd-181">Plik *Web. config* , za pomocą którego usługi IIS konfigurują witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="775dd-181">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="775dd-182">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="775dd-182">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="775dd-183">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="775dd-183">Host as an IIS sub-app</span></span>

<span data-ttu-id="775dd-184">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="775dd-184">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="775dd-185">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="775dd-185">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="775dd-186">Usuń program obsługi w opublikowanym pliku *Web. config* aplikacji Blazor przez dodanie `<handlers>` sekcji do pliku:</span><span class="sxs-lookup"><span data-stu-id="775dd-186">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="775dd-187">Wyłącz dziedziczenie `<system.webServer>` sekcji głównej (nadrzędnej) aplikacji przy użyciu `<location>` elementu z ustawioną opcją: `inheritInChildApplications` `false`</span><span class="sxs-lookup"><span data-stu-id="775dd-187">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="775dd-188">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="775dd-188">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="775dd-189">Ustaw ścieżkę bazową aplikacji w pliku *index. html* aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="775dd-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="775dd-190">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="775dd-190">Brotli and Gzip compression</span></span>

<span data-ttu-id="775dd-191">Usługi IIS można konfigurować za pomocą *pliku Web. config* w celu obsłużenia Brotli lub skompresowanych zasobów Blazor.</span><span class="sxs-lookup"><span data-stu-id="775dd-191">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="775dd-192">Aby zapoznać się z przykładową konfiguracją, zobacz [plik Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="775dd-192">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="775dd-193">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="775dd-193">Troubleshooting</span></span>

<span data-ttu-id="775dd-194">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="775dd-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="775dd-195">Gdy moduł nie jest zainstalowany, nie można przeanalizować pliku *Web. config* przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="775dd-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="775dd-196">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web do obsługi plików statycznych Blazor.</span><span class="sxs-lookup"><span data-stu-id="775dd-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="775dd-197">Aby uzyskać więcej informacji na temat rozwiązywania problemów z <xref:test/troubleshoot-azure-iis>wdrożeniami w usługach IIS, zobacz.</span><span class="sxs-lookup"><span data-stu-id="775dd-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="775dd-198">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="775dd-198">Azure Storage</span></span>

<span data-ttu-id="775dd-199">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia hosting aplikacji bezserwerowych Blazor.</span><span class="sxs-lookup"><span data-stu-id="775dd-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="775dd-200">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="775dd-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="775dd-201">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="775dd-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="775dd-202">Ustaw **nazwę dokumentu indeksu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="775dd-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="775dd-203">Ustaw ścieżkę do `index.html` **dokumentu błędu** .</span><span class="sxs-lookup"><span data-stu-id="775dd-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="775dd-204">Składniki Razor i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="775dd-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="775dd-205">Po otrzymaniu żądania dla jednego z tych zasobów, który powinien zostać obsłużony przez router Blazor, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**.</span><span class="sxs-lookup"><span data-stu-id="775dd-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="775dd-206">Zwracany jest obiekt BLOB *index. html* , a router Blazor ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="775dd-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="775dd-207">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="775dd-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="775dd-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="775dd-208">Nginx</span></span>

<span data-ttu-id="775dd-209">Następujący plik *Nginx. conf* został uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania pliku *index. html* za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="775dd-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="775dd-210">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="775dd-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="775dd-211">Nginx w Docker</span><span class="sxs-lookup"><span data-stu-id="775dd-211">Nginx in Docker</span></span>

<span data-ttu-id="775dd-212">Aby hostować Blazor w platformie Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine.</span><span class="sxs-lookup"><span data-stu-id="775dd-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="775dd-213">Zaktualizuj pliku dockerfile, aby skopiować plik *Nginx. config* do kontenera.</span><span class="sxs-lookup"><span data-stu-id="775dd-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="775dd-214">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="775dd-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="775dd-215">Apache</span><span class="sxs-lookup"><span data-stu-id="775dd-215">Apache</span></span>

<span data-ttu-id="775dd-216">Aby wdrożyć aplikację webassembly Blazor w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="775dd-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="775dd-217">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="775dd-217">Create the Apache configuration file.</span></span> <span data-ttu-id="775dd-218">Poniższy przykład to uproszczony plik konfiguracji (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="775dd-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="775dd-219">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="775dd-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="775dd-220">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="775dd-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="775dd-221">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="775dd-221">Restart the Apache service.</span></span>

<span data-ttu-id="775dd-222">Aby uzyskać więcej informacji, zobacz [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="775dd-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="775dd-223">Strony serwisu GitHub</span><span class="sxs-lookup"><span data-stu-id="775dd-223">GitHub Pages</span></span>

<span data-ttu-id="775dd-224">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj plik *404. html* ze skryptem, który obsługuje przekierowywanie żądania do strony *index. html* .</span><span class="sxs-lookup"><span data-stu-id="775dd-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="775dd-225">Aby zapoznać się z przykładową implementacją dostarczoną przez społeczność, zobacz [aplikacje jednostronicowe dla stron usługi GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/Spa-GitHub-Pages w witrynie GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="775dd-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="775dd-226">Przykład użycia podejścia społecznościowego można znaleźć[w witrynie](https://blazor-demo.github.io/) [GitHub (blazor — Demonstracja/blazor-Demonstracja](https://github.com/blazor-demo/blazor-demo.github.io) ).</span><span class="sxs-lookup"><span data-stu-id="775dd-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="775dd-227">W przypadku korzystania z witryny projektu zamiast witryny organizacji Dodaj lub zaktualizuj `<base>` tag w *pliku index. html*.</span><span class="sxs-lookup"><span data-stu-id="775dd-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="775dd-228">Ustaw wartość `href` atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="775dd-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="775dd-229">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="775dd-229">Host configuration values</span></span>

<span data-ttu-id="775dd-230">[Blazor aplikacje webassembly](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="775dd-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="775dd-231">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="775dd-231">Content root</span></span>

<span data-ttu-id="775dd-232">`--contentroot` Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="775dd-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="775dd-233">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="775dd-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="775dd-234">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="775dd-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="775dd-235">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="775dd-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="775dd-236">Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="775dd-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="775dd-237">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia `dotnet run`z.</span><span class="sxs-lookup"><span data-stu-id="775dd-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="775dd-238">W programie Visual Studio Określ argument w **właściwościach** > **Debuguj** > **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="775dd-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="775dd-239">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="775dd-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="775dd-240">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="775dd-240">Path base</span></span>

<span data-ttu-id="775dd-241">`--pathbase` Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="775dd-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="775dd-242">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="775dd-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="775dd-243">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="775dd-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="775dd-244">W przeciwieństwie do ścieżki przekazanej `href` do `<base>` tagu, nie dodawaj końcowego ukośnika (`/`) podczas przekazywania wartości `--pathbase` argumentu.</span><span class="sxs-lookup"><span data-stu-id="775dd-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="775dd-245">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="775dd-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="775dd-246">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="775dd-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="775dd-247">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="775dd-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="775dd-248">Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="775dd-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="775dd-249">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="775dd-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="775dd-250">W programie Visual Studio Określ argument w **właściwościach** > **Debuguj** > **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="775dd-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="775dd-251">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="775dd-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="775dd-252">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="775dd-252">URLs</span></span>

<span data-ttu-id="775dd-253">`--urls` Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="775dd-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="775dd-254">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="775dd-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="775dd-255">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="775dd-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="775dd-256">Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="775dd-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="775dd-257">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="775dd-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="775dd-258">W programie Visual Studio Określ argument w **właściwościach** > **Debuguj** > **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="775dd-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="775dd-259">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="775dd-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="775dd-260">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="775dd-260">Configure the Linker</span></span>

<span data-ttu-id="775dd-261">Blazor wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="775dd-261">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="775dd-262">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="775dd-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="775dd-263">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="775dd-263">Custom boot resource loading</span></span>

<span data-ttu-id="775dd-264">Aplikację webassembly Blazor można zainicjować przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="775dd-264">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="775dd-265">Należy `loadBootResource` używać w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="775dd-265">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="775dd-266">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane strefy czasowej lub *dotnet. wasm* z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="775dd-266">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="775dd-267">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="775dd-267">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="775dd-268">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="775dd-268">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="775dd-269">`loadBootResource`Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="775dd-269">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="775dd-270">Parametr</span><span class="sxs-lookup"><span data-stu-id="775dd-270">Parameter</span></span>    | <span data-ttu-id="775dd-271">Opis</span><span class="sxs-lookup"><span data-stu-id="775dd-271">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="775dd-272">Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="775dd-272">The type of the resource.</span></span> <span data-ttu-id="775dd-273">Typy Permissable: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="775dd-273">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="775dd-274">Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="775dd-274">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="775dd-275">Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="775dd-275">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="775dd-276">Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="775dd-276">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="775dd-277">`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="775dd-277">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="775dd-278">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="775dd-278">URI string.</span></span> <span data-ttu-id="775dd-279">W poniższym przykładzie (*wwwroot/index.html*) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="775dd-279">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="775dd-280">*dotnet. \*. js*</span><span class="sxs-lookup"><span data-stu-id="775dd-280">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="775dd-281">*dotnet. wasm*</span><span class="sxs-lookup"><span data-stu-id="775dd-281">*dotnet.wasm*</span></span>
  * <span data-ttu-id="775dd-282">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="775dd-282">Timezone data</span></span>

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

* <span data-ttu-id="775dd-283">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="775dd-283">`Promise<Response>`.</span></span> <span data-ttu-id="775dd-284">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="775dd-284">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="775dd-285">Poniższy przykład (*wwwroot/index.html*) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="775dd-285">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="775dd-286">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="775dd-286">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="775dd-287">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="775dd-287">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="775dd-288">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="775dd-288">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="775dd-289">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="775dd-289">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="775dd-290">Typy, które nie `loadBootResource` zostały określone do, są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="775dd-290">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>
