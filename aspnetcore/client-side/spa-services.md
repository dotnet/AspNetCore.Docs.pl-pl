---
title: Korzystanie z usług JavaScript do tworzenia aplikacji jednostronicowych w ASP.NET Core
author: scottaddie
description: Dowiedz się więcej na temat korzyści z używania usług JavaScript do tworzenia aplikacji jednostronicowej (SPA) obsługiwanej przez ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 65bd5157bb3909f8352debcb1a6dfa7d888eec0e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769926"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="c2105-103">Korzystanie z usług JavaScript do tworzenia aplikacji jednostronicowych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2105-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="c2105-104">Przez [Scott Addie](https://github.com/scottaddie) i [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="c2105-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="c2105-105">Aplikacja jednostronicowa (SPA) to popularny typ aplikacji sieci Web ze względu na jego zaawansowaną obsługę.</span><span class="sxs-lookup"><span data-stu-id="c2105-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="c2105-106">Integrowanie struktur lub bibliotek SPA po stronie klienta, takich jak [kątowy](https://angular.io/) lub [reaguje](https://facebook.github.io/react/), ze strukturami po stronie serwera, takimi jak ASP.NET Core, może być trudne.</span><span class="sxs-lookup"><span data-stu-id="c2105-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="c2105-107">Usługi JavaScript opracowano w celu zmniejszenia liczby problemów w procesie integracji.</span><span class="sxs-lookup"><span data-stu-id="c2105-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="c2105-108">Umożliwia bezproblemowe działanie między różnymi stosami technologii klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="c2105-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="c2105-109">Funkcje opisane w tym artykule są przestarzałe w odniesieniu do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c2105-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="c2105-110">W pakiecie NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) jest dostępny prostszy mechanizm integracji systemu Spa Framework.</span><span class="sxs-lookup"><span data-stu-id="c2105-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="c2105-111">Aby uzyskać więcej informacji, zobacz [[anons] Obsoleting Microsoft. AspNetCore. SpaServices i Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="c2105-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="c2105-112">Co to jest usługa JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2105-112">What is JavaScript Services</span></span>

