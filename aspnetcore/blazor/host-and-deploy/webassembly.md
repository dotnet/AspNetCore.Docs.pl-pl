---
title: 'Hostowanie i wdrażanie ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Dowiedz się, jak hostować i wdrażać :::no-loc(Blazor)::: aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 0912b3fbcd0b891deb4985eaa18841c22f4f3264
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055753"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c0bf7-103">Hostowanie i wdrażanie ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="c0bf7-103">Host and deploy ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="c0bf7-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="c0bf7-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="c0bf7-105">Z [ :::no-loc(Blazor WebAssembly)::: modelem hostingu](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-105">With the [:::no-loc(Blazor WebAssembly)::: hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="c0bf7-106">:::no-loc(Blazor):::Aplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-106">The :::no-loc(Blazor)::: app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="c0bf7-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="c0bf7-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="c0bf7-109">:::no-loc(Blazor):::Aplikacja jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-109">The :::no-loc(Blazor)::: app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="c0bf7-110">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="c0bf7-111">:::no-loc(Blazor):::Aplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi :::no-loc(Blazor)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-111">The :::no-loc(Blazor)::: app is placed on a static hosting web server or service, where .NET isn't used to serve the :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="c0bf7-112">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu :::no-loc(Blazor WebAssembly)::: aplikacji jako PODRZĘDNEJ aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a :::no-loc(Blazor WebAssembly)::: app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="c0bf7-113">Kompresja</span><span class="sxs-lookup"><span data-stu-id="c0bf7-113">Compression</span></span>

<span data-ttu-id="c0bf7-114">Po :::no-loc(Blazor WebAssembly)::: opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-114">When a :::no-loc(Blazor WebAssembly)::: app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="c0bf7-115">Używane są następujące algorytmy kompresji:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="c0bf7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)</span><span class="sxs-lookup"><span data-stu-id="c0bf7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="c0bf7-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="c0bf7-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="c0bf7-118">:::no-loc(Blazor)::: korzysta z hosta, aby zapewnić odpowiednie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-118">:::no-loc(Blazor)::: relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="c0bf7-119">W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="c0bf7-120">W przypadku udostępniania :::no-loc(Blazor WebAssembly)::: aplikacji autonomicznej może być wymagane dodatkowe działanie, aby zapewnić obsługę plików skompresowanych statycznie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-120">When hosting a :::no-loc(Blazor WebAssembly)::: standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="c0bf7-121">Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="c0bf7-122">Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="c0bf7-123">Uzyskaj dekoder JavaScript Brotli z repozytorium usługi [GitHub firmy Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c0bf7-124">Od września 2020 plik dekodera ma nazwę `decode.js` i znajduje się w [ `js` folderze](https://github.com/google/brotli/tree/master/js)repozytorium.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="c0bf7-125">Regresja jest obecna w wersji zminimalizowanego `decode.js` skryptu ( `decode.min.js` ) w [repozytorium usługi GitHub Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c0bf7-126">Zminifikować skrypt samodzielnie lub Użyj [pakietu npm](https://www.npmjs.com/package/brotli) do momentu, gdy zostanie usunięte [okno problemu. BrotliDecode nie jest ustawiona w decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="c0bf7-127">Przykładowy kod w tej sekcji używa wersji **unminified** skryptu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="c0bf7-128">Zaktualizuj aplikację, aby użyć dekodera.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-128">Update the app to use the decoder.</span></span> <span data-ttu-id="c0bf7-129">Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      :::no-loc(Blazor):::.start({
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
 
<span data-ttu-id="c0bf7-130">Aby wyłączyć kompresję, należy dodać `:::no-loc(Blazor):::EnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :</span><span class="sxs-lookup"><span data-stu-id="c0bf7-130">To disable compression, add the `:::no-loc(Blazor):::EnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::EnableCompression>false</:::no-loc(Blazor):::EnableCompression>
</PropertyGroup>
```

<span data-ttu-id="c0bf7-131">`:::no-loc(Blazor):::EnableCompression`Właściwość można przesłać do [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z następującą składnią w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-131">The `:::no-loc(Blazor):::EnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p::::no-loc(Blazor):::EnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="c0bf7-132">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="c0bf7-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="c0bf7-133">Żądania routingu dla składników strony w :::no-loc(Blazor WebAssembly)::: aplikacji nie są tak proste jak żądania routingu w :::no-loc(Blazor Server)::: hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-133">Routing requests for page components in a :::no-loc(Blazor WebAssembly)::: app isn't as straightforward as routing requests in a :::no-loc(Blazor Server):::, hosted app.</span></span> <span data-ttu-id="c0bf7-134">Weź pod uwagę :::no-loc(Blazor WebAssembly)::: aplikację z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-134">Consider a :::no-loc(Blazor WebAssembly)::: app with two components:</span></span>

* <span data-ttu-id="c0bf7-135">`Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="c0bf7-136">`About.razor`: `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="c0bf7-137">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="c0bf7-138">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-138">The browser makes a request.</span></span>
1. <span data-ttu-id="c0bf7-139">Zostanie zwrócona domyślna strona, która zwykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="c0bf7-140">`index.html` wyładowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="c0bf7-141">:::no-loc(Blazor):::ładuje router, a :::no-loc(Razor)::: `Main` składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-141">:::no-loc(Blazor):::'s router loads, and the :::no-loc(Razor)::: `Main` component is rendered.</span></span>

<span data-ttu-id="c0bf7-142">Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ :::no-loc(Blazor)::: router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-142">In the Main page, selecting the link to the `About` component works on the client because the :::no-loc(Blazor)::: router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="c0bf7-143">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w :::no-loc(Blazor WebAssembly)::: aplikacji* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-143">All of the requests for internal endpoints *within the :::no-loc(Blazor WebAssembly)::: app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="c0bf7-144">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-144">The router handles the requests internally.</span></span>

<span data-ttu-id="c0bf7-145">Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="c0bf7-146">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="c0bf7-147">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="c0bf7-148">Gdy `index.html` jest zwracany, :::no-loc(Blazor)::: router aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-148">When `index.html` is returned, the app's :::no-loc(Blazor)::: router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="c0bf7-149">Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="c0bf7-150">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="c0bf7-151">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0bf7-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="c0bf7-152">*Wdrożenie hostowane* :::no-loc(Blazor WebAssembly)::: umożliwia aplikacji przeglądarki z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-152">A *hosted deployment* serves the :::no-loc(Blazor WebAssembly)::: app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="c0bf7-153">Aplikacja kliencka :::no-loc(Blazor WebAssembly)::: jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-153">The client :::no-loc(Blazor WebAssembly)::: app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="c0bf7-154">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-154">The two apps are deployed together.</span></span> <span data-ttu-id="c0bf7-155">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c0bf7-156">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu **:::no-loc(Blazor WebAssembly)::: aplikacji** ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-156">For a hosted deployment, Visual Studio includes the **:::no-loc(Blazor WebAssembly)::: App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="c0bf7-157">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c0bf7-158">Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="c0bf7-159">Hostowane wdrożenie z wieloma :::no-loc(Blazor WebAssembly)::: aplikacjami</span><span class="sxs-lookup"><span data-stu-id="c0bf7-159">Hosted deployment with multiple :::no-loc(Blazor WebAssembly)::: apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="c0bf7-160">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="c0bf7-160">App configuration</span></span>

<span data-ttu-id="c0bf7-161">Aby skonfigurować rozwiązanie hostowane :::no-loc(Blazor)::: do obsługi wielu :::no-loc(Blazor WebAssembly)::: aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-161">To configure a hosted :::no-loc(Blazor)::: solution to serve multiple :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* <span data-ttu-id="c0bf7-162">Użyj istniejącego rozwiązania hostowanego :::no-loc(Blazor)::: lub Utwórz nowe rozwiązanie na podstawie :::no-loc(Blazor)::: szablonu hostowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-162">Use an existing hosted :::no-loc(Blazor)::: solution or create a new solution from the :::no-loc(Blazor)::: Hosted project template.</span></span>

* <span data-ttu-id="c0bf7-163">W pliku projektu aplikacji klienckiej Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości, `FirstApp` Aby ustawić ścieżkę bazową dla statycznych zasobów projektu:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="c0bf7-164">Dodaj drugą aplikację kliencką do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="c0bf7-165">Dodaj folder o nazwie `SecondClient` do folderu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="c0bf7-166">Utwórz :::no-loc(Blazor WebAssembly)::: aplikację o nazwie `Second:::no-loc(Blazor):::App.Client` w `SecondClient` folderze z :::no-loc(Blazor WebAssembly)::: szablonu projektu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-166">Create a :::no-loc(Blazor WebAssembly)::: app named `Second:::no-loc(Blazor):::App.Client` in the `SecondClient` folder from the :::no-loc(Blazor WebAssembly)::: project template.</span></span>
  * <span data-ttu-id="c0bf7-167">W pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-167">In the app's project file:</span></span>

    * <span data-ttu-id="c0bf7-168">Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości z `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="c0bf7-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="c0bf7-169">Dodaj odwołanie do projektu do `Shared` projektu:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="c0bf7-170">Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="c0bf7-171">W pliku projektu aplikacji serwera Utwórz odwołanie do projektu dla dodanej aplikacji klienckiej:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\Second:::no-loc(Blazor):::App.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="c0bf7-172">W pliku aplikacji serwera `Properties/launchSettings.json` Skonfiguruj `applicationUrl` profil Kestrel ( `{SOLUTION NAME}.Server` ), aby uzyskać dostęp do aplikacji klienckich na portach 5001 i 5002:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="c0bf7-173">W metodzie aplikacji serwera `Startup.Configure` ( `Startup.cs` ) Usuń następujące wiersze, które pojawią się po wywołaniu <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="c0bf7-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.Use:::no-loc(Blazor):::FrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.Map:::no-loc(Razor):::Pages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="c0bf7-174">Dodaj oprogramowanie pośredniczące, które mapuje żądania do aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="c0bf7-175">Poniższy przykład konfiguruje oprogramowanie pośredniczące do uruchomienia, gdy:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="c0bf7-176">Port żądania ma wartość 5001 dla oryginalnej aplikacji klienckiej lub 5002 dla dodanej aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="c0bf7-177">Host żądania jest `firstapp.com` przeznaczony dla oryginalnej aplikacji klienckiej lub `secondapp.com` dla dodanej aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="c0bf7-178">Przykład przedstawiony w tej sekcji wymaga dodatkowej konfiguracji dla:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="c0bf7-179">Uzyskiwanie dostępu do aplikacji w przykładowych domenach hostów `firstapp.com` i `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="c0bf7-180">Certyfikaty dla aplikacji klienckich, aby włączyć zabezpieczenia protokołu TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="c0bf7-181">Wymagana konfiguracja wykracza poza zakres tego artykułu i zależy od tego, w jaki sposób jest hostowane rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="c0bf7-182">Aby uzyskać więcej informacji, zobacz artykuł dotyczący [hosta i wdrażania](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="c0bf7-183">Umieść następujący kod, w którym wiersze zostały wcześniej usunięte:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.Use:::no-loc(Blazor):::FrameworkFiles("/FirstApp");
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

      second.Use:::no-loc(Blazor):::FrameworkFiles("/SecondApp");
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

* <span data-ttu-id="c0bf7-184">W kontrolerze prognozowania pogody aplikacji serwera `Controllers/WeatherForecastController.cs` Zastąp istniejącą trasę ( `[Route("[controller]")]` ) do `WeatherForecastController` następującej trasy:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="c0bf7-185">Oprogramowanie pośredniczące dodane do metody aplikacji serwerowej `Startup.Configure` wcześniej modyfikuje przychodzące żądania do `/WeatherForecast` albo `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` w zależności od portu (5001/5002) lub domeny ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="c0bf7-186">Powyższe trasy kontrolera są wymagane w celu zwrócenia danych pogody z aplikacji serwerowej do aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="c0bf7-187">Statyczne zasoby i biblioteki klas</span><span class="sxs-lookup"><span data-stu-id="c0bf7-187">Static assets and class libraries</span></span>

<span data-ttu-id="c0bf7-188">W przypadku zasobów statycznych należy stosować następujące podejścia:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="c0bf7-189">Gdy element zawartości znajduje się w folderze aplikacji klienta `wwwroot` , podaj ścieżki zwykle:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="c0bf7-190">Gdy element zawartości znajduje się w `wwwroot` folderze [ :::no-loc(Razor)::: biblioteki klas (RCL)](xref:blazor/components/class-libraries), odwołuje się do zasobu statycznego w aplikacji klienckiej zgodnie ze wskazówkami w [artykule RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-190">When the asset is in the `wwwroot` folder of a [:::no-loc(Razor)::: Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="c0bf7-191">Składniki udostępniane aplikacji klienckiej przez bibliotekę klas są zwykle przywoływane.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="c0bf7-192">Jeśli wszystkie składniki wymagają plików stylów lub JavaScript, plik aplikacji klienta `wwwroot/index.html` musi zawierać poprawne linki do zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-192">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="c0bf7-193">Te podejścia przedstawiono w poniższych przykładach.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-193">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="c0bf7-194">Dodaj następujący `Jeep` składnik do jednej z aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-194">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="c0bf7-195">`Jeep`Składnik używa:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-195">The `Jeep` component uses:</span></span>

* <span data-ttu-id="c0bf7-196">Obraz z folderu aplikacji klienta `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-196">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="c0bf7-197">Obraz z [dodanego folderu :::no-loc(Razor)::: biblioteki składników](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-197">An image from an [added :::no-loc(Razor)::: component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="c0bf7-198">Przykładowy składnik ( `Component1` ) jest tworzony automatycznie przez szablon projektu RCL, gdy `JeepImage` biblioteka zostanie dodana do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-198">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="c0bf7-199">Nie Publikuj obrazów pojazdów publicznie, chyba że są **one** właścicielami.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-199">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="c0bf7-200">W przeciwnym razie ryzyko naruszenia praw autorskich.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-200">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="c0bf7-201">`jeep-yj.png`Obraz biblioteki można również dodać do `Component1` składnika biblioteki ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-201">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="c0bf7-202">Plik aplikacji klienta `wwwroot/index.html` żąda arkusza stylów biblioteki z następującym dodany `<link>` tag:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-202">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="c0bf7-203">Dodaj nawigację do `Jeep` składnika w składniku aplikacji klienckiej `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-203">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="c0bf7-204">Aby uzyskać więcej informacji na temat RCLs, zobacz:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-204">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="c0bf7-205">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="c0bf7-205">Standalone deployment</span></span>

<span data-ttu-id="c0bf7-206">*Wdrożenie samodzielne* służy :::no-loc(Blazor WebAssembly)::: jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-206">A *standalone deployment* serves the :::no-loc(Blazor WebAssembly)::: app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="c0bf7-207">Każdy statyczny serwer plików jest w stanie obsłużyć :::no-loc(Blazor)::: aplikację.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-207">Any static file server is able to serve the :::no-loc(Blazor)::: app.</span></span>

<span data-ttu-id="c0bf7-208">Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-208">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c0bf7-209">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c0bf7-209">Azure App Service</span></span>

<span data-ttu-id="c0bf7-210">:::no-loc(Blazor WebAssembly)::: Aplikacje można wdrażać w usłudze Azure App Services w systemie Windows, który jest hostem aplikacji w [usługach IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-210">:::no-loc(Blazor WebAssembly)::: apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="c0bf7-211">Wdrażanie autonomicznej :::no-loc(Blazor WebAssembly)::: aplikacji do Azure App Service dla systemu Linux nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-211">Deploying a standalone :::no-loc(Blazor WebAssembly)::: app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="c0bf7-212">Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-212">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="c0bf7-213">Trwa wykonywanie pracy, aby włączyć ten scenariusz.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-213">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="c0bf7-214">IIS</span><span class="sxs-lookup"><span data-stu-id="c0bf7-214">IIS</span></span>

<span data-ttu-id="c0bf7-215">Program IIS jest obsługującym statycznego serwera plików dla :::no-loc(Blazor)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-215">IIS is a capable static file server for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="c0bf7-216">Aby skonfigurować usługi IIS do hostowania :::no-loc(Blazor)::: , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-216">To configure IIS to host :::no-loc(Blazor):::, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="c0bf7-217">Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-217">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="c0bf7-218">Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-218">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="c0bf7-219">web.config</span><span class="sxs-lookup"><span data-stu-id="c0bf7-219">web.config</span></span>

<span data-ttu-id="c0bf7-220">Po :::no-loc(Blazor)::: opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-220">When a :::no-loc(Blazor)::: project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="c0bf7-221">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-221">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="c0bf7-222">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="c0bf7-222">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="c0bf7-223">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="c0bf7-223">`.json`: `application/json`</span></span>
  * <span data-ttu-id="c0bf7-224">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="c0bf7-224">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="c0bf7-225">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c0bf7-225">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="c0bf7-226">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c0bf7-226">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="c0bf7-227">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-227">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="c0bf7-228">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-228">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="c0bf7-229">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-229">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="c0bf7-230">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-230">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="c0bf7-231">Użyj niestandardowego web.config</span><span class="sxs-lookup"><span data-stu-id="c0bf7-231">Use a custom web.config</span></span>

<span data-ttu-id="c0bf7-232">Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-232">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="c0bf7-233">Skonfiguruj projekt do publikowania zasobów specyficznych dla usług IIS przy użyciu `PublishIISAssets` w pliku projektu aplikacji i Opublikuj projekt:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-233">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="c0bf7-234">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="c0bf7-234">Install the URL Rewrite Module</span></span>

<span data-ttu-id="c0bf7-235">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-235">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="c0bf7-236">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-236">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="c0bf7-237">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-237">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="c0bf7-238">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-238">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="c0bf7-239">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-239">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="c0bf7-240">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-240">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="c0bf7-241">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-241">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="c0bf7-242">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-242">Copy the installer to the server.</span></span> <span data-ttu-id="c0bf7-243">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-243">Run the installer.</span></span> <span data-ttu-id="c0bf7-244">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-244">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="c0bf7-245">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-245">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="c0bf7-246">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="c0bf7-246">Configure the website</span></span>

<span data-ttu-id="c0bf7-247">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-247">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="c0bf7-248">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-248">The folder contains:</span></span>

* <span data-ttu-id="c0bf7-249">Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-249">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="c0bf7-250">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-250">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="c0bf7-251">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="c0bf7-251">Host as an IIS sub-app</span></span>

<span data-ttu-id="c0bf7-252">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-252">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="c0bf7-253">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-253">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="c0bf7-254">Usuń program obsługi w :::no-loc(Blazor)::: opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-254">Remove the handler in the :::no-loc(Blazor)::: app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="c0bf7-255">Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :</span><span class="sxs-lookup"><span data-stu-id="c0bf7-255">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="c0bf7-256">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-256">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="c0bf7-257">Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-257">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="c0bf7-258">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="c0bf7-258">Brotli and Gzip compression</span></span>

<span data-ttu-id="c0bf7-259">Usługi IIS można skonfigurować `web.config` w taki sposób, aby obsługiwały zasoby Brotli lub Gzip skompresowane :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-259">IIS can be configured via `web.config` to serve Brotli or Gzip compressed :::no-loc(Blazor)::: assets.</span></span> <span data-ttu-id="c0bf7-260">Aby zapoznać się z przykładową konfiguracją, zobacz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-260">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="c0bf7-261">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="c0bf7-261">Troubleshooting</span></span>

<span data-ttu-id="c0bf7-262">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-262">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="c0bf7-263">Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-263">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="c0bf7-264">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie :::no-loc(Blazor)::: plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-264">This prevents the IIS Manager from loading the website's configuration and the website from serving :::no-loc(Blazor):::'s static files.</span></span>

<span data-ttu-id="c0bf7-265">Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-265">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="c0bf7-266">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="c0bf7-266">Azure Storage</span></span>

<span data-ttu-id="c0bf7-267">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia :::no-loc(Blazor)::: hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-267">[Azure Storage](/azure/storage/) static file hosting allows serverless :::no-loc(Blazor)::: app hosting.</span></span> <span data-ttu-id="c0bf7-268">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-268">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="c0bf7-269">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-269">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="c0bf7-270">Ustaw **nazwę dokumentu indeksu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-270">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="c0bf7-271">Ustaw **ścieżkę do dokumentu błędu** `index.html` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-271">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="c0bf7-272">:::no-loc(Razor)::: składniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-272">:::no-loc(Razor)::: components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="c0bf7-273">Po otrzymaniu żądania dla jednego z tych zasobów, który :::no-loc(Blazor)::: powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-273">When a request for one of these resources is received that the :::no-loc(Blazor)::: router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path** .</span></span> <span data-ttu-id="c0bf7-274">`index.html`Obiekt BLOB jest zwracany, a :::no-loc(Blazor)::: router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-274">The `index.html` blob is returned, and the :::no-loc(Blazor)::: router loads and processes the path.</span></span>

<span data-ttu-id="c0bf7-275">Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-275">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="c0bf7-276">Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-276">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="c0bf7-277">Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-277">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="c0bf7-278">W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-278">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="c0bf7-279">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-279">Right-click the file and select **Properties** .</span></span>
  1. <span data-ttu-id="c0bf7-280">Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-280">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="c0bf7-281">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-281">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="c0bf7-282">Nginx</span><span class="sxs-lookup"><span data-stu-id="c0bf7-282">Nginx</span></span>

<span data-ttu-id="c0bf7-283">Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-283">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="c0bf7-284">W przypadku ustawienia [limitu szybkości serii Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) w [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) programie :::no-loc(Blazor WebAssembly)::: aplikacje mogą wymagać dużej `burst` wartości parametru, aby pomieścić stosunkowo dużą liczbę żądań wykonywanych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-284">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), :::no-loc(Blazor WebAssembly)::: apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="c0bf7-285">Początkowo ustaw wartość na co najmniej 60:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-285">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="c0bf7-286">Zwiększ wartość, jeśli narzędzia deweloperskie przeglądarki lub ruch sieciowy wskazuje, że żądania odbierają kod stanu *niedostępny dla usługi 503* .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-286">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="c0bf7-287">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-287">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="c0bf7-288">Nginx w Docker</span><span class="sxs-lookup"><span data-stu-id="c0bf7-288">Nginx in Docker</span></span>

<span data-ttu-id="c0bf7-289">Aby hostować :::no-loc(Blazor)::: w Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-289">To host :::no-loc(Blazor)::: in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="c0bf7-290">Zaktualizuj pliku dockerfile, aby skopiować `nginx.config` plik do kontenera.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-290">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="c0bf7-291">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-291">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="c0bf7-292">Apache</span><span class="sxs-lookup"><span data-stu-id="c0bf7-292">Apache</span></span>

<span data-ttu-id="c0bf7-293">Aby wdrożyć :::no-loc(Blazor WebAssembly)::: aplikację w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-293">To deploy a :::no-loc(Blazor WebAssembly)::: app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="c0bf7-294">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-294">Create the Apache configuration file.</span></span> <span data-ttu-id="c0bf7-295">Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-295">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="c0bf7-296">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-296">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="c0bf7-297">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-297">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="c0bf7-298">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-298">Restart the Apache service.</span></span>

<span data-ttu-id="c0bf7-299">Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-299">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="c0bf7-300">Usługa GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="c0bf7-300">GitHub Pages</span></span>

<span data-ttu-id="c0bf7-301">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `wwwroot/404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-301">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="c0bf7-302">Aby zapoznać się z przykładem, zobacz [ :::no-loc(Blazor)::: repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="c0bf7-302">For an example, see the [SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="c0bf7-303">[Aktywna witryna](https://stevesandersonms.github.io/:::no-loc(Blazor):::OnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="c0bf7-303">[Live site](https://stevesandersonms.github.io/:::no-loc(Blazor):::OnGitHubPages/))</span></span>

<span data-ttu-id="c0bf7-304">W przypadku korzystania z witryny projektu zamiast witryny organizacji zaktualizuj `<base>` tag w temacie `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-304">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="c0bf7-305">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-305">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="c0bf7-306">W [ :::no-loc(Blazor)::: repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages)baza `href` jest aktualizowana przy publikowaniu przez [ `.github/workflows/main.yml` plik konfiguracji](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-306">In the [SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="c0bf7-307">[ :::no-loc(Blazor)::: Repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages) nie należy do ani nie jest obsługiwane przez platformę .NET Foundation ani firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-307">The [SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/:::no-loc(Blazor):::OnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c0bf7-308">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="c0bf7-308">Host configuration values</span></span>

<span data-ttu-id="c0bf7-309">[ :::no-loc(Blazor WebAssembly)::: aplikacje](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-309">[:::no-loc(Blazor WebAssembly)::: apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="c0bf7-310">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="c0bf7-310">Content root</span></span>

<span data-ttu-id="c0bf7-311">`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-311">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="c0bf7-312">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-312">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="c0bf7-313">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-313">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c0bf7-314">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-314">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="c0bf7-315">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-315">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c0bf7-316">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-316">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="c0bf7-317">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-317">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments** .</span></span> <span data-ttu-id="c0bf7-318">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-318">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="c0bf7-319">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="c0bf7-319">Path base</span></span>

<span data-ttu-id="c0bf7-320">`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-320">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="c0bf7-321">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-321">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="c0bf7-322">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-322">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c0bf7-323">W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-323">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="c0bf7-324">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-324">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="c0bf7-325">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-325">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c0bf7-326">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-326">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="c0bf7-327">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-327">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c0bf7-328">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-328">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="c0bf7-329">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-329">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments** .</span></span> <span data-ttu-id="c0bf7-330">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-330">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="c0bf7-331">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="c0bf7-331">URLs</span></span>

<span data-ttu-id="c0bf7-332">`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-332">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="c0bf7-333">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c0bf7-334">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="c0bf7-335">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c0bf7-336">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="c0bf7-337">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments** .</span></span> <span data-ttu-id="c0bf7-338">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="c0bf7-339">Konfigurowanie elementu przycinającego</span><span class="sxs-lookup"><span data-stu-id="c0bf7-339">Configure the Trimmer</span></span>

<span data-ttu-id="c0bf7-340">:::no-loc(Blazor)::: wykonuje przycinanie języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-340">:::no-loc(Blazor)::: performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c0bf7-341">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-341">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="c0bf7-342">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="c0bf7-342">Configure the Linker</span></span>

<span data-ttu-id="c0bf7-343">:::no-loc(Blazor)::: wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-343">:::no-loc(Blazor)::: performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c0bf7-344">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-344">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="c0bf7-345">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="c0bf7-345">Custom boot resource loading</span></span>

<span data-ttu-id="c0bf7-346">:::no-loc(Blazor WebAssembly):::Aplikacja może zostać zainicjowana przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-346">A :::no-loc(Blazor WebAssembly)::: app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="c0bf7-347">Należy używać `loadBootResource` w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-347">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="c0bf7-348">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-348">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="c0bf7-349">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-349">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="c0bf7-350">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-350">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="c0bf7-351">`loadBootResource` Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-351">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="c0bf7-352">Parametr</span><span class="sxs-lookup"><span data-stu-id="c0bf7-352">Parameter</span></span>    | <span data-ttu-id="c0bf7-353">Opis</span><span class="sxs-lookup"><span data-stu-id="c0bf7-353">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="c0bf7-354">Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-354">The type of the resource.</span></span> <span data-ttu-id="c0bf7-355">Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="c0bf7-355">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="c0bf7-356">Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-356">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="c0bf7-357">Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-357">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="c0bf7-358">Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-358">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="c0bf7-359">`loadBootResource` zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-359">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="c0bf7-360">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-360">URI string.</span></span> <span data-ttu-id="c0bf7-361">W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="c0bf7-361">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="c0bf7-362">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="c0bf7-362">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    :::no-loc(Blazor):::.start({
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

* <span data-ttu-id="c0bf7-363">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-363">`Promise<Response>`.</span></span> <span data-ttu-id="c0bf7-364">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-364">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="c0bf7-365">Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-365">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    :::no-loc(Blazor):::.start({
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

* <span data-ttu-id="c0bf7-366">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-366">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="c0bf7-367">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-367">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="c0bf7-368">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-368">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="c0bf7-369">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-369">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="c0bf7-370">Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-370">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="c0bf7-371">Zmień rozszerzenie nazwy pliku DLL</span><span class="sxs-lookup"><span data-stu-id="c0bf7-371">Change the filename extension of DLL files</span></span>

<span data-ttu-id="c0bf7-372">Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-372">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="c0bf7-373">Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-373">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="c0bf7-374">Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-374">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="c0bf7-375">W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-375">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="c0bf7-376">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-376">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="c0bf7-377">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-377">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="c0bf7-378">W systemie Linux lub macOS:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-378">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="c0bf7-379">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-379">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="c0bf7-380">Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-380">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="c0bf7-381">Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-381">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="c0bf7-382">Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-382">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="c0bf7-383">Kompresja jest wyłączona w tym podejściu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-383">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="c0bf7-384">Rekompresuj zaktualizowany `blazor.boot.json` plik.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-384">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="c0bf7-385">Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-385">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="c0bf7-386">Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-386">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="c0bf7-387">W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-387">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="c0bf7-388">Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-388">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="c0bf7-389">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-389">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="c0bf7-390">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-390">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="c0bf7-391">W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-391">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="c0bf7-392">Podczas zmiany nazwy i opóźnionego ładowania tych samych zestawów zobacz wskazówki w temacie <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-392">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="c0bf7-393">Rozwiązywanie błędów sprawdzania integralności</span><span class="sxs-lookup"><span data-stu-id="c0bf7-393">Resolve integrity check failures</span></span>

<span data-ttu-id="c0bf7-394">Po :::no-loc(Blazor WebAssembly)::: pobraniu plików startowych aplikacji nakazuje ona przeprowadzenie kontroli integralności odpowiedzi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-394">When :::no-loc(Blazor WebAssembly)::: downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="c0bf7-395">Używa informacji w pliku, `blazor.boot.json` Aby określić oczekiwane wartości skrótu SHA-256 dla `.dll` , `.wasm` i innych plików.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-395">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="c0bf7-396">Jest to korzystne z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-396">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="c0bf7-397">Gwarantuje to, że nie ma ryzyka ładowania niespójnego zestawu plików, na przykład jeśli nowe wdrożenie jest stosowane do serwera sieci Web, podczas gdy użytkownik jest w trakcie procesu pobierania plików aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-397">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="c0bf7-398">Niespójne pliki mogą prowadzić do niezdefiniowanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-398">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="c0bf7-399">Gwarantuje to, że przeglądarka użytkownika nigdy nie buforuje niespójnych lub nieprawidłowych odpowiedzi, co może uniemożliwić ich uruchomienie aplikacji nawet w przypadku ręcznego odświeżenia strony.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-399">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="c0bf7-400">Pozwala ona bezpiecznie na buforowanie odpowiedzi i nie sprawdza, czy zmiany po stronie serwera są zmieniane, dopóki nie zmienią się oczekiwanych skrótów SHA-256, więc kolejne obciążenia strony obejmują mniejszą liczbę żądań i są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-400">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="c0bf7-401">Jeśli serwer sieci Web zwróci odpowiedzi, które nie pasują do oczekiwanych skrótów SHA-256, zobaczysz błąd podobny do poniższego, pojawia się w konsoli dewelopera przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-401">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/My:::no-loc(Blazor):::App.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="c0bf7-402">W większości przypadków *nie* jest to problem ze sprawdzaniem integralności.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-402">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="c0bf7-403">Zamiast tego oznacza to, że występuje jakiś inny problem, a Kontrola integralności ostrzega o tym, że ten problem wystąpi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-403">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="c0bf7-404">Diagnozowanie problemów z integralnością</span><span class="sxs-lookup"><span data-stu-id="c0bf7-404">Diagnosing integrity problems</span></span>

<span data-ttu-id="c0bf7-405">Po skompilowaniu aplikacji wygenerowany `blazor.boot.json` manifest opisuje skróty SHA-256 zasobów rozruchowych (na przykład, `.dll` `.wasm` i innych plików) w momencie wygenerowania danych wyjściowych kompilacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-405">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="c0bf7-406">Sprawdzanie integralności przebiega tak długo, jak skróty SHA-256 `blazor.boot.json` są zgodne z plikami dostarczonymi do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-406">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="c0bf7-407">Najczęstsze przyczyny tego niepowodzenia to:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-407">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="c0bf7-408">Odpowiedź serwera sieci Web jest błędem (na przykład *404 — nie można znaleźć* lub *500 — wewnętrzny błąd serwera* ) zamiast pliku, którego żądała przeglądarka.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-408">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error* ) instead of the file the browser requested.</span></span> <span data-ttu-id="c0bf7-409">Jest on raportowany przez przeglądarkę jako błąd sprawdzania integralności, a nie jako błąd odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-409">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="c0bf7-410">Coś zmieniło zawartość plików między kompilacją a dostarczeniem plików do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-410">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="c0bf7-411">Może się tak zdarzyć:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-411">This might happen:</span></span>
   * <span data-ttu-id="c0bf7-412">Jeśli narzędzia kompilacji lub kompilacje ręcznie modyfikują dane wyjściowe kompilacji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-412">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="c0bf7-413">Jeśli jakiś aspekt procesu wdrażania zmodyfikował pliki.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-413">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="c0bf7-414">Na przykład jeśli korzystasz z mechanizmu wdrożenia opartego na usłudze git, weź pod uwagę, że git w sposób przezroczysty konwertuje końce wierszy w stylu systemu Windows do końca wiersza w stylu Unix, jeśli zatwierdzisz pliki w systemie Windows i wyewidencjonujesz je w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-414">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="c0bf7-415">Zmiana końców wierszy plików zmienia wartości skrótów SHA-256.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-415">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="c0bf7-416">Aby uniknąć tego problemu, rozważ [użycie `.gitattributes` programu do traktowania artefaktów kompilacji jako `binary` plików](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-416">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="c0bf7-417">Serwer sieci Web modyfikuje zawartość pliku w ramach ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-417">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="c0bf7-418">Na przykład niektóre sieci dystrybucji zawartości (sieci CDN) automatycznie próbują [zminifikować](xref:client-side/bundling-and-minification#minification) kod HTML, modyfikując go.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-418">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="c0bf7-419">Może być konieczne wyłączenie takich funkcji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-419">You may need to disable such features.</span></span>

<span data-ttu-id="c0bf7-420">Aby zdiagnozować, które z nich mają zastosowanie w Twoim przypadku:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-420">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="c0bf7-421">Zwróć uwagę, który plik wyzwala błąd, odczytując komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-421">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="c0bf7-422">Otwórz narzędzia deweloperskie w przeglądarce i sprawdź kartę *Sieć* . W razie potrzeby Załaduj ponownie stronę, aby zobaczyć listę żądań i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-422">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="c0bf7-423">Znajdź plik wyzwalający błąd na tej liście.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-423">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="c0bf7-424">Sprawdź kod stanu HTTP w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-424">Check the HTTP status code in the response.</span></span> <span data-ttu-id="c0bf7-425">Jeśli serwer zwróci coś innego niż *200-OK* (lub inny kod stanu 2xx), wystąpił problem po stronie serwera do zdiagnozowania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-425">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="c0bf7-426">Na przykład kod stanu 403 oznacza problem z autoryzacją, natomiast kod stanu 500 oznacza, że serwer kończy się nieokreślonym sposobem.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-426">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="c0bf7-427">Zapoznaj się z dziennikami po stronie serwera, aby zdiagnozować i naprawić aplikację.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-427">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="c0bf7-428">Jeśli kod stanu to *200-OK* dla zasobu, zapoznaj się z zawartością odpowiedzi w narzędziach deweloperskich przeglądarki i sprawdź, czy zawartość jest zgodna z oczekiwanymi danymi.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-428">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="c0bf7-429">Typowym problemem jest na przykład Nieskonfigurowanie routingu w taki sposób, aby żądania zwracały `index.html` dane nawet dla innych plików.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-429">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="c0bf7-430">Upewnij się, że odpowiedzi na `.wasm` żądania to pliki binarne webassembly, a odpowiedzi na `.dll` żądania to pliki binarne zestawu platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-430">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="c0bf7-431">W przeciwnym razie masz problem z Routing po stronie serwera do zdiagnozowania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-431">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="c0bf7-432">Jeśli potwierdzasz, że serwer zwraca poprawne dane plausibly, konieczne może być zmodyfikowanie zawartości między kompilacją i dostarczeniem pliku.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-432">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="c0bf7-433">Aby zbadać to:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-433">To investigate this:</span></span>

 * <span data-ttu-id="c0bf7-434">Zapoznaj się z mechanizmem kompilowania łańcucha narzędzi i wdrażania na wypadek modyfikacji plików po skompilowaniu plików.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-434">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="c0bf7-435">Przykładem takiej sytuacji jest to, że program git przekształca końce wierszy plików zgodnie z wcześniejszym opisem.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-435">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="c0bf7-436">Zapoznaj się z konfiguracją serwer sieci Web lub sieć CDN w przypadku, gdy są skonfigurowane do dynamicznego modyfikowania odpowiedzi (na przykład próba zminifikować HTML).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-436">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="c0bf7-437">Serwer sieci Web może zaimplementować kompresję HTTP (na przykład zwracając `content-encoding: br` lub `content-encoding: gzip` ), ponieważ nie ma to wpływu na wynik po dekompresji.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-437">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="c0bf7-438">*Nie* jest jednak konieczne, aby serwer sieci Web modyfikował nieskompresowane dane.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-438">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="c0bf7-439">Wyłącz sprawdzanie integralności dla aplikacji innych niż PWA</span><span class="sxs-lookup"><span data-stu-id="c0bf7-439">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="c0bf7-440">W większości przypadków nie należy wyłączać sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-440">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="c0bf7-441">Wyłączenie sprawdzania integralności nie rozwiązuje podstawowego problemu, który spowodował nieoczekiwane odpowiedzi i spowoduje utratę wymienionych wcześniej korzyści.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-441">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="c0bf7-442">Mogą jednak wystąpić sytuacje, w których serwer sieci Web nie może być oparty na zwracaniu spójnych odpowiedzi i nie ma możliwości wyboru, ale w celu wyłączenia kontroli integralności.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-442">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="c0bf7-443">Aby wyłączyć sprawdzanie integralności, Dodaj następujący element do grupy właściwości w :::no-loc(Blazor WebAssembly)::: `.csproj` pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-443">To disable integrity checks, add the following to a property group in the :::no-loc(Blazor WebAssembly)::: project's `.csproj` file:</span></span>

```xml
<:::no-loc(Blazor):::CacheBootResources>false</:::no-loc(Blazor):::CacheBootResources>
```

<span data-ttu-id="c0bf7-444">`:::no-loc(Blazor):::CacheBootResources` wyłącza również :::no-loc(Blazor)::: domyślne zachowanie buforowania `.dll` , `.wasm` i innych plików na podstawie skrótów SHA-256, ponieważ właściwość wskazuje, że nie można opierać się na wartości skrótów SHA-256 w celu poprawnego działania.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-444">`:::no-loc(Blazor):::CacheBootResources` also disables :::no-loc(Blazor):::'s default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="c0bf7-445">Nawet w przypadku tego ustawienia Normalna pamięć podręczna w przeglądarce może nadal buforować te pliki, ale bez względu na to, czy ma to miejsce, zależy od konfiguracji serwera sieci Web i `cache-control` nagłówków, które obsługuje.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-445">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="c0bf7-446">`:::no-loc(Blazor):::CacheBootResources`Właściwość nie wyłącza sprawdzania integralności w przypadku [progresywnych aplikacji sieci Web (PWAs)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="c0bf7-446">The `:::no-loc(Blazor):::CacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="c0bf7-447">Wskazówki dotyczące PWAs można znaleźć w sekcji [Wyłączanie sprawdzania integralności dla PWAs](#disable-integrity-checking-for-pwas) .</span><span class="sxs-lookup"><span data-stu-id="c0bf7-447">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="c0bf7-448">Wyłącz sprawdzanie integralności dla PWAs</span><span class="sxs-lookup"><span data-stu-id="c0bf7-448">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="c0bf7-449">:::no-loc(Blazor):::szablon aplikacji sieci Web progresywnej (PWA) zawiera sugerowany `service-worker.published.js` plik, który jest odpowiedzialny za pobieranie i przechowywanie plików aplikacji do użytku w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-449">:::no-loc(Blazor):::'s Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="c0bf7-450">Jest to oddzielny proces od normalnego mechanizmu uruchamiania aplikacji i ma własną logikę sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-450">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="c0bf7-451">W `service-worker.published.js` pliku znajduje się następujący wiersz:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-451">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="c0bf7-452">Aby wyłączyć sprawdzanie integralności, Usuń `integrity` parametr, zmieniając wiersz na następujący:</span><span class="sxs-lookup"><span data-stu-id="c0bf7-452">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="c0bf7-453">Po ponownym uruchomieniu kontroli integralności oznacza to, że utracisz gwarancje bezpieczeństwa oferowane przez sprawdzanie integralności.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-453">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="c0bf7-454">Na przykład istnieje ryzyko, że jeśli przeglądarka użytkownika będzie buforować aplikację w tym samym czasie, gdy zostanie wdrożona nowa wersja, może to spowodować przełączenie niektórych plików ze starego wdrożenia, a niektóre z nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-454">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="c0bf7-455">W takim przypadku aplikacja zostanie zablokowana w stanie przerwania, dopóki nie zostanie wdrożona dodatkowa aktualizacja.</span><span class="sxs-lookup"><span data-stu-id="c0bf7-455">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
