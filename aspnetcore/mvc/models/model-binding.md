---
title: Powiązanie modelu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak działa powiązanie modelu w ASP.NET Core i jak dostosować jego zachowanie.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/model-binding
ms.openlocfilehash: 2e604cd1869ea077fc0465df91ec083b9db83763
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768973"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="f3d16-103">Powiązanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3d16-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3d16-104">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="f3d16-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f3d16-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="f3d16-106">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-106">What is Model binding</span></span>

<span data-ttu-id="f3d16-107">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="f3d16-108">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="f3d16-109">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="f3d16-110">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="f3d16-110">Model binding automates this process.</span></span> <span data-ttu-id="f3d16-111">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="f3d16-111">The model binding system:</span></span>

* <span data-ttu-id="f3d16-112">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="f3d16-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="f3d16-113">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="f3d16-114">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="f3d16-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="f3d16-115">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="f3d16-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="f3d16-116">Example</span></span>

<span data-ttu-id="f3d16-117">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="f3d16-118">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="f3d16-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="f3d16-119">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="f3d16-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="f3d16-120">Znajduje pierwszy parametr z `GetByID`, liczba całkowita o nazwie `id`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="f3d16-121">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="f3d16-122">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="f3d16-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="f3d16-123">Znajduje następny parametr elementu `GetByID`, wartość logiczna o nazwie `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="f3d16-124">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="f3d16-125">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="f3d16-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="f3d16-126">Konwertuje ciąg "true" na wartość logiczną `true`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="f3d16-127">Struktura następnie `GetById` wywołuje metodę, przekazując wartość 2 `id` dla parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="f3d16-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="f3d16-128">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="f3d16-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="f3d16-129">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="f3d16-130">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="f3d16-131">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="f3d16-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="f3d16-132">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="f3d16-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="f3d16-133">Obiekty docelowe</span><span class="sxs-lookup"><span data-stu-id="f3d16-133">Targets</span></span>

<span data-ttu-id="f3d16-134">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="f3d16-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="f3d16-135">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="f3d16-136">Parametry metody obsługi Razor stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="f3d16-137">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="f3d16-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="f3d16-138">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-138">[BindProperty] attribute</span></span>

<span data-ttu-id="f3d16-139">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="f3d16-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="f3d16-140">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-140">[BindProperties] attribute</span></span>

<span data-ttu-id="f3d16-141">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="f3d16-142">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="f3d16-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="f3d16-143">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="f3d16-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="f3d16-144">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="f3d16-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="f3d16-145">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="f3d16-146">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="f3d16-147">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="f3d16-148">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` właściwość na: `true`</span><span class="sxs-lookup"><span data-stu-id="f3d16-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="f3d16-149">Źródła</span><span class="sxs-lookup"><span data-stu-id="f3d16-149">Sources</span></span>

<span data-ttu-id="f3d16-150">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="f3d16-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="f3d16-151">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="f3d16-151">Form fields</span></span>
1. <span data-ttu-id="f3d16-152">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="f3d16-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="f3d16-153">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="f3d16-153">Route data</span></span>
1. <span data-ttu-id="f3d16-154">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="f3d16-154">Query string parameters</span></span>
1. <span data-ttu-id="f3d16-155">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="f3d16-155">Uploaded files</span></span>

<span data-ttu-id="f3d16-156">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="f3d16-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="f3d16-157">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="f3d16-157">There are a few exceptions:</span></span>

* <span data-ttu-id="f3d16-158">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="f3d16-159">Przekazane pliki są powiązane tylko z typami docelowymi `IFormFile` implementującymi `IEnumerable<IFormFile>`lub.</span><span class="sxs-lookup"><span data-stu-id="f3d16-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="f3d16-160">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="f3d16-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="f3d16-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="f3d16-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="f3d16-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="f3d16-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="f3d16-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="f3d16-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="f3d16-166">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="f3d16-166">These attributes:</span></span>

