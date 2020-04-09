---
title: Tworzenie aplikacji jednostronicowych w ASP.NET Core za pomocą usług JavaScript
author: scottaddie
description: Dowiedz się więcej o korzyściach płynących z korzystania z usług JavaScript w celu utworzenia aplikacji jednostronicowej (SPA) wspieranej przez ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663780"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="64ccc-103">Tworzenie aplikacji jednostronicowych w ASP.NET Core za pomocą usług JavaScript</span><span class="sxs-lookup"><span data-stu-id="64ccc-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="64ccc-104">Przez [Scott Addie](https://github.com/scottaddie) i [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="64ccc-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="64ccc-105">Aplikacja jednostronicowa (SPA) jest popularnym typem aplikacji internetowej ze względu na jej nieodłączne bogate doświadczenie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="64ccc-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="64ccc-106">Integracja struktur lub bibliotek SPA po stronie klienta, takich jak [Angular](https://angular.io/) lub [React,](https://facebook.github.io/react/)z platformami po stronie serwera, takimi jak ASP.NET Core, może być trudna.</span><span class="sxs-lookup"><span data-stu-id="64ccc-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="64ccc-107">Usługi JavaScript zostały opracowane w celu zmniejszenia tarcia w procesie integracji.</span><span class="sxs-lookup"><span data-stu-id="64ccc-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="64ccc-108">Umożliwia bezproblemową obsługę między różnymi stosami technologii klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="64ccc-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="64ccc-109">Funkcje opisane w tym artykule są przestarzałe od ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="64ccc-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="64ccc-110">Prostszy mechanizm integracji struktur SPA jest dostępny w pakiecie [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet.</span><span class="sxs-lookup"><span data-stu-id="64ccc-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="64ccc-111">Aby uzyskać więcej informacji, zobacz [[Anons] Obsoleting Microsoft.AspNetCore.SpaServices i Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="64ccc-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="64ccc-112">Co to są usługi JavaScript</span><span class="sxs-lookup"><span data-stu-id="64ccc-112">What is JavaScript Services</span></span>

<span data-ttu-id="64ccc-113">Usługi JavaScript to zbiór technologii po stronie klienta dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64ccc-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="64ccc-114">Jego celem jest umieszczenie ASP.NET Core jako preferowanej platformy po stronie serwera programistów do tworzenia umów SPA.</span><span class="sxs-lookup"><span data-stu-id="64ccc-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="64ccc-115">Usługi JavaScript składają się z dwóch odrębnych pakietów NuGet:</span><span class="sxs-lookup"><span data-stu-id="64ccc-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="64ccc-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (Usługi węzłowe)</span><span class="sxs-lookup"><span data-stu-id="64ccc-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="64ccc-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (Usługi spa)</span><span class="sxs-lookup"><span data-stu-id="64ccc-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="64ccc-118">Te pakiety są przydatne w następujących scenariuszach:</span><span class="sxs-lookup"><span data-stu-id="64ccc-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="64ccc-119">Uruchamianie języka JavaScript na serwerze</span><span class="sxs-lookup"><span data-stu-id="64ccc-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="64ccc-120">Korzystanie z struktury lub biblioteki SPA</span><span class="sxs-lookup"><span data-stu-id="64ccc-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="64ccc-121">Tworzenie zasobów po stronie klienta za pomocą pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="64ccc-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="64ccc-122">Wiele uwagi w tym artykule jest umieszczony na korzystanie z pakietu SpaServices.</span><span class="sxs-lookup"><span data-stu-id="64ccc-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="64ccc-123">Co to jest SpaServices</span><span class="sxs-lookup"><span data-stu-id="64ccc-123">What is SpaServices</span></span>

<span data-ttu-id="64ccc-124">SpaServices został stworzony, aby pozycjonować ASP.NET Core jako preferowaną platformę po stronie serwera programistów do tworzenia OSO.</span><span class="sxs-lookup"><span data-stu-id="64ccc-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="64ccc-125">SpaServices nie jest wymagane do tworzenia oso z ASP.NET Core i nie blokuje deweloperów w określonej ramach klienta.</span><span class="sxs-lookup"><span data-stu-id="64ccc-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="64ccc-126">SpaServices zapewnia użyteczną infrastrukturę, taką jak:</span><span class="sxs-lookup"><span data-stu-id="64ccc-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="64ccc-127">Prerendering po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="64ccc-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="64ccc-128">Oprogramowanie pośredniczące w programach webpack Dev</span><span class="sxs-lookup"><span data-stu-id="64ccc-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="64ccc-129">Wymiana modułu gorącego</span><span class="sxs-lookup"><span data-stu-id="64ccc-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="64ccc-130">Pomocnicy routingu</span><span class="sxs-lookup"><span data-stu-id="64ccc-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="64ccc-131">Łącznie te składniki infrastruktury zwiększają zarówno przepływ pracy deweloperów, jak i środowisko uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="64ccc-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="64ccc-132">Komponenty mogą być przyjmowane indywidualnie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="64ccc-133">Wymagania wstępne dotyczące korzystania z usług SpaServices</span><span class="sxs-lookup"><span data-stu-id="64ccc-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="64ccc-134">Aby pracować z SpaServices, zainstaluj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="64ccc-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="64ccc-135">[Node.js](https://nodejs.org/) (wersja 6 lub nowsza) z npm</span><span class="sxs-lookup"><span data-stu-id="64ccc-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="64ccc-136">Aby sprawdzić, czy te składniki są zainstalowane i można je znaleźć, uruchom następujące czynności z wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="64ccc-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="64ccc-137">W przypadku wdrażania w witrynie sieci Web&mdash;platformy Azure nie jest wymagana żadna akcja Node.js jest zainstalowana i dostępna w środowiskach serwera.</span><span class="sxs-lookup"><span data-stu-id="64ccc-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="64ccc-138">W systemie Windows przy użyciu programu Visual Studio 2017, SDK jest instalowany przez wybranie **.NET Core obciążenia deweloperskiego między platformami.**</span><span class="sxs-lookup"><span data-stu-id="64ccc-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="64ccc-139">Pakiet [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet</span><span class="sxs-lookup"><span data-stu-id="64ccc-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="64ccc-140">Prerendering po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="64ccc-140">Server-side prerendering</span></span>

<span data-ttu-id="64ccc-141">Uniwersalna (znana również jako izomorficzna) aplikacja jest aplikacją JavaScript zdolną do uruchamiania zarówno na serwerze, jak i na kliencie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="64ccc-142">Angular, React i inne popularne struktury zapewniają uniwersalną platformę dla tego stylu tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="64ccc-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="64ccc-143">Chodzi o to, aby najpierw renderować składniki struktury na serwerze za pośrednictwem node.js, a następnie delegować dalsze wykonanie do klienta.</span><span class="sxs-lookup"><span data-stu-id="64ccc-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="64ccc-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) dostarczone przez SpaServices upraszczają implementację prerendering po stronie serwera, wywołując funkcje JavaScript na serwerze.</span><span class="sxs-lookup"><span data-stu-id="64ccc-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="64ccc-145">Wymagania wstępne po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="64ccc-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="64ccc-146">Zainstaluj pakiet [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm:</span><span class="sxs-lookup"><span data-stu-id="64ccc-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="64ccc-147">Konfiguracja prerenderingu po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="64ccc-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="64ccc-148">Pomocników tagów są wykrywalne poprzez rejestrację obszaru nazw w pliku *_ViewImports.cshtml* projektu:</span><span class="sxs-lookup"><span data-stu-id="64ccc-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="64ccc-149">Te Pomocników Tag abstrakcji od zawiłości komunikowania się bezpośrednio z interfejsów API niskiego poziomu, wykorzystując składnię html-like wewnątrz widoku Razor:</span><span class="sxs-lookup"><span data-stu-id="64ccc-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="64ccc-150">asp-prerender-module Pomocnik znaczników</span><span class="sxs-lookup"><span data-stu-id="64ccc-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="64ccc-151">Pomocnik `asp-prerender-module` tagów, używany w poprzednim przykładzie kodu, wykonuje *ClientApp/dist/main-server.js* na serwerze za pośrednictwem node.js.</span><span class="sxs-lookup"><span data-stu-id="64ccc-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="64ccc-152">Dla jasności, *main-server.js* plik jest artefaktem zadania transpilacji TypeScript-to-JavaScript w procesie kompilacji [webpack.](https://webpack.github.io/)</span><span class="sxs-lookup"><span data-stu-id="64ccc-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="64ccc-153">Webpack definiuje alias punktu `main-server`wejścia ; oraz przechodzenie przez wykres zależności dla tego aliasu rozpoczyna się w pliku *ClientApp/boot-server.ts:*</span><span class="sxs-lookup"><span data-stu-id="64ccc-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="64ccc-154">W poniższym przykładzie Angular plik *ClientApp/boot-server.ts* wykorzystuje `createServerRenderer` funkcję i `RenderResult` typ pakietu `aspnet-prerendering` npm do konfigurowania renderowania serwera za pośrednictwem pliku Node.js.</span><span class="sxs-lookup"><span data-stu-id="64ccc-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="64ccc-155">Znaczniki HTML przeznaczone do renderowania po stronie serwera są przekazywane do wywołania funkcji rozpoznawania, `Promise` które jest zawijane w silnie typiwany obiekt JavaScript.</span><span class="sxs-lookup"><span data-stu-id="64ccc-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="64ccc-156">Istotność `Promise` obiektu polega na tym, że asynchronicznie dostarcza znaczników HTML do strony do iniekcji w elemencie zastępczym DOM.</span><span class="sxs-lookup"><span data-stu-id="64ccc-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="64ccc-157">asp-prerender-data Pomocnik znacznika</span><span class="sxs-lookup"><span data-stu-id="64ccc-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="64ccc-158">W połączeniu z `asp-prerender-module` Pomocnikiem znaczników pomocnik tagów `asp-prerender-data` może być używany do przekazywania informacji kontekstowych z widoku Razor do javascript po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="64ccc-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="64ccc-159">Na przykład następujące znaczniki przekazuje dane `main-server` użytkownika do modułu:</span><span class="sxs-lookup"><span data-stu-id="64ccc-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="64ccc-160">Odebrany `UserName` argument jest serializowany przy użyciu wbudowanego serializatora JSON i jest przechowywany w `params.data` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="64ccc-161">W poniższym przykładzie angular dane są używane do konstruowania spersonalizowane powitanie w elemencie: `h1`</span><span class="sxs-lookup"><span data-stu-id="64ccc-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="64ccc-162">Nazwy właściwości przekazywane w Pomocników tagów są reprezentowane z notacją **PascalCase.**</span><span class="sxs-lookup"><span data-stu-id="64ccc-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="64ccc-163">Kontrast, że do JavaScript, gdzie te same nazwy właściwości są reprezentowane z **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="64ccc-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="64ccc-164">Domyślna konfiguracja serializacji JSON jest odpowiedzialna za tę różnicę.</span><span class="sxs-lookup"><span data-stu-id="64ccc-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="64ccc-165">Aby rozwinąć w poprzednim przykładzie kodu, dane mogą być przekazywane z `globals` serwera do `resolve` widoku przez nawilżanie właściwości dostarczone do funkcji:</span><span class="sxs-lookup"><span data-stu-id="64ccc-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="64ccc-166">Tablica `postList` zdefiniowana `globals` wewnątrz obiektu jest dołączona `window` do obiektu globalnego przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="64ccc-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="64ccc-167">Ta zmienna podnoszenia do zakresu globalnego eliminuje powielanie wysiłku, zwłaszcza, że odnosi się do ładowania tych samych danych raz na serwerze i ponownie na kliencie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globalna zmienna postList dołączona do obiektu okna](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="64ccc-169">Oprogramowanie pośredniczące w programach webpack Dev</span><span class="sxs-lookup"><span data-stu-id="64ccc-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="64ccc-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) wprowadza usprawniony przepływ pracy rozwoju, w którym Webpack buduje zasoby na żądanie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="64ccc-171">Oprogramowanie pośredniczące automatycznie kompiluje i obsługuje zasoby po stronie klienta, gdy strona jest ponownie ładowana w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="64ccc-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="64ccc-172">Alternatywne podejście jest ręcznie wywołać Webpack za pośrednictwem skryptu kompilacji npm projektu, gdy zmienia się zależność innej firmy lub kod niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="64ccc-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="64ccc-173">Skrypt kompilacji npm w pliku *package.json* jest pokazany w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="64ccc-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="64ccc-174">Wymagania wstępne oprogramowania pośredniczącego dev webpack</span><span class="sxs-lookup"><span data-stu-id="64ccc-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="64ccc-175">Zainstaluj pakiet [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm:</span><span class="sxs-lookup"><span data-stu-id="64ccc-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="64ccc-176">Konfiguracja oprogramowania pośredniczącego webpack Dev</span><span class="sxs-lookup"><span data-stu-id="64ccc-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="64ccc-177">Oprogramowanie pośredniczące webpack Dev jest rejestrowane w potoku żądań HTTP `Configure` za pomocą następującego kodu w metodzie pliku *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="64ccc-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="64ccc-178">Metoda `UseWebpackDevMiddleware` rozszerzenia musi być wywoływana przed [zarejestrowaniem statycznego hostingu plików](xref:fundamentals/static-files) za pomocą metody `UseStaticFiles` rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="64ccc-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="64ccc-179">Ze względów bezpieczeństwa zarejestruj oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programowania.</span><span class="sxs-lookup"><span data-stu-id="64ccc-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="64ccc-180">Właściwość pliku *webpack.config.js* informuje oprogramowanie pośredniczące, aby obserwowało folder pod `dist` kątem zmian: `output.publicPath`</span><span class="sxs-lookup"><span data-stu-id="64ccc-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="64ccc-181">Wymiana modułu gorącego</span><span class="sxs-lookup"><span data-stu-id="64ccc-181">Hot Module Replacement</span></span>

<span data-ttu-id="64ccc-182">Pomyśl o Webpack Hot [Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) funkcja jako ewolucja [Webpack Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="64ccc-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="64ccc-183">HmR wprowadza wszystkie te same korzyści, ale dodatkowo usprawnia przepływ pracy rozwoju, automatycznie aktualizując zawartość strony po kompilacji zmian.</span><span class="sxs-lookup"><span data-stu-id="64ccc-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="64ccc-184">Nie należy mylić tego z odświeżeniem przeglądarki, co kolidowałoby z bieżącym stanem w pamięci i sesją debugowania spa.</span><span class="sxs-lookup"><span data-stu-id="64ccc-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="64ccc-185">Istnieje aktywne łącze między usługą Webpack Dev Middleware a przeglądarką, co oznacza, że zmiany są wypychane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="64ccc-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="64ccc-186">Wymagania wstępne wymiany modułu gorącego</span><span class="sxs-lookup"><span data-stu-id="64ccc-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="64ccc-187">Zainstaluj pakiet [npm webpack-hot-middleware:](https://www.npmjs.com/package/webpack-hot-middleware)</span><span class="sxs-lookup"><span data-stu-id="64ccc-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="64ccc-188">Konfiguracja wymiany gorącego modułu</span><span class="sxs-lookup"><span data-stu-id="64ccc-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="64ccc-189">Składnik HMR musi być zarejestrowany w potoku żądań HTTP MVC w metodzie: `Configure`</span><span class="sxs-lookup"><span data-stu-id="64ccc-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="64ccc-190">Podobnie jak w przypadku oprogramowania `UseWebpackDevMiddleware` `UseStaticFiles` [pośredniczącego webpack Dev,](#webpack-dev-middleware)metoda rozszerzenia musi zostać wywołana przed metodą rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="64ccc-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="64ccc-191">Ze względów bezpieczeństwa zarejestruj oprogramowanie pośredniczące tylko wtedy, gdy aplikacja działa w trybie programowania.</span><span class="sxs-lookup"><span data-stu-id="64ccc-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="64ccc-192">Plik *webpack.config.js* musi `plugins` definiować tablicę, nawet jeśli jest pusta:</span><span class="sxs-lookup"><span data-stu-id="64ccc-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="64ccc-193">Po załadowaniu aplikacji w przeglądarce karta Konsola narzędzi programistycznych zawiera potwierdzenie aktywacji HMR:</span><span class="sxs-lookup"><span data-stu-id="64ccc-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Komunikat o wymianie modułu gorącego](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="64ccc-195">Pomocnicy routingu</span><span class="sxs-lookup"><span data-stu-id="64ccc-195">Routing helpers</span></span>

<span data-ttu-id="64ccc-196">W większości ASP.NET oso oparte na rdzeniu routing po stronie klienta jest często pożądane oprócz routingu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="64ccc-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="64ccc-197">Systemy routingu SPA i MVC mogą pracować niezależnie bez zakłóceń.</span><span class="sxs-lookup"><span data-stu-id="64ccc-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="64ccc-198">Istnieje jednak jeden przypadek krawędzi stwarzające wyzwania: identyfikowanie 404 odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="64ccc-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="64ccc-199">Należy wziąć pod uwagę scenariusz, `/some/page` w którym jest używana trasa bez rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="64ccc-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="64ccc-200">Załóżmy, że żądanie nie pasuje do trasy po stronie serwera, ale jego wzorzec pasuje do trasy po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="64ccc-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="64ccc-201">Teraz należy wziąć `/images/user-512.png`pod uwagę przychodzące żądanie dla , który zazwyczaj oczekuje, aby znaleźć plik obrazu na serwerze.</span><span class="sxs-lookup"><span data-stu-id="64ccc-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="64ccc-202">Jeśli żądana ścieżka zasobu nie pasuje do żadnej trasy po stronie serwera lub pliku statycznego,&mdash;jest mało prawdopodobne, że aplikacja po stronie klienta będzie obsługiwać go zazwyczaj zwracany kod stanu HTTP 404 jest pożądane.</span><span class="sxs-lookup"><span data-stu-id="64ccc-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="64ccc-203">Wymagania wstępne pomocników routingu</span><span class="sxs-lookup"><span data-stu-id="64ccc-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="64ccc-204">Zainstaluj pakiet npm routingu po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="64ccc-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="64ccc-205">Za pomocą Angular jako przykład:</span><span class="sxs-lookup"><span data-stu-id="64ccc-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="64ccc-206">Konfiguracja pomocników routingu</span><span class="sxs-lookup"><span data-stu-id="64ccc-206">Routing helpers configuration</span></span>

<span data-ttu-id="64ccc-207">Metoda rozszerzenia `MapSpaFallbackRoute` o nazwie `Configure` jest używana w metodzie:</span><span class="sxs-lookup"><span data-stu-id="64ccc-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="64ccc-208">Trasy są oceniane w kolejności, w jakiej są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="64ccc-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="64ccc-209">W związku `default` z tym trasa w poprzednim przykładzie kodu jest używana najpierw do dopasowywania wzorców.</span><span class="sxs-lookup"><span data-stu-id="64ccc-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="64ccc-210">Tworzenie nowego projektu</span><span class="sxs-lookup"><span data-stu-id="64ccc-210">Create a new project</span></span>

<span data-ttu-id="64ccc-211">Usługi JavaScript zapewniają wstępnie skonfigurowane szablony aplikacji.</span><span class="sxs-lookup"><span data-stu-id="64ccc-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="64ccc-212">SpaServices jest używany w tych szablonach w połączeniu z różnych struktur i bibliotek, takich jak Angular, React i Redux.</span><span class="sxs-lookup"><span data-stu-id="64ccc-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="64ccc-213">Te szablony można zainstalować za pomocą interfejsu wiersza polecenia .NET Core, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="64ccc-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="64ccc-214">Zostanie wyświetlona lista dostępnych szablonów SPA:</span><span class="sxs-lookup"><span data-stu-id="64ccc-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="64ccc-215">Szablony</span><span class="sxs-lookup"><span data-stu-id="64ccc-215">Templates</span></span>                                 | <span data-ttu-id="64ccc-216">Krótka nazwa</span><span class="sxs-lookup"><span data-stu-id="64ccc-216">Short Name</span></span> | <span data-ttu-id="64ccc-217">Język</span><span class="sxs-lookup"><span data-stu-id="64ccc-217">Language</span></span> | <span data-ttu-id="64ccc-218">Tagi</span><span class="sxs-lookup"><span data-stu-id="64ccc-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="64ccc-219">MVC ASP.NET rdzeń z kątowym</span><span class="sxs-lookup"><span data-stu-id="64ccc-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="64ccc-220">Kątowe</span><span class="sxs-lookup"><span data-stu-id="64ccc-220">angular</span></span>    | <span data-ttu-id="64ccc-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="64ccc-221">[C#]</span></span>     | <span data-ttu-id="64ccc-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="64ccc-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="64ccc-223">MVC ASP.NET Core z React.js</span><span class="sxs-lookup"><span data-stu-id="64ccc-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="64ccc-224">Reagować</span><span class="sxs-lookup"><span data-stu-id="64ccc-224">react</span></span>      | <span data-ttu-id="64ccc-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="64ccc-225">[C#]</span></span>     | <span data-ttu-id="64ccc-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="64ccc-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="64ccc-227">MVC ASP.NET Core z React.js i Redux</span><span class="sxs-lookup"><span data-stu-id="64ccc-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="64ccc-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="64ccc-228">reactredux</span></span> | <span data-ttu-id="64ccc-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="64ccc-229">[C#]</span></span>     | <span data-ttu-id="64ccc-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="64ccc-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="64ccc-231">Aby utworzyć nowy projekt przy użyciu jednego z szablonów SPA, należy dołączyć **krótką nazwę** szablonu w nowym poleceniu [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="64ccc-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="64ccc-232">Następujące polecenie tworzy aplikację kątową z ASP.NET Core MVC skonfigurowanym po stronie serwera:</span><span class="sxs-lookup"><span data-stu-id="64ccc-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="64ccc-233">Ustawianie trybu konfiguracji środowiska wykonawczego</span><span class="sxs-lookup"><span data-stu-id="64ccc-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="64ccc-234">Istnieją dwa podstawowe tryby konfiguracji środowiska uruchomieniowego:</span><span class="sxs-lookup"><span data-stu-id="64ccc-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="64ccc-235">**Rozwój**:</span><span class="sxs-lookup"><span data-stu-id="64ccc-235">**Development**:</span></span>
  * <span data-ttu-id="64ccc-236">Zawiera mapy źródłowe ułatwiające debugowanie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="64ccc-237">Nie optymalizuje kodu po stronie klienta pod kątem wydajności.</span><span class="sxs-lookup"><span data-stu-id="64ccc-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="64ccc-238">**Produkcja**:</span><span class="sxs-lookup"><span data-stu-id="64ccc-238">**Production**:</span></span>
  * <span data-ttu-id="64ccc-239">Wyklucza mapy źródłowe.</span><span class="sxs-lookup"><span data-stu-id="64ccc-239">Excludes source maps.</span></span>
  * <span data-ttu-id="64ccc-240">Optymalizuje kod po stronie klienta poprzez łączenie i minyfikowanie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="64ccc-241">ASP.NET Core używa zmiennej środowiskowej `ASPNETCORE_ENVIRONMENT` o nazwie do przechowywania trybu konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="64ccc-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="64ccc-242">Aby uzyskać więcej informacji, zobacz [Ustawianie środowiska](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="64ccc-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="64ccc-243">Uruchamianie z rdzeniem polecenia .NET CORE CLI</span><span class="sxs-lookup"><span data-stu-id="64ccc-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="64ccc-244">Przywróć wymagane pakiety NuGet i npm, uruchamiając następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="64ccc-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="64ccc-245">Skompiluj i uruchom aplikację:</span><span class="sxs-lookup"><span data-stu-id="64ccc-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="64ccc-246">Aplikacja uruchamia się na localhost zgodnie z [trybem konfiguracji środowiska wykonawczego](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="64ccc-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="64ccc-247">Przejście do `http://localhost:5000` przeglądarki powoduje wyświetlenie strony docelowej.</span><span class="sxs-lookup"><span data-stu-id="64ccc-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="64ccc-248">Uruchamianie za pomocą programu Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="64ccc-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="64ccc-249">Otwórz plik *csproj* wygenerowany przez nowe polecenie [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="64ccc-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="64ccc-250">Wymagane pakiety NuGet i npm są przywracane automatycznie po otwarciu projektu.</span><span class="sxs-lookup"><span data-stu-id="64ccc-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="64ccc-251">Ten proces przywracania może potrwać do kilku minut, a aplikacja jest gotowa do uruchomienia po jej zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="64ccc-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="64ccc-252">Kliknij zielony przycisk uruchom `Ctrl + F5`lub naciśnij przycisk , a przeglądarka otworzy stronę docelową aplikacji.</span><span class="sxs-lookup"><span data-stu-id="64ccc-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="64ccc-253">Aplikacja działa na localhost zgodnie z [trybem konfiguracji środowiska wykonawczego](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="64ccc-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="64ccc-254">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="64ccc-254">Test the app</span></span>

<span data-ttu-id="64ccc-255">Szablony SpaServices są wstępnie skonfigurowane do uruchamiania testów po stronie klienta przy użyciu [karmy](https://karma-runner.github.io/1.0/index.html) i [jaśminu.](https://jasmine.github.io/)</span><span class="sxs-lookup"><span data-stu-id="64ccc-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="64ccc-256">Jasmine jest popularnym frameworkiem do testowania jednostek javascript, podczas gdy Karma jest testowym biegaczem dla tych testów.</span><span class="sxs-lookup"><span data-stu-id="64ccc-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="64ccc-257">Karma jest skonfigurowana do pracy z [oprogramowaniem pośredniczącym Webpack Dev](#webpack-dev-middleware) w taki sposób, że deweloper nie musi zatrzymywać i uruchamiać testu za każdym razem, gdy wprowadzane są zmiany.</span><span class="sxs-lookup"><span data-stu-id="64ccc-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="64ccc-258">Niezależnie od tego, czy jest to kod uruchomiony dla przypadku testowego, czy sam przypadek testowy, test jest uruchamiany automatycznie.</span><span class="sxs-lookup"><span data-stu-id="64ccc-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="64ccc-259">Za pomocą aplikacji kątowej jako przykład, dwa przypadki testowe Jasmine są już przewidziane dla `CounterComponent` pliku *counter.component.spec.ts:*</span><span class="sxs-lookup"><span data-stu-id="64ccc-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="64ccc-260">Otwórz wiersz polecenia w katalogu *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="64ccc-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="64ccc-261">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="64ccc-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="64ccc-262">Skrypt uruchamia biegacza testowego Karma, który odczytuje ustawienia zdefiniowane w pliku *karma.conf.js.*</span><span class="sxs-lookup"><span data-stu-id="64ccc-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="64ccc-263">Wśród innych *ustawień, karma.conf.js* identyfikuje pliki testowe, `files` które mają być wykonane za pośrednictwem tablicy:</span><span class="sxs-lookup"><span data-stu-id="64ccc-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="64ccc-264">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="64ccc-264">Publish the app</span></span>

<span data-ttu-id="64ccc-265">Zobacz [ten problem z usługą GitHub,](https://github.com/dotnet/AspNetCore.Docs/issues/12474) aby uzyskać więcej informacji na temat publikowania na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="64ccc-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="64ccc-266">Łączenie wygenerowanych zasobów po stronie klienta i opublikowanych artefaktów ASP.NET core w gotowy do wdrożenia pakiet może być uciążliwe.</span><span class="sxs-lookup"><span data-stu-id="64ccc-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="64ccc-267">Na szczęście SpaServices organizuje cały proces publikacji z niestandardowym `RunWebpack`celem MSBuild o nazwie:</span><span class="sxs-lookup"><span data-stu-id="64ccc-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="64ccc-268">Cel MSBuild ma następujące obowiązki:</span><span class="sxs-lookup"><span data-stu-id="64ccc-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="64ccc-269">Przywróć pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="64ccc-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="64ccc-270">Tworzenie kompilacji klasy produkcyjnej zasobów innych firm po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="64ccc-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="64ccc-271">Tworzenie kompilacji klasy produkcyjnej niestandardowych zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="64ccc-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="64ccc-272">Skopiuj zasoby wygenerowane przez pakiet Web do folderu publikowania.</span><span class="sxs-lookup"><span data-stu-id="64ccc-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="64ccc-273">Obiekt docelowy MSBuild jest wywoływany podczas uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="64ccc-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="64ccc-274">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="64ccc-274">Additional resources</span></span>

* [<span data-ttu-id="64ccc-275">Dokumenty kątowe</span><span class="sxs-lookup"><span data-stu-id="64ccc-275">Angular Docs</span></span>](https://angular.io/docs)
