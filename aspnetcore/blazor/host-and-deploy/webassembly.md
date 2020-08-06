---
title: Hostowanie i wdrażanie ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 9d596e38a1d8350cd4a27f2fec4b262a0edf1015
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818849"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="5053a-103">Hostowanie i wdrażanie ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5053a-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="5053a-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="5053a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="5053a-105">Z [ Blazor WebAssembly modelem hostingu](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="5053a-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="5053a-106">BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5053a-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="5053a-107">Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="5053a-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="5053a-108">Obsługiwane są następujące strategie wdrażania:</span><span class="sxs-lookup"><span data-stu-id="5053a-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="5053a-109">BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5053a-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="5053a-110">Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.</span><span class="sxs-lookup"><span data-stu-id="5053a-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="5053a-111">BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="5053a-112">Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor WebAssembly aplikacji jako PODRZĘDNEJ aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5053a-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="5053a-113">Kompresja</span><span class="sxs-lookup"><span data-stu-id="5053a-113">Compression</span></span>

<span data-ttu-id="5053a-114">Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="5053a-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="5053a-115">Używane są następujące algorytmy kompresji:</span><span class="sxs-lookup"><span data-stu-id="5053a-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="5053a-116">[Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)</span><span class="sxs-lookup"><span data-stu-id="5053a-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="5053a-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="5053a-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="5053a-118">Blazorkorzysta z hosta, aby zapewnić odpowiednie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="5053a-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="5053a-119">W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki.</span><span class="sxs-lookup"><span data-stu-id="5053a-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="5053a-120">W przypadku udostępniania Blazor WebAssembly aplikacji autonomicznej może być wymagane dodatkowe działanie, aby zapewnić obsługę plików skompresowanych statycznie:</span><span class="sxs-lookup"><span data-stu-id="5053a-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="5053a-121">Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="5053a-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="5053a-122">Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:</span><span class="sxs-lookup"><span data-stu-id="5053a-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="5053a-123">Uzyskaj dekoder JavaScript Brotli z repozytorium usługi [GitHub firmy Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="5053a-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="5053a-124">Od lipca 2020 plik dekodera ma nazwę `decode.min.js` i znajduje się w [ `js` folderze](https://github.com/google/brotli/tree/master/js)repozytorium.</span><span class="sxs-lookup"><span data-stu-id="5053a-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="5053a-125">Zaktualizuj aplikację, aby użyć dekodera.</span><span class="sxs-lookup"><span data-stu-id="5053a-125">Update the app to use the decoder.</span></span> <span data-ttu-id="5053a-126">Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5053a-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
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
 
<span data-ttu-id="5053a-127">Aby wyłączyć kompresję, należy dodać `BlazorEnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :</span><span class="sxs-lookup"><span data-stu-id="5053a-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="5053a-128">Ponownie Napisz adresy URL pod kątem prawidłowego routingu</span><span class="sxs-lookup"><span data-stu-id="5053a-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="5053a-129">Żądania routingu dla składników strony w Blazor WebAssembly aplikacji nie są tak proste jak żądania routingu w Blazor Server hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="5053a-130">Weź pod uwagę Blazor WebAssembly aplikację z dwoma składnikami:</span><span class="sxs-lookup"><span data-stu-id="5053a-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="5053a-131">`Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="5053a-132">`About.razor`: `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="5053a-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="5053a-133">Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="5053a-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="5053a-134">Przeglądarka wykonuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="5053a-134">The browser makes a request.</span></span>
1. <span data-ttu-id="5053a-135">Zostanie zwrócona domyślna strona, która zwykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="5053a-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="5053a-136">`index.html`wyładowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="5053a-137">Blazorładuje router, a Razor `Main` składnik jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="5053a-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="5053a-138">Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik.</span><span class="sxs-lookup"><span data-stu-id="5053a-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="5053a-139">Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor WebAssembly aplikacji* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie.</span><span class="sxs-lookup"><span data-stu-id="5053a-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="5053a-140">Router obsługuje wewnętrznie żądania.</span><span class="sxs-lookup"><span data-stu-id="5053a-140">The router handles the requests internally.</span></span>

<span data-ttu-id="5053a-141">Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` .</span><span class="sxs-lookup"><span data-stu-id="5053a-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="5053a-142">Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .</span><span class="sxs-lookup"><span data-stu-id="5053a-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="5053a-143">Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie.</span><span class="sxs-lookup"><span data-stu-id="5053a-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="5053a-144">Gdy `index.html` jest zwracany, Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.</span><span class="sxs-lookup"><span data-stu-id="5053a-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="5053a-145">Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="5053a-146">Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="5053a-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="5053a-147">Hostowane wdrożenie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5053a-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="5053a-148">*Wdrożenie hostowane* Blazor WebAssembly umożliwia aplikacji przeglądarki z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5053a-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="5053a-149">Aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="5053a-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="5053a-150">Te dwie aplikacje są wdrażane razem.</span><span class="sxs-lookup"><span data-stu-id="5053a-150">The two apps are deployed together.</span></span> <span data-ttu-id="5053a-151">Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5053a-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="5053a-152">W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu \*\* Blazor WebAssembly aplikacji\*\* ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).</span><span class="sxs-lookup"><span data-stu-id="5053a-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="5053a-153">Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="5053a-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="5053a-154">Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="5053a-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="5053a-155">Hostowane wdrożenie z wieloma Blazor WebAssembly aplikacjami</span><span class="sxs-lookup"><span data-stu-id="5053a-155">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="5053a-156">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="5053a-156">App configuration</span></span>

