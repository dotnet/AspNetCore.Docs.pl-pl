---
title: Hostowanie i wdrażanie ASP.NET Core Blazor Webassembly
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7e0263200ebb9ce60f7234af3cbb18c5aeaa3e09
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243528"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="88cc7-103">Hostowanie i wdrażanie ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="88cc7-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="88cc7-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="88cc7-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="88cc7-105">Z [ Blazor modelem hostingu zestawu webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="88cc7-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="88cc7-106">BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="88cc7-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="88cc7-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="88cc7-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="88cc7-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="88cc7-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="88cc7-109">BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="88cc7-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="88cc7-110">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="88cc7-111">BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="88cc7-112">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor aplikacji sieci webassembly jako aplikacji podrzędnej IIS.</span><span class="sxs-lookup"><span data-stu-id="88cc7-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="88cc7-113">Kompresja</span><span class="sxs-lookup"><span data-stu-id="88cc7-113">Compression</span></span>

<span data-ttu-id="88cc7-114">Po Blazor opublikowaniu aplikacji webassembly dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="88cc7-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="88cc7-115">Używane są następujące algorytmy kompresji:</span><span class="sxs-lookup"><span data-stu-id="88cc7-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="88cc7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)</span><span class="sxs-lookup"><span data-stu-id="88cc7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="88cc7-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="88cc7-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="88cc7-118">korzysta z hosta, aby zapewnić odpowiednie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="88cc7-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="88cc7-119">W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="88cc7-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="88cc7-120">W przypadku hostowania Blazor aplikacji autonomicznej elementu webassembly może być wymagane przeprowadzenie dodatkowych czynności w celu zapewnienia obsługi plików skompresowanych statycznie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="88cc7-121">Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="88cc7-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="88cc7-122">Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:</span><span class="sxs-lookup"><span data-stu-id="88cc7-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="88cc7-123">Odwołuje się do dekodera Brotli z [repozytorium GitHub/Brotli](https://github.com/google/brotli/) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="88cc7-124">Zaktualizuj aplikację, aby użyć dekodera.</span><span class="sxs-lookup"><span data-stu-id="88cc7-124">Update the app to use the decoder.</span></span> <span data-ttu-id="88cc7-125">Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="88cc7-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
          'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
          { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
  </script>
  ```
   
<span data-ttu-id="88cc7-126">Aby wyłączyć kompresję, należy dodać `BlazorEnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :</span><span class="sxs-lookup"><span data-stu-id="88cc7-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="88cc7-127">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="88cc7-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="88cc7-128">Żądania routingu dla składników strony w Blazor aplikacji webassembly nie są tak proste jak żądania routingu na Blazor serwerze hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="88cc7-129">Weź pod uwagę Blazor aplikację webassembly z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="88cc7-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="88cc7-130">`Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="88cc7-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="88cc7-131">`About.razor`: `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="88cc7-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="88cc7-132">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="88cc7-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="88cc7-133">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="88cc7-133">The browser makes a request.</span></span>
1. <span data-ttu-id="88cc7-134">Zostanie zwrócona domyślna strona, która zwykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="88cc7-135">`index.html`wyładowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-135">`index.html` bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="88cc7-136">ładuje router, a Razor `Main` składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="88cc7-136">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="88cc7-137">Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="88cc7-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="88cc7-138">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor aplikacji webassembly* działają w ten sam sposób: żądania nie wyzwalają żądań opartych na przeglądarce do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="88cc7-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="88cc7-139">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="88cc7-139">The router handles the requests internally.</span></span>

<span data-ttu-id="88cc7-140">Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="88cc7-141">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="88cc7-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="88cc7-142">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie.</span><span class="sxs-lookup"><span data-stu-id="88cc7-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="88cc7-143">Gdy `index.html` jest zwracany, Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="88cc7-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="88cc7-144">Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="88cc7-145">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="88cc7-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="88cc7-146">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88cc7-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="88cc7-147">*Wdrożenie hostowane* obsługuje Blazor aplikację webassembly dla przeglądarek z [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="88cc7-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="88cc7-148">BlazorAplikacja webassembly klienta jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="88cc7-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="88cc7-149">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="88cc7-149">The two apps are deployed together.</span></span> <span data-ttu-id="88cc7-150">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="88cc7-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="88cc7-151">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu \*\* Blazor aplikacji webassembly\*\* ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="88cc7-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="88cc7-152">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="88cc7-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="88cc7-153">Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="88cc7-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="88cc7-154">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="88cc7-154">Standalone deployment</span></span>

<span data-ttu-id="88cc7-155">*Wdrożenie autonomiczne* obsługuje Blazor aplikację webassembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="88cc7-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="88cc7-156">Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="88cc7-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="88cc7-157">Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="88cc7-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="88cc7-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="88cc7-158">Azure App Service</span></span>

Blazor<span data-ttu-id="88cc7-159">Aplikacje webassembly można wdrożyć na platformie Azure App Services w systemie Windows, który hostuje aplikację w [usługach IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="88cc7-159"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="88cc7-160">Wdrożenie autonomicznej Blazor aplikacji webassembly do Azure App Service dla systemu Linux nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="88cc7-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="88cc7-161">Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny.</span><span class="sxs-lookup"><span data-stu-id="88cc7-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="88cc7-162">Trwa wykonywanie pracy, aby włączyć ten scenariusz.</span><span class="sxs-lookup"><span data-stu-id="88cc7-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="88cc7-163">IIS</span><span class="sxs-lookup"><span data-stu-id="88cc7-163">IIS</span></span>

<span data-ttu-id="88cc7-164">Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="88cc7-165">Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="88cc7-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="88cc7-166">Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze.</span><span class="sxs-lookup"><span data-stu-id="88cc7-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="88cc7-167">Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="88cc7-168">web.config</span><span class="sxs-lookup"><span data-stu-id="88cc7-168">web.config</span></span>

<span data-ttu-id="88cc7-169">Po Blazor opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="88cc7-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="88cc7-170">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="88cc7-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="88cc7-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="88cc7-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="88cc7-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="88cc7-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="88cc7-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="88cc7-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="88cc7-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="88cc7-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="88cc7-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="88cc7-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="88cc7-176">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="88cc7-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="88cc7-177">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="88cc7-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="88cc7-178">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="88cc7-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="88cc7-179">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="88cc7-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="88cc7-180">Użyj niestandardowego web.config</span><span class="sxs-lookup"><span data-stu-id="88cc7-180">Use a custom web.config</span></span>

<span data-ttu-id="88cc7-181">Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu i Opublikuj projekt.</span><span class="sxs-lookup"><span data-stu-id="88cc7-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="88cc7-182">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="88cc7-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="88cc7-183">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="88cc7-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="88cc7-184">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="88cc7-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="88cc7-185">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="88cc7-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="88cc7-186">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="88cc7-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="88cc7-187">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="88cc7-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="88cc7-188">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="88cc7-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="88cc7-189">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="88cc7-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="88cc7-190">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="88cc7-190">Copy the installer to the server.</span></span> <span data-ttu-id="88cc7-191">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="88cc7-191">Run the installer.</span></span> <span data-ttu-id="88cc7-192">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="88cc7-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="88cc7-193">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="88cc7-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="88cc7-194">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="88cc7-194">Configure the website</span></span>

<span data-ttu-id="88cc7-195">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="88cc7-196">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="88cc7-196">The folder contains:</span></span>

* <span data-ttu-id="88cc7-197">Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="88cc7-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="88cc7-198">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="88cc7-199">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="88cc7-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="88cc7-200">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="88cc7-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="88cc7-201">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="88cc7-202">Usuń program obsługi w Blazor opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:</span><span class="sxs-lookup"><span data-stu-id="88cc7-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="88cc7-203">Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :</span><span class="sxs-lookup"><span data-stu-id="88cc7-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="88cc7-204">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="88cc7-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="88cc7-205">Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="88cc7-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="88cc7-206">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="88cc7-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="88cc7-207">Usługi IIS można skonfigurować `web.config` w taki sposób, aby obsługiwały zasoby Brotli lub Gzip skompresowane Blazor .</span><span class="sxs-lookup"><span data-stu-id="88cc7-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="88cc7-208">Aby zapoznać się z przykładową konfiguracją, zobacz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="88cc7-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="88cc7-209">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="88cc7-209">Troubleshooting</span></span>

<span data-ttu-id="88cc7-210">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="88cc7-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="88cc7-211">Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="88cc7-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="88cc7-212">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="88cc7-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="88cc7-213">Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="88cc7-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="88cc7-214">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="88cc7-214">Azure Storage</span></span>

<span data-ttu-id="88cc7-215">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="88cc7-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="88cc7-216">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="88cc7-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="88cc7-217">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="88cc7-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="88cc7-218">Ustaw **nazwę dokumentu indeksu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="88cc7-219">Ustaw **ścieżkę do dokumentu błędu** `index.html` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-219">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="88cc7-220">składniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="88cc7-220"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="88cc7-221">Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**.</span><span class="sxs-lookup"><span data-stu-id="88cc7-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="88cc7-222">`index.html`Obiekt BLOB jest zwracany, a Blazor router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="88cc7-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="88cc7-223">Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="88cc7-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="88cc7-224">Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.</span><span class="sxs-lookup"><span data-stu-id="88cc7-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="88cc7-225">Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="88cc7-226">W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:</span><span class="sxs-lookup"><span data-stu-id="88cc7-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="88cc7-227">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="88cc7-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="88cc7-228">Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="88cc7-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="88cc7-229">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="88cc7-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="88cc7-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="88cc7-230">Nginx</span></span>

<span data-ttu-id="88cc7-231">Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="88cc7-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="88cc7-232">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="88cc7-232">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="88cc7-233">Nginx w Docker</span><span class="sxs-lookup"><span data-stu-id="88cc7-233">Nginx in Docker</span></span>

<span data-ttu-id="88cc7-234">Aby hostować Blazor w Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine.</span><span class="sxs-lookup"><span data-stu-id="88cc7-234">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="88cc7-235">Zaktualizuj pliku dockerfile, aby skopiować `nginx.config` plik do kontenera.</span><span class="sxs-lookup"><span data-stu-id="88cc7-235">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="88cc7-236">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-236">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="88cc7-237">Apache</span><span class="sxs-lookup"><span data-stu-id="88cc7-237">Apache</span></span>

<span data-ttu-id="88cc7-238">Aby wdrożyć Blazor aplikację webassembly w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="88cc7-238">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="88cc7-239">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="88cc7-239">Create the Apache configuration file.</span></span> <span data-ttu-id="88cc7-240">Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="88cc7-240">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="88cc7-241">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="88cc7-241">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="88cc7-242">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="88cc7-242">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="88cc7-243">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="88cc7-243">Restart the Apache service.</span></span>

<span data-ttu-id="88cc7-244">Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="88cc7-244">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="88cc7-245">Strony serwisu GitHub</span><span class="sxs-lookup"><span data-stu-id="88cc7-245">GitHub Pages</span></span>

<span data-ttu-id="88cc7-246">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony.</span><span class="sxs-lookup"><span data-stu-id="88cc7-246">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="88cc7-247">Aby zapoznać się z przykładową implementacją dostarczoną przez społeczność, zobacz [aplikacje jednostronicowe dla stron usługi GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/Spa-GitHub-Pages w witrynie GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="88cc7-247">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="88cc7-248">Przykład użycia podejścia społecznościowego można znaleźć[w witrynie](https://blazor-demo.github.io/) [GitHub (blazor — Demonstracja/blazor-Demonstracja](https://github.com/blazor-demo/blazor-demo.github.io) ).</span><span class="sxs-lookup"><span data-stu-id="88cc7-248">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="88cc7-249">W przypadku korzystania z witryny projektu zamiast witryny organizacji Dodaj lub zaktualizuj `<base>` tag w `index.html` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-249">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="88cc7-250">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `my-repository/` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-250">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="88cc7-251">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="88cc7-251">Host configuration values</span></span>

<span data-ttu-id="88cc7-252">[ Blazor Aplikacje webassembly](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="88cc7-252">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="88cc7-253">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="88cc7-253">Content root</span></span>

<span data-ttu-id="88cc7-254">`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="88cc7-254">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="88cc7-255">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-255">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="88cc7-256">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="88cc7-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="88cc7-257">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="88cc7-258">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="88cc7-258">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="88cc7-259">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-259">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="88cc7-260">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="88cc7-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="88cc7-261">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="88cc7-261">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="88cc7-262">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="88cc7-262">Path base</span></span>

<span data-ttu-id="88cc7-263">`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="88cc7-263">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="88cc7-264">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-264">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="88cc7-265">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="88cc7-265">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="88cc7-266">W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-266">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="88cc7-267">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="88cc7-267">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="88cc7-268">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="88cc7-268">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="88cc7-269">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-269">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="88cc7-270">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="88cc7-270">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="88cc7-271">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-271">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="88cc7-272">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="88cc7-272">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="88cc7-273">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="88cc7-273">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="88cc7-274">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="88cc7-274">URLs</span></span>

<span data-ttu-id="88cc7-275">`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="88cc7-275">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="88cc7-276">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="88cc7-276">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="88cc7-277">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-277">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="88cc7-278">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="88cc7-278">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="88cc7-279">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-279">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="88cc7-280">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="88cc7-280">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="88cc7-281">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="88cc7-281">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="88cc7-282">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="88cc7-282">Configure the Linker</span></span>

Blazor<span data-ttu-id="88cc7-283">wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="88cc7-283"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="88cc7-284">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="88cc7-284">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="88cc7-285">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="88cc7-285">Custom boot resource loading</span></span>

<span data-ttu-id="88cc7-286">BlazorAplikację webassembly można zainicjować przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="88cc7-286">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="88cc7-287">Należy używać `loadBootResource` w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="88cc7-287">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="88cc7-288">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="88cc7-288">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="88cc7-289">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="88cc7-289">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="88cc7-290">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="88cc7-290">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="88cc7-291">`loadBootResource`Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="88cc7-291">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="88cc7-292">Parametr</span><span class="sxs-lookup"><span data-stu-id="88cc7-292">Parameter</span></span>    | <span data-ttu-id="88cc7-293">Opis</span><span class="sxs-lookup"><span data-stu-id="88cc7-293">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="88cc7-294">Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-294">The type of the resource.</span></span> <span data-ttu-id="88cc7-295">Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="88cc7-295">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="88cc7-296">Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-296">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="88cc7-297">Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-297">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="88cc7-298">Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="88cc7-298">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="88cc7-299">`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="88cc7-299">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="88cc7-300">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="88cc7-300">URI string.</span></span> <span data-ttu-id="88cc7-301">W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="88cc7-301">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="88cc7-302">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="88cc7-302">Timezone data</span></span>

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

* <span data-ttu-id="88cc7-303">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="88cc7-303">`Promise<Response>`.</span></span> <span data-ttu-id="88cc7-304">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="88cc7-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="88cc7-305">Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="88cc7-305">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="88cc7-306">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="88cc7-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="88cc7-307">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="88cc7-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="88cc7-308">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="88cc7-308">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="88cc7-309">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="88cc7-309">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="88cc7-310">Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="88cc7-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="88cc7-311">Zmień rozszerzenie nazwy pliku DLL</span><span class="sxs-lookup"><span data-stu-id="88cc7-311">Change the filename extension of DLL files</span></span>

<span data-ttu-id="88cc7-312">Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="88cc7-312">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="88cc7-313">Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="88cc7-313">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="88cc7-314">Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="88cc7-314">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="88cc7-315">W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="88cc7-315">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="88cc7-316">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="88cc7-316">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="88cc7-317">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-317">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="88cc7-318">W systemie Linux lub macOS:</span><span class="sxs-lookup"><span data-stu-id="88cc7-318">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="88cc7-319">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-319">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="88cc7-320">Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.</span><span class="sxs-lookup"><span data-stu-id="88cc7-320">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="88cc7-321">Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="88cc7-321">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="88cc7-322">Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki.</span><span class="sxs-lookup"><span data-stu-id="88cc7-322">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="88cc7-323">Kompresja jest wyłączona w tym podejściu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-323">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="88cc7-324">Rekompresuj zaktualizowany `blazor.boot.json` plik.</span><span class="sxs-lookup"><span data-stu-id="88cc7-324">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="88cc7-325">Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi.</span><span class="sxs-lookup"><span data-stu-id="88cc7-325">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="88cc7-326">Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="88cc7-326">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="88cc7-327">W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="88cc7-327">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="88cc7-328">Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="88cc7-328">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="88cc7-329">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="88cc7-329">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="88cc7-330">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="88cc7-330">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="88cc7-331">W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="88cc7-331">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="88cc7-332">Aby przekazać opinię, odwiedź stronę [aspnetcore/problemy #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="88cc7-332">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