<span data-ttu-id="c2105-113">Usługi JavaScript to zbiór technologii po stronie klienta dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2105-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="c2105-114">Celem jest umieszczenie ASP.NET Core jako preferowanej platformy serwerowej dla deweloperów na potrzeby tworzenia aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="c2105-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="c2105-115">Usługi JavaScript składają się z dwóch różnych pakietów NuGet:</span><span class="sxs-lookup"><span data-stu-id="c2105-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="c2105-116">[Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="c2105-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="c2105-117">[Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="c2105-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="c2105-118">Te pakiety są przydatne w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="c2105-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="c2105-119">Uruchamianie kodu JavaScript na serwerze</span><span class="sxs-lookup"><span data-stu-id="c2105-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="c2105-120">Używanie struktury SPA lub biblioteki</span><span class="sxs-lookup"><span data-stu-id="c2105-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="c2105-121">Kompilowanie zasobów po stronie klienta za pomocą pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="c2105-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="c2105-122">Większość ostrości tego artykułu została umieszczona przy użyciu pakietu SpaServices.</span><span class="sxs-lookup"><span data-stu-id="c2105-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="c2105-123">Co to jest SpaServices</span><span class="sxs-lookup"><span data-stu-id="c2105-123">What is SpaServices</span></span>

<span data-ttu-id="c2105-124">SpaServices został utworzony, aby pomieścić ASP.NET Core jako preferowana platforma po stronie serwera dla deweloperów do tworzenia aplikacji jednostronicowych.</span><span class="sxs-lookup"><span data-stu-id="c2105-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="c2105-125">SpaServices nie jest wymagana do opracowania aplikacji jednostronicowych z ASP.NET Core i nie blokuje deweloperów w konkretnym środowisku klienta.</span><span class="sxs-lookup"><span data-stu-id="c2105-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="c2105-126">SpaServices zapewnia przydatną infrastrukturę, taką jak:</span><span class="sxs-lookup"><span data-stu-id="c2105-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="c2105-127">Renderowanie wstępne po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="c2105-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="c2105-128">Oprogramowanie pośredniczące dla deweloperów pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="c2105-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="c2105-129">Zastępowanie modułu gorącego</span><span class="sxs-lookup"><span data-stu-id="c2105-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="c2105-130">Pomocnicy routingu</span><span class="sxs-lookup"><span data-stu-id="c2105-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="c2105-131">Zbiorowo te składniki infrastruktury rozszerzają zarówno przepływ pracy deweloperskiej, jak i środowisko uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="c2105-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="c2105-132">Składniki można przyjmować pojedynczo.</span><span class="sxs-lookup"><span data-stu-id="c2105-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="c2105-133">Wymagania wstępne dotyczące używania SpaServices</span><span class="sxs-lookup"><span data-stu-id="c2105-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="c2105-134">Aby korzystać z SpaServices, Zainstaluj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="c2105-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="c2105-135">[Node. js](https://nodejs.org/) (w wersji 6 lub nowszej) z npm</span><span class="sxs-lookup"><span data-stu-id="c2105-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="c2105-136">Aby sprawdzić, czy te składniki są zainstalowane i można je znaleźć, uruchom następujące polecenie w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="c2105-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="c2105-137">W przypadku wdrażania w witrynie sieci Web systemu Azure nie jest wymagane żadne działanie &mdash; Node. js zainstalowane i dostępne w środowiskach serwerów.</span><span class="sxs-lookup"><span data-stu-id="c2105-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="c2105-138">W systemie Windows przy użyciu programu Visual Studio 2017 zestaw SDK jest instalowany przez wybranie obciążenia **międzyplatformowego dla aplikacji .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="c2105-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="c2105-139">Pakiet NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="c2105-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="c2105-140">Renderowanie wstępne po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="c2105-140">Server-side prerendering</span></span>

<span data-ttu-id="c2105-141">Aplikacja uniwersalna (znana również jako isomorphic) jest aplikacją języka JavaScript, która może działać zarówno na serwerze, jak i na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="c2105-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="c2105-142">Kątowy, reagowanie i inne popularne struktury zapewniają uniwersalną platformę dla tego stylu tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2105-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="c2105-143">Pomysłem jest, aby najpierw renderować składniki struktury na serwerze za pomocą środowiska Node. js, a następnie delegować dalsze wykonywanie do klienta.</span><span class="sxs-lookup"><span data-stu-id="c2105-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="c2105-144">ASP.NET Core [pomocników tagów](xref:mvc/views/tag-helpers/intro) zapewnianych przez SpaServices upraszczają implementację wstępnego renderowania po stronie serwera przez wywoływanie funkcji języka JavaScript na serwerze.</span><span class="sxs-lookup"><span data-stu-id="c2105-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="c2105-145">Wymagania wstępne dotyczące renderowania po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="c2105-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="c2105-146">Zainstaluj pakiet npm [renderowania z obsługą ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="c2105-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="c2105-147">Konfiguracja renderowania wstępnego po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="c2105-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="c2105-148">Pomocników tagów są odnajdywani za pomocą rejestracji przestrzeni nazw w pliku *_ViewImports. cshtml* projektu:</span><span class="sxs-lookup"><span data-stu-id="c2105-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="c2105-149">Te pomocniki tagów dezłożonegoą komunikację bezpośrednio z interfejsami API niskiego poziomu, wykorzystując składnię przypominającą kod HTML w widoku Razor:</span><span class="sxs-lookup"><span data-stu-id="c2105-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="c2105-150">skrypt ASP-PreRender-module tagów modułu</span><span class="sxs-lookup"><span data-stu-id="c2105-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="c2105-151">`asp-prerender-module`Pomocnik tagu, użyty w poprzednim przykładzie kodu, wykonuje *ClientApp/dist/Main-Server. js* na serwerze za pośrednictwem środowiska Node. js.</span><span class="sxs-lookup"><span data-stu-id="c2105-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="c2105-152">Na potrzeby przejrzystości plik *Main-Server. js* jest artefaktem zadania Transpilation języka TypeScript-to-JavaScript w procesie kompilacji [pakietu WebPack](https://webpack.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="c2105-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="c2105-153">Pakiet WebPack definiuje alias punktu wejścia, `main-server` a następnie przechodzenie wykresu zależności dla tego aliasu rozpoczyna się od pliku *ClientApp/Boot-Server. TS* :</span><span class="sxs-lookup"><span data-stu-id="c2105-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="c2105-154">W poniższym przykładzie kątowym plik *ClientApp/Boot-Server. TS* wykorzystuje `createServerRenderer` funkcję i `RenderResult` Typ `aspnet-prerendering` pakietu npm do konfigurowania renderowania serwera za pomocą środowiska Node. js.</span><span class="sxs-lookup"><span data-stu-id="c2105-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="c2105-155">Znaczniki HTML przeznaczone do renderowania po stronie serwera są przesyłane do wywołania funkcji rozpoznawania, które jest opakowane w obiekt JavaScript o jednoznacznie określonym typie `Promise` .</span><span class="sxs-lookup"><span data-stu-id="c2105-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="c2105-156">`Promise`Istotność obiektu polega na tym, że asynchronicznie dostarcza znacznik HTML do strony w celu iniekcji w elemencie symbolu zastępczego modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="c2105-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="c2105-157">ASP-PreRender — tag danych</span><span class="sxs-lookup"><span data-stu-id="c2105-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="c2105-158">W połączeniu z `asp-prerender-module` pomocnikiem tagów, `asp-prerender-data` pomocnika tagów może służyć do przekazywania informacji kontekstowych z widoku Razor do kodu JavaScript po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="c2105-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="c2105-159">Na przykład następujące znaczniki przekazują dane użytkownika do `main-server` modułu:</span><span class="sxs-lookup"><span data-stu-id="c2105-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="c2105-160">Otrzymany `UserName` argument jest serializowany przy użyciu wbudowanego serializatora JSON i jest przechowywany w `params.data` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="c2105-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="c2105-161">W poniższym przykładzie kątowym dane są używane do konstruowania spersonalizowanego powitania w obrębie `h1` elementu:</span><span class="sxs-lookup"><span data-stu-id="c2105-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="c2105-162">Nazwy właściwości przesyłane przez pomocników tagów są reprezentowane przy użyciu notacji **PascalCase** .</span><span class="sxs-lookup"><span data-stu-id="c2105-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="c2105-163">Przeciwieństwo do języka JavaScript, gdzie te same nazwy właściwości są reprezentowane przez **CamelCase**.</span><span class="sxs-lookup"><span data-stu-id="c2105-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="c2105-164">Domyślna konfiguracja serializacji JSON jest odpowiedzialna za tę różnicę.</span><span class="sxs-lookup"><span data-stu-id="c2105-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="c2105-165">Aby rozwijać poprzedni przykład kodu, dane mogą być przekazywane z serwera do widoku przez Hydrating `globals` Właściwości dostarczonej do `resolve` funkcji:</span><span class="sxs-lookup"><span data-stu-id="c2105-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="c2105-166">`postList`Tablica zdefiniowana wewnątrz `globals` obiektu jest dołączona do obiektu globalnego przeglądarki `window` .</span><span class="sxs-lookup"><span data-stu-id="c2105-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="c2105-167">Ta zmienna podnoszenia do zakresu globalnego eliminuje duplikowanie nakładów pracy, szczególnie w odniesieniu do ładowania tych samych danych na serwerze i ponownie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="c2105-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globalna zmienna postList dołączona do obiektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="c2105-169">Oprogramowanie pośredniczące dla deweloperów pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="c2105-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="c2105-170">[Oprogramowanie pośredniczące dla deweloperów pakietu WebPack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) wprowadza Ulepszony przepływ pracy programistycznej, dzięki czemu pakiet WebPack kompiluje zasoby na żądanie.</span><span class="sxs-lookup"><span data-stu-id="c2105-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="c2105-171">Oprogramowanie pośredniczące automatycznie kompiluje i obsługuje zasoby po stronie klienta po ponownym załadowaniu strony w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="c2105-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="c2105-172">Alternatywnym podejściem jest ręczne wywoływanie elementu WebPack za pośrednictwem skryptu kompilacji npm projektu, gdy zostanie zmieniona zależność innej firmy lub kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="c2105-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="c2105-173">Skrypt kompilacji npm w pliku *Package. JSON* jest przedstawiony w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="c2105-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="c2105-174">Wymagania wstępne dotyczące oprogramowania pośredniczącego WebPack dev</span><span class="sxs-lookup"><span data-stu-id="c2105-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="c2105-175">Zainstaluj pakiet npm [ASPNET-WebPack](https://www.npmjs.com/package/aspnet-webpack) :</span><span class="sxs-lookup"><span data-stu-id="c2105-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="c2105-176">Konfiguracja oprogramowania pośredniczącego WebPack dev</span><span class="sxs-lookup"><span data-stu-id="c2105-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="c2105-177">Oprogramowanie pośredniczące programu WebPack dla deweloperów jest rejestrowane w potoku żądania HTTP przez następujący kod *Startup.cs* w `Configure` metodzie pliku Startup.cs:</span><span class="sxs-lookup"><span data-stu-id="c2105-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="c2105-178">`UseWebpackDevMiddleware`Metoda rozszerzenia musi być wywoływana przed [zarejestrowaniem hostingu pliku statycznego](xref:fundamentals/static-files) za pośrednictwem `UseStaticFiles` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="c2105-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="c2105-179">Ze względów bezpieczeństwa należy zarejestrować oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programistycznym.</span><span class="sxs-lookup"><span data-stu-id="c2105-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="c2105-180">Właściwość pliku *WebPack. config. js* `output.publicPath` nakazuje oprogramowaniu pośredniczącemu oglądanie `dist` folderu pod kątem zmian:</span><span class="sxs-lookup"><span data-stu-id="c2105-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="c2105-181">Zastępowanie modułu gorącego</span><span class="sxs-lookup"><span data-stu-id="c2105-181">Hot Module Replacement</span></span>

<span data-ttu-id="c2105-182">Zastanów się nad funkcją [zastępowania modułu](https://webpack.js.org/concepts/hot-module-replacement/) internetowego (HMR) elementu WebPack jako ewolucją [oprogramowania pośredniczącego dla deweloperów pakietu WebPack](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="c2105-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="c2105-183">HMR wprowadza wszystkie te same korzyści, ale dodatkowo usprawnia przepływ pracy programistycznej przez automatyczne aktualizowanie zawartości strony po skompilowaniu zmian.</span><span class="sxs-lookup"><span data-stu-id="c2105-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="c2105-184">Nie należy mylić tego programu z odświeżaniem przeglądarki, co może zakłócać bieżący stan w pamięci i sesję debugowania SPA.</span><span class="sxs-lookup"><span data-stu-id="c2105-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="c2105-185">Istnieje link na żywo między usługą oprogramowania pośredniczącego aplikacji WebPack a przeglądarką, co oznacza, że zmiany są przekazywane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="c2105-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="c2105-186">Wymagania wstępne dotyczące zastępowania modułu aktywnego</span><span class="sxs-lookup"><span data-stu-id="c2105-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="c2105-187">Zainstaluj pakiet [WebPack-gorąca-Middle](https://www.npmjs.com/package/webpack-hot-middleware) npm Pack:</span><span class="sxs-lookup"><span data-stu-id="c2105-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="c2105-188">Konfiguracja wymiany modułu aktywnego</span><span class="sxs-lookup"><span data-stu-id="c2105-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="c2105-189">Składnik HMR musi być zarejestrowany w potoku żądania HTTP MVC w `Configure` metodzie:</span><span class="sxs-lookup"><span data-stu-id="c2105-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="c2105-190">Podobnie jak w przypadku [oprogramowania pośredniczącego w pakiecie WebPack](#webpack-dev-middleware), `UseWebpackDevMiddleware` Metoda rozszerzenia musi być wywoływana przed `UseStaticFiles` metodą rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="c2105-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="c2105-191">Ze względów bezpieczeństwa należy zarejestrować oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programistycznym.</span><span class="sxs-lookup"><span data-stu-id="c2105-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="c2105-192">Plik *WebPack. config. js* musi definiować `plugins` tablicę, nawet jeśli jest pusta:</span><span class="sxs-lookup"><span data-stu-id="c2105-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="c2105-193">Po załadowaniu aplikacji w przeglądarce karta konsolowa narzędzia deweloperskie zawiera potwierdzenie aktywacji HMR:</span><span class="sxs-lookup"><span data-stu-id="c2105-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Komunikat połączony z zastępowaniem aktywnego modułu](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="c2105-195">Pomocnicy routingu</span><span class="sxs-lookup"><span data-stu-id="c2105-195">Routing helpers</span></span>

<span data-ttu-id="c2105-196">W większości aplikacji jednostronicowych opartych na ASP.NET Core, routing po stronie klienta jest często pożądany oprócz routingu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="c2105-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="c2105-197">Systemy routingu SPA i MVC mogą działać niezależnie bez zakłóceń.</span><span class="sxs-lookup"><span data-stu-id="c2105-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="c2105-198">Istnieje jednak jeden przypadek graniczny proponujący wyzwania: Identyfikowanie odpowiedzi HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="c2105-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="c2105-199">Rozważmy scenariusz, w którym jest używana trasa bezrozszerzająca `/some/page` .</span><span class="sxs-lookup"><span data-stu-id="c2105-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="c2105-200">Załóżmy, że żądanie nie jest zgodne ze wzorcem po stronie serwera, ale jego wzorzec jest zgodny z trasą po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="c2105-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="c2105-201">Teraz Rozważmy żądanie przychodzące dla `/images/user-512.png` , które zwykle oczekuje na znalezienie pliku obrazu na serwerze.</span><span class="sxs-lookup"><span data-stu-id="c2105-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="c2105-202">Jeśli żądana ścieżka zasobu nie jest zgodna z żadną trasą po stronie serwera lub plikiem statycznym, jest mało prawdopodobne, że aplikacja po stronie klienta obsłuży jej &mdash; zwykle zwrócenie kodu stanu HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="c2105-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="c2105-203">Wymagania wstępne pomocników routingu</span><span class="sxs-lookup"><span data-stu-id="c2105-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="c2105-204">Zainstaluj pakiet npm routingu po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="c2105-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="c2105-205">Użycie kątowe jako przykładu:</span><span class="sxs-lookup"><span data-stu-id="c2105-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="c2105-206">Konfiguracja pomocników routingu</span><span class="sxs-lookup"><span data-stu-id="c2105-206">Routing helpers configuration</span></span>

<span data-ttu-id="c2105-207">Metoda rozszerzająca o nazwie `MapSpaFallbackRoute` jest używana w `Configure` metodzie:</span><span class="sxs-lookup"><span data-stu-id="c2105-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="c2105-208">Trasy są oceniane w kolejności, w jakiej są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="c2105-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="c2105-209">W związku z tym `default` trasa w poprzednim przykładzie kodu jest najpierw używana do dopasowania do wzorca.</span><span class="sxs-lookup"><span data-stu-id="c2105-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="c2105-210">Tworzenie nowego projektu</span><span class="sxs-lookup"><span data-stu-id="c2105-210">Create a new project</span></span>

<span data-ttu-id="c2105-211">Usługi JavaScript udostępniają wstępnie skonfigurowane szablony aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2105-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="c2105-212">SpaServices jest używany w tych szablonach w połączeniu z różnymi strukturami i bibliotekami, takimi jak kątowy, reagowanie i Redux.</span><span class="sxs-lookup"><span data-stu-id="c2105-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="c2105-213">Te szablony można zainstalować za pośrednictwem interfejs wiersza polecenia platformy .NET Core, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c2105-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="c2105-214">Zostanie wyświetlona lista dostępnych szablonów SPA:</span><span class="sxs-lookup"><span data-stu-id="c2105-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="c2105-215">Szablony</span><span class="sxs-lookup"><span data-stu-id="c2105-215">Templates</span></span>                                 | <span data-ttu-id="c2105-216">Krótka nazwa</span><span class="sxs-lookup"><span data-stu-id="c2105-216">Short Name</span></span> | <span data-ttu-id="c2105-217">Język</span><span class="sxs-lookup"><span data-stu-id="c2105-217">Language</span></span> | <span data-ttu-id="c2105-218">Tagi</span><span class="sxs-lookup"><span data-stu-id="c2105-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="c2105-219">ASP.NET Core MVC ze Skośnością</span><span class="sxs-lookup"><span data-stu-id="c2105-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="c2105-220">kątow</span><span class="sxs-lookup"><span data-stu-id="c2105-220">angular</span></span>    | <span data-ttu-id="c2105-221">Znajd</span><span class="sxs-lookup"><span data-stu-id="c2105-221">[C#]</span></span>     | <span data-ttu-id="c2105-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="c2105-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="c2105-223">ASP.NET Core MVC z funkcją reagując. js</span><span class="sxs-lookup"><span data-stu-id="c2105-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="c2105-224">biern</span><span class="sxs-lookup"><span data-stu-id="c2105-224">react</span></span>      | <span data-ttu-id="c2105-225">Znajd</span><span class="sxs-lookup"><span data-stu-id="c2105-225">[C#]</span></span>     | <span data-ttu-id="c2105-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="c2105-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="c2105-227">ASP.NET Core MVC z reakcjęmi. js i Redux</span><span class="sxs-lookup"><span data-stu-id="c2105-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="c2105-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="c2105-228">reactredux</span></span> | <span data-ttu-id="c2105-229">Znajd</span><span class="sxs-lookup"><span data-stu-id="c2105-229">[C#]</span></span>     | <span data-ttu-id="c2105-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="c2105-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="c2105-231">Aby utworzyć nowy projekt przy użyciu jednego z szablonów SPA, należy dołączyć **krótką nazwę** szablonu do polecenia [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="c2105-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="c2105-232">Następujące polecenie tworzy aplikację kątową z ASP.NET Core MVC skonfigurowanym po stronie serwera:</span><span class="sxs-lookup"><span data-stu-id="c2105-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="c2105-233">Ustawianie trybu konfiguracji środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="c2105-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="c2105-234">Istnieją dwa podstawowe tryby konfiguracji środowiska uruchomieniowego:</span><span class="sxs-lookup"><span data-stu-id="c2105-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="c2105-235">**Programowanie**:</span><span class="sxs-lookup"><span data-stu-id="c2105-235">**Development**:</span></span>
  * <span data-ttu-id="c2105-236">Zawiera mapy źródłowe ułatwiające debugowanie.</span><span class="sxs-lookup"><span data-stu-id="c2105-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="c2105-237">Nie optymalizuje kodu po stronie klienta pod kątem wydajności.</span><span class="sxs-lookup"><span data-stu-id="c2105-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="c2105-238">**Produkcja**:</span><span class="sxs-lookup"><span data-stu-id="c2105-238">**Production**:</span></span>
  * <span data-ttu-id="c2105-239">Wyklucza mapy źródłowe.</span><span class="sxs-lookup"><span data-stu-id="c2105-239">Excludes source maps.</span></span>
  * <span data-ttu-id="c2105-240">Optymalizuje kod po stronie klienta za pomocą funkcji grupowania i minifikacja.</span><span class="sxs-lookup"><span data-stu-id="c2105-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="c2105-241">ASP.NET Core używa zmiennej środowiskowej o nazwie `ASPNETCORE_ENVIRONMENT` do przechowywania trybu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="c2105-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="c2105-242">Aby uzyskać więcej informacji, zobacz [Ustawianie środowiska](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="c2105-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="c2105-243">Uruchom z interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="c2105-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="c2105-244">Przywróć wymagane pakiety NuGet i npm, uruchamiając następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="c2105-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="c2105-245">Kompiluj i uruchom aplikację:</span><span class="sxs-lookup"><span data-stu-id="c2105-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="c2105-246">Aplikacja jest uruchamiana na hoście lokalnym zgodnie z [trybem konfiguracji środowiska uruchomieniowego](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="c2105-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="c2105-247">Przechodzenie do `http://localhost:5000` programu w przeglądarce powoduje wyświetlenie strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="c2105-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="c2105-248">Uruchamianie z programem Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="c2105-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="c2105-249">Otwórz plik *. csproj* wygenerowany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="c2105-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="c2105-250">Wymagane pakiety NuGet i npm są przywracane automatycznie po otwarciu projektu.</span><span class="sxs-lookup"><span data-stu-id="c2105-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="c2105-251">Ten proces przywracania może potrwać kilka minut, a aplikacja jest gotowa do uruchomienia po jego zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="c2105-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="c2105-252">Kliknij przycisk z zielonym uruchomieniem lub naciśnij klawisz `Ctrl + F5` , a w przeglądarce zostanie otwarta strona docelowa aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2105-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="c2105-253">Aplikacja jest uruchamiana na hoście lokalnym zgodnie z [trybem konfiguracji środowiska uruchomieniowego](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="c2105-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c2105-254">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c2105-254">Test the app</span></span>

<span data-ttu-id="c2105-255">Szablony SpaServices są wstępnie skonfigurowane do uruchamiania testów po stronie klienta za pomocą [Karma](https://karma-runner.github.io/1.0/index.html) i [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="c2105-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="c2105-256">Jasmine to popularne środowisko testów jednostkowych dla języka JavaScript, a Karma to moduł uruchamiający testy dla tych testów.</span><span class="sxs-lookup"><span data-stu-id="c2105-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="c2105-257">Karma jest skonfigurowany do współdziałania z [pakietem pośredniczącym tworzenia pakietów WebPack](#webpack-dev-middleware) w taki sposób, aby deweloperzy nie musieli zatrzymywać i uruchamiać testów przy każdym wprowadzeniu zmian.</span><span class="sxs-lookup"><span data-stu-id="c2105-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="c2105-258">Niezależnie od tego, czy jest to kod działający w przypadku testowym lub przypadku testowego, test jest uruchamiany automatycznie.</span><span class="sxs-lookup"><span data-stu-id="c2105-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="c2105-259">Używając aplikacji kątowej jako przykładu, dwa przypadki testowe Jasmine są już podane dla `CounterComponent` pliku *Counter. Component. spec. TS* :</span><span class="sxs-lookup"><span data-stu-id="c2105-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="c2105-260">Otwórz wiersz polecenia w katalogu *ClientApp* .</span><span class="sxs-lookup"><span data-stu-id="c2105-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="c2105-261">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="c2105-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="c2105-262">Skrypt uruchamia moduł Karma Test Runner, który odczytuje ustawienia zdefiniowane w pliku *Karma. conf. js* .</span><span class="sxs-lookup"><span data-stu-id="c2105-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="c2105-263">Oprócz innych ustawień *Karma. conf. js* identyfikuje pliki testowe do wykonania za pośrednictwem `files` tablicy:</span><span class="sxs-lookup"><span data-stu-id="c2105-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="c2105-264">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c2105-264">Publish the app</span></span>

<span data-ttu-id="c2105-265">Aby uzyskać więcej informacji na temat publikowania na platformie Azure, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/12474) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="c2105-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="c2105-266">Połączenie wygenerowanych zasobów po stronie klienta i opublikowanych ASP.NET Core artefaktów w gotowy do wdrożenia pakiet może być kłopotliwe.</span><span class="sxs-lookup"><span data-stu-id="c2105-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="c2105-267">Thankfully, SpaServices organizuje cały proces publikacji z niestandardowym elementem docelowym programu MSBuild o nazwie `RunWebpack` :</span><span class="sxs-lookup"><span data-stu-id="c2105-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="c2105-268">Obiekt docelowy programu MSBuild ma następujące obowiązki:</span><span class="sxs-lookup"><span data-stu-id="c2105-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="c2105-269">Przywróć pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="c2105-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="c2105-270">Utwórz kompilację klasy produkcyjnej innych firm, które są zasobami po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="c2105-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="c2105-271">Utwórz kompilację klasy produkcyjnej dla niestandardowych zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="c2105-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="c2105-272">Skopiuj zasoby wygenerowane przez pakiet WebPack do folderu publikowanie.</span><span class="sxs-lookup"><span data-stu-id="c2105-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="c2105-273">Obiekt docelowy MSBuild jest wywoływany podczas uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="c2105-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="c2105-274">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c2105-274">Additional resources</span></span>

* [<span data-ttu-id="c2105-275">Dokumenty kątowe</span><span class="sxs-lookup"><span data-stu-id="c2105-275">Angular Docs</span></span>](https://angular.io/docs)