<span data-ttu-id="5053a-157">Aby skonfigurować rozwiązanie hostowane Blazor do obsługi wielu Blazor WebAssembly aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5053a-157">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="5053a-158">Użyj istniejącego rozwiązania hostowanego Blazor lub Utwórz nowe rozwiązanie na podstawie Blazor szablonu hostowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="5053a-158">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="5053a-159">W pliku projektu aplikacji klienckiej Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości, `FirstApp` Aby ustawić ścieżkę bazową dla statycznych zasobów projektu:</span><span class="sxs-lookup"><span data-stu-id="5053a-159">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="5053a-160">Dodaj drugą aplikację kliencką do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="5053a-160">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="5053a-161">Dodaj folder o nazwie `SecondClient` do folderu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5053a-161">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="5053a-162">Utwórz Blazor WebAssembly aplikację o nazwie `SecondBlazorApp.Client` w `SecondClient` folderze z Blazor WebAssembly szablonu projektu.</span><span class="sxs-lookup"><span data-stu-id="5053a-162">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="5053a-163">W pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5053a-163">In the app's project file:</span></span>

    * <span data-ttu-id="5053a-164">Dodaj `<StaticWebAssetBasePath>` Właściwość do `<PropertyGroup>` wartości z `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="5053a-164">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="5053a-165">Dodaj odwołanie do projektu do `Shared` projektu:</span><span class="sxs-lookup"><span data-stu-id="5053a-165">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="5053a-166">Symbol zastępczy `{SOLUTION NAME}` jest nazwą rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5053a-166">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="5053a-167">W pliku projektu aplikacji serwera Utwórz odwołanie do projektu dla dodanej aplikacji klienckiej:</span><span class="sxs-lookup"><span data-stu-id="5053a-167">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="5053a-168">W pliku aplikacji serwera `Properties/launchSettings.json` Skonfiguruj `applicationUrl` profil Kestrel ( `{SOLUTION NAME}.Server` ), aby uzyskać dostęp do aplikacji klienckich na portach 5001 i 5002:</span><span class="sxs-lookup"><span data-stu-id="5053a-168">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="5053a-169">W metodzie aplikacji serwera `Startup.Configure` ( `Startup.cs` ) Usuń następujące wiersze, które pojawią się po wywołaniu <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="5053a-169">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="5053a-170">Dodaj oprogramowanie pośredniczące, które mapuje żądania do aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="5053a-170">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="5053a-171">Poniższy przykład konfiguruje oprogramowanie pośredniczące do uruchomienia, gdy:</span><span class="sxs-lookup"><span data-stu-id="5053a-171">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="5053a-172">Port żądania ma wartość 5001 dla oryginalnej aplikacji klienckiej lub 5002 dla dodanej aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="5053a-172">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="5053a-173">Host żądania jest `firstapp.com` przeznaczony dla oryginalnej aplikacji klienckiej lub `secondapp.com` dla dodanej aplikacji klienckiej.</span><span class="sxs-lookup"><span data-stu-id="5053a-173">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="5053a-174">Przykład przedstawiony w tej sekcji wymaga dodatkowej konfiguracji dla:</span><span class="sxs-lookup"><span data-stu-id="5053a-174">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="5053a-175">Uzyskiwanie dostępu do aplikacji w przykładowych domenach hostów `firstapp.com` i `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="5053a-175">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="5053a-176">Certyfikaty dla aplikacji klienckich, aby włączyć zabezpieczenia protokołu TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5053a-176">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="5053a-177">Wymagana konfiguracja wykracza poza zakres tego artykułu i zależy od tego, w jaki sposób jest hostowane rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="5053a-177">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="5053a-178">Aby uzyskać więcej informacji, zobacz artykuł dotyczący [hosta i wdrażania](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="5053a-178">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="5053a-179">Umieść następujący kod, w którym wiersze zostały wcześniej usunięte:</span><span class="sxs-lookup"><span data-stu-id="5053a-179">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="5053a-180">W kontrolerze prognozowania pogody aplikacji serwera `Controllers/WeatherForecastController.cs` Zastąp istniejącą trasę ( `[Route("[controller]")]` ) do `WeatherForecastController` następującej trasy:</span><span class="sxs-lookup"><span data-stu-id="5053a-180">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="5053a-181">Oprogramowanie pośredniczące dodane do metody aplikacji serwerowej `Startup.Configure` wcześniej modyfikuje przychodzące żądania do `/WeatherForecast` albo `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` w zależności od portu (5001/5002) lub domeny ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-181">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="5053a-182">Powyższe trasy kontrolera są wymagane w celu zwrócenia danych pogody z aplikacji serwerowej do aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="5053a-182">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="5053a-183">Statyczne zasoby i biblioteki klas</span><span class="sxs-lookup"><span data-stu-id="5053a-183">Static assets and class libraries</span></span>

