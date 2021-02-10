---
title: Hostowanie i wdrażanie ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: b849cbfe803ef51cbb845566b4d5d27b2761e00c
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106872"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="26980-103">Hostowanie i wdrażanie ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="26980-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="26980-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="26980-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="26980-105">Z [ Blazor WebAssembly modelem hostingu](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="26980-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="26980-106">BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="26980-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="26980-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="26980-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="26980-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="26980-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="26980-109">BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26980-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="26980-110">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="26980-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="26980-111">BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="26980-112">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor WebAssembly aplikacji jako PODRZĘDNEJ aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="26980-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="26980-113">Kompresja</span><span class="sxs-lookup"><span data-stu-id="26980-113">Compression</span></span>

<span data-ttu-id="26980-114">Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="26980-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="26980-115">Używane są następujące algorytmy kompresji:</span><span class="sxs-lookup"><span data-stu-id="26980-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="26980-116">[Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)</span><span class="sxs-lookup"><span data-stu-id="26980-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="26980-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="26980-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="26980-118">Blazor korzysta z hosta, aby zapewnić odpowiednie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="26980-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="26980-119">W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="26980-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="26980-120">W przypadku udostępniania Blazor WebAssembly aplikacji autonomicznej może być wymagane dodatkowe działanie, aby zapewnić obsługę plików skompresowanych statycznie:</span><span class="sxs-lookup"><span data-stu-id="26980-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="26980-121">Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="26980-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="26980-122">Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:</span><span class="sxs-lookup"><span data-stu-id="26980-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="26980-123">Uzyskaj dekoder JavaScript Brotli z repozytorium usługi [GitHub firmy Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="26980-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="26980-124">Plik dekodera ma nazwę `decode.js` i znajduje się w [ `js` folderze](https://github.com/google/brotli/tree/master/js)repozytorium.</span><span class="sxs-lookup"><span data-stu-id="26980-124">The decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="26980-125">Regresja jest obecna w wersji zminimalizowanego `decode.js` skryptu ( `decode.min.js` ) w [repozytorium usługi GitHub Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="26980-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="26980-126">Zminifikować skrypt samodzielnie lub Użyj [pakietu npm](https://www.npmjs.com/package/brotli) do momentu rozwiązania problemu [TypeError w decode.min.js (#881 Google/brotli)](https://github.com/google/brotli/issues/881) .</span><span class="sxs-lookup"><span data-stu-id="26980-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [TypeError in decode.min.js (google/brotli #881)](https://github.com/google/brotli/issues/881) is resolved.</span></span> <span data-ttu-id="26980-127">Przykładowy kod w tej sekcji używa wersji **unminified** skryptu.</span><span class="sxs-lookup"><span data-stu-id="26980-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="26980-128">Zaktualizuj aplikację, aby użyć dekodera.</span><span class="sxs-lookup"><span data-stu-id="26980-128">Update the app to use the decoder.</span></span> <span data-ttu-id="26980-129">Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="26980-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="26980-130">Aby wyłączyć kompresję, należy dodać `BlazorEnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :</span><span class="sxs-lookup"><span data-stu-id="26980-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="26980-131">`BlazorEnableCompression`Właściwość można przesłać do [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z następującą składnią w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="26980-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="26980-132">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="26980-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="26980-133">Żądania routingu dla składników strony w Blazor WebAssembly aplikacji nie są tak proste jak żądania routingu w Blazor Server hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="26980-134">Weź pod uwagę Blazor WebAssembly aplikację z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="26980-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="26980-135">`Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="26980-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="26980-136">`About.razor`: `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="26980-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="26980-137">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="26980-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="26980-138">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="26980-138">The browser makes a request.</span></span>
1. <span data-ttu-id="26980-139">Zostanie zwrócona domyślna strona, która zwykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="26980-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="26980-140">`index.html` wyładowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="26980-141">Blazorładuje router, a Razor `Main` składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="26980-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="26980-142">Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="26980-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="26980-143">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor WebAssembly aplikacji* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="26980-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="26980-144">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="26980-144">The router handles the requests internally.</span></span>

<span data-ttu-id="26980-145">Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` .</span><span class="sxs-lookup"><span data-stu-id="26980-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="26980-146">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="26980-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="26980-147">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie.</span><span class="sxs-lookup"><span data-stu-id="26980-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="26980-148">Gdy `index.html` jest zwracany, Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="26980-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="26980-149">Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="26980-150">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="26980-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="26980-151">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26980-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="26980-152">*Wdrożenie hostowane* Blazor WebAssembly umożliwia aplikacji przeglądarki z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="26980-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="26980-153">Aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="26980-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="26980-154">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="26980-154">The two apps are deployed together.</span></span> <span data-ttu-id="26980-155">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26980-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="26980-156">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu **Blazor WebAssembly aplikacji** ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="26980-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="26980-157">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="26980-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="26980-158">Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="26980-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-blazor-webassembly-apps"></a><span data-ttu-id="26980-159">Hostowane wdrożenie z wieloma Blazor WebAssembly aplikacjami</span><span class="sxs-lookup"><span data-stu-id="26980-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="26980-160">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="26980-160">App configuration</span></span>

<span data-ttu-id="26980-161">Rozwiązania hostowane Blazor mogą obsługiwać wiele Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-161">Hosted Blazor solutions can serve multiple Blazor WebAssembly apps.</span></span>

> [!NOTE]
> <span data-ttu-id="26980-162">W przykładzie w tej sekcji odwołuje się do korzystania z *rozwiązania* Visual Studio, ale użycie programu Visual Studio i rozwiązania Visual Studio nie jest wymagane, aby wiele aplikacji klienckich działało w Blazor WebAssembly scenariuszu hostowanych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-162">The example in this section references the use of a Visual Studio *solution*, but the use of Visual Studio and a Visual Studio solution isn't required for multiple client apps to work in a hosted Blazor WebAssembly apps scenario.</span></span> <span data-ttu-id="26980-163">Jeśli nie używasz programu Visual Studio, zignoruj `{SOLUTION NAME}.sln` plik i wszystkie inne pliki utworzone dla programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="26980-163">If you aren't using Visual Studio, ignore the `{SOLUTION NAME}.sln` file and any other files created for Visual Studio.</span></span>

<span data-ttu-id="26980-164">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="26980-164">In the following example:</span></span>

* <span data-ttu-id="26980-165">Początkowa (pierwsza) aplikacja kliencka jest domyślnym projektem klienta rozwiązania utworzonego na podstawie Blazor WebAssembly szablonu projektu.</span><span class="sxs-lookup"><span data-stu-id="26980-165">The initial (first) client app is the default client project of a solution created from the Blazor WebAssembly project template.</span></span> <span data-ttu-id="26980-166">Pierwsza aplikacja kliencka jest dostępna w przeglądarce z adresu URL `/FirstApp` na porcie 5001 lub na hoście `firstapp.com` .</span><span class="sxs-lookup"><span data-stu-id="26980-166">The first client app is accessible in a browser from the URL `/FirstApp` on either port 5001 or with a host of `firstapp.com`.</span></span>
* <span data-ttu-id="26980-167">Druga aplikacja kliencka jest dodawana do rozwiązania `SecondBlazorApp.Client` .</span><span class="sxs-lookup"><span data-stu-id="26980-167">A second client app is added to the solution, `SecondBlazorApp.Client`.</span></span> <span data-ttu-id="26980-168">Druga aplikacja kliencka jest dostępna w przeglądarce z adresu URL `/SecondApp` na porcie 5002 lub na hoście `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="26980-168">The second client app is accessible in a browser from the the URL `/SecondApp` on either port 5002 or with a host of `secondapp.com`.</span></span>

<span data-ttu-id="26980-169">Użyj istniejącego rozwiązania hostowanego Blazor lub Utwórz nowe rozwiązanie z Blazor szablonu hostowanego projektu:</span><span class="sxs-lookup"><span data-stu-id="26980-169">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template:</span></span>

* <span data-ttu-id="26980-170">W pliku projektu aplikacji klienckiej Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości, `FirstApp` Aby ustawić ścieżkę bazową dla statycznych zasobów projektu:</span><span class="sxs-lookup"><span data-stu-id="26980-170">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="26980-171">Dodaj drugą aplikację kliencką do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="26980-171">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="26980-172">Dodaj folder o nazwie `SecondClient` do folderu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="26980-172">Add a folder named `SecondClient` to the solution's folder.</span></span> <span data-ttu-id="26980-173">Folder rozwiązania utworzony na podstawie szablonu projektu zawiera następujący plik rozwiązania i foldery po `SecondClient` dodaniu folderu:</span><span class="sxs-lookup"><span data-stu-id="26980-173">The solution folder created from the project template contains the following solution file and folders after the `SecondClient` folder is added:</span></span>
  
    * <span data-ttu-id="26980-174">`Client` system32\drivers\etc</span><span class="sxs-lookup"><span data-stu-id="26980-174">`Client` (folder)</span></span>
    * <span data-ttu-id="26980-175">`SecondClient` system32\drivers\etc</span><span class="sxs-lookup"><span data-stu-id="26980-175">`SecondClient` (folder)</span></span>
    * <span data-ttu-id="26980-176">`Server` system32\drivers\etc</span><span class="sxs-lookup"><span data-stu-id="26980-176">`Server` (folder)</span></span>
    * <span data-ttu-id="26980-177">`Shared` system32\drivers\etc</span><span class="sxs-lookup"><span data-stu-id="26980-177">`Shared` (folder)</span></span>
    * <span data-ttu-id="26980-178">`{SOLUTION NAME}.sln` rozszerzeniem</span><span class="sxs-lookup"><span data-stu-id="26980-178">`{SOLUTION NAME}.sln` (file)</span></span>
    
    <span data-ttu-id="26980-179">Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="26980-179">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

  * <span data-ttu-id="26980-180">Utwórz Blazor WebAssembly aplikację o nazwie `SecondBlazorApp.Client` w `SecondClient` folderze z Blazor WebAssembly szablonu projektu.</span><span class="sxs-lookup"><span data-stu-id="26980-180">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>

  * <span data-ttu-id="26980-181">W `SecondBlazorApp.Client` pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="26980-181">In the `SecondBlazorApp.Client` app's project file:</span></span>

    * <span data-ttu-id="26980-182">Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości z `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="26980-182">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="26980-183">Dodaj odwołanie do projektu do `Shared` projektu:</span><span class="sxs-lookup"><span data-stu-id="26980-183">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="26980-184">Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="26980-184">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="26980-185">W pliku projektu aplikacji serwera Utwórz odwołanie do projektu dla dodanej `SecondBlazorApp.Client` aplikacji klienckiej:</span><span class="sxs-lookup"><span data-stu-id="26980-185">In the server app's project file, create a project reference for the added `SecondBlazorApp.Client` client app:</span></span>

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  <span data-ttu-id="26980-186">Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="26980-186">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="26980-187">W pliku aplikacji serwera `Properties/launchSettings.json` Skonfiguruj `applicationUrl` profil Kestrel ( `{SOLUTION NAME}.Server` ), aby uzyskać dostęp do aplikacji klienckich na portach 5001 i 5002:</span><span class="sxs-lookup"><span data-stu-id="26980-187">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="26980-188">W metodzie aplikacji serwera `Startup.Configure` ( `Startup.cs` ) Usuń następujące wiersze, które pojawią się po wywołaniu <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="26980-188">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="26980-189">Dodaj oprogramowanie pośredniczące, które mapuje żądania do aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="26980-189">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="26980-190">Poniższy przykład konfiguruje oprogramowanie pośredniczące do uruchomienia, gdy:</span><span class="sxs-lookup"><span data-stu-id="26980-190">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="26980-191">Port żądania ma wartość 5001 dla oryginalnej aplikacji klienckiej lub 5002 dla dodanej aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="26980-191">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="26980-192">Host żądania jest `firstapp.com` przeznaczony dla oryginalnej aplikacji klienckiej lub `secondapp.com` dla dodanej aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="26980-192">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="26980-193">Przykład przedstawiony w tej sekcji wymaga dodatkowej konfiguracji dla:</span><span class="sxs-lookup"><span data-stu-id="26980-193">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="26980-194">Uzyskiwanie dostępu do aplikacji w przykładowych domenach hostów `firstapp.com` i `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="26980-194">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="26980-195">Certyfikaty dla aplikacji klienckich, aby włączyć zabezpieczenia protokołu TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="26980-195">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="26980-196">Wymagana konfiguracja wykracza poza zakres tego artykułu i zależy od tego, w jaki sposób jest hostowane rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="26980-196">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="26980-197">Aby uzyskać więcej informacji, zobacz artykuł dotyczący [hosta i wdrażania](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="26980-197">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="26980-198">Umieść następujący kod, w którym wiersze zostały wcześniej usunięte:</span><span class="sxs-lookup"><span data-stu-id="26980-198">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="26980-199">W kontrolerze prognozowania pogody aplikacji serwera `Controllers/WeatherForecastController.cs` Zastąp istniejącą trasę ( `[Route("[controller]")]` ) do `WeatherForecastController` następującej trasy:</span><span class="sxs-lookup"><span data-stu-id="26980-199">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="26980-200">Oprogramowanie pośredniczące dodane do metody aplikacji serwerowej `Startup.Configure` wcześniej modyfikuje przychodzące żądania do `/WeatherForecast` albo `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` w zależności od portu (5001/5002) lub domeny ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="26980-200">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="26980-201">Powyższe trasy kontrolera są wymagane w celu zwrócenia danych pogody z aplikacji serwerowej do aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="26980-201">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="26980-202">Statyczne zasoby i biblioteki klas</span><span class="sxs-lookup"><span data-stu-id="26980-202">Static assets and class libraries</span></span>

<span data-ttu-id="26980-203">W przypadku zasobów statycznych należy stosować następujące podejścia:</span><span class="sxs-lookup"><span data-stu-id="26980-203">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="26980-204">Gdy element zawartości znajduje się w folderze aplikacji klienta `wwwroot` , podaj ścieżki zwykle:</span><span class="sxs-lookup"><span data-stu-id="26980-204">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="26980-205">Gdy element zawartości znajduje się w `wwwroot` folderze [ Razor biblioteki klas (RCL)](xref:blazor/components/class-libraries), odwołuje się do zasobu statycznego w aplikacji klienckiej zgodnie ze wskazówkami w [artykule RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="26980-205">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="26980-206">Składniki udostępniane aplikacji klienckiej przez bibliotekę klas są zwykle przywoływane.</span><span class="sxs-lookup"><span data-stu-id="26980-206">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="26980-207">Jeśli wszystkie składniki wymagają plików stylów lub JavaScript, plik aplikacji klienta `wwwroot/index.html` musi zawierać poprawne linki do zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="26980-207">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="26980-208">Te podejścia przedstawiono w poniższych przykładach.</span><span class="sxs-lookup"><span data-stu-id="26980-208">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="26980-209">Dodaj następujący `Jeep` składnik do jednej z aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="26980-209">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="26980-210">`Jeep`Składnik używa:</span><span class="sxs-lookup"><span data-stu-id="26980-210">The `Jeep` component uses:</span></span>

* <span data-ttu-id="26980-211">Obraz z folderu aplikacji klienta `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="26980-211">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="26980-212">Obraz z [dodanego folderu Razor biblioteki składników](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="26980-212">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="26980-213">Przykładowy składnik ( `Component1` ) jest tworzony automatycznie przez szablon projektu RCL, gdy `JeepImage` biblioteka zostanie dodana do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="26980-213">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="26980-214">Nie Publikuj obrazów pojazdów publicznie, chyba że są **one** właścicielami.</span><span class="sxs-lookup"><span data-stu-id="26980-214">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="26980-215">W przeciwnym razie ryzyko naruszenia praw autorskich.</span><span class="sxs-lookup"><span data-stu-id="26980-215">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="26980-216">`jeep-yj.png`Obraz biblioteki można również dodać do `Component1` składnika biblioteki ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="26980-216">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="26980-217">Plik aplikacji klienta `wwwroot/index.html` żąda arkusza stylów biblioteki z następującym dodany `<link>` tag:</span><span class="sxs-lookup"><span data-stu-id="26980-217">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="26980-218">Dodaj nawigację do `Jeep` składnika w składniku aplikacji klienckiej `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="26980-218">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="26980-219">Aby uzyskać więcej informacji na temat RCLs, zobacz:</span><span class="sxs-lookup"><span data-stu-id="26980-219">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="26980-220">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="26980-220">Standalone deployment</span></span>

<span data-ttu-id="26980-221">*Wdrożenie samodzielne* służy Blazor WebAssembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="26980-221">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="26980-222">Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="26980-222">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="26980-223">Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="26980-223">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="26980-224">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="26980-224">Azure App Service</span></span>

<span data-ttu-id="26980-225">Blazor WebAssembly Aplikacje można wdrażać w usłudze Azure App Services w systemie Windows, który jest hostem aplikacji w [usługach IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="26980-225">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="26980-226">Wdrażanie autonomicznej Blazor WebAssembly aplikacji do Azure App Service dla systemu Linux nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="26980-226">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="26980-227">Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny.</span><span class="sxs-lookup"><span data-stu-id="26980-227">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="26980-228">Trwa wykonywanie pracy, aby włączyć ten scenariusz.</span><span class="sxs-lookup"><span data-stu-id="26980-228">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="26980-229">IIS</span><span class="sxs-lookup"><span data-stu-id="26980-229">IIS</span></span>

<span data-ttu-id="26980-230">Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-230">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="26980-231">Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="26980-231">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="26980-232">Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze.</span><span class="sxs-lookup"><span data-stu-id="26980-232">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="26980-233">Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="26980-233">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="26980-234">web.config</span><span class="sxs-lookup"><span data-stu-id="26980-234">web.config</span></span>

<span data-ttu-id="26980-235">Po Blazor opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="26980-235">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="26980-236">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="26980-236">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="26980-237">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="26980-237">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="26980-238">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="26980-238">`.json`: `application/json`</span></span>
  * <span data-ttu-id="26980-239">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="26980-239">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="26980-240">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="26980-240">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="26980-241">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="26980-241">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="26980-242">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="26980-242">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="26980-243">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="26980-243">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="26980-244">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="26980-244">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="26980-245">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="26980-245">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="26980-246">Użyj niestandardowego web.config</span><span class="sxs-lookup"><span data-stu-id="26980-246">Use a custom web.config</span></span>

<span data-ttu-id="26980-247">Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="26980-247">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="26980-248">Skonfiguruj projekt do publikowania zasobów specyficznych dla usług IIS przy użyciu `PublishIISAssets` w pliku projektu aplikacji i Opublikuj projekt:</span><span class="sxs-lookup"><span data-stu-id="26980-248">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="26980-249">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="26980-249">Install the URL Rewrite Module</span></span>

<span data-ttu-id="26980-250">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="26980-250">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="26980-251">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="26980-251">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="26980-252">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="26980-252">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="26980-253">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="26980-253">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="26980-254">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="26980-254">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="26980-255">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="26980-255">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="26980-256">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="26980-256">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="26980-257">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="26980-257">Copy the installer to the server.</span></span> <span data-ttu-id="26980-258">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="26980-258">Run the installer.</span></span> <span data-ttu-id="26980-259">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="26980-259">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="26980-260">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="26980-260">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="26980-261">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="26980-261">Configure the website</span></span>

<span data-ttu-id="26980-262">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-262">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="26980-263">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="26980-263">The folder contains:</span></span>

* <span data-ttu-id="26980-264">Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="26980-264">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="26980-265">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-265">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="26980-266">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="26980-266">Host as an IIS sub-app</span></span>

<span data-ttu-id="26980-267">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="26980-267">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="26980-268">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="26980-268">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="26980-269">Usuń program obsługi w Blazor opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:</span><span class="sxs-lookup"><span data-stu-id="26980-269">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="26980-270">Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :</span><span class="sxs-lookup"><span data-stu-id="26980-270">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="26980-271">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="26980-271">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="26980-272">Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="26980-272">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="26980-273">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="26980-273">Brotli and Gzip compression</span></span>

<span data-ttu-id="26980-274">*Ta sekcja dotyczy tylko aplikacji autonomicznych Blazor WebAssembly . aplikacje hostowane Blazor używają domyślnego pliku aplikacji ASP.NET Core `web.config` , a nie pliku połączonego z tą sekcją.*</span><span class="sxs-lookup"><span data-stu-id="26980-274">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="26980-275">Program IIS można skonfigurować `web.config` w taki sposób, aby obsługiwał skompresowane zasoby Brotli lub gzip Blazor dla aplikacji autonomicznych Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="26980-275">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="26980-276">Przykładowy plik konfiguracji można znaleźć w temacie [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="26980-276">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="26980-277">Dodatkowa konfiguracja przykładowego `web.config` pliku może być wymagana w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="26980-277">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="26980-278">Specyfikacja aplikacji wywołuje jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="26980-278">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="26980-279">Obsługa skompresowanych plików, które nie są konfigurowane przez przykładowy `web.config` plik.</span><span class="sxs-lookup"><span data-stu-id="26980-279">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="26980-280">Obsługa skompresowanych plików skonfigurowanych przez przykładowy `web.config` plik w nieskompresowanym formacie.</span><span class="sxs-lookup"><span data-stu-id="26980-280">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="26980-281">Konfiguracja usług IIS na serwerze (na przykład `applicationHost.config` ) zapewnia ustawienia domyślne usług IIS na poziomie serwera.</span><span class="sxs-lookup"><span data-stu-id="26980-281">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="26980-282">W zależności od konfiguracji na poziomie serwera aplikacja może wymagać innej konfiguracji usług IIS niż zawartość pliku przykładowego `web.config` .</span><span class="sxs-lookup"><span data-stu-id="26980-282">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="26980-283">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="26980-283">Troubleshooting</span></span>

<span data-ttu-id="26980-284">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="26980-284">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="26980-285">Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="26980-285">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="26980-286">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="26980-286">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="26980-287">Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="26980-287">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="26980-288">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="26980-288">Azure Storage</span></span>

<span data-ttu-id="26980-289">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="26980-289">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="26980-290">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="26980-290">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="26980-291">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="26980-291">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="26980-292">Ustaw **nazwę dokumentu indeksu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="26980-292">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="26980-293">Ustaw **ścieżkę do dokumentu błędu** `index.html` .</span><span class="sxs-lookup"><span data-stu-id="26980-293">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="26980-294">Razor składniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="26980-294">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="26980-295">Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**.</span><span class="sxs-lookup"><span data-stu-id="26980-295">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="26980-296">`index.html`Obiekt BLOB jest zwracany, a Blazor router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="26980-296">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="26980-297">Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="26980-297">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="26980-298">Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.</span><span class="sxs-lookup"><span data-stu-id="26980-298">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="26980-299">Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-299">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="26980-300">W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:</span><span class="sxs-lookup"><span data-stu-id="26980-300">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="26980-301">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="26980-301">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="26980-302">Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="26980-302">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="26980-303">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="26980-303">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="26980-304">Nginx</span><span class="sxs-lookup"><span data-stu-id="26980-304">Nginx</span></span>

<span data-ttu-id="26980-305">Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="26980-305">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="26980-306">W przypadku ustawienia [limitu szybkości serii Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) w [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) programie Blazor WebAssembly aplikacje mogą wymagać dużej `burst` wartości parametru, aby pomieścić stosunkowo dużą liczbę żądań wykonywanych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="26980-306">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="26980-307">Początkowo ustaw wartość na co najmniej 60:</span><span class="sxs-lookup"><span data-stu-id="26980-307">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="26980-308">Zwiększ wartość, jeśli narzędzia deweloperskie przeglądarki lub ruch sieciowy wskazuje, że żądania odbierają kod stanu *niedostępny dla usługi 503* .</span><span class="sxs-lookup"><span data-stu-id="26980-308">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="26980-309">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="26980-309">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="apache"></a><span data-ttu-id="26980-310">Apache</span><span class="sxs-lookup"><span data-stu-id="26980-310">Apache</span></span>

<span data-ttu-id="26980-311">Aby wdrożyć Blazor WebAssembly aplikację w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="26980-311">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="26980-312">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="26980-312">Create the Apache configuration file.</span></span> <span data-ttu-id="26980-313">Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="26980-313">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="26980-314">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="26980-314">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="26980-315">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="26980-315">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="26980-316">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="26980-316">Restart the Apache service.</span></span>

<span data-ttu-id="26980-317">Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="26980-317">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="26980-318">Usługa GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="26980-318">GitHub Pages</span></span>

<span data-ttu-id="26980-319">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `wwwroot/404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony.</span><span class="sxs-lookup"><span data-stu-id="26980-319">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="26980-320">Aby zapoznać się z przykładem, zobacz [ Blazor repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="26980-320">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="26980-321">[Aktywna witryna](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="26980-321">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="26980-322">W przypadku korzystania z witryny projektu zamiast witryny organizacji zaktualizuj `<base>` tag w temacie `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="26980-322">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="26980-323">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="26980-323">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="26980-324">W [ Blazor repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)baza `href` jest aktualizowana przy publikowaniu przez [ `.github/workflows/main.yml` plik konfiguracji](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="26980-324">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="26980-325">[ Blazor Repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) nie należy do ani nie jest obsługiwane przez platformę .NET Foundation ani firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="26980-325">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="26980-326">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="26980-326">Host configuration values</span></span>

<span data-ttu-id="26980-327">[ Blazor WebAssembly aplikacje](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="26980-327">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="26980-328">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="26980-328">Content root</span></span>

<span data-ttu-id="26980-329">`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="26980-329">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="26980-330">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-330">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="26980-331">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="26980-331">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="26980-332">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="26980-332">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="26980-333">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="26980-333">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="26980-334">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="26980-334">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="26980-335">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="26980-335">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="26980-336">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="26980-336">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="26980-337">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="26980-337">Path base</span></span>

<span data-ttu-id="26980-338">`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="26980-338">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="26980-339">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-339">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="26980-340">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="26980-340">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="26980-341">W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu.</span><span class="sxs-lookup"><span data-stu-id="26980-341">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="26980-342">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="26980-342">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="26980-343">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="26980-343">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="26980-344">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="26980-344">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="26980-345">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="26980-345">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="26980-346">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="26980-346">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="26980-347">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="26980-347">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="26980-348">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="26980-348">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="26980-349">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="26980-349">URLs</span></span>

<span data-ttu-id="26980-350">`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="26980-350">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="26980-351">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="26980-351">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="26980-352">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="26980-352">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="26980-353">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="26980-353">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="26980-354">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="26980-354">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="26980-355">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="26980-355">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="26980-356">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="26980-356">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="26980-357">Konfigurowanie elementu przycinającego</span><span class="sxs-lookup"><span data-stu-id="26980-357">Configure the Trimmer</span></span>

<span data-ttu-id="26980-358">Blazor wykonuje przycinanie języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="26980-358">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="26980-359">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="26980-359">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="26980-360">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="26980-360">Configure the Linker</span></span>

<span data-ttu-id="26980-361">Blazor wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="26980-361">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="26980-362">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="26980-362">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="26980-363">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="26980-363">Custom boot resource loading</span></span>

<span data-ttu-id="26980-364">Blazor WebAssemblyAplikacja może zostać zainicjowana przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="26980-364">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="26980-365">Należy używać `loadBootResource` w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="26980-365">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="26980-366">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="26980-366">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="26980-367">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="26980-367">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="26980-368">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="26980-368">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="26980-369">`loadBootResource` Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="26980-369">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="26980-370">Parametr</span><span class="sxs-lookup"><span data-stu-id="26980-370">Parameter</span></span>    | <span data-ttu-id="26980-371">Opis</span><span class="sxs-lookup"><span data-stu-id="26980-371">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="26980-372">Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="26980-372">The type of the resource.</span></span> <span data-ttu-id="26980-373">Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="26980-373">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="26980-374">Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="26980-374">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="26980-375">Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="26980-375">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="26980-376">Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="26980-376">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="26980-377">`loadBootResource` zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="26980-377">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="26980-378">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="26980-378">URI string.</span></span> <span data-ttu-id="26980-379">W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="26980-379">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="26980-380">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="26980-380">Timezone data</span></span>

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

* <span data-ttu-id="26980-381">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="26980-381">`Promise<Response>`.</span></span> <span data-ttu-id="26980-382">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-382">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="26980-383">Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="26980-383">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="26980-384">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="26980-384">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="26980-385">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="26980-385">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="26980-386">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="26980-386">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="26980-387">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="26980-387">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="26980-388">Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="26980-388">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="26980-389">Zmień rozszerzenie nazwy pliku DLL</span><span class="sxs-lookup"><span data-stu-id="26980-389">Change the filename extension of DLL files</span></span>

<span data-ttu-id="26980-390">Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="26980-390">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="26980-391">Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="26980-391">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="26980-392">Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="26980-392">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="26980-393">W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="26980-393">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="26980-394">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="26980-394">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="26980-395">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="26980-395">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="26980-396">W systemie Linux lub macOS:</span><span class="sxs-lookup"><span data-stu-id="26980-396">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="26980-397">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="26980-397">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="26980-398">Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.</span><span class="sxs-lookup"><span data-stu-id="26980-398">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="26980-399">Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="26980-399">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="26980-400">Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki.</span><span class="sxs-lookup"><span data-stu-id="26980-400">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="26980-401">Kompresja jest wyłączona w tym podejściu.</span><span class="sxs-lookup"><span data-stu-id="26980-401">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="26980-402">Rekompresuj zaktualizowany `blazor.boot.json` plik.</span><span class="sxs-lookup"><span data-stu-id="26980-402">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="26980-403">Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi.</span><span class="sxs-lookup"><span data-stu-id="26980-403">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="26980-404">Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="26980-404">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="26980-405">W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="26980-405">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="26980-406">Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="26980-406">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="26980-407">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="26980-407">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="26980-408">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="26980-408">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="26980-409">W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="26980-409">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="26980-410">Podczas zmiany nazwy i opóźnionego ładowania tych samych zestawów zobacz wskazówki w temacie <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="26980-410">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="26980-411">Rozwiązywanie błędów sprawdzania integralności</span><span class="sxs-lookup"><span data-stu-id="26980-411">Resolve integrity check failures</span></span>

<span data-ttu-id="26980-412">Po Blazor WebAssembly pobraniu plików startowych aplikacji nakazuje ona przeprowadzenie kontroli integralności odpowiedzi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="26980-412">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="26980-413">Używa informacji w pliku, `blazor.boot.json` Aby określić oczekiwane wartości skrótu SHA-256 dla `.dll` , `.wasm` i innych plików.</span><span class="sxs-lookup"><span data-stu-id="26980-413">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="26980-414">Jest to korzystne z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="26980-414">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="26980-415">Gwarantuje to, że nie ma ryzyka ładowania niespójnego zestawu plików, na przykład jeśli nowe wdrożenie jest stosowane do serwera sieci Web, podczas gdy użytkownik jest w trakcie procesu pobierania plików aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-415">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="26980-416">Niespójne pliki mogą prowadzić do niezdefiniowanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="26980-416">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="26980-417">Gwarantuje to, że przeglądarka użytkownika nigdy nie buforuje niespójnych lub nieprawidłowych odpowiedzi, co może uniemożliwić ich uruchomienie aplikacji nawet w przypadku ręcznego odświeżenia strony.</span><span class="sxs-lookup"><span data-stu-id="26980-417">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="26980-418">Pozwala ona bezpiecznie na buforowanie odpowiedzi i nie sprawdza, czy zmiany po stronie serwera są zmieniane, dopóki nie zmienią się oczekiwanych skrótów SHA-256, więc kolejne obciążenia strony obejmują mniejszą liczbę żądań i są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="26980-418">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="26980-419">Jeśli serwer sieci Web zwróci odpowiedzi, które nie pasują do oczekiwanych skrótów SHA-256, zobaczysz błąd podobny do poniższego, pojawia się w konsoli dewelopera przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="26980-419">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

> <span data-ttu-id="26980-420">Nie można znaleźć prawidłowego skrótu w atrybucie "Integrity" dla zasobu " https://myapp.example.com/\_framework/My BlazorApp.dll" z obliczoną integralnością SHA-256 "IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY =".</span><span class="sxs-lookup"><span data-stu-id="26980-420">Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/\_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='.</span></span> <span data-ttu-id="26980-421">Zasób został zablokowany.</span><span class="sxs-lookup"><span data-stu-id="26980-421">The resource has been blocked.</span></span>

<span data-ttu-id="26980-422">W większości przypadków *nie* jest to problem ze sprawdzaniem integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-422">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="26980-423">Zamiast tego oznacza to, że występuje jakiś inny problem, a Kontrola integralności ostrzega o tym, że ten problem wystąpi.</span><span class="sxs-lookup"><span data-stu-id="26980-423">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="26980-424">Diagnozowanie problemów z integralnością</span><span class="sxs-lookup"><span data-stu-id="26980-424">Diagnosing integrity problems</span></span>

<span data-ttu-id="26980-425">Po skompilowaniu aplikacji wygenerowany `blazor.boot.json` manifest opisuje skróty SHA-256 zasobów rozruchowych (na przykład, `.dll` `.wasm` i innych plików) w momencie wygenerowania danych wyjściowych kompilacji.</span><span class="sxs-lookup"><span data-stu-id="26980-425">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="26980-426">Sprawdzanie integralności przebiega tak długo, jak skróty SHA-256 `blazor.boot.json` są zgodne z plikami dostarczonymi do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="26980-426">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="26980-427">Najczęstsze przyczyny tego niepowodzenia to:</span><span class="sxs-lookup"><span data-stu-id="26980-427">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="26980-428">Odpowiedź serwera sieci Web jest błędem (na przykład *404 — nie można znaleźć* lub *500 — wewnętrzny błąd serwera*) zamiast pliku, którego żądała przeglądarka.</span><span class="sxs-lookup"><span data-stu-id="26980-428">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="26980-429">Jest on raportowany przez przeglądarkę jako błąd sprawdzania integralności, a nie jako błąd odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="26980-429">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="26980-430">Coś zmieniło zawartość plików między kompilacją a dostarczeniem plików do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="26980-430">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="26980-431">Może się tak zdarzyć:</span><span class="sxs-lookup"><span data-stu-id="26980-431">This might happen:</span></span>
   * <span data-ttu-id="26980-432">Jeśli narzędzia kompilacji lub kompilacje ręcznie modyfikują dane wyjściowe kompilacji.</span><span class="sxs-lookup"><span data-stu-id="26980-432">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="26980-433">Jeśli jakiś aspekt procesu wdrażania zmodyfikował pliki.</span><span class="sxs-lookup"><span data-stu-id="26980-433">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="26980-434">Na przykład jeśli korzystasz z mechanizmu wdrożenia opartego na usłudze git, weź pod uwagę, że git w sposób przezroczysty konwertuje końce wierszy w stylu systemu Windows do końca wiersza w stylu Unix, jeśli zatwierdzisz pliki w systemie Windows i wyewidencjonujesz je w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="26980-434">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="26980-435">Zmiana końców wierszy plików zmienia wartości skrótów SHA-256.</span><span class="sxs-lookup"><span data-stu-id="26980-435">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="26980-436">Aby uniknąć tego problemu, rozważ [użycie `.gitattributes` programu do traktowania artefaktów kompilacji jako `binary` plików](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span><span class="sxs-lookup"><span data-stu-id="26980-436">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="26980-437">Serwer sieci Web modyfikuje zawartość pliku w ramach ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="26980-437">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="26980-438">Na przykład niektóre sieci dystrybucji zawartości (sieci CDN) automatycznie próbują [zminifikować](xref:client-side/bundling-and-minification#minification) kod HTML, modyfikując go.</span><span class="sxs-lookup"><span data-stu-id="26980-438">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="26980-439">Może być konieczne wyłączenie takich funkcji.</span><span class="sxs-lookup"><span data-stu-id="26980-439">You may need to disable such features.</span></span>

<span data-ttu-id="26980-440">Aby zdiagnozować, które z nich mają zastosowanie w Twoim przypadku:</span><span class="sxs-lookup"><span data-stu-id="26980-440">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="26980-441">Zwróć uwagę, który plik wyzwala błąd, odczytując komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="26980-441">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="26980-442">Otwórz narzędzia deweloperskie w przeglądarce i sprawdź kartę *Sieć* . W razie potrzeby Załaduj ponownie stronę, aby zobaczyć listę żądań i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="26980-442">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="26980-443">Znajdź plik wyzwalający błąd na tej liście.</span><span class="sxs-lookup"><span data-stu-id="26980-443">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="26980-444">Sprawdź kod stanu HTTP w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="26980-444">Check the HTTP status code in the response.</span></span> <span data-ttu-id="26980-445">Jeśli serwer zwróci coś innego niż *200-OK* (lub inny kod stanu 2xx), wystąpił problem po stronie serwera do zdiagnozowania.</span><span class="sxs-lookup"><span data-stu-id="26980-445">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="26980-446">Na przykład kod stanu 403 oznacza problem z autoryzacją, natomiast kod stanu 500 oznacza, że serwer kończy się nieokreślonym sposobem.</span><span class="sxs-lookup"><span data-stu-id="26980-446">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="26980-447">Zapoznaj się z dziennikami po stronie serwera, aby zdiagnozować i naprawić aplikację.</span><span class="sxs-lookup"><span data-stu-id="26980-447">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="26980-448">Jeśli kod stanu to *200-OK* dla zasobu, zapoznaj się z zawartością odpowiedzi w narzędziach deweloperskich przeglądarki i sprawdź, czy zawartość jest zgodna z oczekiwanymi danymi.</span><span class="sxs-lookup"><span data-stu-id="26980-448">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matches up with the data expected.</span></span> <span data-ttu-id="26980-449">Typowym problemem jest na przykład Nieskonfigurowanie routingu w taki sposób, aby żądania zwracały `index.html` dane nawet dla innych plików.</span><span class="sxs-lookup"><span data-stu-id="26980-449">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="26980-450">Upewnij się, że odpowiedzi na `.wasm` żądania to pliki binarne webassembly, a odpowiedzi na `.dll` żądania to pliki binarne zestawu platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="26980-450">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="26980-451">W przeciwnym razie masz problem z Routing po stronie serwera do zdiagnozowania.</span><span class="sxs-lookup"><span data-stu-id="26980-451">If not, you have a server-side routing problem to diagnose.</span></span>
 1. <span data-ttu-id="26980-452">Sprawdź poprawność opublikowanych i wdrożonych danych wyjściowych aplikacji za pomocą [skryptu Rozwiązywanie problemów ze integralnością programu PowerShell](#troubleshoot-integrity-powershell-script).</span><span class="sxs-lookup"><span data-stu-id="26980-452">Seek to validate the app's published and deployed output with the [Troubleshoot integrity PowerShell script](#troubleshoot-integrity-powershell-script).</span></span>

<span data-ttu-id="26980-453">Jeśli potwierdzasz, że serwer zwraca poprawne dane plausibly, konieczne może być zmodyfikowanie zawartości między kompilacją i dostarczeniem pliku.</span><span class="sxs-lookup"><span data-stu-id="26980-453">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="26980-454">Aby zbadać to:</span><span class="sxs-lookup"><span data-stu-id="26980-454">To investigate this:</span></span>

 * <span data-ttu-id="26980-455">Zapoznaj się z mechanizmem kompilowania łańcucha narzędzi i wdrażania na wypadek modyfikacji plików po skompilowaniu plików.</span><span class="sxs-lookup"><span data-stu-id="26980-455">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="26980-456">Przykładem takiej sytuacji jest to, że program git przekształca końce wierszy plików zgodnie z wcześniejszym opisem.</span><span class="sxs-lookup"><span data-stu-id="26980-456">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="26980-457">Zapoznaj się z konfiguracją serwer sieci Web lub sieć CDN w przypadku, gdy są skonfigurowane do dynamicznego modyfikowania odpowiedzi (na przykład próba zminifikować HTML).</span><span class="sxs-lookup"><span data-stu-id="26980-457">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="26980-458">Serwer sieci Web może zaimplementować kompresję HTTP (na przykład zwracając `content-encoding: br` lub `content-encoding: gzip` ), ponieważ nie ma to wpływu na wynik po dekompresji.</span><span class="sxs-lookup"><span data-stu-id="26980-458">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="26980-459">*Nie* jest jednak konieczne, aby serwer sieci Web modyfikował nieskompresowane dane.</span><span class="sxs-lookup"><span data-stu-id="26980-459">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="troubleshoot-integrity-powershell-script"></a><span data-ttu-id="26980-460">Rozwiązywanie problemów z integralnością skryptu programu PowerShell</span><span class="sxs-lookup"><span data-stu-id="26980-460">Troubleshoot integrity PowerShell script</span></span>

<span data-ttu-id="26980-461">Użyj [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) skryptu programu PowerShell, aby zweryfikować opublikowaną i wdrożoną Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="26980-461">Use the [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell script to validate a published and deployed Blazor app.</span></span> <span data-ttu-id="26980-462">Skrypt jest dostarczany jako punkt wyjścia, gdy aplikacja ma problemy ze integralnością, których Blazor nie można zidentyfikować w strukturze.</span><span class="sxs-lookup"><span data-stu-id="26980-462">The script is provided as a starting point when the app has integrity issues that the Blazor framework can't identify.</span></span> <span data-ttu-id="26980-463">Dla Twoich aplikacji może być wymagane dostosowanie skryptu.</span><span class="sxs-lookup"><span data-stu-id="26980-463">Customization of the script might be required for your apps.</span></span>

<span data-ttu-id="26980-464">Skrypt sprawdza pliki w `publish` folderze i pobrane ze wdrożonej aplikacji w celu wykrywania problemów w różnych manifestach zawierających skróty integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-464">The script checks the files in the `publish` folder and downloaded from the deployed app to detect issues in the different manifests that contain integrity hashes.</span></span> <span data-ttu-id="26980-465">Te testy powinny wykrywać najczęstsze problemy:</span><span class="sxs-lookup"><span data-stu-id="26980-465">These checks should detect the most common problems:</span></span>

* <span data-ttu-id="26980-466">Plik został zmodyfikowany w opublikowanym danych wyjściowych bez jego realizacji.</span><span class="sxs-lookup"><span data-stu-id="26980-466">You modified a file in the published output without realizing it.</span></span>
* <span data-ttu-id="26980-467">Aplikacja nie została poprawnie wdrożona w miejscu docelowym wdrożenia lub coś uległo zmianie w środowisku docelowym wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="26980-467">The app wasn't correctly deployed to the deployment target, or something changed within the deployment target's environment.</span></span>
* <span data-ttu-id="26980-468">Istnieją różnice między wdrożoną aplikacją a danymi wyjściowymi publikowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-468">There are differences between the deployed app and the output from publishing the app.</span></span>

<span data-ttu-id="26980-469">Wywołaj skrypt za pomocą następującego polecenia w powłoce poleceń programu PowerShell:</span><span class="sxs-lookup"><span data-stu-id="26980-469">Invoke the script with the following command in a PowerShell command shell:</span></span>

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

<span data-ttu-id="26980-470">Symbole zastępcze</span><span class="sxs-lookup"><span data-stu-id="26980-470">Placeholders:</span></span>

* <span data-ttu-id="26980-471">`{BASE URL}`: Adres URL wdrożonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="26980-471">`{BASE URL}`: The URL of the deployed app.</span></span>
* <span data-ttu-id="26980-472">`{PUBLISH OUTPUT FOLDER}`: Ścieżka do `publish` folderu lub lokalizacji aplikacji, w której aplikacja została opublikowana do wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="26980-472">`{PUBLISH OUTPUT FOLDER}`: The path to the app's `publish` folder or location where the app is published for deployment.</span></span>

> [!NOTE]
> <span data-ttu-id="26980-473">Aby sklonować `dotnet/AspNetCore.Docs` repozytorium GitHub do systemu, który używa skanera antywirusowego [BitDefender](https://www.bitdefender.com) , Dodaj wyjątek do BitDefender dla `integrity.ps1` skryptu.</span><span class="sxs-lookup"><span data-stu-id="26980-473">To clone the `dotnet/AspNetCore.Docs` GitHub repository to a system that uses the [Bitdefender](https://www.bitdefender.com) virus scanner, add an exception to Bitdefender for the `integrity.ps1` script.</span></span> <span data-ttu-id="26980-474">Dodaj wyjątek do BitDefender przed klonem repozytorium, aby uniknąć posiadania skryptu poddanego kwarantannie przez skaner wirusów.</span><span class="sxs-lookup"><span data-stu-id="26980-474">Add the exception to Bitdefender before cloning the repo to avoid having the script quarantined by the virus scanner.</span></span> <span data-ttu-id="26980-475">Poniższy przykład jest typową ścieżką do skryptu dla sklonowanego repozytorium w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="26980-475">The following example is a typical path to the script for the cloned repo on a Windows system.</span></span> <span data-ttu-id="26980-476">Dostosuj ścieżkę w razie konieczności.</span><span class="sxs-lookup"><span data-stu-id="26980-476">Adjust the path as needed.</span></span> <span data-ttu-id="26980-477">Symbol zastępczy `{USER}` jest segmentem ścieżki użytkownika.</span><span class="sxs-lookup"><span data-stu-id="26980-477">The placeholder `{USER}` is the user's path segment.</span></span>
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="26980-478">Wyłącz sprawdzanie integralności dla aplikacji innych niż PWA</span><span class="sxs-lookup"><span data-stu-id="26980-478">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="26980-479">W większości przypadków nie należy wyłączać sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-479">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="26980-480">Wyłączenie sprawdzania integralności nie rozwiązuje podstawowego problemu, który spowodował nieoczekiwane odpowiedzi i spowoduje utratę wymienionych wcześniej korzyści.</span><span class="sxs-lookup"><span data-stu-id="26980-480">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="26980-481">Mogą jednak wystąpić sytuacje, w których serwer sieci Web nie może być oparty na zwracaniu spójnych odpowiedzi i nie ma możliwości wyboru, ale w celu wyłączenia kontroli integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-481">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="26980-482">Aby wyłączyć sprawdzanie integralności, Dodaj następujący element do grupy właściwości w Blazor WebAssembly `.csproj` pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="26980-482">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="26980-483">`BlazorCacheBootResources` wyłącza również Blazor domyślne zachowanie buforowania `.dll` , `.wasm` i innych plików na podstawie skrótów SHA-256, ponieważ właściwość wskazuje, że nie można opierać się na wartości skrótów SHA-256 w celu poprawnego działania.</span><span class="sxs-lookup"><span data-stu-id="26980-483">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="26980-484">Nawet w przypadku tego ustawienia Normalna pamięć podręczna w przeglądarce może nadal buforować te pliki, ale bez względu na to, czy ma to miejsce, zależy od konfiguracji serwera sieci Web i `cache-control` nagłówków, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="26980-484">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="26980-485">`BlazorCacheBootResources`Właściwość nie wyłącza sprawdzania integralności w przypadku [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="26980-485">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="26980-486">Wskazówki dotyczące PWAs można znaleźć w sekcji [Wyłączanie sprawdzania integralności dla PWAs](#disable-integrity-checking-for-pwas) .</span><span class="sxs-lookup"><span data-stu-id="26980-486">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="26980-487">Wyłącz sprawdzanie integralności dla PWAs</span><span class="sxs-lookup"><span data-stu-id="26980-487">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="26980-488">Blazorszablon aplikacji sieci Web progresywnej (PWA) zawiera sugerowany `service-worker.published.js` plik, który jest odpowiedzialny za pobieranie i przechowywanie plików aplikacji do użytku w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="26980-488">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="26980-489">Jest to oddzielny proces od normalnego mechanizmu uruchamiania aplikacji i ma własną logikę sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-489">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="26980-490">W `service-worker.published.js` pliku znajduje się następujący wiersz:</span><span class="sxs-lookup"><span data-stu-id="26980-490">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="26980-491">Aby wyłączyć sprawdzanie integralności, Usuń `integrity` parametr, zmieniając wiersz na następujący:</span><span class="sxs-lookup"><span data-stu-id="26980-491">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="26980-492">Po ponownym uruchomieniu kontroli integralności oznacza to, że utracisz gwarancje bezpieczeństwa oferowane przez sprawdzanie integralności.</span><span class="sxs-lookup"><span data-stu-id="26980-492">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="26980-493">Na przykład istnieje ryzyko, że jeśli przeglądarka użytkownika będzie buforować aplikację w tym samym czasie, gdy zostanie wdrożona nowa wersja, może to spowodować przełączenie niektórych plików ze starego wdrożenia, a niektóre z nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="26980-493">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="26980-494">W takim przypadku aplikacja zostanie zablokowana w stanie przerwania, dopóki nie zostanie wdrożona dodatkowa aktualizacja.</span><span class="sxs-lookup"><span data-stu-id="26980-494">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
