---
title: JsonPatch w interfejsie Web API ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obsługiwać żądania poprawek w formacie JSON w ASP.NET Core internetowym interfejsie API.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 4ed44a0fca9e0834a78e433cdd48cbd153c58666
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587856"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="6f6e8-103">JsonPatch w interfejsie Web API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f6e8-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="6f6e8-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6f6e8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f6e8-105">W tym artykule wyjaśniono, jak obsłużyć żądania poprawek w formacie JSON w ASP.NET Core interfejsie API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="6f6e8-106">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-106">Package installation</span></span>

<span data-ttu-id="6f6e8-107">Aby włączyć obsługę poprawek JSON w aplikacji, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="6f6e8-108">Zainstaluj [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="6f6e8-109">Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu wywołania <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="6f6e8-110">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="6f6e8-111">`AddNewtonsoftJson` jest zgodny z metodami rejestracji usługi MVC:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="6f6e8-112">Poprawka JSON, AddNewtonsoftJson i System.Text.Jsna</span><span class="sxs-lookup"><span data-stu-id="6f6e8-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="6f6e8-113">`AddNewtonsoftJson` zastępuje utworzone w oparciu o dane `System.Text.Json` wejściowe i wyjściowe elementy formatujące używane do formatowania **całej** zawartości JSON.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="6f6e8-114">Aby dodać obsługę poprawki JSON przy użyciu `Newtonsoft.Json` , pozostawiając inne elementy formatujące bez zmian, zaktualizuj `Startup.ConfigureServices` metodę projektu w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="6f6e8-115">Poprzedzający kod wymaga `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pakietu i następujących `using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="6f6e8-116">Poprawka metody żądania HTTP</span><span class="sxs-lookup"><span data-stu-id="6f6e8-116">PATCH HTTP request method</span></span>

<span data-ttu-id="6f6e8-117">Metody PUT i [patch](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="6f6e8-118">Różnica między nimi polega na tym, że zastępuje cały zasób, podczas gdy poprawka określa tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="6f6e8-119">Poprawka JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-119">JSON Patch</span></span>

<span data-ttu-id="6f6e8-120">[Poprawka JSON](https://tools.ietf.org/html/rfc6902) to format służący do określania aktualizacji, które mają zostać zastosowane do zasobu.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="6f6e8-121">Dokument poprawki JSON zawiera tablicę *operacji*.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="6f6e8-122">Każda operacja identyfikuje określony typ zmiany.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="6f6e8-123">Przykłady takich zmian obejmują dodanie elementu tablicy lub zastępowanie wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="6f6e8-124">Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu oraz wynik zastosowania operacji patch.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="6f6e8-125">Przykład zasobu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="6f6e8-126">Przykład poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="6f6e8-127">W powyższym kodzie JSON:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-127">In the preceding JSON:</span></span>

* <span data-ttu-id="6f6e8-128">`op`Właściwość wskazuje typ operacji.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="6f6e8-129">`path`Właściwość wskazuje element do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="6f6e8-130">`value`Właściwość zawiera nową wartość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="6f6e8-131">Zasób po zastosowaniu poprawki</span><span class="sxs-lookup"><span data-stu-id="6f6e8-131">Resource after patch</span></span>

<span data-ttu-id="6f6e8-132">Poniżej znajduje się zasób po zastosowaniu poprzedniego dokumentu poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="6f6e8-133">Zmiany wprowadzone przez zastosowanie dokumentu poprawki JSON do zasobu są niepodzielne.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="6f6e8-134">Jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="6f6e8-135">Składnia ścieżki</span><span class="sxs-lookup"><span data-stu-id="6f6e8-135">Path syntax</span></span>

<span data-ttu-id="6f6e8-136">Właściwość [Path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="6f6e8-137">Na przykład `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="6f6e8-138">W celu określenia elementów tablicy są używane indeksy oparte na wartości zero.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="6f6e8-139">Pierwszy element `addresses` tablicy będzie miał wartość `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="6f6e8-140">Na `add` koniec tablicy Użyj łącznika ( `-` ), a nie numeru indeksu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="6f6e8-141">Operacje</span><span class="sxs-lookup"><span data-stu-id="6f6e8-141">Operations</span></span>

<span data-ttu-id="6f6e8-142">W poniższej tabeli przedstawiono obsługiwane operacje zgodnie z definicją w [specyfikacji poprawek JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="6f6e8-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="6f6e8-143">Operacja</span><span class="sxs-lookup"><span data-stu-id="6f6e8-143">Operation</span></span>  | <span data-ttu-id="6f6e8-144">Uwagi</span><span class="sxs-lookup"><span data-stu-id="6f6e8-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="6f6e8-145">Dodaj właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-145">Add a property or array element.</span></span> <span data-ttu-id="6f6e8-146">Dla istniejącej właściwości: Ustaw wartość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="6f6e8-147">Usuń właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="6f6e8-148">Takie same, jak `remove` następuje `add` w tej samej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="6f6e8-149">Takie samo jak `remove` ze źródła, a następnie `add` do miejsca docelowego przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="6f6e8-150">Takie samo jak `add` miejsce docelowe przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="6f6e8-151">Zwróć kod stanu sukcesu, jeśli wartość jest równa `path` = `value` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="6f6e8-152">Poprawka JSON w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f6e8-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="6f6e8-153">ASP.NET Core implementacja poprawki JSON znajduje się w pakiecie NuGet [Microsoft.AspNetCore.Jsonpatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="6f6e8-154">Kod metody akcji</span><span class="sxs-lookup"><span data-stu-id="6f6e8-154">Action method code</span></span>

<span data-ttu-id="6f6e8-155">W kontrolerze interfejsu API Metoda akcji dla poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="6f6e8-156">Ma adnotację z `HttpPatch` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="6f6e8-157">Akceptuje element `JsonPatchDocument<T>` , zazwyczaj z `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="6f6e8-158">Wywołuje `ApplyTo` dokument poprawki, aby zastosować zmiany.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="6f6e8-159">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="6f6e8-160">Ten kod z przykładowej aplikacji współdziała z następującym `Customer` modelem:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="6f6e8-161">Przykładowa Metoda akcji:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-161">The sample action method:</span></span>

* <span data-ttu-id="6f6e8-162">Konstruuje a `Customer` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="6f6e8-163">Stosuje poprawkę.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-163">Applies the patch.</span></span>
* <span data-ttu-id="6f6e8-164">Zwraca wynik w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="6f6e8-165">W rzeczywistej aplikacji kod pobiera dane z magazynu, takiego jak baza danych, i aktualizuje bazę danych po zastosowaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="6f6e8-166">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-166">Model state</span></span>

<span data-ttu-id="6f6e8-167">Poprzednia metoda akcji przykład wywołuje Przeciążenie `ApplyTo` , które ma stan modelu jako jeden z jego parametrów.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="6f6e8-168">W przypadku tej opcji można odbierać komunikaty o błędach w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="6f6e8-169">Poniższy przykład przedstawia treść nieprawidłowej odpowiedzi na żądanie 400 dla `test` operacji:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="6f6e8-170">Obiekty dynamiczne</span><span class="sxs-lookup"><span data-stu-id="6f6e8-170">Dynamic objects</span></span>

<span data-ttu-id="6f6e8-171">W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="6f6e8-172">Operacja dodawania</span><span class="sxs-lookup"><span data-stu-id="6f6e8-172">The add operation</span></span>

* <span data-ttu-id="6f6e8-173">Jeśli `path` wskazuje element tablicy: wstawia nowy element przed określony przez `path` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="6f6e8-174">Jeśli `path` wskazuje Właściwość: ustawia wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="6f6e8-175">Jeśli `path` wskazuje nieistniejącą lokalizację:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="6f6e8-176">Jeśli zasób do poprawki jest obiektem dynamicznym: dodaje właściwość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="6f6e8-177">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="6f6e8-178">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i dodaje `Order` obiekt na końcu `Orders` tablicy.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="6f6e8-179">Operacja usuwania</span><span class="sxs-lookup"><span data-stu-id="6f6e8-179">The remove operation</span></span>

* <span data-ttu-id="6f6e8-180">Jeśli `path` wskazuje element tablicy: usuwa element.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="6f6e8-181">Jeśli `path` wskazuje Właściwość:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="6f6e8-182">Jeśli zasób do poprawki jest obiektem dynamicznym: usuwa właściwość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="6f6e8-183">Jeśli zasób do poprawki jest obiektem statycznym:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="6f6e8-184">Jeśli właściwość dopuszcza wartość null: ustawia ją na wartość null.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="6f6e8-185">Jeśli właściwość nie dopuszcza wartości null, ustawia ją na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="6f6e8-186">Następujący Przykładowa poprawka zestawów dokumentów `CustomerName` do wartości null i usuwa `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="6f6e8-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="6f6e8-187">Operacja zamiany</span><span class="sxs-lookup"><span data-stu-id="6f6e8-187">The replace operation</span></span>

<span data-ttu-id="6f6e8-188">Ta operacja jest funkcjonalnie taka sama jak `remove` po nim `add` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="6f6e8-189">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i zastępuje ją `Orders[0]` nowym `Order` obiektem:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="6f6e8-190">Operacja przenoszenia</span><span class="sxs-lookup"><span data-stu-id="6f6e8-190">The move operation</span></span>

* <span data-ttu-id="6f6e8-191">Jeśli `path` wskazuje element tablicy: kopiuje `from` element do lokalizacji `path` elementu, a następnie uruchamia `remove` operację na `from` elemencie.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="6f6e8-192">Jeśli `path` wskazuje Właściwość: kopiuje wartość `from` właściwości do `path` właściwości, a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="6f6e8-193">Jeśli `path` wskazuje na nieistniejącą Właściwość:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="6f6e8-194">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="6f6e8-195">Jeśli zasób do poprawki jest obiektem dynamicznym: kopiuje `from` Właściwość do lokalizacji wskazywanej przez `path` , a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="6f6e8-196">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-196">The following sample patch document:</span></span>

* <span data-ttu-id="6f6e8-197">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6f6e8-198">Ustawia `Orders[0].OrderName` wartość null.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="6f6e8-199">Przenosi `Orders[1]` do przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="6f6e8-200">Operacja kopiowania</span><span class="sxs-lookup"><span data-stu-id="6f6e8-200">The copy operation</span></span>

<span data-ttu-id="6f6e8-201">Ta operacja jest funkcjonalnie taka sama jak `move` operacja bez `remove` kroku końcowego.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="6f6e8-202">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-202">The following sample patch document:</span></span>

* <span data-ttu-id="6f6e8-203">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6f6e8-204">Wstawia kopię `Orders[1]` przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="6f6e8-205">Operacja testowa</span><span class="sxs-lookup"><span data-stu-id="6f6e8-205">The test operation</span></span>

<span data-ttu-id="6f6e8-206">Jeśli wartość w lokalizacji wskazywanej przez różni `path` się od wartości podanej w `value` , żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="6f6e8-207">W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki zakończyły się powodzeniem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="6f6e8-208">`test`Operacja jest często używana do zapobiegania aktualizacji, gdy występuje konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="6f6e8-209">Następujący przykładowy dokument poprawek nie ma wpływu, jeśli początkowa wartość `CustomerName` to "Jan", ponieważ test zakończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="6f6e8-210">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-210">Get the code</span></span>

<span data-ttu-id="6f6e8-211">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="6f6e8-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="6f6e8-212">([Jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6f6e8-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6f6e8-213">Aby przetestować przykład, uruchom aplikację i Wyślij żądania HTTP z następującymi ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="6f6e8-214">Adres URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="6f6e8-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="6f6e8-215">Metoda HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="6f6e8-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="6f6e8-216">Nagłówki `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="6f6e8-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="6f6e8-217">Treść: Skopiuj i wklej jeden z przykładów dokumentu poprawki JSON z folderu projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f6e8-218">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="6f6e8-218">Additional resources</span></span>

* [<span data-ttu-id="6f6e8-219">IETF RFC 5789 Specyfikacja metody poprawek</span><span class="sxs-lookup"><span data-stu-id="6f6e8-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="6f6e8-220">IETF RFC 6902 — Specyfikacja poprawek JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="6f6e8-221">IETF RFC 6901 JSON Format ścieżki poprawek</span><span class="sxs-lookup"><span data-stu-id="6f6e8-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="6f6e8-222">[Dokumentacja poprawek JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="6f6e8-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="6f6e8-223">Zawiera linki do zasobów do tworzenia dokumentów poprawek JSON.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="6f6e8-224">ASP.NET Core kod źródłowy poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f6e8-225">W tym artykule wyjaśniono, jak obsłużyć żądania poprawek w formacie JSON w ASP.NET Core interfejsie API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="6f6e8-226">Poprawka metody żądania HTTP</span><span class="sxs-lookup"><span data-stu-id="6f6e8-226">PATCH HTTP request method</span></span>

<span data-ttu-id="6f6e8-227">Metody PUT i [patch](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="6f6e8-228">Różnica między nimi polega na tym, że zastępuje cały zasób, podczas gdy poprawka określa tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="6f6e8-229">Poprawka JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-229">JSON Patch</span></span>

<span data-ttu-id="6f6e8-230">[Poprawka JSON](https://tools.ietf.org/html/rfc6902) to format służący do określania aktualizacji, które mają zostać zastosowane do zasobu.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="6f6e8-231">Dokument poprawki JSON zawiera tablicę *operacji*.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="6f6e8-232">Każda operacja identyfikuje określony typ zmiany, na przykład Dodaj element tablicy lub Zastąp wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="6f6e8-233">Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu oraz wynik zastosowania operacji patch.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="6f6e8-234">Przykład zasobu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="6f6e8-235">Przykład poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="6f6e8-236">W powyższym kodzie JSON:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-236">In the preceding JSON:</span></span>

* <span data-ttu-id="6f6e8-237">`op`Właściwość wskazuje typ operacji.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="6f6e8-238">`path`Właściwość wskazuje element do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="6f6e8-239">`value`Właściwość zawiera nową wartość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="6f6e8-240">Zasób po zastosowaniu poprawki</span><span class="sxs-lookup"><span data-stu-id="6f6e8-240">Resource after patch</span></span>

<span data-ttu-id="6f6e8-241">Poniżej znajduje się zasób po zastosowaniu poprzedniego dokumentu poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="6f6e8-242">Zmiany wprowadzone przez zastosowanie dokumentu poprawek JSON do zasobu są niepodzielne: Jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="6f6e8-243">Składnia ścieżki</span><span class="sxs-lookup"><span data-stu-id="6f6e8-243">Path syntax</span></span>

<span data-ttu-id="6f6e8-244">Właściwość [Path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="6f6e8-245">Na przykład `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="6f6e8-246">W celu określenia elementów tablicy są używane indeksy oparte na wartości zero.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="6f6e8-247">Pierwszy element `addresses` tablicy będzie miał wartość `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="6f6e8-248">Na `add` koniec tablicy Użyj łącznika (-), a nie numeru indeksu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="6f6e8-249">Operacje</span><span class="sxs-lookup"><span data-stu-id="6f6e8-249">Operations</span></span>

<span data-ttu-id="6f6e8-250">W poniższej tabeli przedstawiono obsługiwane operacje zgodnie z definicją w [specyfikacji poprawek JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="6f6e8-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="6f6e8-251">Operacja</span><span class="sxs-lookup"><span data-stu-id="6f6e8-251">Operation</span></span>  | <span data-ttu-id="6f6e8-252">Uwagi</span><span class="sxs-lookup"><span data-stu-id="6f6e8-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="6f6e8-253">Dodaj właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-253">Add a property or array element.</span></span> <span data-ttu-id="6f6e8-254">Dla istniejącej właściwości: Ustaw wartość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="6f6e8-255">Usuń właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="6f6e8-256">Takie same, jak `remove` następuje `add` w tej samej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="6f6e8-257">Takie samo jak `remove` ze źródła, a następnie `add` do miejsca docelowego przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="6f6e8-258">Takie samo jak `add` miejsce docelowe przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="6f6e8-259">Zwróć kod stanu sukcesu, jeśli wartość jest równa `path` = `value` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="6f6e8-260">JsonPatch w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f6e8-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="6f6e8-261">ASP.NET Core implementacja poprawki JSON znajduje się w pakiecie NuGet [Microsoft.AspNetCore.Jsonpatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="6f6e8-262">Pakiet jest zawarty w pakiecie [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="6f6e8-263">Kod metody akcji</span><span class="sxs-lookup"><span data-stu-id="6f6e8-263">Action method code</span></span>

<span data-ttu-id="6f6e8-264">W kontrolerze interfejsu API Metoda akcji dla poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="6f6e8-265">Ma adnotację z `HttpPatch` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="6f6e8-266">Akceptuje element `JsonPatchDocument<T>` , zazwyczaj z `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="6f6e8-267">Wywołuje `ApplyTo` dokument poprawki, aby zastosować zmiany.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="6f6e8-268">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="6f6e8-269">Ten kod z przykładowej aplikacji współdziała z następującym `Customer` modelem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="6f6e8-270">Przykładowa Metoda akcji:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-270">The sample action method:</span></span>

* <span data-ttu-id="6f6e8-271">Konstruuje a `Customer` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="6f6e8-272">Stosuje poprawkę.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-272">Applies the patch.</span></span>
* <span data-ttu-id="6f6e8-273">Zwraca wynik w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="6f6e8-274">W rzeczywistej aplikacji kod pobiera dane z magazynu, takiego jak baza danych, i aktualizuje bazę danych po zastosowaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="6f6e8-275">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-275">Model state</span></span>

<span data-ttu-id="6f6e8-276">Poprzednia metoda akcji przykład wywołuje Przeciążenie `ApplyTo` , które ma stan modelu jako jeden z jego parametrów.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="6f6e8-277">W przypadku tej opcji można odbierać komunikaty o błędach w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="6f6e8-278">Poniższy przykład przedstawia treść nieprawidłowej odpowiedzi na żądanie 400 dla `test` operacji:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="6f6e8-279">Obiekty dynamiczne</span><span class="sxs-lookup"><span data-stu-id="6f6e8-279">Dynamic objects</span></span>

<span data-ttu-id="6f6e8-280">W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="6f6e8-281">Operacja dodawania</span><span class="sxs-lookup"><span data-stu-id="6f6e8-281">The add operation</span></span>

* <span data-ttu-id="6f6e8-282">Jeśli `path` wskazuje element tablicy: wstawia nowy element przed określony przez `path` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="6f6e8-283">Jeśli `path` wskazuje Właściwość: ustawia wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="6f6e8-284">Jeśli `path` wskazuje nieistniejącą lokalizację:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="6f6e8-285">Jeśli zasób do poprawki jest obiektem dynamicznym: dodaje właściwość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="6f6e8-286">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="6f6e8-287">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i dodaje `Order` obiekt na końcu `Orders` tablicy.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="6f6e8-288">Operacja usuwania</span><span class="sxs-lookup"><span data-stu-id="6f6e8-288">The remove operation</span></span>

* <span data-ttu-id="6f6e8-289">Jeśli `path` wskazuje element tablicy: usuwa element.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="6f6e8-290">Jeśli `path` wskazuje Właściwość:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="6f6e8-291">Jeśli zasób do poprawki jest obiektem dynamicznym: usuwa właściwość.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="6f6e8-292">Jeśli zasób do poprawki jest obiektem statycznym:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="6f6e8-293">Jeśli właściwość dopuszcza wartość null: ustawia ją na wartość null.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="6f6e8-294">Jeśli właściwość nie dopuszcza wartości null, ustawia ją na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="6f6e8-295">Poniższy Przykładowa poprawka zestawów dokumentów `CustomerName` do wartości null i usuwa `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="6f6e8-296">Operacja zamiany</span><span class="sxs-lookup"><span data-stu-id="6f6e8-296">The replace operation</span></span>

<span data-ttu-id="6f6e8-297">Ta operacja jest funkcjonalnie taka sama jak `remove` po nim `add` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="6f6e8-298">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i zastępuje ją `Orders[0]` nowym `Order` obiektem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="6f6e8-299">Operacja przenoszenia</span><span class="sxs-lookup"><span data-stu-id="6f6e8-299">The move operation</span></span>

* <span data-ttu-id="6f6e8-300">Jeśli `path` wskazuje element tablicy: kopiuje `from` element do lokalizacji `path` elementu, a następnie uruchamia `remove` operację na `from` elemencie.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="6f6e8-301">Jeśli `path` wskazuje Właściwość: kopiuje wartość `from` właściwości do `path` właściwości, a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="6f6e8-302">Jeśli `path` wskazuje na nieistniejącą Właściwość:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="6f6e8-303">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="6f6e8-304">Jeśli zasób do poprawki jest obiektem dynamicznym: kopiuje `from` Właściwość do lokalizacji wskazywanej przez `path` , a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="6f6e8-305">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-305">The following sample patch document:</span></span>

* <span data-ttu-id="6f6e8-306">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6f6e8-307">Ustawia `Orders[0].OrderName` wartość null.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="6f6e8-308">Przenosi `Orders[1]` do przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="6f6e8-309">Operacja kopiowania</span><span class="sxs-lookup"><span data-stu-id="6f6e8-309">The copy operation</span></span>

<span data-ttu-id="6f6e8-310">Ta operacja jest funkcjonalnie taka sama jak `move` operacja bez `remove` kroku końcowego.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="6f6e8-311">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-311">The following sample patch document:</span></span>

* <span data-ttu-id="6f6e8-312">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6f6e8-313">Wstawia kopię `Orders[1]` przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="6f6e8-314">Operacja testowa</span><span class="sxs-lookup"><span data-stu-id="6f6e8-314">The test operation</span></span>

<span data-ttu-id="6f6e8-315">Jeśli wartość w lokalizacji wskazywanej przez różni `path` się od wartości podanej w `value` , żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="6f6e8-316">W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki zakończyły się powodzeniem.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="6f6e8-317">`test`Operacja jest często używana do zapobiegania aktualizacji, gdy występuje konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="6f6e8-318">Następujący przykładowy dokument poprawek nie ma wpływu, jeśli początkowa wartość `CustomerName` to "Jan", ponieważ test zakończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="6f6e8-319">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="6f6e8-319">Get the code</span></span>

<span data-ttu-id="6f6e8-320">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="6f6e8-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="6f6e8-321">([Jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6f6e8-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6f6e8-322">Aby przetestować przykład, uruchom aplikację i Wyślij żądania HTTP z następującymi ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="6f6e8-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="6f6e8-323">Adres URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="6f6e8-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="6f6e8-324">Metoda HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="6f6e8-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="6f6e8-325">Nagłówki `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="6f6e8-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="6f6e8-326">Treść: Skopiuj i wklej jeden z przykładów dokumentu poprawki JSON z folderu projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6f6e8-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f6e8-327">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="6f6e8-327">Additional resources</span></span>

* [<span data-ttu-id="6f6e8-328">IETF RFC 5789 Specyfikacja metody poprawek</span><span class="sxs-lookup"><span data-stu-id="6f6e8-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="6f6e8-329">IETF RFC 6902 — Specyfikacja poprawek JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="6f6e8-330">IETF RFC 6901 JSON Format ścieżki poprawek</span><span class="sxs-lookup"><span data-stu-id="6f6e8-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="6f6e8-331">[Dokumentacja poprawek JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="6f6e8-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="6f6e8-332">Zawiera linki do zasobów do tworzenia dokumentów poprawek JSON.</span><span class="sxs-lookup"><span data-stu-id="6f6e8-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="6f6e8-333">ASP.NET Core kod źródłowy poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="6f6e8-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end
