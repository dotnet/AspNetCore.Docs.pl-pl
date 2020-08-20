---
title: Migrowanie z interfejsu API sieci Web ASP.NET do ASP.NET Core
author: ardalis
description: Dowiedz się, jak migrować implementację internetowego interfejsu API z ASP.NET 4. x sieci Web API do ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
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
uid: migration/webapi
ms.openlocfilehash: e3e46f8050ba87c3108885341675c9d2a2cb7847
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635167"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="297bf-103">Migrowanie z interfejsu API sieci Web ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="297bf-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="297bf-104">Przez [Scott Addie](https://twitter.com/scott_addie) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="297bf-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="297bf-105">Internetowy interfejs API ASP.NET 4. x to usługa HTTP, która dociera do szerokiego zakresu klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="297bf-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="297bf-106">ASP.NET Core łączy modele aplikacji MVC i Web API ASP.NET 4. x w jeden model programowania znany jako ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="297bf-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="297bf-107">W tym artykule przedstawiono kroki wymagane do przeprowadzenia migracji z interfejsu API sieci Web ASP.NET 4. x do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="297bf-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="297bf-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="297bf-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="297bf-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="297bf-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="297bf-110">Przegląd projektu interfejsu API sieci Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="297bf-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="297bf-111">W tym artykule jest stosowany projekt *ProductsApp* utworzony w [wprowadzenie z ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="297bf-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="297bf-112">W tym projekcie projekt internetowego interfejsu API ASP.NET 4. x został skonfigurowany w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="297bf-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="297bf-113">W *Global.asax.cs*następuje wywołanie `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="297bf-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="297bf-114">`WebApiConfig`Klasa znajduje się w folderze *App_Start* i ma metodę statyczną `Register` :</span><span class="sxs-lookup"><span data-stu-id="297bf-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="297bf-115">Poprzednia Klasa:</span><span class="sxs-lookup"><span data-stu-id="297bf-115">The preceding class:</span></span>

* <span data-ttu-id="297bf-116">Konfiguruje [Routing atrybutów](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), chociaż nie jest faktycznie używany.</span><span class="sxs-lookup"><span data-stu-id="297bf-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="297bf-117">Konfiguruje tabelę routingu.</span><span class="sxs-lookup"><span data-stu-id="297bf-117">Configures the routing table.</span></span>
<span data-ttu-id="297bf-118">Przykładowy kod oczekuje, że adresy URL są zgodne z formatem `/api/{controller}/{id}` i `{id}` są opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="297bf-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="297bf-119">W poniższych sekcjach przedstawiono migrację projektu interfejsu API sieci Web do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="297bf-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="297bf-120">Utwórz projekt docelowy</span><span class="sxs-lookup"><span data-stu-id="297bf-120">Create the destination project</span></span>

<span data-ttu-id="297bf-121">Utwórz nowe puste rozwiązanie w programie Visual Studio i Dodaj projekt interfejsu API sieci Web ASP.NET 4. x do migracji:</span><span class="sxs-lookup"><span data-stu-id="297bf-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="297bf-122">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="297bf-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="297bf-123">Wybierz szablon **pustego rozwiązania** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="297bf-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="297bf-124">Nazwij rozwiązanie *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="297bf-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="297bf-125">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="297bf-125">Select **Create**.</span></span>
1. <span data-ttu-id="297bf-126">Dodaj istniejący projekt *ProductsApp* do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="297bf-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="297bf-127">Dodaj nowy projekt interfejsu API do migracji do:</span><span class="sxs-lookup"><span data-stu-id="297bf-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="297bf-128">Dodaj nowy projekt **aplikacji sieci Web ASP.NET Core** do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="297bf-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="297bf-129">W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij projekt *ProductsCore*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="297bf-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="297bf-130">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="297bf-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="297bf-131">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="297bf-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="297bf-132">Usuń przykładowe pliki *WeatherForecast.cs* i *controllers/WeatherForecastController. cs* z nowego projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="297bf-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="297bf-133">Rozwiązanie zawiera teraz dwa projekty.</span><span class="sxs-lookup"><span data-stu-id="297bf-133">The solution now contains two projects.</span></span> <span data-ttu-id="297bf-134">W poniższych sekcjach opisano Migrowanie zawartości projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="297bf-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="297bf-135">Migruj konfigurację</span><span class="sxs-lookup"><span data-stu-id="297bf-135">Migrate configuration</span></span>

<span data-ttu-id="297bf-136">ASP.NET Core nie używa folderu *App_Start* ani pliku *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="297bf-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="297bf-137">Ponadto plik *web.config* zostanie dodany w czasie publikacji.</span><span class="sxs-lookup"><span data-stu-id="297bf-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="297bf-138">Klasa `Startup`:</span><span class="sxs-lookup"><span data-stu-id="297bf-138">The `Startup` class:</span></span>

* <span data-ttu-id="297bf-139">Zastępuje *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="297bf-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="297bf-140">Obsługuje wszystkie zadania uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="297bf-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="297bf-141">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="297bf-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="297bf-142">Migrowanie modeli i kontrolerów</span><span class="sxs-lookup"><span data-stu-id="297bf-142">Migrate models and controllers</span></span>

<span data-ttu-id="297bf-143">Poniższy kod przedstawia `ProductsController` aktualizację do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="297bf-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="297bf-144">Zaktualizuj `ProductsController` dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="297bf-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="297bf-145">Skopiuj *Kontrolery/ProductsController. cs* i folder *modele* z oryginalnego projektu do nowego.</span><span class="sxs-lookup"><span data-stu-id="297bf-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="297bf-146">Zmień przestrzeń nazw skopiowanych plików na `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="297bf-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="297bf-147">Zaktualizuj `using ProductsApp.Models;` instrukcję do `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="297bf-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="297bf-148">Następujące składniki nie istnieją w ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="297bf-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="297bf-149">Klasa `ApiController`</span><span class="sxs-lookup"><span data-stu-id="297bf-149">`ApiController` class</span></span>
* <span data-ttu-id="297bf-150">`System.Web.Http` obszaru</span><span class="sxs-lookup"><span data-stu-id="297bf-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="297bf-151">`IHttpActionResult` interfejsu</span><span class="sxs-lookup"><span data-stu-id="297bf-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="297bf-152">Wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="297bf-152">Make the following changes:</span></span>

1. <span data-ttu-id="297bf-153">Zmień `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="297bf-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="297bf-154">Dodaj `using Microsoft.AspNetCore.Mvc;` , aby rozwiązać `ControllerBase` odwołanie.</span><span class="sxs-lookup"><span data-stu-id="297bf-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="297bf-155">Usuń folder `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="297bf-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="297bf-156">Zmień `GetProduct` Typ zwracany akcji z `IHttpActionResult` na `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="297bf-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="297bf-157">Uprość `GetProduct` instrukcję akcji `return` do następujących:</span><span class="sxs-lookup"><span data-stu-id="297bf-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="297bf-158">Configure routing (Konfigurowanie routingu)</span><span class="sxs-lookup"><span data-stu-id="297bf-158">Configure routing</span></span>

<span data-ttu-id="297bf-159">Szablon projektu *interfejsu API* ASP.NET Core obejmuje konfigurację routingu punktu końcowego w wygenerowanym kodzie.</span><span class="sxs-lookup"><span data-stu-id="297bf-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="297bf-160">Następujące <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> i <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> wywołania:</span><span class="sxs-lookup"><span data-stu-id="297bf-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="297bf-161">Zarejestruj dopasowanie tras i wykonywanie punktów końcowych w potoku [pośredniczącego](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="297bf-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="297bf-162">Zastąp plik *App_Start/webapiconfig.cs* projektu *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="297bf-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="297bf-163">Skonfiguruj Routing w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="297bf-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="297bf-164">Oznacz `ProductsController` klasę następującymi atrybutami:</span><span class="sxs-lookup"><span data-stu-id="297bf-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="297bf-165">Poprzedni [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atrybut konfiguruje wzorzec routingu atrybutu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="297bf-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="297bf-166">Ten [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut powoduje, że atrybut routingu wymaga dla wszystkich akcji w tym kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="297bf-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="297bf-167">Routing atrybutów obsługuje tokeny, takie jak `[controller]` i `[action]` .</span><span class="sxs-lookup"><span data-stu-id="297bf-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="297bf-168">W czasie wykonywania każdy token jest zastępowany odpowiednio nazwą kontrolera lub akcji, do którego zastosowano atrybut.</span><span class="sxs-lookup"><span data-stu-id="297bf-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="297bf-169">Tokeny:</span><span class="sxs-lookup"><span data-stu-id="297bf-169">The tokens:</span></span>
    * <span data-ttu-id="297bf-170">Zmniejsz liczbę ciągów magicznych w projekcie.</span><span class="sxs-lookup"><span data-stu-id="297bf-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="297bf-171">Upewnij się, że trasy pozostają zsynchronizowane z odpowiednimi kontrolerami i akcjami, gdy stosowane są refaktoryzacje automatycznej zmiany nazwy.</span><span class="sxs-lookup"><span data-stu-id="297bf-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="297bf-172">Włącz żądania HTTP GET do `ProductController` akcji:</span><span class="sxs-lookup"><span data-stu-id="297bf-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="297bf-173">Zastosuj [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut do `GetAllProducts` akcji.</span><span class="sxs-lookup"><span data-stu-id="297bf-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="297bf-174">Zastosuj `[HttpGet("{id}")]` atrybut do `GetProduct` akcji.</span><span class="sxs-lookup"><span data-stu-id="297bf-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="297bf-175">Uruchom zmigrowany projekt i przejdź do `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="297bf-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="297bf-176">Zostanie wyświetlona pełna lista trzech produktów.</span><span class="sxs-lookup"><span data-stu-id="297bf-176">A full list of three products appears.</span></span> <span data-ttu-id="297bf-177">Przejdź na stronę `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="297bf-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="297bf-178">Zostanie wyświetlony pierwszy produkt.</span><span class="sxs-lookup"><span data-stu-id="297bf-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="297bf-179">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="297bf-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="297bf-180">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="297bf-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="297bf-181">Przegląd projektu interfejsu API sieci Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="297bf-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="297bf-182">W tym artykule jest stosowany projekt *ProductsApp* utworzony w [wprowadzenie z ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="297bf-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="297bf-183">W tym projekcie projekt internetowego interfejsu API ASP.NET 4. x został skonfigurowany w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="297bf-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="297bf-184">W *Global.asax.cs*następuje wywołanie `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="297bf-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="297bf-185">`WebApiConfig`Klasa znajduje się w folderze *App_Start* i ma metodę statyczną `Register` :</span><span class="sxs-lookup"><span data-stu-id="297bf-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="297bf-186">Ta klasa konfiguruje [Routing atrybutów](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), chociaż nie jest faktycznie używana w projekcie.</span><span class="sxs-lookup"><span data-stu-id="297bf-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="297bf-187">Konfiguruje również tabelę routingu, która jest używana przez interfejs API sieci Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="297bf-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="297bf-188">W takim przypadku interfejs API sieci Web ASP.NET 4. x oczekuje, że adresy URL są zgodne z formatem `/api/{controller}/{id}` i `{id}` są opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="297bf-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="297bf-189">W poniższych sekcjach przedstawiono migrację projektu interfejsu API sieci Web do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="297bf-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="297bf-190">Utwórz projekt docelowy</span><span class="sxs-lookup"><span data-stu-id="297bf-190">Create the destination project</span></span>

<span data-ttu-id="297bf-191">Wykonaj następujące kroki w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="297bf-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="297bf-192">Przejdź do pozycji **plik**  >  **Nowy**  >  **projekt**  >  **Inne typy projektów**  >  **Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="297bf-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="297bf-193">Wybierz pozycję **puste rozwiązanie**i nazwij rozwiązanie *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="297bf-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="297bf-194">Kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="297bf-194">Click the **OK** button.</span></span>
* <span data-ttu-id="297bf-195">Dodaj istniejący projekt *ProductsApp* do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="297bf-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="297bf-196">Dodaj nowy projekt **aplikacji sieci Web ASP.NET Core** do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="297bf-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="297bf-197">Wybierz platformę docelową **platformy .NET Core** z listy rozwijanej i wybierz szablon projektu **interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="297bf-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="297bf-198">Nazwij projekt *ProductsCore*, a następnie kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="297bf-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="297bf-199">Rozwiązanie zawiera teraz dwa projekty.</span><span class="sxs-lookup"><span data-stu-id="297bf-199">The solution now contains two projects.</span></span> <span data-ttu-id="297bf-200">W poniższych sekcjach opisano Migrowanie zawartości projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="297bf-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="297bf-201">Migruj konfigurację</span><span class="sxs-lookup"><span data-stu-id="297bf-201">Migrate configuration</span></span>

<span data-ttu-id="297bf-202">ASP.NET Core nie używa:</span><span class="sxs-lookup"><span data-stu-id="297bf-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="297bf-203">Folder *App_Start* lub plik *Global. asax*</span><span class="sxs-lookup"><span data-stu-id="297bf-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="297bf-204">Plik *web.config* jest dodawany w czasie publikowania.</span><span class="sxs-lookup"><span data-stu-id="297bf-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="297bf-205">Klasa `Startup`:</span><span class="sxs-lookup"><span data-stu-id="297bf-205">The `Startup` class:</span></span>

* <span data-ttu-id="297bf-206">Zastępuje *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="297bf-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="297bf-207">Obsługuje wszystkie zadania uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="297bf-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="297bf-208">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="297bf-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="297bf-209">W ASP.NET Core MVC, routing atrybutu jest uwzględniany domyślnie, gdy <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> jest wywoływana w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="297bf-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="297bf-210">Następujące `UseMvc` wywołanie zastępuje plik */webapiconfig.cs App_Start* projektu *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="297bf-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="297bf-211">Migrowanie modeli i kontrolerów</span><span class="sxs-lookup"><span data-stu-id="297bf-211">Migrate models and controllers</span></span>

<span data-ttu-id="297bf-212">Poniższy kod przedstawia `ProductsController` aktualizację ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="297bf-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="297bf-213">Zaktualizuj `ProductsController` dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="297bf-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="297bf-214">Skopiuj *Kontrolery/ProductsController. cs* z oryginalnego projektu do nowego.</span><span class="sxs-lookup"><span data-stu-id="297bf-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="297bf-215">Skopiuj folder *modele* z oryginalnego projektu do nowego.</span><span class="sxs-lookup"><span data-stu-id="297bf-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="297bf-216">Zmień przestrzeń nazw skopiowanych plików na `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="297bf-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="297bf-217">Zaktualizuj `using ProductsApp.Models;` instrukcję do `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="297bf-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="297bf-218">Następujące składniki nie istnieją w ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="297bf-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="297bf-219">Klasa `ApiController`</span><span class="sxs-lookup"><span data-stu-id="297bf-219">`ApiController` class</span></span>
* <span data-ttu-id="297bf-220">`System.Web.Http` obszaru</span><span class="sxs-lookup"><span data-stu-id="297bf-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="297bf-221">`IHttpActionResult` interfejsu</span><span class="sxs-lookup"><span data-stu-id="297bf-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="297bf-222">Wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="297bf-222">Make the following changes:</span></span>

1. <span data-ttu-id="297bf-223">Zmień `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="297bf-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="297bf-224">Dodaj `using Microsoft.AspNetCore.Mvc;` , aby rozwiązać `ControllerBase` odwołanie.</span><span class="sxs-lookup"><span data-stu-id="297bf-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="297bf-225">Usuń folder `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="297bf-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="297bf-226">Zmień `GetProduct` Typ zwracany akcji z `IHttpActionResult` na `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="297bf-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="297bf-227">Uprość `GetProduct` instrukcję akcji `return` do następujących:</span><span class="sxs-lookup"><span data-stu-id="297bf-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="297bf-228">Configure routing (Konfigurowanie routingu)</span><span class="sxs-lookup"><span data-stu-id="297bf-228">Configure routing</span></span>

<span data-ttu-id="297bf-229">Skonfiguruj Routing w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="297bf-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="297bf-230">Oznacz `ProductsController` klasę następującymi atrybutami:</span><span class="sxs-lookup"><span data-stu-id="297bf-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="297bf-231">Poprzedni [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atrybut konfiguruje wzorzec routingu atrybutu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="297bf-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="297bf-232">Ten [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut powoduje, że atrybut routingu wymaga dla wszystkich akcji w tym kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="297bf-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="297bf-233">Routing atrybutów obsługuje tokeny, takie jak `[controller]` i `[action]` .</span><span class="sxs-lookup"><span data-stu-id="297bf-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="297bf-234">W czasie wykonywania każdy token jest zastępowany odpowiednio nazwą kontrolera lub akcji, do którego zastosowano atrybut.</span><span class="sxs-lookup"><span data-stu-id="297bf-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="297bf-235">Tokeny zmniejszają liczbę ciągów magicznych w projekcie.</span><span class="sxs-lookup"><span data-stu-id="297bf-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="297bf-236">Tokeny zapewniają również, że trasy pozostają zsynchronizowane z odpowiednimi kontrolerami i akcjami, gdy stosowane są automatyczne refaktoryzacje zmiany nazwy.</span><span class="sxs-lookup"><span data-stu-id="297bf-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="297bf-237">Ustaw tryb zgodności projektu na ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="297bf-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="297bf-238">Poprzednia zmiana:</span><span class="sxs-lookup"><span data-stu-id="297bf-238">The preceding change:</span></span>

    * <span data-ttu-id="297bf-239">Jest wymagany do użycia `[ApiController]` atrybutu na poziomie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="297bf-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="297bf-240">Umożliwia potencjalne uszkodzenie zachowań wprowadzonych w ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="297bf-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="297bf-241">Włącz żądania HTTP GET do `ProductController` akcji:</span><span class="sxs-lookup"><span data-stu-id="297bf-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="297bf-242">Zastosuj [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut do `GetAllProducts` akcji.</span><span class="sxs-lookup"><span data-stu-id="297bf-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="297bf-243">Zastosuj `[HttpGet("{id}")]` atrybut do `GetProduct` akcji.</span><span class="sxs-lookup"><span data-stu-id="297bf-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="297bf-244">Uruchom zmigrowany projekt i przejdź do `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="297bf-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="297bf-245">Zostanie wyświetlona pełna lista trzech produktów.</span><span class="sxs-lookup"><span data-stu-id="297bf-245">A full list of three products appears.</span></span> <span data-ttu-id="297bf-246">Przejdź na stronę `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="297bf-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="297bf-247">Zostanie wyświetlony pierwszy produkt.</span><span class="sxs-lookup"><span data-stu-id="297bf-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="297bf-248">Podkładka zgodności</span><span class="sxs-lookup"><span data-stu-id="297bf-248">Compatibility shim</span></span>

<span data-ttu-id="297bf-249">Biblioteka [Microsoft. AspNetCore. MVC. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) zawiera podkładkę zgodności do przenoszenia projektów interfejsu API sieci Web ASP.NET 4. x do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="297bf-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="297bf-250">Podkładka zgodności rozszerza ASP.NET Core do obsługi wielu konwencji z ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="297bf-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="297bf-251">Przykładowy port podany wcześniej w tym dokumencie jest wystarczająco mały, że podkładka zgodności była niepotrzebna.</span><span class="sxs-lookup"><span data-stu-id="297bf-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="297bf-252">W przypadku większych projektów użycie podkładki zgodności może być przydatne do tymczasowego mostkowania przerwy między ASP.NET Core i ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="297bf-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="297bf-253">Podkładka zgodności dla interfejsu API sieci Web ma służyć jako tymczasowa miara do obsługi migrowania dużych ASP.NETych projektów interfejsu API sieci Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="297bf-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="297bf-254">W miarę upływu czasu projekty należy zaktualizować tak, aby korzystały z wzorców ASP.NET Core zamiast polegać na podkładki zgodności.</span><span class="sxs-lookup"><span data-stu-id="297bf-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="297bf-255">Funkcje zgodności zawarte w programie `Microsoft.AspNetCore.Mvc.WebApiCompatShim` obejmują:</span><span class="sxs-lookup"><span data-stu-id="297bf-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="297bf-256">Dodaje `ApiController` Typ, aby nie trzeba było aktualizować typów podstawowych kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="297bf-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="297bf-257">Włącza powiązanie modelu internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="297bf-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="297bf-258">ASP.NET Core funkcji powiązania modelu MVC podobnie jak w przypadku ASP.NET 4. x MVC 5, domyślnie.</span><span class="sxs-lookup"><span data-stu-id="297bf-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="297bf-259">W ramach podkładki zgodności zmiany powiązania modelu są bardziej podobne do Konwencji powiązań modelu ASP.NET 4. x sieci Web.</span><span class="sxs-lookup"><span data-stu-id="297bf-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="297bf-260">Na przykład typy złożone są automatycznie powiązane z treścią żądania.</span><span class="sxs-lookup"><span data-stu-id="297bf-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="297bf-261">Rozszerza powiązanie modelu, aby akcje kontrolera mogły przyjmować parametry typu `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="297bf-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="297bf-262">Dodaje elementy formatujące komunikaty umożliwiające działanie zwracające wyniki typu `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="297bf-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="297bf-263">Dodaje dodatkowe metody odpowiedzi, które mogą być używane przez działania Web API 2 do obsłużenia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="297bf-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="297bf-264">`HttpResponseMessage` wytwornic</span><span class="sxs-lookup"><span data-stu-id="297bf-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="297bf-265">Metody wyniku akcji:</span><span class="sxs-lookup"><span data-stu-id="297bf-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="297bf-266">Dodaje wystąpienie `IContentNegotiator` do kontenera iniekcji zależności aplikacji i udostępnia typy powiązane z negocjowaniem zawartości z [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="297bf-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="297bf-267">Przykłady takich typów obejmują `DefaultContentNegotiator` i `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="297bf-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="297bf-268">Aby użyć podkładek zgodności:</span><span class="sxs-lookup"><span data-stu-id="297bf-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="297bf-269">Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="297bf-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="297bf-270">Zarejestruj usługi podkładki zgodności z kontenerem DI aplikacji, wywołując `services.AddMvc().AddWebApiConventions()` w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="297bf-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="297bf-271">Zdefiniuj trasy specyficzne dla interfejsu API sieci Web za pomocą polecenia `MapWebApiRoute` `IRouteBuilder` w `IApplicationBuilder.UseMvc` wywołaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="297bf-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="297bf-272">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="297bf-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
