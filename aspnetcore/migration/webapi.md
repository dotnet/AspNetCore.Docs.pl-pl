---
<span data-ttu-id="5192e-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5192e-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5192e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5192e-102">'Blazor'</span></span>
- <span data-ttu-id="5192e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5192e-103">'Identity'</span></span>
- <span data-ttu-id="5192e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5192e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5192e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5192e-105">'Razor'</span></span>
- <span data-ttu-id="5192e-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5192e-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="5192e-107">Migrowanie z interfejsu API sieci Web ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5192e-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="5192e-108">Przez [Scott Addie](https://twitter.com/scott_addie) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5192e-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5192e-109">Internetowy interfejs API ASP.NET 4. x to usługa HTTP, która dociera do szerokiego zakresu klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="5192e-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="5192e-110">ASP.NET Core łączy modele aplikacji MVC i Web API ASP.NET 4. x w jeden model programowania znany jako ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5192e-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="5192e-111">W tym artykule przedstawiono kroki wymagane do przeprowadzenia migracji z interfejsu API sieci Web ASP.NET 4. x do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5192e-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="5192e-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5192e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="5192e-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5192e-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="5192e-114">Przegląd projektu interfejsu API sieci Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="5192e-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="5192e-115">W tym artykule jest stosowany projekt *ProductsApp* utworzony w [wprowadzenie z ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="5192e-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="5192e-116">W tym projekcie projekt internetowego interfejsu API ASP.NET 4. x został skonfigurowany w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="5192e-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="5192e-117">W *Global.asax.cs*następuje wywołanie `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="5192e-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="5192e-118">`WebApiConfig`Klasa znajduje się w folderze *App_Start* i ma metodę statyczną `Register` :</span><span class="sxs-lookup"><span data-stu-id="5192e-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="5192e-119">Poprzednia Klasa:</span><span class="sxs-lookup"><span data-stu-id="5192e-119">The preceding class:</span></span>

* <span data-ttu-id="5192e-120">Konfiguruje [Routing atrybutów](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), chociaż nie jest faktycznie używany.</span><span class="sxs-lookup"><span data-stu-id="5192e-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="5192e-121">Konfiguruje tabelę routingu.</span><span class="sxs-lookup"><span data-stu-id="5192e-121">Configures the routing table.</span></span>
<span data-ttu-id="5192e-122">Przykładowy kod oczekuje, że adresy URL są zgodne z formatem `/api/{controller}/{id}` i `{id}` są opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="5192e-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="5192e-123">W poniższych sekcjach przedstawiono migrację projektu interfejsu API sieci Web do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5192e-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="5192e-124">Utwórz projekt docelowy</span><span class="sxs-lookup"><span data-stu-id="5192e-124">Create the destination project</span></span>

<span data-ttu-id="5192e-125">Utwórz nowe puste rozwiązanie w programie Visual Studio i Dodaj projekt interfejsu API sieci Web ASP.NET 4. x do migracji:</span><span class="sxs-lookup"><span data-stu-id="5192e-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="5192e-126">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5192e-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="5192e-127">Wybierz szablon **pustego rozwiązania** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="5192e-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="5192e-128">Nazwij rozwiązanie *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="5192e-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="5192e-129">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5192e-129">Select **Create**.</span></span>
1. <span data-ttu-id="5192e-130">Dodaj istniejący projekt *ProductsApp* do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5192e-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="5192e-131">Dodaj nowy projekt interfejsu API do migracji do:</span><span class="sxs-lookup"><span data-stu-id="5192e-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="5192e-132">Dodaj nowy projekt **aplikacji sieci Web ASP.NET Core** do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5192e-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="5192e-133">W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij projekt *ProductsCore*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5192e-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="5192e-134">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="5192e-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="5192e-135">Wybierz szablon projektu **interfejsu API** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5192e-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="5192e-136">Usuń przykładowe pliki *WeatherForecast.cs* i *controllers/WeatherForecastController. cs* z nowego projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="5192e-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="5192e-137">Rozwiązanie zawiera teraz dwa projekty.</span><span class="sxs-lookup"><span data-stu-id="5192e-137">The solution now contains two projects.</span></span> <span data-ttu-id="5192e-138">W poniższych sekcjach opisano Migrowanie zawartości projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="5192e-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="5192e-139">Migruj konfigurację</span><span class="sxs-lookup"><span data-stu-id="5192e-139">Migrate configuration</span></span>

<span data-ttu-id="5192e-140">ASP.NET Core nie używa folderu *App_Start* ani pliku *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="5192e-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="5192e-141">Ponadto plik *Web. config* jest dodawany w czasie publikacji.</span><span class="sxs-lookup"><span data-stu-id="5192e-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="5192e-142">Klasa `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5192e-142">The `Startup` class:</span></span>

* <span data-ttu-id="5192e-143">Zastępuje *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="5192e-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="5192e-144">Obsługuje wszystkie zadania uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5192e-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="5192e-145">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="5192e-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="5192e-146">Migrowanie modeli i kontrolerów</span><span class="sxs-lookup"><span data-stu-id="5192e-146">Migrate models and controllers</span></span>

<span data-ttu-id="5192e-147">Poniższy kod przedstawia `ProductsController` aktualizację do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5192e-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="5192e-148">Zaktualizuj `ProductsController` dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5192e-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="5192e-149">Skopiuj *Kontrolery/ProductsController. cs* i folder *modele* z oryginalnego projektu do nowego.</span><span class="sxs-lookup"><span data-stu-id="5192e-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="5192e-150">Zmień przestrzeń nazw skopiowanych plików na `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="5192e-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="5192e-151">Zaktualizuj `using ProductsApp.Models;` instrukcję do `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="5192e-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="5192e-152">Następujące składniki nie istnieją w ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5192e-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="5192e-153">Klasa `ApiController`</span><span class="sxs-lookup"><span data-stu-id="5192e-153">`ApiController` class</span></span>
* <span data-ttu-id="5192e-154">`System.Web.Http`obszaru</span><span class="sxs-lookup"><span data-stu-id="5192e-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="5192e-155">`IHttpActionResult`interfejsu</span><span class="sxs-lookup"><span data-stu-id="5192e-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="5192e-156">Wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5192e-156">Make the following changes:</span></span>

1. <span data-ttu-id="5192e-157">Zmień `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="5192e-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="5192e-158">Dodaj `using Microsoft.AspNetCore.Mvc;` , aby rozwiązać `ControllerBase` odwołanie.</span><span class="sxs-lookup"><span data-stu-id="5192e-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="5192e-159">Usuń folder `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="5192e-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="5192e-160">Zmień `GetProduct` Typ zwracany akcji z `IHttpActionResult` na `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="5192e-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="5192e-161">Uprość `GetProduct` instrukcję akcji `return` do następujących:</span><span class="sxs-lookup"><span data-stu-id="5192e-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="5192e-162">Configure routing (Konfigurowanie routingu)</span><span class="sxs-lookup"><span data-stu-id="5192e-162">Configure routing</span></span>

<span data-ttu-id="5192e-163">Szablon projektu *interfejsu API* ASP.NET Core obejmuje konfigurację routingu punktu końcowego w wygenerowanym kodzie.</span><span class="sxs-lookup"><span data-stu-id="5192e-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="5192e-164">Następujące <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> i <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> wywołania:</span><span class="sxs-lookup"><span data-stu-id="5192e-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="5192e-165">Zarejestruj dopasowanie tras i wykonywanie punktów końcowych w potoku [pośredniczącego](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="5192e-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="5192e-166">Zastąp plik *App_Start/webapiconfig.cs* projektu *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="5192e-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="5192e-167">Skonfiguruj Routing w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5192e-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="5192e-168">Oznacz `ProductsController` klasę następującymi atrybutami:</span><span class="sxs-lookup"><span data-stu-id="5192e-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="5192e-169">Poprzedni [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atrybut konfiguruje wzorzec routingu atrybutu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="5192e-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="5192e-170">Ten [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut powoduje, że atrybut routingu wymaga dla wszystkich akcji w tym kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="5192e-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="5192e-171">Routing atrybutów obsługuje tokeny, takie jak `[controller]` i `[action]` .</span><span class="sxs-lookup"><span data-stu-id="5192e-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="5192e-172">W czasie wykonywania każdy token jest zastępowany odpowiednio nazwą kontrolera lub akcji, do którego zastosowano atrybut.</span><span class="sxs-lookup"><span data-stu-id="5192e-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="5192e-173">Tokeny:</span><span class="sxs-lookup"><span data-stu-id="5192e-173">The tokens:</span></span>
    * <span data-ttu-id="5192e-174">Zmniejsz liczbę ciągów magicznych w projekcie.</span><span class="sxs-lookup"><span data-stu-id="5192e-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="5192e-175">Upewnij się, że trasy pozostają zsynchronizowane z odpowiednimi kontrolerami i akcjami, gdy stosowane są refaktoryzacje automatycznej zmiany nazwy.</span><span class="sxs-lookup"><span data-stu-id="5192e-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="5192e-176">Włącz żądania HTTP GET do `ProductController` akcji:</span><span class="sxs-lookup"><span data-stu-id="5192e-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="5192e-177">Zastosuj [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut do `GetAllProducts` akcji.</span><span class="sxs-lookup"><span data-stu-id="5192e-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="5192e-178">Zastosuj `[HttpGet("{id}")]` atrybut do `GetProduct` akcji.</span><span class="sxs-lookup"><span data-stu-id="5192e-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="5192e-179">Uruchom zmigrowany projekt i przejdź do `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="5192e-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="5192e-180">Zostanie wyświetlona pełna lista trzech produktów.</span><span class="sxs-lookup"><span data-stu-id="5192e-180">A full list of three products appears.</span></span> <span data-ttu-id="5192e-181">Przejdź do `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="5192e-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="5192e-182">Zostanie wyświetlony pierwszy produkt.</span><span class="sxs-lookup"><span data-stu-id="5192e-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5192e-183">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5192e-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="5192e-184">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5192e-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="5192e-185">Przegląd projektu interfejsu API sieci Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="5192e-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="5192e-186">W tym artykule jest stosowany projekt *ProductsApp* utworzony w [wprowadzenie z ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="5192e-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="5192e-187">W tym projekcie projekt internetowego interfejsu API ASP.NET 4. x został skonfigurowany w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="5192e-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="5192e-188">W *Global.asax.cs*następuje wywołanie `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="5192e-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="5192e-189">`WebApiConfig`Klasa znajduje się w folderze *App_Start* i ma metodę statyczną `Register` :</span><span class="sxs-lookup"><span data-stu-id="5192e-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="5192e-190">Ta klasa konfiguruje [Routing atrybutów](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), chociaż nie jest faktycznie używana w projekcie.</span><span class="sxs-lookup"><span data-stu-id="5192e-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="5192e-191">Konfiguruje również tabelę routingu, która jest używana przez interfejs API sieci Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5192e-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="5192e-192">W takim przypadku interfejs API sieci Web ASP.NET 4. x oczekuje, że adresy URL są zgodne z formatem `/api/{controller}/{id}` i `{id}` są opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="5192e-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="5192e-193">W poniższych sekcjach przedstawiono migrację projektu interfejsu API sieci Web do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5192e-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="5192e-194">Utwórz projekt docelowy</span><span class="sxs-lookup"><span data-stu-id="5192e-194">Create the destination project</span></span>

<span data-ttu-id="5192e-195">Wykonaj następujące kroki w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5192e-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="5192e-196">Przejdź do pozycji **plik**  >  **Nowy**  >  **projekt**  >  **Inne typy projektów**  >  **Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="5192e-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="5192e-197">Wybierz pozycję **puste rozwiązanie**i nazwij rozwiązanie *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="5192e-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="5192e-198">Kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="5192e-198">Click the **OK** button.</span></span>
* <span data-ttu-id="5192e-199">Dodaj istniejący projekt *ProductsApp* do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5192e-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="5192e-200">Dodaj nowy projekt **aplikacji sieci Web ASP.NET Core** do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="5192e-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="5192e-201">Wybierz platformę docelową **platformy .NET Core** z listy rozwijanej i wybierz szablon projektu **interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="5192e-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="5192e-202">Nazwij projekt *ProductsCore*, a następnie kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="5192e-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="5192e-203">Rozwiązanie zawiera teraz dwa projekty.</span><span class="sxs-lookup"><span data-stu-id="5192e-203">The solution now contains two projects.</span></span> <span data-ttu-id="5192e-204">W poniższych sekcjach opisano Migrowanie zawartości projektu *ProductsApp* do projektu *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="5192e-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="5192e-205">Migruj konfigurację</span><span class="sxs-lookup"><span data-stu-id="5192e-205">Migrate configuration</span></span>

<span data-ttu-id="5192e-206">ASP.NET Core nie używa:</span><span class="sxs-lookup"><span data-stu-id="5192e-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="5192e-207">Folder *App_Start* lub plik *Global. asax*</span><span class="sxs-lookup"><span data-stu-id="5192e-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="5192e-208">plik *Web. config* jest dodawany w czasie publikacji.</span><span class="sxs-lookup"><span data-stu-id="5192e-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="5192e-209">Klasa `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5192e-209">The `Startup` class:</span></span>

* <span data-ttu-id="5192e-210">Zastępuje *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="5192e-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="5192e-211">Obsługuje wszystkie zadania uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5192e-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="5192e-212">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="5192e-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="5192e-213">W ASP.NET Core MVC, routing atrybutu jest uwzględniany domyślnie, gdy <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> jest wywoływana w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5192e-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="5192e-214">Następujące `UseMvc` wywołanie zastępuje plik */webapiconfig.cs App_Start* projektu *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="5192e-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="5192e-215">Migrowanie modeli i kontrolerów</span><span class="sxs-lookup"><span data-stu-id="5192e-215">Migrate models and controllers</span></span>

<span data-ttu-id="5192e-216">Poniższy kod przedstawia `ProductsController` aktualizację ASP.NET Core:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="5192e-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="5192e-217">Zaktualizuj `ProductsController` dla ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5192e-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="5192e-218">Skopiuj *Kontrolery/ProductsController. cs* z oryginalnego projektu do nowego.</span><span class="sxs-lookup"><span data-stu-id="5192e-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="5192e-219">Skopiuj folder *modele* z oryginalnego projektu do nowego.</span><span class="sxs-lookup"><span data-stu-id="5192e-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="5192e-220">Zmień przestrzeń nazw skopiowanych plików na `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="5192e-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="5192e-221">Zaktualizuj `using ProductsApp.Models;` instrukcję do `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="5192e-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="5192e-222">Następujące składniki nie istnieją w ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5192e-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="5192e-223">Klasa `ApiController`</span><span class="sxs-lookup"><span data-stu-id="5192e-223">`ApiController` class</span></span>
* <span data-ttu-id="5192e-224">`System.Web.Http`obszaru</span><span class="sxs-lookup"><span data-stu-id="5192e-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="5192e-225">`IHttpActionResult`interfejsu</span><span class="sxs-lookup"><span data-stu-id="5192e-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="5192e-226">Wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="5192e-226">Make the following changes:</span></span>

1. <span data-ttu-id="5192e-227">Zmień `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="5192e-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="5192e-228">Dodaj `using Microsoft.AspNetCore.Mvc;` , aby rozwiązać `ControllerBase` odwołanie.</span><span class="sxs-lookup"><span data-stu-id="5192e-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="5192e-229">Usuń folder `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="5192e-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="5192e-230">Zmień `GetProduct` Typ zwracany akcji z `IHttpActionResult` na `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="5192e-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="5192e-231">Uprość `GetProduct` instrukcję akcji `return` do następujących:</span><span class="sxs-lookup"><span data-stu-id="5192e-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="5192e-232">Configure routing (Konfigurowanie routingu)</span><span class="sxs-lookup"><span data-stu-id="5192e-232">Configure routing</span></span>

<span data-ttu-id="5192e-233">Skonfiguruj Routing w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5192e-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="5192e-234">Oznacz `ProductsController` klasę następującymi atrybutami:</span><span class="sxs-lookup"><span data-stu-id="5192e-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="5192e-235">Poprzedni [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atrybut konfiguruje wzorzec routingu atrybutu kontrolera.</span><span class="sxs-lookup"><span data-stu-id="5192e-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="5192e-236">Ten [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut powoduje, że atrybut routingu wymaga dla wszystkich akcji w tym kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="5192e-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="5192e-237">Routing atrybutów obsługuje tokeny, takie jak `[controller]` i `[action]` .</span><span class="sxs-lookup"><span data-stu-id="5192e-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="5192e-238">W czasie wykonywania każdy token jest zastępowany odpowiednio nazwą kontrolera lub akcji, do którego zastosowano atrybut.</span><span class="sxs-lookup"><span data-stu-id="5192e-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="5192e-239">Tokeny zmniejszają liczbę ciągów magicznych w projekcie.</span><span class="sxs-lookup"><span data-stu-id="5192e-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="5192e-240">Tokeny zapewniają również, że trasy pozostają zsynchronizowane z odpowiednimi kontrolerami i akcjami, gdy stosowane są automatyczne refaktoryzacje zmiany nazwy.</span><span class="sxs-lookup"><span data-stu-id="5192e-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="5192e-241">Ustaw tryb zgodności projektu na ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="5192e-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="5192e-242">Poprzednia zmiana:</span><span class="sxs-lookup"><span data-stu-id="5192e-242">The preceding change:</span></span>

    * <span data-ttu-id="5192e-243">Jest wymagany do użycia `[ApiController]` atrybutu na poziomie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="5192e-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="5192e-244">Umożliwia potencjalne uszkodzenie zachowań wprowadzonych w ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="5192e-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="5192e-245">Włącz żądania HTTP GET do `ProductController` akcji:</span><span class="sxs-lookup"><span data-stu-id="5192e-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="5192e-246">Zastosuj [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut do `GetAllProducts` akcji.</span><span class="sxs-lookup"><span data-stu-id="5192e-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="5192e-247">Zastosuj `[HttpGet("{id}")]` atrybut do `GetProduct` akcji.</span><span class="sxs-lookup"><span data-stu-id="5192e-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="5192e-248">Uruchom zmigrowany projekt i przejdź do `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="5192e-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="5192e-249">Zostanie wyświetlona pełna lista trzech produktów.</span><span class="sxs-lookup"><span data-stu-id="5192e-249">A full list of three products appears.</span></span> <span data-ttu-id="5192e-250">Przejdź do `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="5192e-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="5192e-251">Zostanie wyświetlony pierwszy produkt.</span><span class="sxs-lookup"><span data-stu-id="5192e-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="5192e-252">Podkładka zgodności</span><span class="sxs-lookup"><span data-stu-id="5192e-252">Compatibility shim</span></span>

<span data-ttu-id="5192e-253">Biblioteka [Microsoft. AspNetCore. MVC. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) zawiera podkładkę zgodności do przenoszenia projektów interfejsu API sieci Web ASP.NET 4. x do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5192e-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="5192e-254">Podkładka zgodności rozszerza ASP.NET Core do obsługi wielu konwencji z ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="5192e-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="5192e-255">Przykładowy port podany wcześniej w tym dokumencie jest wystarczająco mały, że podkładka zgodności była niepotrzebna.</span><span class="sxs-lookup"><span data-stu-id="5192e-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="5192e-256">W przypadku większych projektów użycie podkładki zgodności może być przydatne do tymczasowego mostkowania przerwy między ASP.NET Core i ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="5192e-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="5192e-257">Podkładka zgodności dla interfejsu API sieci Web ma służyć jako tymczasowa miara do obsługi migrowania dużych ASP.NETych projektów interfejsu API sieci Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5192e-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="5192e-258">W miarę upływu czasu projekty należy zaktualizować tak, aby korzystały z wzorców ASP.NET Core zamiast polegać na podkładki zgodności.</span><span class="sxs-lookup"><span data-stu-id="5192e-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="5192e-259">Funkcje zgodności zawarte w programie `Microsoft.AspNetCore.Mvc.WebApiCompatShim` obejmują:</span><span class="sxs-lookup"><span data-stu-id="5192e-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="5192e-260">Dodaje `ApiController` Typ, aby nie trzeba było aktualizować typów podstawowych kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="5192e-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="5192e-261">Włącza powiązanie modelu internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5192e-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="5192e-262">ASP.NET Core funkcji powiązania modelu MVC podobnie jak w przypadku ASP.NET 4. x MVC 5, domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5192e-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="5192e-263">W ramach podkładki zgodności zmiany powiązania modelu są bardziej podobne do Konwencji powiązań modelu ASP.NET 4. x sieci Web.</span><span class="sxs-lookup"><span data-stu-id="5192e-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="5192e-264">Na przykład typy złożone są automatycznie powiązane z treścią żądania.</span><span class="sxs-lookup"><span data-stu-id="5192e-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="5192e-265">Rozszerza powiązanie modelu, aby akcje kontrolera mogły przyjmować parametry typu `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="5192e-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="5192e-266">Dodaje elementy formatujące komunikaty umożliwiające działanie zwracające wyniki typu `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="5192e-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="5192e-267">Dodaje dodatkowe metody odpowiedzi, które mogą być używane przez działania Web API 2 do obsłużenia odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="5192e-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="5192e-268">`HttpResponseMessage`wytwornic</span><span class="sxs-lookup"><span data-stu-id="5192e-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="5192e-269">Metody wyniku akcji:</span><span class="sxs-lookup"><span data-stu-id="5192e-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="5192e-270">Dodaje wystąpienie `IContentNegotiator` do kontenera iniekcji zależności aplikacji i udostępnia typy powiązane z negocjowaniem zawartości z [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="5192e-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="5192e-271">Przykłady takich typów obejmują `DefaultContentNegotiator` i `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="5192e-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="5192e-272">Aby użyć podkładek zgodności:</span><span class="sxs-lookup"><span data-stu-id="5192e-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="5192e-273">Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="5192e-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="5192e-274">Zarejestruj usługi podkładki zgodności z kontenerem DI aplikacji, wywołując `services.AddMvc().AddWebApiConventions()` w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5192e-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="5192e-275">Zdefiniuj trasy specyficzne dla interfejsu API sieci Web za pomocą polecenia `MapWebApiRoute` `IRouteBuilder` w `IApplicationBuilder.UseMvc` wywołaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5192e-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5192e-276">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5192e-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