* <span data-ttu-id="f3d16-167">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="f3d16-168">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="f3d16-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="f3d16-169">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="f3d16-170">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="f3d16-171">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-171">[FromBody] attribute</span></span>

<span data-ttu-id="f3d16-172">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="f3d16-173">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="f3d16-174">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f3d16-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="f3d16-175">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="f3d16-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="f3d16-176">Na przykład Poniższa `Create` akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="f3d16-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="f3d16-177">`Pet` Klasa określa, że jej `Breed` właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="f3d16-178">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-178">In the preceding example:</span></span>

* <span data-ttu-id="f3d16-179">`[FromQuery]` Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="f3d16-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="f3d16-180">`Breed` Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="f3d16-181">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="f3d16-182">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="f3d16-183">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="f3d16-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="f3d16-184">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="f3d16-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="f3d16-185">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="f3d16-185">Additional sources</span></span>

<span data-ttu-id="f3d16-186">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="f3d16-187">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="f3d16-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="f3d16-188">Możesz na przykład potrzebować danych z plików cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="f3d16-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="f3d16-189">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="f3d16-189">To get data from a new source:</span></span>

* <span data-ttu-id="f3d16-190">Utwórz klasę implementującą `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="f3d16-191">Utwórz klasę implementującą `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="f3d16-192">Zarejestruj klasę fabryki w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="f3d16-193">Przykładowa aplikacja zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z plików cookie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="f3d16-194">Oto kod rejestracji w programie `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f3d16-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="f3d16-195">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="f3d16-196">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast. `Add`</span><span class="sxs-lookup"><span data-stu-id="f3d16-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="f3d16-197">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-197">No source for a model property</span></span>

<span data-ttu-id="f3d16-198">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="f3d16-199">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="f3d16-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="f3d16-200">Typy proste o wartości null są `null`ustawione na wartość.</span><span class="sxs-lookup"><span data-stu-id="f3d16-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="f3d16-201">Typy wartości, które nie są dopuszczane `default(T)`do wartości null, są ustawione na.</span><span class="sxs-lookup"><span data-stu-id="f3d16-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="f3d16-202">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="f3d16-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="f3d16-203">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="f3d16-204">Tablice są ustawiane `Array.Empty<T>()`na, z `byte[]` tą różnicą, `null`że tablice są ustawione na.</span><span class="sxs-lookup"><span data-stu-id="f3d16-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="f3d16-205">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="f3d16-206">Należy zauważyć, `[BindRequired]` że to zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="f3d16-207">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f3d16-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="f3d16-208">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="f3d16-208">Type conversion errors</span></span>

<span data-ttu-id="f3d16-209">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="f3d16-210">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="f3d16-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="f3d16-211">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="f3d16-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="f3d16-212">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="f3d16-213">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w Razor przeciwnym razie zostałyby przesłane do formularza stron.</span><span class="sxs-lookup"><span data-stu-id="f3d16-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="f3d16-214">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="f3d16-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="f3d16-215">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="f3d16-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="f3d16-216">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="f3d16-217">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="f3d16-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="f3d16-218">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="f3d16-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="f3d16-219">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="f3d16-220">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="f3d16-221">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="f3d16-222">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="f3d16-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="f3d16-223">Typy proste</span><span class="sxs-lookup"><span data-stu-id="f3d16-223">Simple types</span></span>

<span data-ttu-id="f3d16-224">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="f3d16-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="f3d16-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="f3d16-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="f3d16-226">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="f3d16-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="f3d16-227">Delikatn</span><span class="sxs-lookup"><span data-stu-id="f3d16-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="f3d16-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="f3d16-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="f3d16-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="f3d16-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="f3d16-230">Dokładności</span><span class="sxs-lookup"><span data-stu-id="f3d16-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="f3d16-231">Double</span><span class="sxs-lookup"><span data-stu-id="f3d16-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="f3d16-232">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="f3d16-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="f3d16-233">Ident</span><span class="sxs-lookup"><span data-stu-id="f3d16-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="f3d16-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="f3d16-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="f3d16-235">Single</span><span class="sxs-lookup"><span data-stu-id="f3d16-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="f3d16-236">Czasu</span><span class="sxs-lookup"><span data-stu-id="f3d16-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="f3d16-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="f3d16-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="f3d16-238">Adresu</span><span class="sxs-lookup"><span data-stu-id="f3d16-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="f3d16-239">Wersja</span><span class="sxs-lookup"><span data-stu-id="f3d16-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="f3d16-240">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="f3d16-240">Complex types</span></span>

<span data-ttu-id="f3d16-241">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="f3d16-242">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="f3d16-243">Dla każdej właściwości typu złożonego powiązanie modelu przeszukuje źródła dla *prefiksu wzorca nazwy. property_name*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="f3d16-244">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="f3d16-245">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="f3d16-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="f3d16-246">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="f3d16-247">Niektóre atrybuty mają `Prefix` właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="f3d16-248">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="f3d16-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="f3d16-249">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="f3d16-249">Prefix = parameter name</span></span>

<span data-ttu-id="f3d16-250">Jeśli modelem, który ma zostać powiązany, jest `instructorToUpdate`parametr o nazwie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="f3d16-251">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="f3d16-252">Jeśli ta wartość nie zostanie znaleziona, `ID` szuka bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="f3d16-253">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="f3d16-253">Prefix = property name</span></span>

<span data-ttu-id="f3d16-254">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="f3d16-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="f3d16-255">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f3d16-256">Jeśli ta wartość nie zostanie znaleziona, `ID` szuka bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="f3d16-257">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="f3d16-257">Custom prefix</span></span>

<span data-ttu-id="f3d16-258">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="f3d16-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="f3d16-259">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f3d16-260">Jeśli ta wartość nie zostanie znaleziona, `ID` szuka bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="f3d16-261">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="f3d16-261">Attributes for complex type targets</span></span>

<span data-ttu-id="f3d16-262">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="f3d16-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="f3d16-263">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="f3d16-264">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="f3d16-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="f3d16-265">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f3d16-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="f3d16-266">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="f3d16-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="f3d16-267">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-267">[BindRequired] attribute</span></span>

<span data-ttu-id="f3d16-268">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f3d16-269">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="f3d16-270">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="f3d16-271">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-271">[BindNever] attribute</span></span>

<span data-ttu-id="f3d16-272">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f3d16-273">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="f3d16-274">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="f3d16-275">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-275">[Bind] attribute</span></span>

<span data-ttu-id="f3d16-276">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="f3d16-277">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="f3d16-278">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="f3d16-279">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest `OnPost` Metoda:</span><span class="sxs-lookup"><span data-stu-id="f3d16-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="f3d16-280">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="f3d16-281">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="f3d16-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="f3d16-282">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="f3d16-283">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="f3d16-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="f3d16-284">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="f3d16-284">Collections</span></span>

<span data-ttu-id="f3d16-285">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f3d16-286">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f3d16-287">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-287">For example:</span></span>

* <span data-ttu-id="f3d16-288">Załóżmy, że parametr, który ma zostać powiązany, jest `selectedCourses`tablicą o nazwie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="f3d16-289">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="f3d16-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="f3d16-290">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="f3d16-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="f3d16-291">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="f3d16-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f3d16-292">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="f3d16-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="f3d16-293">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="f3d16-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="f3d16-294">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="f3d16-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="f3d16-295">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="f3d16-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="f3d16-296">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="f3d16-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="f3d16-297">Słowniki</span><span class="sxs-lookup"><span data-stu-id="f3d16-297">Dictionaries</span></span>

<span data-ttu-id="f3d16-298">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f3d16-299">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f3d16-300">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-300">For example:</span></span>

* <span data-ttu-id="f3d16-301">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany: `selectedCourses`</span><span class="sxs-lookup"><span data-stu-id="f3d16-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="f3d16-302">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="f3d16-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="f3d16-303">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="f3d16-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f3d16-304">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="f3d16-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="f3d16-305">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="f3d16-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="f3d16-306">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="f3d16-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="f3d16-307">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="f3d16-308">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="f3d16-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="f3d16-309">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="f3d16-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="f3d16-310">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="f3d16-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="f3d16-311">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="f3d16-312">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="f3d16-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="f3d16-313">Dziedzicz po<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="f3d16-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="f3d16-314">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="f3d16-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="f3d16-315">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="f3d16-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="f3d16-316">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="f3d16-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="f3d16-317">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="f3d16-317">Special data types</span></span>

<span data-ttu-id="f3d16-318">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="f3d16-319">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="f3d16-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="f3d16-320">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="f3d16-321">Obsługiwane jest `IEnumerable<IFormFile>` również dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="f3d16-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="f3d16-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="f3d16-322">CancellationToken</span></span>

<span data-ttu-id="f3d16-323">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="f3d16-324">Formularz</span><span class="sxs-lookup"><span data-stu-id="f3d16-324">FormCollection</span></span>

<span data-ttu-id="f3d16-325">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="f3d16-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="f3d16-326">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="f3d16-326">Input formatters</span></span>

<span data-ttu-id="f3d16-327">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="f3d16-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="f3d16-328">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="f3d16-329">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="f3d16-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="f3d16-330">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="f3d16-331">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f3d16-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="f3d16-332">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="f3d16-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="f3d16-333">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="f3d16-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="f3d16-334">Zainstaluj pakiet `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet.</span><span class="sxs-lookup"><span data-stu-id="f3d16-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="f3d16-335">W `Startup.ConfigureServices`, <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> Wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>lub.</span><span class="sxs-lookup"><span data-stu-id="f3d16-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="f3d16-336">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="f3d16-337">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="f3d16-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="f3d16-338">Dostosowywanie powiązania modelu z użyciem danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="f3d16-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="f3d16-339">Wejściowy program formatujący pobiera pełną odpowiedzialność za odczytywanie danych z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="f3d16-340">Aby dostosować ten proces, należy skonfigurować interfejsy API używane przez dane wejściowe programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="f3d16-341">W tej sekcji opisano sposób dostosowywania programu `System.Text.Json`formatującego wejściowego opartego na danych wejściowych w celu `ObjectId`zrozumienia niestandardowego typu o nazwie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="f3d16-342">Rozważmy następujący model, który zawiera właściwość `ObjectId` niestandardową `Id`o nazwie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="f3d16-343">Aby dostosować proces powiązania modelu przy użyciu `System.Text.Json`, Utwórz klasę pochodną: <xref:System.Text.Json.Serialization.JsonConverter%601></span><span class="sxs-lookup"><span data-stu-id="f3d16-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="f3d16-344">Aby użyć niestandardowego konwertera, Zastosuj <xref:System.Text.Json.Serialization.JsonConverterAttribute> atrybut do typu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="f3d16-345">W poniższym przykładzie `ObjectId` typ jest skonfigurowany z użyciem `ObjectIdConverter` jako konwertera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="f3d16-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="f3d16-346">Aby uzyskać więcej informacji, zobacz [jak pisać konwertery niestandardowe](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="f3d16-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="f3d16-347">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="f3d16-348">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="f3d16-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="f3d16-349">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="f3d16-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="f3d16-350">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="f3d16-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="f3d16-351">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="f3d16-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f3d16-352">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="f3d16-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="f3d16-353">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="f3d16-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f3d16-354">Na przykład aby wyłączyć walidację właściwości typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="f3d16-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="f3d16-355">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-355">Custom model binders</span></span>

<span data-ttu-id="f3d16-356">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając atrybutu `[ModelBinder]` , aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="f3d16-357">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="f3d16-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="f3d16-358">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-358">Manual model binding</span></span> 

<span data-ttu-id="f3d16-359">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="f3d16-360">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="f3d16-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="f3d16-361">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="f3d16-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="f3d16-362">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="f3d16-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="f3d16-363">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="f3d16-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>używa dostawców wartości do pobierania danych z formularza, ciągu zapytania i danych tras.</span><span class="sxs-lookup"><span data-stu-id="f3d16-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="f3d16-365">`TryUpdateModelAsync`zwykle:</span><span class="sxs-lookup"><span data-stu-id="f3d16-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="f3d16-366">Używany ze Razor stronami i aplikacjami MVC korzystającymi z kontrolerów i widoków, aby zapobiec nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="f3d16-367">Nieużywany z interfejsem API sieci Web, chyba że jest używane z danych formularza, ciągów zapytań i danych tras.</span><span class="sxs-lookup"><span data-stu-id="f3d16-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="f3d16-368">Punkty końcowe interfejsu API sieci Web, które [używają formatu JSON, do](#input-formatters) deserializacji treści żądania do obiektu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="f3d16-369">Aby uzyskać więcej informacji, zobacz [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="f3d16-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="f3d16-370">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-370">[FromServices] attribute</span></span>

<span data-ttu-id="f3d16-371">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="f3d16-372">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="f3d16-373">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="f3d16-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f3d16-374">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="f3d16-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3d16-375">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f3d16-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3d16-376">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="f3d16-377">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f3d16-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="f3d16-378">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-378">What is Model binding</span></span>

<span data-ttu-id="f3d16-379">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="f3d16-380">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="f3d16-381">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="f3d16-382">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="f3d16-382">Model binding automates this process.</span></span> <span data-ttu-id="f3d16-383">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="f3d16-383">The model binding system:</span></span>

* <span data-ttu-id="f3d16-384">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="f3d16-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="f3d16-385">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="f3d16-386">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="f3d16-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="f3d16-387">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="f3d16-388">Przykład</span><span class="sxs-lookup"><span data-stu-id="f3d16-388">Example</span></span>

<span data-ttu-id="f3d16-389">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="f3d16-390">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="f3d16-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="f3d16-391">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="f3d16-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="f3d16-392">Znajduje pierwszy parametr z `GetByID`, liczba całkowita o nazwie `id`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="f3d16-393">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="f3d16-394">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="f3d16-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="f3d16-395">Znajduje następny parametr elementu `GetByID`, wartość logiczna o nazwie `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="f3d16-396">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="f3d16-397">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="f3d16-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="f3d16-398">Konwertuje ciąg "true" na wartość logiczną `true`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="f3d16-399">Struktura następnie `GetById` wywołuje metodę, przekazując wartość 2 `id` dla parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="f3d16-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="f3d16-400">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="f3d16-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="f3d16-401">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="f3d16-402">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="f3d16-403">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="f3d16-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="f3d16-404">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="f3d16-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="f3d16-405">Obiekty docelowe</span><span class="sxs-lookup"><span data-stu-id="f3d16-405">Targets</span></span>

<span data-ttu-id="f3d16-406">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="f3d16-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="f3d16-407">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="f3d16-408">Parametry metody obsługi Razor stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="f3d16-409">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="f3d16-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="f3d16-410">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-410">[BindProperty] attribute</span></span>

<span data-ttu-id="f3d16-411">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="f3d16-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="f3d16-412">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-412">[BindProperties] attribute</span></span>

<span data-ttu-id="f3d16-413">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="f3d16-414">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="f3d16-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="f3d16-415">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="f3d16-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="f3d16-416">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="f3d16-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="f3d16-417">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="f3d16-418">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="f3d16-419">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="f3d16-420">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` właściwość na: `true`</span><span class="sxs-lookup"><span data-stu-id="f3d16-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="f3d16-421">Źródła</span><span class="sxs-lookup"><span data-stu-id="f3d16-421">Sources</span></span>

<span data-ttu-id="f3d16-422">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="f3d16-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="f3d16-423">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="f3d16-423">Form fields</span></span>
1. <span data-ttu-id="f3d16-424">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="f3d16-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="f3d16-425">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="f3d16-425">Route data</span></span>
1. <span data-ttu-id="f3d16-426">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="f3d16-426">Query string parameters</span></span>
1. <span data-ttu-id="f3d16-427">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="f3d16-427">Uploaded files</span></span>

<span data-ttu-id="f3d16-428">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="f3d16-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="f3d16-429">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="f3d16-429">There are a few exceptions:</span></span>

* <span data-ttu-id="f3d16-430">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="f3d16-431">Przekazane pliki są powiązane tylko z typami docelowymi `IFormFile` implementującymi `IEnumerable<IFormFile>`lub.</span><span class="sxs-lookup"><span data-stu-id="f3d16-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="f3d16-432">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="f3d16-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="f3d16-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="f3d16-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="f3d16-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="f3d16-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="f3d16-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="f3d16-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="f3d16-438">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="f3d16-438">These attributes:</span></span>

* <span data-ttu-id="f3d16-439">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="f3d16-440">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="f3d16-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="f3d16-441">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="f3d16-442">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="f3d16-443">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-443">[FromBody] attribute</span></span>

<span data-ttu-id="f3d16-444">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="f3d16-445">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="f3d16-446">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f3d16-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="f3d16-447">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="f3d16-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="f3d16-448">Na przykład Poniższa `Create` akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="f3d16-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="f3d16-449">`Pet` Klasa określa, że jej `Breed` właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="f3d16-450">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-450">In the preceding example:</span></span>

* <span data-ttu-id="f3d16-451">`[FromQuery]` Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="f3d16-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="f3d16-452">`Breed` Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="f3d16-453">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="f3d16-454">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="f3d16-455">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="f3d16-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="f3d16-456">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="f3d16-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="f3d16-457">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="f3d16-457">Additional sources</span></span>

<span data-ttu-id="f3d16-458">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="f3d16-459">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="f3d16-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="f3d16-460">Możesz na przykład potrzebować danych z plików cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="f3d16-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="f3d16-461">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="f3d16-461">To get data from a new source:</span></span>

* <span data-ttu-id="f3d16-462">Utwórz klasę implementującą `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="f3d16-463">Utwórz klasę implementującą `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="f3d16-464">Zarejestruj klasę fabryki w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="f3d16-465">Przykładowa aplikacja zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z plików cookie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="f3d16-466">Oto kod rejestracji w programie `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f3d16-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="f3d16-467">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="f3d16-468">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast. `Add`</span><span class="sxs-lookup"><span data-stu-id="f3d16-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="f3d16-469">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-469">No source for a model property</span></span>

<span data-ttu-id="f3d16-470">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="f3d16-471">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="f3d16-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="f3d16-472">Typy proste o wartości null są `null`ustawione na wartość.</span><span class="sxs-lookup"><span data-stu-id="f3d16-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="f3d16-473">Typy wartości, które nie są dopuszczane `default(T)`do wartości null, są ustawione na.</span><span class="sxs-lookup"><span data-stu-id="f3d16-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="f3d16-474">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="f3d16-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="f3d16-475">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="f3d16-476">Tablice są ustawiane `Array.Empty<T>()`na, z `byte[]` tą różnicą, `null`że tablice są ustawione na.</span><span class="sxs-lookup"><span data-stu-id="f3d16-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="f3d16-477">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="f3d16-478">Należy zauważyć, `[BindRequired]` że to zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="f3d16-479">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f3d16-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="f3d16-480">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="f3d16-480">Type conversion errors</span></span>

<span data-ttu-id="f3d16-481">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="f3d16-482">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="f3d16-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="f3d16-483">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="f3d16-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="f3d16-484">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="f3d16-485">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w Razor przeciwnym razie zostałyby przesłane do formularza stron.</span><span class="sxs-lookup"><span data-stu-id="f3d16-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="f3d16-486">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="f3d16-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="f3d16-487">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="f3d16-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="f3d16-488">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="f3d16-489">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="f3d16-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="f3d16-490">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="f3d16-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="f3d16-491">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="f3d16-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="f3d16-492">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="f3d16-493">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="f3d16-494">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="f3d16-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="f3d16-495">Typy proste</span><span class="sxs-lookup"><span data-stu-id="f3d16-495">Simple types</span></span>

<span data-ttu-id="f3d16-496">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="f3d16-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="f3d16-497">Boolean</span><span class="sxs-lookup"><span data-stu-id="f3d16-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="f3d16-498">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="f3d16-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="f3d16-499">Delikatn</span><span class="sxs-lookup"><span data-stu-id="f3d16-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="f3d16-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="f3d16-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="f3d16-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="f3d16-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="f3d16-502">Dokładności</span><span class="sxs-lookup"><span data-stu-id="f3d16-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="f3d16-503">Double</span><span class="sxs-lookup"><span data-stu-id="f3d16-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="f3d16-504">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="f3d16-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="f3d16-505">Ident</span><span class="sxs-lookup"><span data-stu-id="f3d16-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="f3d16-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="f3d16-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="f3d16-507">Single</span><span class="sxs-lookup"><span data-stu-id="f3d16-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="f3d16-508">Czasu</span><span class="sxs-lookup"><span data-stu-id="f3d16-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="f3d16-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="f3d16-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="f3d16-510">Adresu</span><span class="sxs-lookup"><span data-stu-id="f3d16-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="f3d16-511">Wersja</span><span class="sxs-lookup"><span data-stu-id="f3d16-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="f3d16-512">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="f3d16-512">Complex types</span></span>

<span data-ttu-id="f3d16-513">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="f3d16-514">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="f3d16-515">Dla każdej właściwości typu złożonego powiązanie modelu przeszukuje źródła dla *prefiksu wzorca nazwy. property_name*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="f3d16-516">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="f3d16-517">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="f3d16-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="f3d16-518">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="f3d16-519">Niektóre atrybuty mają `Prefix` właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="f3d16-520">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="f3d16-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="f3d16-521">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="f3d16-521">Prefix = parameter name</span></span>

<span data-ttu-id="f3d16-522">Jeśli modelem, który ma zostać powiązany, jest `instructorToUpdate`parametr o nazwie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="f3d16-523">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="f3d16-524">Jeśli ta wartość nie zostanie znaleziona, `ID` szuka bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="f3d16-525">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="f3d16-525">Prefix = property name</span></span>

<span data-ttu-id="f3d16-526">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="f3d16-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="f3d16-527">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f3d16-528">Jeśli ta wartość nie zostanie znaleziona, `ID` szuka bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="f3d16-529">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="f3d16-529">Custom prefix</span></span>

<span data-ttu-id="f3d16-530">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="f3d16-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="f3d16-531">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f3d16-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f3d16-532">Jeśli ta wartość nie zostanie znaleziona, `ID` szuka bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="f3d16-533">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="f3d16-533">Attributes for complex type targets</span></span>

<span data-ttu-id="f3d16-534">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="f3d16-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="f3d16-535">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="f3d16-536">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="f3d16-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="f3d16-537">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f3d16-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="f3d16-538">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="f3d16-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="f3d16-539">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-539">[BindRequired] attribute</span></span>

<span data-ttu-id="f3d16-540">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f3d16-541">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="f3d16-542">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="f3d16-543">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-543">[BindNever] attribute</span></span>

<span data-ttu-id="f3d16-544">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f3d16-545">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="f3d16-546">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="f3d16-547">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-547">[Bind] attribute</span></span>

<span data-ttu-id="f3d16-548">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="f3d16-549">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="f3d16-550">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="f3d16-551">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest `OnPost` Metoda:</span><span class="sxs-lookup"><span data-stu-id="f3d16-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="f3d16-552">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f3d16-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="f3d16-553">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="f3d16-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="f3d16-554">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="f3d16-555">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="f3d16-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="f3d16-556">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="f3d16-556">Collections</span></span>

<span data-ttu-id="f3d16-557">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f3d16-558">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f3d16-559">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-559">For example:</span></span>

* <span data-ttu-id="f3d16-560">Załóżmy, że parametr, który ma zostać powiązany, jest `selectedCourses`tablicą o nazwie:</span><span class="sxs-lookup"><span data-stu-id="f3d16-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="f3d16-561">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="f3d16-561">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="f3d16-562">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="f3d16-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="f3d16-563">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="f3d16-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f3d16-564">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="f3d16-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="f3d16-565">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="f3d16-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="f3d16-566">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="f3d16-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="f3d16-567">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="f3d16-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="f3d16-568">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="f3d16-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="f3d16-569">Słowniki</span><span class="sxs-lookup"><span data-stu-id="f3d16-569">Dictionaries</span></span>

<span data-ttu-id="f3d16-570">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="f3d16-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f3d16-571">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f3d16-572">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-572">For example:</span></span>

* <span data-ttu-id="f3d16-573">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany: `selectedCourses`</span><span class="sxs-lookup"><span data-stu-id="f3d16-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="f3d16-574">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="f3d16-574">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="f3d16-575">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="f3d16-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f3d16-576">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="f3d16-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="f3d16-577">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="f3d16-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="f3d16-578">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="f3d16-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="f3d16-579">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="f3d16-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="f3d16-580">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="f3d16-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="f3d16-581">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="f3d16-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="f3d16-582">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="f3d16-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="f3d16-583">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="f3d16-584">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="f3d16-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="f3d16-585">Dziedzicz po<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="f3d16-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="f3d16-586">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="f3d16-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="f3d16-587">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="f3d16-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="f3d16-588">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="f3d16-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="f3d16-589">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="f3d16-589">Special data types</span></span>

<span data-ttu-id="f3d16-590">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="f3d16-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="f3d16-591">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="f3d16-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="f3d16-592">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3d16-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="f3d16-593">Obsługiwane jest `IEnumerable<IFormFile>` również dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="f3d16-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="f3d16-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="f3d16-594">CancellationToken</span></span>

<span data-ttu-id="f3d16-595">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="f3d16-596">Formularz</span><span class="sxs-lookup"><span data-stu-id="f3d16-596">FormCollection</span></span>

<span data-ttu-id="f3d16-597">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="f3d16-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="f3d16-598">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="f3d16-598">Input formatters</span></span>

<span data-ttu-id="f3d16-599">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="f3d16-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="f3d16-600">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="f3d16-601">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="f3d16-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="f3d16-602">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="f3d16-603">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f3d16-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="f3d16-604">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="f3d16-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="f3d16-605">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="f3d16-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="f3d16-606">Zainstaluj pakiet `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet.</span><span class="sxs-lookup"><span data-stu-id="f3d16-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="f3d16-607">W `Startup.ConfigureServices`, <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> Wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>lub.</span><span class="sxs-lookup"><span data-stu-id="f3d16-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="f3d16-608">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f3d16-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="f3d16-609">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="f3d16-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="f3d16-610">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="f3d16-611">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="f3d16-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="f3d16-612">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="f3d16-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="f3d16-613">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="f3d16-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="f3d16-614">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="f3d16-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f3d16-615">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="f3d16-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="f3d16-616">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="f3d16-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f3d16-617">Na przykład aby wyłączyć walidację właściwości typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="f3d16-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="f3d16-618">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-618">Custom model binders</span></span>

<span data-ttu-id="f3d16-619">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając atrybutu `[ModelBinder]` , aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="f3d16-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="f3d16-620">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="f3d16-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="f3d16-621">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="f3d16-621">Manual model binding</span></span>

<span data-ttu-id="f3d16-622">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="f3d16-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="f3d16-623">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="f3d16-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="f3d16-624">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="f3d16-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="f3d16-625">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="f3d16-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="f3d16-626">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3d16-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="f3d16-627">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="f3d16-627">[FromServices] attribute</span></span>

<span data-ttu-id="f3d16-628">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="f3d16-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="f3d16-629">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="f3d16-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="f3d16-630">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="f3d16-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f3d16-631">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="f3d16-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3d16-632">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f3d16-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
