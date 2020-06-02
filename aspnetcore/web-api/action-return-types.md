---
title: typ zwracany akcji kontrolera w ASP.NET Core Web API Author: scottaddie Description: informacje na temat używania różnej metody akcji kontrolera zwraca typy w ASP.NET Core internetowy interfejs API.
MS. Author: scaddie MS. Custom: MVC MS. Date: 02/03/2020 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: Web-API/Action-Return-Types

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Typy zwracane akcje kontrolera w ASP.NET Core Web API

Przez [Scott Addie](https://github.com/scottaddie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

ASP.NET Core oferuje następujące opcje dla zwracanych typów akcji kontrolera API sieci Web:

::: moniker range=">= aspnetcore-2.1"

* [Określony typ](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Określony typ](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Ten dokument wyjaśnia, kiedy jest najbardziej odpowiedni do użycia każdego typu zwracanego.

## <a name="specific-type"></a>Określony typ

Najprostsza akcja zwraca typ danych pierwotnych lub złożonych (na przykład `string` Typ obiektu niestandardowego). Rozważ poniższą akcję, która zwraca kolekcję obiektów niestandardowych `Product` :

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Bez znanych warunków zabezpieczających przed wykonaniem akcji, zwrócenie określonego typu może być wystarczające. Poprzednia akcja nie akceptuje żadnych parametrów, więc Walidacja ograniczeń parametrów nie jest konieczna.

Jeśli istnieje wiele typów zwracanych, często można mieszać <xref:Microsoft.AspNetCore.Mvc.ActionResult> Typ zwracany z pierwotnym lub złożonym typem zwracanym. [IActionResult](#iactionresult-type) lub [ActionResult \<T> ](#actionresultt-type) są niezbędne do zaspokojenia tego typu akcji. W tym dokumencie podano kilka przykładów dla wielu typów zwracanych.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Zwróć interfejs IEnumerable \<T> lub IAsyncEnumerable\<T>

W ASP.NET Core 2,2 i wcześniejszych zwraca <xref:System.Collections.Generic.IEnumerable%601> z akcji wyniki w iteracji kolekcji synchronicznej przez serializator. Wynikiem jest blokowanie wywołań i potencjalna blokada puli wątków. Aby zilustrować, Wyobraź sobie, że Entity Framework (EF) rdzeń jest używany do potrzeb dostępu do danych interfejsu API sieci Web. Zwracany typ następującego działania jest synchronicznie wyliczany podczas serializacji:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Aby uniknąć synchronicznego wyliczania i blokowania w bazie danych w ASP.NET Core 2,2 i wcześniejszych, wywołaj `ToListAsync` :

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

W ASP.NET Core 3,0 i nowszych zwraca `IAsyncEnumerable<T>` z akcji:

* Nie jest już wynikiem iteracji synchronicznej.
* Staną się efektywnym sposobem powrotu <xref:System.Collections.Generic.IEnumerable%601> .

ASP.NET Core 3,0 i później buforuje wynik poniższego działania przed dostarczeniem go do serializatora:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Rozważ zadeklarowanie zwracanego typu sygnatury akcji jako `IAsyncEnumerable<T>` do zagwarantowania asynchronicznej iteracji. Ostatecznie tryb iteracji jest oparty na zwracanym konkretnym typie. MVC automatycznie buforuje każdy konkretny typ, który implementuje `IAsyncEnumerable<T>` .

Należy wziąć pod uwagę następujące działania, które zwracają rekordy produktu z ceną sprzedaży `IEnumerable<Product>` :

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

`IAsyncEnumerable<Product>`Odpowiednik poprzedniej akcji to:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Obie powyższe akcje nie są blokowane w przypadku ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Typ IActionResult

<xref:Microsoft.AspNetCore.Mvc.IActionResult>Typ zwracany jest odpowiedni, gdy `ActionResult` w akcji jest możliwe wiele typów zwracanych. `ActionResult`Typy reprezentują różne kody stanu HTTP. Każda Klasa nieabstrakcyjna będąca wynikiem `ActionResult` kwalifikatora jest prawidłowym typem zwracanym. Niektóre typowe typy zwracane w tej kategorii to <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) i <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternatywnie, wygodne metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase> klasy mogą być używane do zwracania `ActionResult` typów z akcji. Na przykład, `return BadRequest();` jest to Skrócona forma `return new BadRequestResult();` .

Ponieważ istnieje wiele typów zwracanych i ścieżek w tym typie akcji, konieczne jest zliberalizowane użycie [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) atrybutu. Ten atrybut zawiera bardziej szczegółowe szczegóły odpowiedzi dla stron pomocy interfejsu API sieci Web generowanych przez narzędzia takie jak [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]`wskazuje znane typy i kody stanu HTTP do zwrócenia przez akcję.

### <a name="synchronous-action"></a>Akcja synchroniczna

Rozważ wykonanie następującej akcji synchronicznej, w której istnieją dwa możliwe typy zwracane:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

W poprzedniej akcji:

* Kod stanu 404 jest zwracany, gdy produkt reprezentowany przez `id` nie istnieje w źródłowym magazynie danych. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Wygodna metoda jest wywoływana jako skrót dla `return new NotFoundResult();` .
* Kod stanu 200 jest zwracany wraz z `Product` obiektem, gdy produkt jest istniejący. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>Wygodna metoda jest wywoływana jako skrót dla `return new OkObjectResult(product);` .

### <a name="asynchronous-action"></a>Akcja asynchroniczna

Rozważ wykonanie następującej akcji asynchronicznej, w której istnieją dwa możliwe typy zwracane:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

W poprzedniej akcji:

* Kod stanu 400 jest zwracany, gdy opis produktu zawiera "widżet XYZ". <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Wygodna metoda jest wywoływana jako skrót dla `return new BadRequestResult();` .
* Kod stanu 201 jest generowany przez <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> wygodną metodę podczas tworzenia produktu. Alternatywą dla wywołania `CreatedAtAction` jest `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` . W tej ścieżce kodu `Product` obiekt jest podany w treści odpowiedzi. `Location`Podano nagłówek odpowiedzi zawierający nowo utworzony adres URL produktu.

Na przykład następujący model wskazuje, że żądania muszą zawierać `Name` `Description` właściwości i. Niedostarczenie `Name` i `Description` w żądaniu powoduje niepowodzenie walidacji modelu.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Jeśli [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut w ASP.NET Core 2,1 lub nowszej zostanie zastosowany, błędy walidacji modelu powodują powstanie kodu stanu 400. Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>\<T>Typ ActionResult

ASP.NET Core 2,1 wprowadził typ [zwracany \<T> ActionResult](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) dla akcji kontrolera interfejsu API sieci Web. Umożliwia zwracanie typu pochodnego <xref:Microsoft.AspNetCore.Mvc.ActionResult> lub zwracanego [określonego typu](#specific-type). `ActionResult<T>`oferuje następujące korzyści dotyczące [typu IActionResult](#iactionresult-type):

* [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Właściwość atrybutu może być wykluczona. Na przykład `[ProducesResponseType(200, Type = typeof(Product))]` uproszczony dla `[ProducesResponseType(200)]` . Oczekiwany typ zwracany akcji jest wywnioskowany na podstawie `T` `ActionResult<T>` .
* [Operatory rzutowania niejawnego](/dotnet/csharp/language-reference/keywords/implicit) obsługują konwersję obu `T` i `ActionResult` do `ActionResult<T>` . `T`Konwertuje na <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , co oznacza, że `return new ObjectResult(T);` jest uproszczony dla `return T;` .

Język C# nie obsługuje operatorów rzutowania niejawnego w interfejsach. W związku z tym konwersja interfejsu na konkretny typ jest niezbędna do użycia `ActionResult<T>` . Na przykład użycie `IEnumerable` w programie w poniższym przykładzie nie działa:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Jedną z opcji naprawy powyższego kodu jest zwrócenie `_repository.GetProducts().ToList();` .

Większość akcji ma określony typ zwracany. Podczas wykonywania akcji mogą wystąpić nieoczekiwane warunki. w takim przypadku określony typ nie jest zwracany. Na przykład parametr wejściowy akcji może kończyć się niepowodzeniem walidacji modelu. W takim przypadku często zwraca odpowiedni `ActionResult` Typ zamiast określonego typu.

### <a name="synchronous-action"></a>Akcja synchroniczna

Należy rozważyć akcję synchroniczną, w której są dostępne dwa typy zwracane:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

W poprzedniej akcji:

* Kod stanu 404 jest zwracany, gdy produkt nie istnieje w bazie danych.
* Kod stanu 200 jest zwracany wraz z odpowiednim `Product` obiektem, gdy produkt istnieje. Przed ASP.NET Core 2,1, `return product;` wiersz musi być `return Ok(product);` .

### <a name="asynchronous-action"></a>Akcja asynchroniczna

Rozważ akcję asynchroniczną, w której istnieją dwa możliwe typy zwracane:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

W poprzedniej akcji:

* Kod stanu 400 ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) jest zwracany przez środowisko uruchomieniowe ASP.NET Core w przypadku:
  * [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Atrybut został zastosowany i walidacja modelu kończy się niepowodzeniem.
  * Opis produktu zawiera "widżet XYZ".
* Kod stanu 201 jest generowany przez <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodę podczas tworzenia produktu. W tej ścieżce kodu `Product` obiekt jest podany w treści odpowiedzi. `Location`Podano nagłówek odpowiedzi zawierający nowo utworzony adres URL produktu.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
