---
title: JsonPatch w ASP.NET Core web API
author: rick-anderson
description: Dowiedz się, jak obsługiwać żądania poprawek JSON w internetowym interfejsie API ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625880"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="4199e-103">JsonPatch w ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="4199e-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="4199e-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4199e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4199e-105">W tym artykule wyjaśniono, jak obsługiwać żądania poprawek JSON w interfejsie API sieci web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4199e-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="4199e-106">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="4199e-106">Package installation</span></span>

<span data-ttu-id="4199e-107">Aby włączyć obsługę poprawek JSON w aplikacji, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="4199e-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="4199e-108">Zainstaluj pakiet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4199e-108">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="4199e-109">Zaktualizuj `Startup.ConfigureServices` metodę <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>projektu, aby wywołać .</span><span class="sxs-lookup"><span data-stu-id="4199e-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="4199e-110">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4199e-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="4199e-111">`AddNewtonsoftJson`jest kompatybilny z metodami rejestracji usługi MVC:</span><span class="sxs-lookup"><span data-stu-id="4199e-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="4199e-112">Łatka JSON, AddNewtonsoftJson i System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="4199e-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="4199e-113">`AddNewtonsoftJson`zastępuje `System.Text.Json`formatery wejściowe i wyjściowe oparte na podstawie formatowania używane do formatowania **całej** zawartości JSON.</span><span class="sxs-lookup"><span data-stu-id="4199e-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="4199e-114">Aby dodać obsługę poprawki `Newtonsoft.Json`JSON przy użyciu , pozostawiając inne formaterów `Startup.ConfigureServices` bez zmian, zaktualizuj metodę projektu w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="4199e-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="4199e-115">Poprzedni kod wymaga `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pakietu i `using` następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="4199e-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="4199e-116">Metoda żądania HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="4199e-116">PATCH HTTP request method</span></span>

