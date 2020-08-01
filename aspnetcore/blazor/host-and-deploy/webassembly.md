---
title: Hostowanie i wdrażanie ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 15c5f02043a83e499eb5ec36fda52171124fe202
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87443984"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="e46dd-103">Hostowanie i wdrażanie ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e46dd-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="e46dd-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="e46dd-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="e46dd-105">Z [ Blazor WebAssembly modelem hostingu](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="e46dd-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="e46dd-106">BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e46dd-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="e46dd-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e46dd-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="e46dd-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="e46dd-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="e46dd-109">BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e46dd-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="e46dd-110">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="e46dd-111">BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="e46dd-112">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor WebAssembly aplikacji jako PODRZĘDNEJ aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="e46dd-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="e46dd-113">Kompresja</span><span class="sxs-lookup"><span data-stu-id="e46dd-113">Compression</span></span>

<span data-ttu-id="e46dd-114">Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="e46dd-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="e46dd-115">Używane są następujące algorytmy kompresji:</span><span class="sxs-lookup"><span data-stu-id="e46dd-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="e46dd-116">[Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)</span><span class="sxs-lookup"><span data-stu-id="e46dd-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="e46dd-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="e46dd-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="e46dd-118">Blazorkorzysta z hosta, aby zapewnić odpowiednie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="e46dd-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="e46dd-119">W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="e46dd-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="e46dd-120">W przypadku udostępniania Blazor WebAssembly aplikacji autonomicznej może być wymagane dodatkowe działanie, aby zapewnić obsługę plików skompresowanych statycznie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="e46dd-121">Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="e46dd-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="e46dd-122">Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:</span><span class="sxs-lookup"><span data-stu-id="e46dd-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="e46dd-123">Uzyskaj dekoder JavaScript Brotli z repozytorium usługi [GitHub firmy Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="e46dd-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="e46dd-124">Od lipca 2020 plik dekodera ma nazwę `decode.min.js` i znajduje się w [ `js` folderze](https://github.com/google/brotli/tree/master/js)repozytorium.</span><span class="sxs-lookup"><span data-stu-id="e46dd-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="e46dd-125">Zaktualizuj aplikację, aby użyć dekodera.</span><span class="sxs-lookup"><span data-stu-id="e46dd-125">Update the app to use the decoder.</span></span> <span data-ttu-id="e46dd-126">Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e46dd-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="e46dd-127">Aby wyłączyć kompresję, należy dodać `BlazorEnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :</span><span class="sxs-lookup"><span data-stu-id="e46dd-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="e46dd-128">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="e46dd-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="e46dd-129">Żądania routingu dla składników strony w Blazor WebAssembly aplikacji nie są tak proste jak żądania routingu w Blazor Server hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="e46dd-130">Weź pod uwagę Blazor WebAssembly aplikację z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="e46dd-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="e46dd-131">`Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="e46dd-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="e46dd-132">`About.razor`: `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="e46dd-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="e46dd-133">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="e46dd-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="e46dd-134">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="e46dd-134">The browser makes a request.</span></span>
1. <span data-ttu-id="e46dd-135">Zostanie zwrócona domyślna strona, która zwykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="e46dd-136">`index.html`wyładowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="e46dd-137">Blazorładuje router, a Razor `Main` składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="e46dd-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="e46dd-138">Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="e46dd-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="e46dd-139">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor WebAssembly aplikacji* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="e46dd-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="e46dd-140">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="e46dd-140">The router handles the requests internally.</span></span>

<span data-ttu-id="e46dd-141">Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="e46dd-142">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="e46dd-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="e46dd-143">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie.</span><span class="sxs-lookup"><span data-stu-id="e46dd-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="e46dd-144">Gdy `index.html` jest zwracany, Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="e46dd-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="e46dd-145">Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="e46dd-146">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="e46dd-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="e46dd-147">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e46dd-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="e46dd-148">*Wdrożenie hostowane* Blazor WebAssembly umożliwia aplikacji przeglądarki z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="e46dd-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="e46dd-149">Aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="e46dd-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="e46dd-150">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="e46dd-150">The two apps are deployed together.</span></span> <span data-ttu-id="e46dd-151">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e46dd-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="e46dd-152">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu \*\* Blazor WebAssembly aplikacji\*\* ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="e46dd-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="e46dd-153">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="e46dd-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="e46dd-154">Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="e46dd-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="e46dd-155">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="e46dd-155">Standalone deployment</span></span>

<span data-ttu-id="e46dd-156">*Wdrożenie samodzielne* służy Blazor WebAssembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="e46dd-156">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="e46dd-157">Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="e46dd-157">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="e46dd-158">Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="e46dd-158">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="e46dd-159">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e46dd-159">Azure App Service</span></span>

<span data-ttu-id="e46dd-160">Blazor WebAssemblyAplikacje można wdrażać w usłudze Azure App Services w systemie Windows, który jest hostem aplikacji w [usługach IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="e46dd-160">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="e46dd-161">Wdrażanie autonomicznej Blazor WebAssembly aplikacji do Azure App Service dla systemu Linux nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="e46dd-161">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="e46dd-162">Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny.</span><span class="sxs-lookup"><span data-stu-id="e46dd-162">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="e46dd-163">Trwa wykonywanie pracy, aby włączyć ten scenariusz.</span><span class="sxs-lookup"><span data-stu-id="e46dd-163">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="e46dd-164">IIS</span><span class="sxs-lookup"><span data-stu-id="e46dd-164">IIS</span></span>

<span data-ttu-id="e46dd-165">Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-165">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="e46dd-166">Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="e46dd-166">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="e46dd-167">Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze.</span><span class="sxs-lookup"><span data-stu-id="e46dd-167">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="e46dd-168">Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-168">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="e46dd-169">web.config</span><span class="sxs-lookup"><span data-stu-id="e46dd-169">web.config</span></span>

<span data-ttu-id="e46dd-170">Po Blazor opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="e46dd-170">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="e46dd-171">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="e46dd-171">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="e46dd-172">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="e46dd-172">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="e46dd-173">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="e46dd-173">`.json`: `application/json`</span></span>
  * <span data-ttu-id="e46dd-174">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="e46dd-174">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="e46dd-175">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="e46dd-175">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="e46dd-176">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="e46dd-176">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="e46dd-177">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="e46dd-177">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="e46dd-178">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="e46dd-178">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="e46dd-179">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="e46dd-179">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="e46dd-180">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="e46dd-180">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="e46dd-181">Użyj niestandardowego web.config</span><span class="sxs-lookup"><span data-stu-id="e46dd-181">Use a custom web.config</span></span>

<span data-ttu-id="e46dd-182">Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu i Opublikuj projekt.</span><span class="sxs-lookup"><span data-stu-id="e46dd-182">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="e46dd-183">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="e46dd-183">Install the URL Rewrite Module</span></span>

<span data-ttu-id="e46dd-184">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="e46dd-184">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="e46dd-185">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="e46dd-185">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="e46dd-186">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="e46dd-186">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="e46dd-187">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="e46dd-187">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="e46dd-188">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="e46dd-188">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="e46dd-189">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="e46dd-189">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="e46dd-190">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="e46dd-190">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="e46dd-191">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="e46dd-191">Copy the installer to the server.</span></span> <span data-ttu-id="e46dd-192">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="e46dd-192">Run the installer.</span></span> <span data-ttu-id="e46dd-193">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="e46dd-193">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="e46dd-194">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="e46dd-194">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="e46dd-195">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="e46dd-195">Configure the website</span></span>

<span data-ttu-id="e46dd-196">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-196">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="e46dd-197">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="e46dd-197">The folder contains:</span></span>

* <span data-ttu-id="e46dd-198">Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="e46dd-198">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="e46dd-199">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-199">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="e46dd-200">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="e46dd-200">Host as an IIS sub-app</span></span>

<span data-ttu-id="e46dd-201">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="e46dd-201">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="e46dd-202">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-202">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="e46dd-203">Usuń program obsługi w Blazor opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:</span><span class="sxs-lookup"><span data-stu-id="e46dd-203">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="e46dd-204">Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :</span><span class="sxs-lookup"><span data-stu-id="e46dd-204">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="e46dd-205">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="e46dd-205">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="e46dd-206">Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="e46dd-206">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="e46dd-207">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="e46dd-207">Brotli and Gzip compression</span></span>

<span data-ttu-id="e46dd-208">Usługi IIS można skonfigurować `web.config` w taki sposób, aby obsługiwały zasoby Brotli lub Gzip skompresowane Blazor .</span><span class="sxs-lookup"><span data-stu-id="e46dd-208">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="e46dd-209">Aby zapoznać się z przykładową konfiguracją, zobacz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="e46dd-209">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="e46dd-210">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="e46dd-210">Troubleshooting</span></span>

<span data-ttu-id="e46dd-211">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="e46dd-211">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="e46dd-212">Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="e46dd-212">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="e46dd-213">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="e46dd-213">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="e46dd-214">Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="e46dd-214">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="e46dd-215">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="e46dd-215">Azure Storage</span></span>

<span data-ttu-id="e46dd-216">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="e46dd-216">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="e46dd-217">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e46dd-217">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="e46dd-218">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="e46dd-218">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="e46dd-219">Ustaw **nazwę dokumentu indeksu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-219">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="e46dd-220">Ustaw **ścieżkę do dokumentu błędu** `index.html` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-220">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="e46dd-221">Razorskładniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="e46dd-221">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="e46dd-222">Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**.</span><span class="sxs-lookup"><span data-stu-id="e46dd-222">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="e46dd-223">`index.html`Obiekt BLOB jest zwracany, a Blazor router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="e46dd-223">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="e46dd-224">Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="e46dd-224">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="e46dd-225">Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.</span><span class="sxs-lookup"><span data-stu-id="e46dd-225">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="e46dd-226">Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-226">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="e46dd-227">W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:</span><span class="sxs-lookup"><span data-stu-id="e46dd-227">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="e46dd-228">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="e46dd-228">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="e46dd-229">Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="e46dd-229">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="e46dd-230">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="e46dd-230">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="e46dd-231">Nginx</span><span class="sxs-lookup"><span data-stu-id="e46dd-231">Nginx</span></span>

<span data-ttu-id="e46dd-232">Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="e46dd-232">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="e46dd-233">W przypadku ustawienia [limitu szybkości serii Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) w [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) programie Blazor WebAssembly aplikacje mogą wymagać dużej `burst` wartości parametru, aby pomieścić stosunkowo dużą liczbę żądań wykonywanych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="e46dd-233">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="e46dd-234">Początkowo ustaw wartość na co najmniej 60:</span><span class="sxs-lookup"><span data-stu-id="e46dd-234">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="e46dd-235">Zwiększ wartość, jeśli narzędzia deweloperskie przeglądarki lub ruch sieciowy wskazuje, że żądania odbierają kod stanu *niedostępny dla usługi 503* .</span><span class="sxs-lookup"><span data-stu-id="e46dd-235">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="e46dd-236">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="e46dd-236">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="e46dd-237">Nginx w Docker</span><span class="sxs-lookup"><span data-stu-id="e46dd-237">Nginx in Docker</span></span>

<span data-ttu-id="e46dd-238">Aby hostować Blazor w Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine.</span><span class="sxs-lookup"><span data-stu-id="e46dd-238">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="e46dd-239">Zaktualizuj pliku dockerfile, aby skopiować `nginx.config` plik do kontenera.</span><span class="sxs-lookup"><span data-stu-id="e46dd-239">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="e46dd-240">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-240">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="e46dd-241">Apache</span><span class="sxs-lookup"><span data-stu-id="e46dd-241">Apache</span></span>

<span data-ttu-id="e46dd-242">Aby wdrożyć Blazor WebAssembly aplikację w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="e46dd-242">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="e46dd-243">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="e46dd-243">Create the Apache configuration file.</span></span> <span data-ttu-id="e46dd-244">Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="e46dd-244">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="e46dd-245">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="e46dd-245">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="e46dd-246">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="e46dd-246">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="e46dd-247">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="e46dd-247">Restart the Apache service.</span></span>

<span data-ttu-id="e46dd-248">Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="e46dd-248">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="e46dd-249">Usługa GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="e46dd-249">GitHub Pages</span></span>

<span data-ttu-id="e46dd-250">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony.</span><span class="sxs-lookup"><span data-stu-id="e46dd-250">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="e46dd-251">Aby zapoznać się z przykładową implementacją dostarczoną przez społeczność, zobacz [aplikacje jednostronicowe dla stron usługi GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/Spa-GitHub-Pages w witrynie GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="e46dd-251">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="e46dd-252">Przykład użycia podejścia społecznościowego można znaleźć[w witrynie](https://blazor-demo.github.io/) [GitHub (blazor — Demonstracja/blazor-Demonstracja](https://github.com/blazor-demo/blazor-demo.github.io) ).</span><span class="sxs-lookup"><span data-stu-id="e46dd-252">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="e46dd-253">W przypadku korzystania z witryny projektu zamiast witryny organizacji Dodaj lub zaktualizuj `<base>` tag w `index.html` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-253">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="e46dd-254">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `my-repository/` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-254">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="e46dd-255">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="e46dd-255">Host configuration values</span></span>

<span data-ttu-id="e46dd-256">[ Blazor WebAssembly aplikacje](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="e46dd-256">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="e46dd-257">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="e46dd-257">Content root</span></span>

<span data-ttu-id="e46dd-258">`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="e46dd-258">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="e46dd-259">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-259">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="e46dd-260">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="e46dd-260">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="e46dd-261">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-261">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="e46dd-262">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="e46dd-262">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="e46dd-263">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-263">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="e46dd-264">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="e46dd-264">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="e46dd-265">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="e46dd-265">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="e46dd-266">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="e46dd-266">Path base</span></span>

<span data-ttu-id="e46dd-267">`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="e46dd-267">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="e46dd-268">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-268">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="e46dd-269">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="e46dd-269">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e46dd-270">W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-270">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="e46dd-271">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="e46dd-271">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="e46dd-272">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="e46dd-272">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="e46dd-273">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-273">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="e46dd-274">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="e46dd-274">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="e46dd-275">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-275">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="e46dd-276">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="e46dd-276">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="e46dd-277">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="e46dd-277">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="e46dd-278">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="e46dd-278">URLs</span></span>

<span data-ttu-id="e46dd-279">`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="e46dd-279">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="e46dd-280">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="e46dd-280">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="e46dd-281">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-281">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="e46dd-282">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="e46dd-282">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="e46dd-283">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-283">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="e46dd-284">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="e46dd-284">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="e46dd-285">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="e46dd-285">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="e46dd-286">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="e46dd-286">Configure the Linker</span></span>

<span data-ttu-id="e46dd-287">Blazorwykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="e46dd-287">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="e46dd-288">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="e46dd-288">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="e46dd-289">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="e46dd-289">Custom boot resource loading</span></span>

<span data-ttu-id="e46dd-290">Blazor WebAssemblyAplikacja może zostać zainicjowana przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="e46dd-290">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="e46dd-291">Należy używać `loadBootResource` w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="e46dd-291">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="e46dd-292">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="e46dd-292">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="e46dd-293">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="e46dd-293">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="e46dd-294">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="e46dd-294">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="e46dd-295">`loadBootResource`Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="e46dd-295">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="e46dd-296">Parametr</span><span class="sxs-lookup"><span data-stu-id="e46dd-296">Parameter</span></span>    | <span data-ttu-id="e46dd-297">Opis</span><span class="sxs-lookup"><span data-stu-id="e46dd-297">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="e46dd-298">Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-298">The type of the resource.</span></span> <span data-ttu-id="e46dd-299">Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="e46dd-299">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="e46dd-300">Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-300">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="e46dd-301">Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-301">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="e46dd-302">Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e46dd-302">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="e46dd-303">`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="e46dd-303">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="e46dd-304">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="e46dd-304">URI string.</span></span> <span data-ttu-id="e46dd-305">W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="e46dd-305">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="e46dd-306">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="e46dd-306">Timezone data</span></span>

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

* <span data-ttu-id="e46dd-307">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="e46dd-307">`Promise<Response>`.</span></span> <span data-ttu-id="e46dd-308">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="e46dd-308">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="e46dd-309">Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="e46dd-309">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="e46dd-310">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="e46dd-310">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="e46dd-311">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="e46dd-311">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="e46dd-312">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="e46dd-312">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="e46dd-313">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="e46dd-313">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="e46dd-314">Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="e46dd-314">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="e46dd-315">Zmień rozszerzenie nazwy pliku DLL</span><span class="sxs-lookup"><span data-stu-id="e46dd-315">Change the filename extension of DLL files</span></span>

<span data-ttu-id="e46dd-316">Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="e46dd-316">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="e46dd-317">Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="e46dd-317">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="e46dd-318">Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="e46dd-318">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="e46dd-319">W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="e46dd-319">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="e46dd-320">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="e46dd-320">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="e46dd-321">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-321">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="e46dd-322">W systemie Linux lub macOS:</span><span class="sxs-lookup"><span data-stu-id="e46dd-322">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="e46dd-323">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-323">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="e46dd-324">Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.</span><span class="sxs-lookup"><span data-stu-id="e46dd-324">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="e46dd-325">Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="e46dd-325">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="e46dd-326">Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki.</span><span class="sxs-lookup"><span data-stu-id="e46dd-326">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="e46dd-327">Kompresja jest wyłączona w tym podejściu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-327">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="e46dd-328">Rekompresuj zaktualizowany `blazor.boot.json` plik.</span><span class="sxs-lookup"><span data-stu-id="e46dd-328">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="e46dd-329">Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi.</span><span class="sxs-lookup"><span data-stu-id="e46dd-329">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="e46dd-330">Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="e46dd-330">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="e46dd-331">W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="e46dd-331">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="e46dd-332">Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="e46dd-332">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="e46dd-333">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="e46dd-333">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="e46dd-334">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e46dd-334">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="e46dd-335">W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e46dd-335">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="e46dd-336">Aby przekazać opinię, odwiedź stronę [aspnetcore/problemy #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="e46dd-336">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
