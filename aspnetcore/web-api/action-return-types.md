---
<span data-ttu-id="e7750-101">title: typ zwracany akcji kontrolera w ASP.NET Core Web API Author: scottaddie Description: informacje na temat używania różnej metody akcji kontrolera zwraca typy w ASP.NET Core internetowy interfejs API.</span><span class="sxs-lookup"><span data-stu-id="e7750-101">title: Controller action return types in ASP.NET Core web API author: scottaddie description: Learn about using the various controller action method return types in an ASP.NET Core web API.</span></span>
<span data-ttu-id="e7750-102">MS. Author: scaddie MS. Custom: MVC MS. Date: 02/03/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e7750-102">ms.author: scaddie ms.custom: mvc ms.date: 02/03/2020 no-loc:</span></span>
- <span data-ttu-id="e7750-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7750-103">'Blazor'</span></span>
- <span data-ttu-id="e7750-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7750-104">'Identity'</span></span>
- <span data-ttu-id="e7750-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7750-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7750-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7750-106">'Razor'</span></span>
- <span data-ttu-id="e7750-107">SignalRIdentyfikator UID: Web-API/Action-Return-Types</span><span class="sxs-lookup"><span data-stu-id="e7750-107">'SignalR' uid: web-api/action-return-types</span></span>

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="e7750-108">Typy zwracane akcje kontrolera w ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="e7750-108">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="e7750-109">Przez [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e7750-109">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e7750-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7750-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e7750-111">ASP.NET Core oferuje następujące opcje dla zwracanych typów akcji kontrolera API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="e7750-111">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="e7750-112">Określony typ</span><span class="sxs-lookup"><span data-stu-id="e7750-112">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="e7750-113">IActionResult</span><span class="sxs-lookup"><span data-stu-id="e7750-113">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="e7750-114">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="e7750-114">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="e7750-115">Określony typ</span><span class="sxs-lookup"><span data-stu-id="e7750-115">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="e7750-116">IActionResult</span><span class="sxs-lookup"><span data-stu-id="e7750-116">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="e7750-117">Ten dokument wyjaśnia, kiedy jest najbardziej odpowiedni do użycia każdego typu zwracanego.</span><span class="sxs-lookup"><span data-stu-id="e7750-117">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="e7750-118">Określony typ</span><span class="sxs-lookup"><span data-stu-id="e7750-118">Specific type</span></span>

<span data-ttu-id="e7750-119">Najprostsza akcja zwraca typ danych pierwotnych lub złożonych (na przykład `string` Typ obiektu niestandardowego).</span><span class="sxs-lookup"><span data-stu-id="e7750-119">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="e7750-120">Rozważ poniższą akcję, która zwraca kolekcję obiektów niestandardowych `Product` :</span><span class="sxs-lookup"><span data-stu-id="e7750-120">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="e7750-121">Bez znanych warunków zabezpieczających przed wykonaniem akcji, zwrócenie określonego typu może być wystarczające.</span><span class="sxs-lookup"><span data-stu-id="e7750-121">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="e7750-122">Poprzednia akcja nie akceptuje żadnych parametrów, więc Walidacja ograniczeń parametrów nie jest konieczna.</span><span class="sxs-lookup"><span data-stu-id="e7750-122">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="e7750-123">Jeśli istnieje wiele typów zwracanych, często można mieszać <xref:Microsoft.AspNetCore.Mvc.ActionResult> Typ zwracany z pierwotnym lub złożonym typem zwracanym.</span><span class="sxs-lookup"><span data-stu-id="e7750-123">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="e7750-124">[IActionResult](#iactionresult-type) lub [ActionResult \<T> ](#actionresultt-type) są niezbędne do zaspokojenia tego typu akcji.</span><span class="sxs-lookup"><span data-stu-id="e7750-124">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="e7750-125">W tym dokumencie podano kilka przykładów dla wielu typów zwracanych.</span><span class="sxs-lookup"><span data-stu-id="e7750-125">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="e7750-126">Zwróć interfejs IEnumerable \<T> lub IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="e7750-126">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="e7750-127">W ASP.NET Core 2,2 i wcześniejszych zwraca <xref:System.Collections.Generic.IEnumerable%601> z akcji wyniki w iteracji kolekcji synchronicznej przez serializator.</span><span class="sxs-lookup"><span data-stu-id="e7750-127">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="e7750-128">Wynikiem jest blokowanie wywołań i potencjalna blokada puli wątków.</span><span class="sxs-lookup"><span data-stu-id="e7750-128">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="e7750-129">Aby zilustrować, Wyobraź sobie, że Entity Framework (EF) rdzeń jest używany do potrzeb dostępu do danych interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="e7750-129">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="e7750-130">Zwracany typ następującego działania jest synchronicznie wyliczany podczas serializacji:</span><span class="sxs-lookup"><span data-stu-id="e7750-130">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="e7750-131">Aby uniknąć synchronicznego wyliczania i blokowania w bazie danych w ASP.NET Core 2,2 i wcześniejszych, wywołaj `ToListAsync` :</span><span class="sxs-lookup"><span data-stu-id="e7750-131">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="e7750-132">W ASP.NET Core 3,0 i nowszych zwraca `IAsyncEnumerable<T>` z akcji:</span><span class="sxs-lookup"><span data-stu-id="e7750-132">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="e7750-133">Nie jest już wynikiem iteracji synchronicznej.</span><span class="sxs-lookup"><span data-stu-id="e7750-133">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="e7750-134">Staną się efektywnym sposobem powrotu <xref:System.Collections.Generic.IEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="e7750-134">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="e7750-135">ASP.NET Core 3,0 i później buforuje wynik poniższego działania przed dostarczeniem go do serializatora:</span><span class="sxs-lookup"><span data-stu-id="e7750-135">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="e7750-136">Rozważ zadeklarowanie zwracanego typu sygnatury akcji jako `IAsyncEnumerable<T>` do zagwarantowania asynchronicznej iteracji.</span><span class="sxs-lookup"><span data-stu-id="e7750-136">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="e7750-137">Ostatecznie tryb iteracji jest oparty na zwracanym konkretnym typie.</span><span class="sxs-lookup"><span data-stu-id="e7750-137">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="e7750-138">MVC automatycznie buforuje każdy konkretny typ, który implementuje `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="e7750-138">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="e7750-139">Należy wziąć pod uwagę następujące działania, które zwracają rekordy produktu z ceną sprzedaży `IEnumerable<Product>` :</span><span class="sxs-lookup"><span data-stu-id="e7750-139">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="e7750-140">`IAsyncEnumerable<Product>`Odpowiednik poprzedniej akcji to:</span><span class="sxs-lookup"><span data-stu-id="e7750-140">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="e7750-141">Obie powyższe akcje nie są blokowane w przypadku ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="e7750-141">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="e7750-142">Typ IActionResult</span><span class="sxs-lookup"><span data-stu-id="e7750-142">IActionResult type</span></span>

<span data-ttu-id="e7750-143"><xref:Microsoft.AspNetCore.Mvc.IActionResult>Typ zwracany jest odpowiedni, gdy `ActionResult` w akcji jest możliwe wiele typów zwracanych.</span><span class="sxs-lookup"><span data-stu-id="e7750-143">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="e7750-144">`ActionResult`Typy reprezentują różne kody stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="e7750-144">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="e7750-145">Każda Klasa nieabstrakcyjna będąca wynikiem `ActionResult` kwalifikatora jest prawidłowym typem zwracanym.</span><span class="sxs-lookup"><span data-stu-id="e7750-145">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="e7750-146">Niektóre typowe typy zwracane w tej kategorii to <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) i <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="e7750-146">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="e7750-147">Alternatywnie, wygodne metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase> klasy mogą być używane do zwracania `ActionResult` typów z akcji.</span><span class="sxs-lookup"><span data-stu-id="e7750-147">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="e7750-148">Na przykład, `return BadRequest();` jest to Skrócona forma `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="e7750-148">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="e7750-149">Ponieważ istnieje wiele typów zwracanych i ścieżek w tym typie akcji, konieczne jest zliberalizowane użycie [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="e7750-149">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="e7750-150">Ten atrybut zawiera bardziej szczegółowe szczegóły odpowiedzi dla stron pomocy interfejsu API sieci Web generowanych przez narzędzia takie jak [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="e7750-150">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="e7750-151">`[ProducesResponseType]`wskazuje znane typy i kody stanu HTTP do zwrócenia przez akcję.</span><span class="sxs-lookup"><span data-stu-id="e7750-151">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="e7750-152">Akcja synchroniczna</span><span class="sxs-lookup"><span data-stu-id="e7750-152">Synchronous action</span></span>

<span data-ttu-id="e7750-153">Rozważ wykonanie następującej akcji synchronicznej, w której istnieją dwa możliwe typy zwracane:</span><span class="sxs-lookup"><span data-stu-id="e7750-153">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="e7750-154">W poprzedniej akcji:</span><span class="sxs-lookup"><span data-stu-id="e7750-154">In the preceding action:</span></span>

* <span data-ttu-id="e7750-155">Kod stanu 404 jest zwracany, gdy produkt reprezentowany przez `id` nie istnieje w źródłowym magazynie danych.</span><span class="sxs-lookup"><span data-stu-id="e7750-155">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="e7750-156"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Wygodna metoda jest wywoływana jako skrót dla `return new NotFoundResult();` .</span><span class="sxs-lookup"><span data-stu-id="e7750-156">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="e7750-157">Kod stanu 200 jest zwracany wraz z `Product` obiektem, gdy produkt jest istniejący.</span><span class="sxs-lookup"><span data-stu-id="e7750-157">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="e7750-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>Wygodna metoda jest wywoływana jako skrót dla `return new OkObjectResult(product);` .</span><span class="sxs-lookup"><span data-stu-id="e7750-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="e7750-159">Akcja asynchroniczna</span><span class="sxs-lookup"><span data-stu-id="e7750-159">Asynchronous action</span></span>

<span data-ttu-id="e7750-160">Rozważ wykonanie następującej akcji asynchronicznej, w której istnieją dwa możliwe typy zwracane:</span><span class="sxs-lookup"><span data-stu-id="e7750-160">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="e7750-161">W poprzedniej akcji:</span><span class="sxs-lookup"><span data-stu-id="e7750-161">In the preceding action:</span></span>

* <span data-ttu-id="e7750-162">Kod stanu 400 jest zwracany, gdy opis produktu zawiera "widżet XYZ".</span><span class="sxs-lookup"><span data-stu-id="e7750-162">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="e7750-163"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Wygodna metoda jest wywoływana jako skrót dla `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="e7750-163">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="e7750-164">Kod stanu 201 jest generowany przez <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> wygodną metodę podczas tworzenia produktu.</span><span class="sxs-lookup"><span data-stu-id="e7750-164">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="e7750-165">Alternatywą dla wywołania `CreatedAtAction` jest `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` .</span><span class="sxs-lookup"><span data-stu-id="e7750-165">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="e7750-166">W tej ścieżce kodu `Product` obiekt jest podany w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e7750-166">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="e7750-167">`Location`Podano nagłówek odpowiedzi zawierający nowo utworzony adres URL produktu.</span><span class="sxs-lookup"><span data-stu-id="e7750-167">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="e7750-168">Na przykład następujący model wskazuje, że żądania muszą zawierać `Name` `Description` właściwości i.</span><span class="sxs-lookup"><span data-stu-id="e7750-168">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="e7750-169">Niedostarczenie `Name` i `Description` w żądaniu powoduje niepowodzenie walidacji modelu.</span><span class="sxs-lookup"><span data-stu-id="e7750-169">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e7750-170">Jeśli [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut w ASP.NET Core 2,1 lub nowszej zostanie zastosowany, błędy walidacji modelu powodują powstanie kodu stanu 400.</span><span class="sxs-lookup"><span data-stu-id="e7750-170">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="e7750-171">Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="e7750-171">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="e7750-172">\<T>Typ ActionResult</span><span class="sxs-lookup"><span data-stu-id="e7750-172">ActionResult\<T> type</span></span>

<span data-ttu-id="e7750-173">ASP.NET Core 2,1 wprowadził typ [zwracany \<T> ActionResult](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) dla akcji kontrolera interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="e7750-173">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="e7750-174">Umożliwia zwracanie typu pochodnego <xref:Microsoft.AspNetCore.Mvc.ActionResult> lub zwracanego [określonego typu](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="e7750-174">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="e7750-175">`ActionResult<T>`oferuje następujące korzyści dotyczące [typu IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="e7750-175">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="e7750-176">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Właściwość atrybutu może być wykluczona.</span><span class="sxs-lookup"><span data-stu-id="e7750-176">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="e7750-177">Na przykład `[ProducesResponseType(200, Type = typeof(Product))]` uproszczony dla `[ProducesResponseType(200)]` .</span><span class="sxs-lookup"><span data-stu-id="e7750-177">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="e7750-178">Oczekiwany typ zwracany akcji jest wywnioskowany na podstawie `T` `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="e7750-178">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="e7750-179">[Operatory rzutowania niejawnego](/dotnet/csharp/language-reference/keywords/implicit) obsługują konwersję obu `T` i `ActionResult` do `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="e7750-179">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="e7750-180">`T`Konwertuje na <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , co oznacza, że `return new ObjectResult(T);` jest uproszczony dla `return T;` .</span><span class="sxs-lookup"><span data-stu-id="e7750-180">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="e7750-181">Język C# nie obsługuje operatorów rzutowania niejawnego w interfejsach.</span><span class="sxs-lookup"><span data-stu-id="e7750-181">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="e7750-182">W związku z tym konwersja interfejsu na konkretny typ jest niezbędna do użycia `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="e7750-182">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="e7750-183">Na przykład użycie `IEnumerable` w programie w poniższym przykładzie nie działa:</span><span class="sxs-lookup"><span data-stu-id="e7750-183">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="e7750-184">Jedną z opcji naprawy powyższego kodu jest zwrócenie `_repository.GetProducts().ToList();` .</span><span class="sxs-lookup"><span data-stu-id="e7750-184">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="e7750-185">Większość akcji ma określony typ zwracany.</span><span class="sxs-lookup"><span data-stu-id="e7750-185">Most actions have a specific return type.</span></span> <span data-ttu-id="e7750-186">Podczas wykonywania akcji mogą wystąpić nieoczekiwane warunki. w takim przypadku określony typ nie jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="e7750-186">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="e7750-187">Na przykład parametr wejściowy akcji może kończyć się niepowodzeniem walidacji modelu.</span><span class="sxs-lookup"><span data-stu-id="e7750-187">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="e7750-188">W takim przypadku często zwraca odpowiedni `ActionResult` Typ zamiast określonego typu.</span><span class="sxs-lookup"><span data-stu-id="e7750-188">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="e7750-189">Akcja synchroniczna</span><span class="sxs-lookup"><span data-stu-id="e7750-189">Synchronous action</span></span>

<span data-ttu-id="e7750-190">Należy rozważyć akcję synchroniczną, w której są dostępne dwa typy zwracane:</span><span class="sxs-lookup"><span data-stu-id="e7750-190">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="e7750-191">W poprzedniej akcji:</span><span class="sxs-lookup"><span data-stu-id="e7750-191">In the preceding action:</span></span>

* <span data-ttu-id="e7750-192">Kod stanu 404 jest zwracany, gdy produkt nie istnieje w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e7750-192">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="e7750-193">Kod stanu 200 jest zwracany wraz z odpowiednim `Product` obiektem, gdy produkt istnieje.</span><span class="sxs-lookup"><span data-stu-id="e7750-193">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="e7750-194">Przed ASP.NET Core 2,1, `return product;` wiersz musi być `return Ok(product);` .</span><span class="sxs-lookup"><span data-stu-id="e7750-194">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="e7750-195">Akcja asynchroniczna</span><span class="sxs-lookup"><span data-stu-id="e7750-195">Asynchronous action</span></span>

<span data-ttu-id="e7750-196">Rozważ akcję asynchroniczną, w której istnieją dwa możliwe typy zwracane:</span><span class="sxs-lookup"><span data-stu-id="e7750-196">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="e7750-197">W poprzedniej akcji:</span><span class="sxs-lookup"><span data-stu-id="e7750-197">In the preceding action:</span></span>

* <span data-ttu-id="e7750-198">Kod stanu 400 ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) jest zwracany przez środowisko uruchomieniowe ASP.NET Core w przypadku:</span><span class="sxs-lookup"><span data-stu-id="e7750-198">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="e7750-199">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Atrybut został zastosowany i walidacja modelu kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="e7750-199">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="e7750-200">Opis produktu zawiera "widżet XYZ".</span><span class="sxs-lookup"><span data-stu-id="e7750-200">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="e7750-201">Kod stanu 201 jest generowany przez <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodę podczas tworzenia produktu.</span><span class="sxs-lookup"><span data-stu-id="e7750-201">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="e7750-202">W tej ścieżce kodu `Product` obiekt jest podany w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e7750-202">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="e7750-203">`Location`Podano nagłówek odpowiedzi zawierający nowo utworzony adres URL produktu.</span><span class="sxs-lookup"><span data-stu-id="e7750-203">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e7750-204">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e7750-204">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