<span data-ttu-id="5053a-184">W przypadku zasobów statycznych należy stosować następujące podejścia:</span><span class="sxs-lookup"><span data-stu-id="5053a-184">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="5053a-185">Gdy element zawartości znajduje się w folderze aplikacji klienta `wwwroot` , podaj ścieżki zwykle:</span><span class="sxs-lookup"><span data-stu-id="5053a-185">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="5053a-186">Gdy element zawartości znajduje się w `wwwroot` folderze [ Razor biblioteki klas (RCL)](xref:blazor/components/class-libraries), odwołuje się do zasobu statycznego w aplikacji klienckiej zgodnie ze wskazówkami w [artykule RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="5053a-186">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5053a-187">Składniki udostępniane aplikacji klienckiej przez bibliotekę klas są zwykle przywoływane.</span><span class="sxs-lookup"><span data-stu-id="5053a-187">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="5053a-188">Jeśli wszystkie składniki wymagają plików stylów lub JavaScript, użyj jednego z poniższych metod, aby uzyskać zasoby statyczne:</span><span class="sxs-lookup"><span data-stu-id="5053a-188">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="5053a-189">Plik aplikacji klienckiej `wwwroot/index.html` może łączyć ( `<link>` ) ze statycznymi zasobami.</span><span class="sxs-lookup"><span data-stu-id="5053a-189">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="5053a-190">Składnik może używać [ `Link` składnika](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) struktury do uzyskiwania zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="5053a-190">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="5053a-191">Powyższe podejścia przedstawiono w poniższych przykładach.</span><span class="sxs-lookup"><span data-stu-id="5053a-191">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5053a-192">Składniki udostępniane aplikacji klienckiej przez bibliotekę klas są zwykle przywoływane.</span><span class="sxs-lookup"><span data-stu-id="5053a-192">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="5053a-193">Jeśli wszystkie składniki wymagają plików stylów lub JavaScript, plik aplikacji klienta `wwwroot/index.html` musi zawierać poprawne linki do zasobów statycznych.</span><span class="sxs-lookup"><span data-stu-id="5053a-193">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="5053a-194">Te podejścia przedstawiono w poniższych przykładach.</span><span class="sxs-lookup"><span data-stu-id="5053a-194">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="5053a-195">Dodaj następujący `Jeep` składnik do jednej z aplikacji klienckich.</span><span class="sxs-lookup"><span data-stu-id="5053a-195">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="5053a-196">`Jeep`Składnik używa:</span><span class="sxs-lookup"><span data-stu-id="5053a-196">The `Jeep` component uses:</span></span>

* <span data-ttu-id="5053a-197">Obraz z folderu aplikacji klienta `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-197">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="5053a-198">Obraz z [dodanego folderu Razor biblioteki składników](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="5053a-198">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="5053a-199">Przykładowy składnik ( `Component1` ) jest tworzony automatycznie przez szablon projektu RCL, gdy `JeepImage` biblioteka zostanie dodana do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5053a-199">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="5053a-200">Nie Publikuj obrazów pojazdów publicznie, chyba że są **one** właścicielami.</span><span class="sxs-lookup"><span data-stu-id="5053a-200">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="5053a-201">W przeciwnym razie ryzyko naruszenia praw autorskich.</span><span class="sxs-lookup"><span data-stu-id="5053a-201">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5053a-202">`jeep-yj.png`Obraz biblioteki można również dodać do `Component1` składnika biblioteki ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-202">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="5053a-203">Aby zapewnić `my-component` klasę CSS stronie aplikacji klienta, Połącz się z arkuszem stylów biblioteki przy użyciu [ `Link` składnika](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)struktury:</span><span class="sxs-lookup"><span data-stu-id="5053a-203">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="5053a-204">Alternatywą dla korzystania ze [ `Link` składnika](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) jest załadowanie arkusza stylów z pliku aplikacji klienckiej `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="5053a-204">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="5053a-205">Takie podejście sprawia, że arkusz stylów jest dostępny dla wszystkich składników w aplikacji klienckiej:</span><span class="sxs-lookup"><span data-stu-id="5053a-205">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5053a-206">`jeep-yj.png`Obraz biblioteki można również dodać do `Component1` składnika biblioteki ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5053a-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="5053a-207">Plik aplikacji klienta `wwwroot/index.html` żąda arkusza stylów biblioteki z następującym dodany `<link>` tag:</span><span class="sxs-lookup"><span data-stu-id="5053a-207">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="5053a-208">Dodaj nawigację do `Jeep` składnika w składniku aplikacji klienckiej `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="5053a-208">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="5053a-209">Aby uzyskać więcej informacji na temat RCLs, zobacz:</span><span class="sxs-lookup"><span data-stu-id="5053a-209">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="5053a-210">Wdrożenie autonomiczne</span><span class="sxs-lookup"><span data-stu-id="5053a-210">Standalone deployment</span></span>

<span data-ttu-id="5053a-211">*Wdrożenie samodzielne* służy Blazor WebAssembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów.</span><span class="sxs-lookup"><span data-stu-id="5053a-211">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="5053a-212">Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="5053a-212">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="5053a-213">Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="5053a-213">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="5053a-214">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5053a-214">Azure App Service</span></span>

<span data-ttu-id="5053a-215">Blazor WebAssemblyAplikacje można wdrażać w usłudze Azure App Services w systemie Windows, który jest hostem aplikacji w [usługach IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="5053a-215">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="5053a-216">Wdrażanie autonomicznej Blazor WebAssembly aplikacji do Azure App Service dla systemu Linux nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="5053a-216">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="5053a-217">Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny.</span><span class="sxs-lookup"><span data-stu-id="5053a-217">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="5053a-218">Trwa wykonywanie pracy, aby włączyć ten scenariusz.</span><span class="sxs-lookup"><span data-stu-id="5053a-218">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="5053a-219">IIS</span><span class="sxs-lookup"><span data-stu-id="5053a-219">IIS</span></span>

<span data-ttu-id="5053a-220">Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-220">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="5053a-221">Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="5053a-221">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="5053a-222">Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze.</span><span class="sxs-lookup"><span data-stu-id="5053a-222">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="5053a-223">Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.</span><span class="sxs-lookup"><span data-stu-id="5053a-223">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="5053a-224">web.config</span><span class="sxs-lookup"><span data-stu-id="5053a-224">web.config</span></span>

<span data-ttu-id="5053a-225">Po Blazor opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:</span><span class="sxs-lookup"><span data-stu-id="5053a-225">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="5053a-226">Typy MIME są ustawiane dla następujących rozszerzeń plików:</span><span class="sxs-lookup"><span data-stu-id="5053a-226">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="5053a-227">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="5053a-227">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="5053a-228">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="5053a-228">`.json`: `application/json`</span></span>
  * <span data-ttu-id="5053a-229">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="5053a-229">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="5053a-230">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="5053a-230">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="5053a-231">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="5053a-231">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="5053a-232">Kompresja HTTP jest włączona dla następujących typów MIME:</span><span class="sxs-lookup"><span data-stu-id="5053a-232">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="5053a-233">Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:</span><span class="sxs-lookup"><span data-stu-id="5053a-233">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="5053a-234">Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-234">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="5053a-235">Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-235">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="5053a-236">Użyj niestandardowego web.config</span><span class="sxs-lookup"><span data-stu-id="5053a-236">Use a custom web.config</span></span>

<span data-ttu-id="5053a-237">Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu i Opublikuj projekt.</span><span class="sxs-lookup"><span data-stu-id="5053a-237">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="5053a-238">Zainstaluj moduł ponownego zapisywania adresów URL</span><span class="sxs-lookup"><span data-stu-id="5053a-238">Install the URL Rewrite Module</span></span>

<span data-ttu-id="5053a-239">[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="5053a-239">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="5053a-240">Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="5053a-240">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="5053a-241">Moduł musi zostać pobrany z witryny sieci Web usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5053a-241">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="5053a-242">Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:</span><span class="sxs-lookup"><span data-stu-id="5053a-242">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="5053a-243">Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="5053a-243">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="5053a-244">W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI.</span><span class="sxs-lookup"><span data-stu-id="5053a-244">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="5053a-245">W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.</span><span class="sxs-lookup"><span data-stu-id="5053a-245">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="5053a-246">Skopiuj Instalatora na serwer.</span><span class="sxs-lookup"><span data-stu-id="5053a-246">Copy the installer to the server.</span></span> <span data-ttu-id="5053a-247">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="5053a-247">Run the installer.</span></span> <span data-ttu-id="5053a-248">Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="5053a-248">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="5053a-249">Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.</span><span class="sxs-lookup"><span data-stu-id="5053a-249">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="5053a-250">Skonfiguruj witrynę sieci Web</span><span class="sxs-lookup"><span data-stu-id="5053a-250">Configure the website</span></span>

<span data-ttu-id="5053a-251">Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-251">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="5053a-252">Folder zawiera:</span><span class="sxs-lookup"><span data-stu-id="5053a-252">The folder contains:</span></span>

* <span data-ttu-id="5053a-253">Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="5053a-253">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="5053a-254">Folder elementu zawartości statycznej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-254">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="5053a-255">Host jako podrzędną aplikację usług IIS</span><span class="sxs-lookup"><span data-stu-id="5053a-255">Host as an IIS sub-app</span></span>

<span data-ttu-id="5053a-256">Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="5053a-256">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="5053a-257">Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.</span><span class="sxs-lookup"><span data-stu-id="5053a-257">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="5053a-258">Usuń program obsługi w Blazor opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:</span><span class="sxs-lookup"><span data-stu-id="5053a-258">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="5053a-259">Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :</span><span class="sxs-lookup"><span data-stu-id="5053a-259">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="5053a-260">Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="5053a-260">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="5053a-261">Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.</span><span class="sxs-lookup"><span data-stu-id="5053a-261">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="5053a-262">Brotli i Kompresja gzip</span><span class="sxs-lookup"><span data-stu-id="5053a-262">Brotli and Gzip compression</span></span>

<span data-ttu-id="5053a-263">Usługi IIS można skonfigurować `web.config` w taki sposób, aby obsługiwały zasoby Brotli lub Gzip skompresowane Blazor .</span><span class="sxs-lookup"><span data-stu-id="5053a-263">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="5053a-264">Aby zapoznać się z przykładową konfiguracją, zobacz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="5053a-264">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="5053a-265">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5053a-265">Troubleshooting</span></span>

<span data-ttu-id="5053a-266">W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL.</span><span class="sxs-lookup"><span data-stu-id="5053a-266">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="5053a-267">Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="5053a-267">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="5053a-268">Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="5053a-268">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="5053a-269">Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="5053a-269">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="5053a-270">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="5053a-270">Azure Storage</span></span>

<span data-ttu-id="5053a-271">Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="5053a-271">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="5053a-272">Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5053a-272">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="5053a-273">Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:</span><span class="sxs-lookup"><span data-stu-id="5053a-273">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="5053a-274">Ustaw **nazwę dokumentu indeksu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="5053a-274">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="5053a-275">Ustaw **ścieżkę do dokumentu błędu** `index.html` .</span><span class="sxs-lookup"><span data-stu-id="5053a-275">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="5053a-276">Razorskładniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB.</span><span class="sxs-lookup"><span data-stu-id="5053a-276">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="5053a-277">Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**.</span><span class="sxs-lookup"><span data-stu-id="5053a-277">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="5053a-278">`index.html`Obiekt BLOB jest zwracany, a Blazor router ładuje i przetwarza ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="5053a-278">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="5053a-279">Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="5053a-279">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="5053a-280">Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.</span><span class="sxs-lookup"><span data-stu-id="5053a-280">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="5053a-281">Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-281">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="5053a-282">W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:</span><span class="sxs-lookup"><span data-stu-id="5053a-282">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="5053a-283">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="5053a-283">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="5053a-284">Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="5053a-284">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="5053a-285">Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="5053a-285">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="5053a-286">Nginx</span><span class="sxs-lookup"><span data-stu-id="5053a-286">Nginx</span></span>

<span data-ttu-id="5053a-287">Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.</span><span class="sxs-lookup"><span data-stu-id="5053a-287">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="5053a-288">W przypadku ustawienia [limitu szybkości serii Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) w [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) programie Blazor WebAssembly aplikacje mogą wymagać dużej `burst` wartości parametru, aby pomieścić stosunkowo dużą liczbę żądań wykonywanych przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="5053a-288">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="5053a-289">Początkowo ustaw wartość na co najmniej 60:</span><span class="sxs-lookup"><span data-stu-id="5053a-289">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="5053a-290">Zwiększ wartość, jeśli narzędzia deweloperskie przeglądarki lub ruch sieciowy wskazuje, że żądania odbierają kod stanu *niedostępny dla usługi 503* .</span><span class="sxs-lookup"><span data-stu-id="5053a-290">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="5053a-291">Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="5053a-291">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="5053a-292">Nginx w Docker</span><span class="sxs-lookup"><span data-stu-id="5053a-292">Nginx in Docker</span></span>

<span data-ttu-id="5053a-293">Aby hostować Blazor w Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine.</span><span class="sxs-lookup"><span data-stu-id="5053a-293">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="5053a-294">Zaktualizuj pliku dockerfile, aby skopiować `nginx.config` plik do kontenera.</span><span class="sxs-lookup"><span data-stu-id="5053a-294">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="5053a-295">Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5053a-295">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="5053a-296">Apache</span><span class="sxs-lookup"><span data-stu-id="5053a-296">Apache</span></span>

<span data-ttu-id="5053a-297">Aby wdrożyć Blazor WebAssembly aplikację w programie CentOS 7 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="5053a-297">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="5053a-298">Utwórz plik konfiguracji Apache.</span><span class="sxs-lookup"><span data-stu-id="5053a-298">Create the Apache configuration file.</span></span> <span data-ttu-id="5053a-299">Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="5053a-299">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="5053a-300">Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="5053a-300">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="5053a-301">Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).</span><span class="sxs-lookup"><span data-stu-id="5053a-301">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="5053a-302">Uruchom ponownie usługę Apache.</span><span class="sxs-lookup"><span data-stu-id="5053a-302">Restart the Apache service.</span></span>

<span data-ttu-id="5053a-303">Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="5053a-303">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="5053a-304">Usługa GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="5053a-304">GitHub Pages</span></span>

<span data-ttu-id="5053a-305">Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `wwwroot/404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony.</span><span class="sxs-lookup"><span data-stu-id="5053a-305">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="5053a-306">Aby zapoznać się z przykładem, zobacz [ Blazor repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="5053a-306">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="5053a-307">[Aktywna witryna](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="5053a-307">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="5053a-308">W przypadku korzystania z witryny projektu zamiast witryny organizacji zaktualizuj `<base>` tag w temacie `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="5053a-308">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="5053a-309">Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-309">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="5053a-310">W [ Blazor repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)baza `href` jest aktualizowana przy publikowaniu przez [ `.github/workflows/main.yml` plik konfiguracji](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="5053a-310">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="5053a-311">[ Blazor Repozytorium GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) nie należy do ani nie jest obsługiwane przez platformę .NET Foundation ani firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5053a-311">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="5053a-312">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="5053a-312">Host configuration values</span></span>

<span data-ttu-id="5053a-313">[ Blazor WebAssembly aplikacje](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="5053a-313">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="5053a-314">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="5053a-314">Content root</span></span>

<span data-ttu-id="5053a-315">`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="5053a-315">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="5053a-316">W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-316">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="5053a-317">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="5053a-317">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="5053a-318">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5053a-318">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="5053a-319">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="5053a-319">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="5053a-320">To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="5053a-320">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="5053a-321">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="5053a-321">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="5053a-322">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="5053a-322">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="5053a-323">Baza ścieżki</span><span class="sxs-lookup"><span data-stu-id="5053a-323">Path base</span></span>

<span data-ttu-id="5053a-324">`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji).</span><span class="sxs-lookup"><span data-stu-id="5053a-324">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="5053a-325">W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5053a-325">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="5053a-326">Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="5053a-326">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5053a-327">W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu.</span><span class="sxs-lookup"><span data-stu-id="5053a-327">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="5053a-328">Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).</span><span class="sxs-lookup"><span data-stu-id="5053a-328">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="5053a-329">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="5053a-329">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="5053a-330">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5053a-330">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="5053a-331">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="5053a-331">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="5053a-332">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="5053a-332">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="5053a-333">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="5053a-333">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="5053a-334">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="5053a-334">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="5053a-335">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="5053a-335">URLs</span></span>

<span data-ttu-id="5053a-336">`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="5053a-336">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="5053a-337">Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="5053a-337">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="5053a-338">W katalogu aplikacji wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5053a-338">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="5053a-339">Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="5053a-339">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="5053a-340">To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="5053a-340">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="5053a-341">W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="5053a-341">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="5053a-342">Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.</span><span class="sxs-lookup"><span data-stu-id="5053a-342">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="5053a-343">Konfigurowanie konsolidatora</span><span class="sxs-lookup"><span data-stu-id="5053a-343">Configure the Linker</span></span>

<span data-ttu-id="5053a-344">Blazorwykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="5053a-344">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="5053a-345">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="5053a-345">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="5053a-346">Ładowanie niestandardowego zasobu rozruchowego</span><span class="sxs-lookup"><span data-stu-id="5053a-346">Custom boot resource loading</span></span>

<span data-ttu-id="5053a-347">Blazor WebAssemblyAplikacja może zostać zainicjowana przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego.</span><span class="sxs-lookup"><span data-stu-id="5053a-347">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="5053a-348">Należy używać `loadBootResource` w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="5053a-348">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="5053a-349">Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="5053a-349">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="5053a-350">Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.</span><span class="sxs-lookup"><span data-stu-id="5053a-350">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="5053a-351">Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.</span><span class="sxs-lookup"><span data-stu-id="5053a-351">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="5053a-352">`loadBootResource`Parametry znajdują się w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5053a-352">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="5053a-353">Parametr</span><span class="sxs-lookup"><span data-stu-id="5053a-353">Parameter</span></span>    | <span data-ttu-id="5053a-354">Opis</span><span class="sxs-lookup"><span data-stu-id="5053a-354">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="5053a-355">Typ zasobu.</span><span class="sxs-lookup"><span data-stu-id="5053a-355">The type of the resource.</span></span> <span data-ttu-id="5053a-356">Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="5053a-356">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="5053a-357">Nazwa zasobu.</span><span class="sxs-lookup"><span data-stu-id="5053a-357">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="5053a-358">Względny lub bezwzględny identyfikator URI zasobu.</span><span class="sxs-lookup"><span data-stu-id="5053a-358">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="5053a-359">Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="5053a-359">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="5053a-360">`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:</span><span class="sxs-lookup"><span data-stu-id="5053a-360">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="5053a-361">Ciąg identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="5053a-361">URI string.</span></span> <span data-ttu-id="5053a-362">W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="5053a-362">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="5053a-363">Dane strefy czasowej</span><span class="sxs-lookup"><span data-stu-id="5053a-363">Timezone data</span></span>

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

* <span data-ttu-id="5053a-364">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="5053a-364">`Promise<Response>`.</span></span> <span data-ttu-id="5053a-365">Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.</span><span class="sxs-lookup"><span data-stu-id="5053a-365">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="5053a-366">Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:</span><span class="sxs-lookup"><span data-stu-id="5053a-366">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="5053a-367">`null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.</span><span class="sxs-lookup"><span data-stu-id="5053a-367">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="5053a-368">Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami.</span><span class="sxs-lookup"><span data-stu-id="5053a-368">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="5053a-369">Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.</span><span class="sxs-lookup"><span data-stu-id="5053a-369">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="5053a-370">Wystarczy określić typy zachowań niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="5053a-370">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="5053a-371">Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.</span><span class="sxs-lookup"><span data-stu-id="5053a-371">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="5053a-372">Zmień rozszerzenie nazwy pliku DLL</span><span class="sxs-lookup"><span data-stu-id="5053a-372">Change the filename extension of DLL files</span></span>

<span data-ttu-id="5053a-373">Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5053a-373">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="5053a-374">Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="5053a-374">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="5053a-375">Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="5053a-375">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="5053a-376">W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="5053a-376">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="5053a-377">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="5053a-377">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="5053a-378">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5053a-378">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="5053a-379">W systemie Linux lub macOS:</span><span class="sxs-lookup"><span data-stu-id="5053a-379">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="5053a-380">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5053a-380">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="5053a-381">Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.</span><span class="sxs-lookup"><span data-stu-id="5053a-381">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="5053a-382">Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5053a-382">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="5053a-383">Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki.</span><span class="sxs-lookup"><span data-stu-id="5053a-383">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="5053a-384">Kompresja jest wyłączona w tym podejściu.</span><span class="sxs-lookup"><span data-stu-id="5053a-384">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="5053a-385">Rekompresuj zaktualizowany `blazor.boot.json` plik.</span><span class="sxs-lookup"><span data-stu-id="5053a-385">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="5053a-386">Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi.</span><span class="sxs-lookup"><span data-stu-id="5053a-386">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="5053a-387">Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="5053a-387">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="5053a-388">W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="5053a-388">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="5053a-389">Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="5053a-389">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="5053a-390">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="5053a-390">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="5053a-391">Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5053a-391">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="5053a-392">W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5053a-392">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="5053a-393">Aby przekazać opinię, odwiedź stronę [aspnetcore/problemy #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="5053a-393">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