<span data-ttu-id="4199e-117">Metody PUT i [PATCH](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu.</span><span class="sxs-lookup"><span data-stu-id="4199e-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="4199e-118">Różnica między nimi polega na tym, że PUT zastępuje cały zasób, podczas gdy patch określa tylko zmiany.</span><span class="sxs-lookup"><span data-stu-id="4199e-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="4199e-119">Łatka JSON</span><span class="sxs-lookup"><span data-stu-id="4199e-119">JSON Patch</span></span>

<span data-ttu-id="4199e-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) to format określania aktualizacji, które mają być stosowane do zasobu.</span><span class="sxs-lookup"><span data-stu-id="4199e-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="4199e-121">Dokument poprawki JSON ma tablicę *operacji*.</span><span class="sxs-lookup"><span data-stu-id="4199e-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="4199e-122">Każda operacja identyfikuje określony typ zmiany.</span><span class="sxs-lookup"><span data-stu-id="4199e-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="4199e-123">Przykłady takich zmian obejmują dodawanie elementu tablicy lub zastąpienie wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="4199e-124">Na przykład następujące dokumenty JSON reprezentują zasób, dokument JSON Patch dla zasobu i wynik zastosowania operacji poprawki.</span><span class="sxs-lookup"><span data-stu-id="4199e-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="4199e-125">Przykład zasobu</span><span class="sxs-lookup"><span data-stu-id="4199e-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="4199e-126">Przykład poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="4199e-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="4199e-127">W poprzednim JSON:</span><span class="sxs-lookup"><span data-stu-id="4199e-127">In the preceding JSON:</span></span>

* <span data-ttu-id="4199e-128">Właściwość `op` wskazuje typ operacji.</span><span class="sxs-lookup"><span data-stu-id="4199e-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="4199e-129">Właściwość `path` wskazuje element do aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="4199e-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="4199e-130">Właściwość `value` zawiera nową wartość.</span><span class="sxs-lookup"><span data-stu-id="4199e-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="4199e-131">Zasób po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="4199e-131">Resource after patch</span></span>

<span data-ttu-id="4199e-132">Oto zasób po zastosowaniu poprzedniego dokumentu JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="4199e-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="4199e-133">Zmiany wprowadzone przez zastosowanie dokumentu JSON Patch do zasobu są niepodzielne.</span><span class="sxs-lookup"><span data-stu-id="4199e-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="4199e-134">Jeśli jakakolwiek operacja na liście nie powiedzie się, nie jest stosowana żadna operacja na liście.</span><span class="sxs-lookup"><span data-stu-id="4199e-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="4199e-135">Składnia ścieżki</span><span class="sxs-lookup"><span data-stu-id="4199e-135">Path syntax</span></span>

<span data-ttu-id="4199e-136">Właściwość [path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami.</span><span class="sxs-lookup"><span data-stu-id="4199e-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="4199e-137">Na przykład `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="4199e-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="4199e-138">Indeksy oparte na wartościach zerowych są używane do określania elementów tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="4199e-139">Pierwszy element tablicy `addresses` będzie `/addresses/0`w .</span><span class="sxs-lookup"><span data-stu-id="4199e-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="4199e-140">Aby `add` zakończyć tablicę, należy użyć`-`łącznika ( ) `/addresses/-`zamiast numeru indeksu: .</span><span class="sxs-lookup"><span data-stu-id="4199e-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="4199e-141">Operacje</span><span class="sxs-lookup"><span data-stu-id="4199e-141">Operations</span></span>

<span data-ttu-id="4199e-142">W poniższej tabeli przedstawiono obsługiwane operacje zdefiniowane w [specyfikacji JSON Patch:](https://tools.ietf.org/html/rfc6902)</span><span class="sxs-lookup"><span data-stu-id="4199e-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="4199e-143">Operacja</span><span class="sxs-lookup"><span data-stu-id="4199e-143">Operation</span></span>  | <span data-ttu-id="4199e-144">Uwagi</span><span class="sxs-lookup"><span data-stu-id="4199e-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="4199e-145">Dodaj właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-145">Add a property or array element.</span></span> <span data-ttu-id="4199e-146">Dla istniejącej właściwości: ustaw wartość.</span><span class="sxs-lookup"><span data-stu-id="4199e-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="4199e-147">Usuń właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="4199e-148">Tak `remove` samo `add` jak w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="4199e-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="4199e-149">Tak `remove` samo jak `add` ze źródła, po którym następuje miejsce docelowe przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="4199e-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="4199e-150">Tak `add` samo jak w przypadku miejsca docelowego przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="4199e-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="4199e-151">Zwraca kod stanu sukcesu, jeśli wartość w `path` = pod warunkiem `value`.</span><span class="sxs-lookup"><span data-stu-id="4199e-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="4199e-152">Łatka JSON w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4199e-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="4199e-153">Implementacja ASP.NET Core poprawki JSON jest dostępna w pakiecie [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4199e-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="4199e-154">Kod metody akcji</span><span class="sxs-lookup"><span data-stu-id="4199e-154">Action method code</span></span>

<span data-ttu-id="4199e-155">W kontrolerze interfejsu API: metoda akcji dla poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="4199e-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="4199e-156">Jest opisywany z `HttpPatch` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="4199e-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="4199e-157">Akceptuje , `JsonPatchDocument<T>`zazwyczaj z `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="4199e-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="4199e-158">Wzywa `ApplyTo` dokument poprawki do zastosowania zmian.</span><span class="sxs-lookup"><span data-stu-id="4199e-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="4199e-159">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="4199e-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="4199e-160">Ten kod z przykładowej aplikacji `Customer` działa z następującym modelem:</span><span class="sxs-lookup"><span data-stu-id="4199e-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="4199e-161">Przykładowa metoda działania:</span><span class="sxs-lookup"><span data-stu-id="4199e-161">The sample action method:</span></span>

* <span data-ttu-id="4199e-162">Konstruuje `Customer`plik .</span><span class="sxs-lookup"><span data-stu-id="4199e-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="4199e-163">Stosuje poprawkę.</span><span class="sxs-lookup"><span data-stu-id="4199e-163">Applies the patch.</span></span>
* <span data-ttu-id="4199e-164">Zwraca wynik w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="4199e-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="4199e-165">W prawdziwej aplikacji kod będzie pobierać dane z magazynu, takich jak baza danych i zaktualizować bazę danych po zastosowaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="4199e-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="4199e-166">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="4199e-166">Model state</span></span>

<span data-ttu-id="4199e-167">W poprzednim przykładzie metody akcji `ApplyTo` wywołuje przeciążenie tego przyjmuje stan modelu jako jeden z jego parametrów.</span><span class="sxs-lookup"><span data-stu-id="4199e-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="4199e-168">Za pomocą tej opcji można otrzymywać komunikaty o błędach w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="4199e-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="4199e-169">Poniższy przykład przedstawia treść odpowiedzi 400 Bad `test` Request dla operacji:</span><span class="sxs-lookup"><span data-stu-id="4199e-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="4199e-170">Obiekty dynamiczne</span><span class="sxs-lookup"><span data-stu-id="4199e-170">Dynamic objects</span></span>

<span data-ttu-id="4199e-171">Poniższa metoda akcji pokazuje, jak zastosować poprawkę do obiektu dynamicznego:</span><span class="sxs-lookup"><span data-stu-id="4199e-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="4199e-172">Operacja dodawania</span><span class="sxs-lookup"><span data-stu-id="4199e-172">The add operation</span></span>

* <span data-ttu-id="4199e-173">Jeśli `path` wskazuje element tablicy: wstawia nowy `path`element przed elementem określonym przez .</span><span class="sxs-lookup"><span data-stu-id="4199e-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="4199e-174">Jeśli `path` wskazuje na właściwość: ustawia wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="4199e-175">Jeśli `path` wskazuje nieistniejącą lokalizację:</span><span class="sxs-lookup"><span data-stu-id="4199e-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="4199e-176">Jeśli zasób do poprawki jest obiekt dynamiczny: dodaje właściwość.</span><span class="sxs-lookup"><span data-stu-id="4199e-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="4199e-177">Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="4199e-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="4199e-178">Poniższy przykładowy dokument poprawki `CustomerName` ustawia `Order` wartość i dodaje `Orders` obiekt na końcu tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="4199e-179">Operacja usuwania</span><span class="sxs-lookup"><span data-stu-id="4199e-179">The remove operation</span></span>

* <span data-ttu-id="4199e-180">Jeśli `path` wskazuje element tablicy: usuwa element.</span><span class="sxs-lookup"><span data-stu-id="4199e-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="4199e-181">Jeśli `path` wskazuje na właściwość:</span><span class="sxs-lookup"><span data-stu-id="4199e-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="4199e-182">Jeśli zasób do poprawki jest obiekt dynamiczny: usuwa właściwość.</span><span class="sxs-lookup"><span data-stu-id="4199e-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="4199e-183">Jeśli zasób do poprawki jest obiektem statycznym:</span><span class="sxs-lookup"><span data-stu-id="4199e-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="4199e-184">Jeśli właściwość jest nullable: ustawia go na null.</span><span class="sxs-lookup"><span data-stu-id="4199e-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="4199e-185">Jeśli właściwość nie może być nullowa, ustawia ją na `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="4199e-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="4199e-186">Następujący przykładowy dokument `CustomerName` poprawki ustawia `Orders[0]`wartość null i usuwa:</span><span class="sxs-lookup"><span data-stu-id="4199e-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="4199e-187">Operacja wymiany</span><span class="sxs-lookup"><span data-stu-id="4199e-187">The replace operation</span></span>

<span data-ttu-id="4199e-188">Ta operacja jest funkcjonalnie `remove` taka `add`sama jak po .</span><span class="sxs-lookup"><span data-stu-id="4199e-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="4199e-189">Następujący przykładowy dokument poprawki `CustomerName` ustawia `Orders[0]`wartość i `Order` zastępuje go nowym obiektem:</span><span class="sxs-lookup"><span data-stu-id="4199e-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="4199e-190">Operacja przenoszenia</span><span class="sxs-lookup"><span data-stu-id="4199e-190">The move operation</span></span>

* <span data-ttu-id="4199e-191">Jeśli `path` wskazuje element tablicy: `from` kopiuje `path` element do `remove` lokalizacji elementu, a następnie uruchamia operację na elemencie. `from`</span><span class="sxs-lookup"><span data-stu-id="4199e-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="4199e-192">Jeśli `path` wskazuje właściwość: `from` kopiuje `path` wartość właściwości do `remove` właściwości, `from` a następnie uruchamia operację na właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="4199e-193">Jeśli `path` wskazuje nieistniejącą właściwość:</span><span class="sxs-lookup"><span data-stu-id="4199e-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="4199e-194">Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="4199e-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="4199e-195">Jeśli zasób do poprawki jest `from` obiektem dynamicznym: `path`kopiuje `remove` właściwość `from` do lokalizacji wskazanej przez program , a następnie uruchamia operację we właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="4199e-196">Następujący przykładowy dokument poprawki:</span><span class="sxs-lookup"><span data-stu-id="4199e-196">The following sample patch document:</span></span>

* <span data-ttu-id="4199e-197">Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .</span><span class="sxs-lookup"><span data-stu-id="4199e-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4199e-198">Ustawia `Orders[0].OrderName` wartość null.</span><span class="sxs-lookup"><span data-stu-id="4199e-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="4199e-199">Przechodzi `Orders[1]` do `Orders[0]`przed .</span><span class="sxs-lookup"><span data-stu-id="4199e-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="4199e-200">Operacja kopiowania</span><span class="sxs-lookup"><span data-stu-id="4199e-200">The copy operation</span></span>

<span data-ttu-id="4199e-201">Ta operacja jest funkcjonalnie `move` taka sama `remove` jak operacja bez ostatniego kroku.</span><span class="sxs-lookup"><span data-stu-id="4199e-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="4199e-202">Następujący przykładowy dokument poprawki:</span><span class="sxs-lookup"><span data-stu-id="4199e-202">The following sample patch document:</span></span>

* <span data-ttu-id="4199e-203">Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .</span><span class="sxs-lookup"><span data-stu-id="4199e-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4199e-204">Wstawia kopię `Orders[1]` `Orders[0]`przed .</span><span class="sxs-lookup"><span data-stu-id="4199e-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="4199e-205">Operacja testowa</span><span class="sxs-lookup"><span data-stu-id="4199e-205">The test operation</span></span>

<span data-ttu-id="4199e-206">Jeśli wartość w lokalizacji wskazanej przez `path` różni się `value`od wartości podanej w , żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="4199e-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="4199e-207">W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki w przeciwnym razie zakończyłyby się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="4199e-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="4199e-208">Operacja `test` jest często używana, aby zapobiec aktualizacji, gdy istnieje konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4199e-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="4199e-209">Następujący przykładowy dokument poprawki nie ma `CustomerName` wpływu, jeśli wartość początkowa jest "John", ponieważ test nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="4199e-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="4199e-210">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="4199e-210">Get the code</span></span>

<span data-ttu-id="4199e-211">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="4199e-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="4199e-212">(Jak[pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4199e-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4199e-213">Aby przetestować przykład, uruchom aplikację i wyślij żądania HTTP z następującymi ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="4199e-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="4199e-214">Adres url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="4199e-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="4199e-215">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="4199e-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="4199e-216">Nagłówka:`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="4199e-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="4199e-217">Treść: Skopiuj i wklej jedną z próbek dokumentu poprawki JSON z folderu projektu *JSON.*</span><span class="sxs-lookup"><span data-stu-id="4199e-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4199e-218">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4199e-218">Additional resources</span></span>

* [<span data-ttu-id="4199e-219">Specyfikacja metody IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="4199e-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="4199e-220">IETF RFC 6902 JSON Patch specyfikacja</span><span class="sxs-lookup"><span data-stu-id="4199e-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="4199e-221">Specyfikacja formatu ścieżki poprawki IETF RFC 6901 JSON</span><span class="sxs-lookup"><span data-stu-id="4199e-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="4199e-222">[Dokumentacja JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="4199e-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="4199e-223">Zawiera łącza do zasobów do tworzenia dokumentów JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="4199e-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="4199e-224">ASP.NET Podstawowy kod źródłowy JSON Patch</span><span class="sxs-lookup"><span data-stu-id="4199e-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4199e-225">W tym artykule wyjaśniono, jak obsługiwać żądania poprawek JSON w interfejsie API sieci web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4199e-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="4199e-226">Metoda żądania HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="4199e-226">PATCH HTTP request method</span></span>

<span data-ttu-id="4199e-227">Metody PUT i [PATCH](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu.</span><span class="sxs-lookup"><span data-stu-id="4199e-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="4199e-228">Różnica między nimi polega na tym, że PUT zastępuje cały zasób, podczas gdy patch określa tylko zmiany.</span><span class="sxs-lookup"><span data-stu-id="4199e-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="4199e-229">Łatka JSON</span><span class="sxs-lookup"><span data-stu-id="4199e-229">JSON Patch</span></span>

<span data-ttu-id="4199e-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) to format określania aktualizacji, które mają być stosowane do zasobu.</span><span class="sxs-lookup"><span data-stu-id="4199e-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="4199e-231">Dokument poprawki JSON ma tablicę *operacji*.</span><span class="sxs-lookup"><span data-stu-id="4199e-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="4199e-232">Każda operacja identyfikuje określonego typu zmiany, takich jak dodać element tablicy lub zastąpić wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="4199e-233">Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu i wynik zastosowania operacji poprawki.</span><span class="sxs-lookup"><span data-stu-id="4199e-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="4199e-234">Przykład zasobu</span><span class="sxs-lookup"><span data-stu-id="4199e-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="4199e-235">Przykład poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="4199e-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="4199e-236">W poprzednim JSON:</span><span class="sxs-lookup"><span data-stu-id="4199e-236">In the preceding JSON:</span></span>

* <span data-ttu-id="4199e-237">Właściwość `op` wskazuje typ operacji.</span><span class="sxs-lookup"><span data-stu-id="4199e-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="4199e-238">Właściwość `path` wskazuje element do aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="4199e-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="4199e-239">Właściwość `value` zawiera nową wartość.</span><span class="sxs-lookup"><span data-stu-id="4199e-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="4199e-240">Zasób po aktualizacji</span><span class="sxs-lookup"><span data-stu-id="4199e-240">Resource after patch</span></span>

<span data-ttu-id="4199e-241">Oto zasób po zastosowaniu poprzedniego dokumentu JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="4199e-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="4199e-242">Zmiany wprowadzone przez zastosowanie dokumentu JSON Patch do zasobu są niepodzielne: jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.</span><span class="sxs-lookup"><span data-stu-id="4199e-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="4199e-243">Składnia ścieżki</span><span class="sxs-lookup"><span data-stu-id="4199e-243">Path syntax</span></span>

<span data-ttu-id="4199e-244">Właściwość [path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami.</span><span class="sxs-lookup"><span data-stu-id="4199e-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="4199e-245">Na przykład `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="4199e-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="4199e-246">Indeksy oparte na wartościach zerowych są używane do określania elementów tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="4199e-247">Pierwszy element tablicy `addresses` będzie `/addresses/0`w .</span><span class="sxs-lookup"><span data-stu-id="4199e-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="4199e-248">Na `add` końcu tablicy należy użyć łącznika (-), a nie `/addresses/-`numeru indeksu: .</span><span class="sxs-lookup"><span data-stu-id="4199e-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="4199e-249">Operacje</span><span class="sxs-lookup"><span data-stu-id="4199e-249">Operations</span></span>

<span data-ttu-id="4199e-250">W poniższej tabeli przedstawiono obsługiwane operacje zdefiniowane w [specyfikacji JSON Patch:](https://tools.ietf.org/html/rfc6902)</span><span class="sxs-lookup"><span data-stu-id="4199e-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="4199e-251">Operacja</span><span class="sxs-lookup"><span data-stu-id="4199e-251">Operation</span></span>  | <span data-ttu-id="4199e-252">Uwagi</span><span class="sxs-lookup"><span data-stu-id="4199e-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="4199e-253">Dodaj właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-253">Add a property or array element.</span></span> <span data-ttu-id="4199e-254">Dla istniejącej właściwości: ustaw wartość.</span><span class="sxs-lookup"><span data-stu-id="4199e-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="4199e-255">Usuń właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="4199e-256">Tak `remove` samo `add` jak w tym samym miejscu.</span><span class="sxs-lookup"><span data-stu-id="4199e-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="4199e-257">Tak `remove` samo jak `add` ze źródła, po którym następuje miejsce docelowe przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="4199e-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="4199e-258">Tak `add` samo jak w przypadku miejsca docelowego przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="4199e-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="4199e-259">Zwraca kod stanu sukcesu, jeśli wartość w `path` = pod warunkiem `value`.</span><span class="sxs-lookup"><span data-stu-id="4199e-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="4199e-260">JsonPatch w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4199e-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="4199e-261">Implementacja ASP.NET Core poprawki JSON jest dostępna w pakiecie [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4199e-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="4199e-262">Pakiet znajduje się w metapakiecie [Microsoft.AspnetCore.App.](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="4199e-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="4199e-263">Kod metody akcji</span><span class="sxs-lookup"><span data-stu-id="4199e-263">Action method code</span></span>

<span data-ttu-id="4199e-264">W kontrolerze interfejsu API: metoda akcji dla poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="4199e-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="4199e-265">Jest opisywany z `HttpPatch` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="4199e-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="4199e-266">Akceptuje , `JsonPatchDocument<T>`zazwyczaj z `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="4199e-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="4199e-267">Wzywa `ApplyTo` dokument poprawki do zastosowania zmian.</span><span class="sxs-lookup"><span data-stu-id="4199e-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="4199e-268">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="4199e-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="4199e-269">Ten kod z przykładowej aplikacji `Customer` działa z następującym modelem.</span><span class="sxs-lookup"><span data-stu-id="4199e-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="4199e-270">Przykładowa metoda działania:</span><span class="sxs-lookup"><span data-stu-id="4199e-270">The sample action method:</span></span>

* <span data-ttu-id="4199e-271">Konstruuje `Customer`plik .</span><span class="sxs-lookup"><span data-stu-id="4199e-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="4199e-272">Stosuje poprawkę.</span><span class="sxs-lookup"><span data-stu-id="4199e-272">Applies the patch.</span></span>
* <span data-ttu-id="4199e-273">Zwraca wynik w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="4199e-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="4199e-274">W prawdziwej aplikacji kod będzie pobierać dane z magazynu, takich jak baza danych i zaktualizować bazę danych po zastosowaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="4199e-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="4199e-275">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="4199e-275">Model state</span></span>

<span data-ttu-id="4199e-276">W poprzednim przykładzie metody akcji `ApplyTo` wywołuje przeciążenie tego przyjmuje stan modelu jako jeden z jego parametrów.</span><span class="sxs-lookup"><span data-stu-id="4199e-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="4199e-277">Za pomocą tej opcji można otrzymywać komunikaty o błędach w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="4199e-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="4199e-278">Poniższy przykład przedstawia treść odpowiedzi 400 Bad `test` Request dla operacji:</span><span class="sxs-lookup"><span data-stu-id="4199e-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="4199e-279">Obiekty dynamiczne</span><span class="sxs-lookup"><span data-stu-id="4199e-279">Dynamic objects</span></span>

<span data-ttu-id="4199e-280">W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego.</span><span class="sxs-lookup"><span data-stu-id="4199e-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="4199e-281">Operacja dodawania</span><span class="sxs-lookup"><span data-stu-id="4199e-281">The add operation</span></span>

* <span data-ttu-id="4199e-282">Jeśli `path` wskazuje element tablicy: wstawia nowy `path`element przed elementem określonym przez .</span><span class="sxs-lookup"><span data-stu-id="4199e-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="4199e-283">Jeśli `path` wskazuje na właściwość: ustawia wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="4199e-284">Jeśli `path` wskazuje nieistniejącą lokalizację:</span><span class="sxs-lookup"><span data-stu-id="4199e-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="4199e-285">Jeśli zasób do poprawki jest obiekt dynamiczny: dodaje właściwość.</span><span class="sxs-lookup"><span data-stu-id="4199e-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="4199e-286">Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="4199e-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="4199e-287">Poniższy przykładowy dokument poprawki `CustomerName` ustawia `Order` wartość i dodaje `Orders` obiekt na końcu tablicy.</span><span class="sxs-lookup"><span data-stu-id="4199e-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="4199e-288">Operacja usuwania</span><span class="sxs-lookup"><span data-stu-id="4199e-288">The remove operation</span></span>

* <span data-ttu-id="4199e-289">Jeśli `path` wskazuje element tablicy: usuwa element.</span><span class="sxs-lookup"><span data-stu-id="4199e-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="4199e-290">Jeśli `path` wskazuje na właściwość:</span><span class="sxs-lookup"><span data-stu-id="4199e-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="4199e-291">Jeśli zasób do poprawki jest obiekt dynamiczny: usuwa właściwość.</span><span class="sxs-lookup"><span data-stu-id="4199e-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="4199e-292">Jeśli zasób do poprawki jest obiektem statycznym:</span><span class="sxs-lookup"><span data-stu-id="4199e-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="4199e-293">Jeśli właściwość jest nullable: ustawia go na null.</span><span class="sxs-lookup"><span data-stu-id="4199e-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="4199e-294">Jeśli właściwość nie może być nullowa, ustawia ją na `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="4199e-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="4199e-295">Poniższy przykładowy `CustomerName` dokument poprawki ustawia `Orders[0]`wartość null i usuwa .</span><span class="sxs-lookup"><span data-stu-id="4199e-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="4199e-296">Operacja wymiany</span><span class="sxs-lookup"><span data-stu-id="4199e-296">The replace operation</span></span>

<span data-ttu-id="4199e-297">Ta operacja jest funkcjonalnie `remove` taka `add`sama jak po .</span><span class="sxs-lookup"><span data-stu-id="4199e-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="4199e-298">Poniższy przykładowy dokument poprawki `CustomerName` ustawia `Orders[0]`wartość i `Order` zastępuje się nowym obiektem.</span><span class="sxs-lookup"><span data-stu-id="4199e-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="4199e-299">Operacja przenoszenia</span><span class="sxs-lookup"><span data-stu-id="4199e-299">The move operation</span></span>

* <span data-ttu-id="4199e-300">Jeśli `path` wskazuje element tablicy: `from` kopiuje `path` element do `remove` lokalizacji elementu, a następnie uruchamia operację na elemencie. `from`</span><span class="sxs-lookup"><span data-stu-id="4199e-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="4199e-301">Jeśli `path` wskazuje właściwość: `from` kopiuje `path` wartość właściwości do `remove` właściwości, `from` a następnie uruchamia operację na właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="4199e-302">Jeśli `path` wskazuje nieistniejącą właściwość:</span><span class="sxs-lookup"><span data-stu-id="4199e-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="4199e-303">Jeśli zasób do poprawki jest obiektstatyczny: żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="4199e-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="4199e-304">Jeśli zasób do poprawki jest `from` obiektem dynamicznym: `path`kopiuje `remove` właściwość `from` do lokalizacji wskazanej przez program , a następnie uruchamia operację we właściwości.</span><span class="sxs-lookup"><span data-stu-id="4199e-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="4199e-305">Następujący przykładowy dokument poprawki:</span><span class="sxs-lookup"><span data-stu-id="4199e-305">The following sample patch document:</span></span>

* <span data-ttu-id="4199e-306">Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .</span><span class="sxs-lookup"><span data-stu-id="4199e-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4199e-307">Ustawia `Orders[0].OrderName` wartość null.</span><span class="sxs-lookup"><span data-stu-id="4199e-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="4199e-308">Przechodzi `Orders[1]` do `Orders[0]`przed .</span><span class="sxs-lookup"><span data-stu-id="4199e-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="4199e-309">Operacja kopiowania</span><span class="sxs-lookup"><span data-stu-id="4199e-309">The copy operation</span></span>

<span data-ttu-id="4199e-310">Ta operacja jest funkcjonalnie `move` taka sama `remove` jak operacja bez ostatniego kroku.</span><span class="sxs-lookup"><span data-stu-id="4199e-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="4199e-311">Następujący przykładowy dokument poprawki:</span><span class="sxs-lookup"><span data-stu-id="4199e-311">The following sample patch document:</span></span>

* <span data-ttu-id="4199e-312">Kopiuje wartość `Orders[0].OrderName` `CustomerName`do .</span><span class="sxs-lookup"><span data-stu-id="4199e-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4199e-313">Wstawia kopię `Orders[1]` `Orders[0]`przed .</span><span class="sxs-lookup"><span data-stu-id="4199e-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="4199e-314">Operacja testowa</span><span class="sxs-lookup"><span data-stu-id="4199e-314">The test operation</span></span>

<span data-ttu-id="4199e-315">Jeśli wartość w lokalizacji wskazanej przez `path` różni się `value`od wartości podanej w , żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="4199e-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="4199e-316">W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki w przeciwnym razie zakończyłyby się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="4199e-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="4199e-317">Operacja `test` jest często używana, aby zapobiec aktualizacji, gdy istnieje konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4199e-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="4199e-318">Następujący przykładowy dokument poprawki nie ma `CustomerName` wpływu, jeśli wartość początkowa jest "John", ponieważ test nie powiedzie się:</span><span class="sxs-lookup"><span data-stu-id="4199e-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="4199e-319">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="4199e-319">Get the code</span></span>

<span data-ttu-id="4199e-320">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="4199e-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="4199e-321">(Jak[pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4199e-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4199e-322">Aby przetestować przykład, uruchom aplikację i wyślij żądania HTTP z następującymi ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="4199e-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="4199e-323">Adres url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="4199e-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="4199e-324">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="4199e-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="4199e-325">Nagłówka:`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="4199e-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="4199e-326">Treść: Skopiuj i wklej jedną z próbek dokumentu poprawki JSON z folderu projektu *JSON.*</span><span class="sxs-lookup"><span data-stu-id="4199e-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4199e-327">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4199e-327">Additional resources</span></span>

* [<span data-ttu-id="4199e-328">Specyfikacja metody IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="4199e-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="4199e-329">IETF RFC 6902 JSON Patch specyfikacja</span><span class="sxs-lookup"><span data-stu-id="4199e-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="4199e-330">Specyfikacja formatu ścieżki poprawki IETF RFC 6901 JSON</span><span class="sxs-lookup"><span data-stu-id="4199e-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="4199e-331">[Dokumentacja JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="4199e-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="4199e-332">Zawiera łącza do zasobów do tworzenia dokumentów JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="4199e-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="4199e-333">ASP.NET Podstawowy kod źródłowy JSON Patch</span><span class="sxs-lookup"><span data-stu-id="4199e-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
