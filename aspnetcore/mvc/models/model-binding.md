---
title: Powiązanie modelu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak działa powiązanie modelu w ASP.NET Core i jak dostosować jego zachowanie.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/model-binding
ms.openlocfilehash: b3dcb3a80e8d5150d8513ef558531749d0884568
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400157"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="b510b-103">Powiązanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b510b-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b510b-104">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="b510b-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="b510b-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b510b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="b510b-106">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-106">What is Model binding</span></span>

<span data-ttu-id="b510b-107">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="b510b-108">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="b510b-109">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="b510b-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="b510b-110">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="b510b-110">Model binding automates this process.</span></span> <span data-ttu-id="b510b-111">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="b510b-111">The model binding system:</span></span>

* <span data-ttu-id="b510b-112">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="b510b-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="b510b-113">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="b510b-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="b510b-114">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="b510b-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="b510b-115">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="b510b-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="b510b-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="b510b-116">Example</span></span>

<span data-ttu-id="b510b-117">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="b510b-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="b510b-118">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="b510b-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="b510b-119">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="b510b-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="b510b-120">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="b510b-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="b510b-121">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="b510b-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="b510b-122">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="b510b-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="b510b-123">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="b510b-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="b510b-124">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="b510b-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="b510b-125">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b510b-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="b510b-126">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="b510b-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="b510b-127">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="b510b-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="b510b-128">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="b510b-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="b510b-129">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="b510b-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="b510b-130">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="b510b-131">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="b510b-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="b510b-132">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="b510b-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="b510b-133">Obiekty docelowe</span><span class="sxs-lookup"><span data-stu-id="b510b-133">Targets</span></span>

<span data-ttu-id="b510b-134">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="b510b-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="b510b-135">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="b510b-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="b510b-136">Parametry Razor metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="b510b-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="b510b-137">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="b510b-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="b510b-138">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-138">[BindProperty] attribute</span></span>

<span data-ttu-id="b510b-139">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="b510b-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="b510b-140">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-140">[BindProperties] attribute</span></span>

<span data-ttu-id="b510b-141">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="b510b-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="b510b-142">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="b510b-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="b510b-143">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="b510b-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="b510b-144">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="b510b-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="b510b-145">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="b510b-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="b510b-146">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="b510b-147">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="b510b-148">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="b510b-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="b510b-149">Źródła</span><span class="sxs-lookup"><span data-stu-id="b510b-149">Sources</span></span>

<span data-ttu-id="b510b-150">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="b510b-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="b510b-151">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="b510b-151">Form fields</span></span>
1. <span data-ttu-id="b510b-152">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="b510b-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="b510b-153">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="b510b-153">Route data</span></span>
1. <span data-ttu-id="b510b-154">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="b510b-154">Query string parameters</span></span>
1. <span data-ttu-id="b510b-155">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="b510b-155">Uploaded files</span></span>

<span data-ttu-id="b510b-156">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="b510b-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="b510b-157">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="b510b-157">There are a few exceptions:</span></span>

* <span data-ttu-id="b510b-158">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="b510b-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="b510b-159">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="b510b-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="b510b-160">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="b510b-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="b510b-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="b510b-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="b510b-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="b510b-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="b510b-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="b510b-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="b510b-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="b510b-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="b510b-166">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="b510b-166">These attributes:</span></span>

* <span data-ttu-id="b510b-167">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="b510b-168">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="b510b-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="b510b-169">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="b510b-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="b510b-170">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="b510b-171">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-171">[FromBody] attribute</span></span>

<span data-ttu-id="b510b-172">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="b510b-173">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="b510b-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="b510b-174">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="b510b-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="b510b-175">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="b510b-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="b510b-176">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="b510b-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="b510b-177">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="b510b-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="b510b-178">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-178">In the preceding example:</span></span>

* <span data-ttu-id="b510b-179">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="b510b-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="b510b-180">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="b510b-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="b510b-181">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="b510b-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="b510b-182">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="b510b-183">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="b510b-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="b510b-184">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="b510b-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="b510b-185">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="b510b-185">Additional sources</span></span>

<span data-ttu-id="b510b-186">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="b510b-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="b510b-187">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="b510b-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="b510b-188">Możesz na przykład potrzebować danych z plików cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="b510b-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="b510b-189">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="b510b-189">To get data from a new source:</span></span>

* <span data-ttu-id="b510b-190">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="b510b-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="b510b-191">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="b510b-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="b510b-192">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b510b-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="b510b-193">Przykładowa aplikacja zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b510b-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="b510b-194">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b510b-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="b510b-195">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="b510b-196">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="b510b-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="b510b-197">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-197">No source for a model property</span></span>

<span data-ttu-id="b510b-198">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="b510b-199">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="b510b-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="b510b-200">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="b510b-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="b510b-201">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="b510b-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="b510b-202">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="b510b-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="b510b-203">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="b510b-204">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="b510b-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="b510b-205">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="b510b-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="b510b-206">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="b510b-207">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="b510b-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="b510b-208">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="b510b-208">Type conversion errors</span></span>

<span data-ttu-id="b510b-209">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="b510b-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="b510b-210">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="b510b-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="b510b-211">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="b510b-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="b510b-212">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="b510b-213">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do Razor formularza stron.</span><span class="sxs-lookup"><span data-stu-id="b510b-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="b510b-214">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="b510b-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="b510b-215">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="b510b-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="b510b-216">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="b510b-217">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="b510b-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="b510b-218">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="b510b-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="b510b-219">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="b510b-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="b510b-220">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="b510b-221">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="b510b-222">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="b510b-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="b510b-223">Typy proste</span><span class="sxs-lookup"><span data-stu-id="b510b-223">Simple types</span></span>

<span data-ttu-id="b510b-224">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="b510b-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="b510b-225">Wartość logiczna</span><span class="sxs-lookup"><span data-stu-id="b510b-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="b510b-226">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="b510b-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="b510b-227">Delikatn</span><span class="sxs-lookup"><span data-stu-id="b510b-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="b510b-228">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="b510b-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="b510b-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b510b-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="b510b-230">Dokładności</span><span class="sxs-lookup"><span data-stu-id="b510b-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="b510b-231">Double</span><span class="sxs-lookup"><span data-stu-id="b510b-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="b510b-232">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="b510b-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="b510b-233">Ident</span><span class="sxs-lookup"><span data-stu-id="b510b-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="b510b-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="b510b-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="b510b-235">Pojedyncze</span><span class="sxs-lookup"><span data-stu-id="b510b-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="b510b-236">Czasu</span><span class="sxs-lookup"><span data-stu-id="b510b-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="b510b-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="b510b-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="b510b-238">Adresu</span><span class="sxs-lookup"><span data-stu-id="b510b-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="b510b-239">Wersja</span><span class="sxs-lookup"><span data-stu-id="b510b-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="b510b-240">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="b510b-240">Complex types</span></span>

<span data-ttu-id="b510b-241">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="b510b-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="b510b-242">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="b510b-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="b510b-243">Dla każdej właściwości typu złożonego powiązanie modelu przeszukuje źródła dla *prefiksu wzorca nazwy. property_name*.</span><span class="sxs-lookup"><span data-stu-id="b510b-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="b510b-244">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="b510b-245">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="b510b-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="b510b-246">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="b510b-247">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="b510b-248">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="b510b-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="b510b-249">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="b510b-249">Prefix = parameter name</span></span>

<span data-ttu-id="b510b-250">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="b510b-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="b510b-251">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="b510b-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="b510b-252">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="b510b-253">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="b510b-253">Prefix = property name</span></span>

<span data-ttu-id="b510b-254">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="b510b-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="b510b-255">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="b510b-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b510b-256">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="b510b-257">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="b510b-257">Custom prefix</span></span>

<span data-ttu-id="b510b-258">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="b510b-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="b510b-259">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="b510b-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b510b-260">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="b510b-261">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="b510b-261">Attributes for complex type targets</span></span>

<span data-ttu-id="b510b-262">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="b510b-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="b510b-263">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="b510b-264">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="b510b-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="b510b-265">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="b510b-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="b510b-266">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="b510b-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="b510b-267">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-267">[BindRequired] attribute</span></span>

<span data-ttu-id="b510b-268">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b510b-269">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="b510b-270">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="b510b-271">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-271">[BindNever] attribute</span></span>

<span data-ttu-id="b510b-272">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b510b-273">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="b510b-274">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="b510b-275">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-275">[Bind] attribute</span></span>

<span data-ttu-id="b510b-276">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="b510b-277">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="b510b-278">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="b510b-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="b510b-279">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="b510b-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="b510b-280">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="b510b-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="b510b-281">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="b510b-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="b510b-282">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="b510b-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="b510b-283">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="b510b-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="b510b-284">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="b510b-284">Collections</span></span>

<span data-ttu-id="b510b-285">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="b510b-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="b510b-286">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b510b-287">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-287">For example:</span></span>

* <span data-ttu-id="b510b-288">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="b510b-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="b510b-289">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="b510b-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="b510b-290">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="b510b-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="b510b-291">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="b510b-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b510b-292">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="b510b-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="b510b-293">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="b510b-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="b510b-294">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="b510b-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="b510b-295">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="b510b-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="b510b-296">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="b510b-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="b510b-297">Słowniki</span><span class="sxs-lookup"><span data-stu-id="b510b-297">Dictionaries</span></span>

<span data-ttu-id="b510b-298">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="b510b-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="b510b-299">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b510b-300">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-300">For example:</span></span>

* <span data-ttu-id="b510b-301">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="b510b-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="b510b-302">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="b510b-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="b510b-303">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="b510b-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b510b-304">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="b510b-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="b510b-305">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="b510b-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="b510b-306">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="b510b-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="b510b-307">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="b510b-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="b510b-308">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="b510b-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="b510b-309">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="b510b-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="b510b-310">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="b510b-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="b510b-311">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="b510b-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="b510b-312">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="b510b-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="b510b-313">Dziedzicz po<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="b510b-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="b510b-314">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="b510b-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="b510b-315">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="b510b-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="b510b-316">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="b510b-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="b510b-317">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="b510b-317">Special data types</span></span>

<span data-ttu-id="b510b-318">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="b510b-319">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="b510b-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="b510b-320">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="b510b-321">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="b510b-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="b510b-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="b510b-322">CancellationToken</span></span>

<span data-ttu-id="b510b-323">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="b510b-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="b510b-324">Formularz</span><span class="sxs-lookup"><span data-stu-id="b510b-324">FormCollection</span></span>

<span data-ttu-id="b510b-325">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="b510b-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="b510b-326">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="b510b-326">Input formatters</span></span>

<span data-ttu-id="b510b-327">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="b510b-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="b510b-328">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="b510b-329">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="b510b-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="b510b-330">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="b510b-331">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b510b-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="b510b-332">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="b510b-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="b510b-333">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="b510b-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="b510b-334">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="b510b-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="b510b-335">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="b510b-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="b510b-336">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="b510b-337">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="b510b-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="b510b-338">Dostosowywanie powiązania modelu z użyciem danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="b510b-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="b510b-339">Wejściowy program formatujący pobiera pełną odpowiedzialność za odczytywanie danych z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="b510b-340">Aby dostosować ten proces, należy skonfigurować interfejsy API używane przez dane wejściowe programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="b510b-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="b510b-341">W tej sekcji opisano sposób dostosowywania programu `System.Text.Json` formatującego wejściowego opartego na danych wejściowych w celu zrozumienia niestandardowego typu o nazwie `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="b510b-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="b510b-342">Rozważmy następujący model, który zawiera właściwość niestandardową `ObjectId` o nazwie `Id` :</span><span class="sxs-lookup"><span data-stu-id="b510b-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="b510b-343">Aby dostosować proces powiązania modelu przy użyciu `System.Text.Json` , Utwórz klasę pochodną <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="b510b-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="b510b-344">Aby użyć niestandardowego konwertera, Zastosuj <xref:System.Text.Json.Serialization.JsonConverterAttribute> atrybut do typu.</span><span class="sxs-lookup"><span data-stu-id="b510b-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="b510b-345">W poniższym przykładzie `ObjectId` Typ jest skonfigurowany z użyciem `ObjectIdConverter` jako konwertera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="b510b-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="b510b-346">Aby uzyskać więcej informacji, zobacz [jak pisać konwertery niestandardowe](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="b510b-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="b510b-347">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="b510b-348">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="b510b-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="b510b-349">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="b510b-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="b510b-350">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="b510b-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="b510b-351">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b510b-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b510b-352">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="b510b-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="b510b-353">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b510b-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b510b-354">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="b510b-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="b510b-355">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-355">Custom model binders</span></span>

<span data-ttu-id="b510b-356">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="b510b-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="b510b-357">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="b510b-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="b510b-358">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-358">Manual model binding</span></span> 

<span data-ttu-id="b510b-359">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="b510b-360">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="b510b-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="b510b-361">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="b510b-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="b510b-362">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="b510b-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="b510b-363">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="b510b-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>używa dostawców wartości do pobierania danych z formularza, ciągu zapytania i danych tras.</span><span class="sxs-lookup"><span data-stu-id="b510b-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="b510b-365">`TryUpdateModelAsync`zwykle:</span><span class="sxs-lookup"><span data-stu-id="b510b-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="b510b-366">Używany ze Razor stronami i aplikacjami MVC korzystającymi z kontrolerów i widoków, aby zapobiec nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="b510b-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="b510b-367">Nieużywany z interfejsem API sieci Web, chyba że jest używane z danych formularza, ciągów zapytań i danych tras.</span><span class="sxs-lookup"><span data-stu-id="b510b-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="b510b-368">Punkty końcowe interfejsu API sieci Web, które [używają formatu JSON, do](#input-formatters) deserializacji treści żądania do obiektu.</span><span class="sxs-lookup"><span data-stu-id="b510b-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="b510b-369">Aby uzyskać więcej informacji, zobacz [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="b510b-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="b510b-370">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-370">[FromServices] attribute</span></span>

<span data-ttu-id="b510b-371">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="b510b-372">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="b510b-373">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="b510b-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b510b-374">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="b510b-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b510b-375">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b510b-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b510b-376">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="b510b-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="b510b-377">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b510b-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="b510b-378">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-378">What is Model binding</span></span>

<span data-ttu-id="b510b-379">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="b510b-380">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="b510b-381">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="b510b-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="b510b-382">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="b510b-382">Model binding automates this process.</span></span> <span data-ttu-id="b510b-383">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="b510b-383">The model binding system:</span></span>

* <span data-ttu-id="b510b-384">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="b510b-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="b510b-385">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="b510b-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="b510b-386">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="b510b-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="b510b-387">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="b510b-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="b510b-388">Przykład</span><span class="sxs-lookup"><span data-stu-id="b510b-388">Example</span></span>

<span data-ttu-id="b510b-389">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="b510b-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="b510b-390">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="b510b-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="b510b-391">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="b510b-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="b510b-392">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="b510b-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="b510b-393">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="b510b-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="b510b-394">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="b510b-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="b510b-395">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="b510b-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="b510b-396">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="b510b-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="b510b-397">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b510b-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="b510b-398">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="b510b-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="b510b-399">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="b510b-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="b510b-400">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="b510b-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="b510b-401">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="b510b-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="b510b-402">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="b510b-403">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="b510b-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="b510b-404">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="b510b-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="b510b-405">Obiekty docelowe</span><span class="sxs-lookup"><span data-stu-id="b510b-405">Targets</span></span>

<span data-ttu-id="b510b-406">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="b510b-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="b510b-407">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="b510b-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="b510b-408">Parametry Razor metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="b510b-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="b510b-409">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="b510b-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="b510b-410">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-410">[BindProperty] attribute</span></span>

<span data-ttu-id="b510b-411">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="b510b-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="b510b-412">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-412">[BindProperties] attribute</span></span>

<span data-ttu-id="b510b-413">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="b510b-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="b510b-414">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="b510b-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="b510b-415">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="b510b-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="b510b-416">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="b510b-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="b510b-417">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="b510b-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="b510b-418">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="b510b-419">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="b510b-420">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="b510b-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="b510b-421">Źródła</span><span class="sxs-lookup"><span data-stu-id="b510b-421">Sources</span></span>

<span data-ttu-id="b510b-422">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="b510b-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="b510b-423">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="b510b-423">Form fields</span></span>
1. <span data-ttu-id="b510b-424">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="b510b-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="b510b-425">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="b510b-425">Route data</span></span>
1. <span data-ttu-id="b510b-426">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="b510b-426">Query string parameters</span></span>
1. <span data-ttu-id="b510b-427">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="b510b-427">Uploaded files</span></span>

<span data-ttu-id="b510b-428">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="b510b-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="b510b-429">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="b510b-429">There are a few exceptions:</span></span>

* <span data-ttu-id="b510b-430">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="b510b-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="b510b-431">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="b510b-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="b510b-432">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="b510b-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="b510b-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="b510b-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="b510b-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="b510b-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="b510b-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="b510b-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="b510b-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="b510b-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="b510b-438">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="b510b-438">These attributes:</span></span>

* <span data-ttu-id="b510b-439">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="b510b-440">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="b510b-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="b510b-441">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="b510b-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="b510b-442">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="b510b-443">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-443">[FromBody] attribute</span></span>

<span data-ttu-id="b510b-444">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="b510b-445">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="b510b-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="b510b-446">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="b510b-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="b510b-447">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="b510b-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="b510b-448">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="b510b-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="b510b-449">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="b510b-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="b510b-450">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-450">In the preceding example:</span></span>

* <span data-ttu-id="b510b-451">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="b510b-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="b510b-452">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="b510b-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="b510b-453">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="b510b-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="b510b-454">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="b510b-455">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="b510b-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="b510b-456">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="b510b-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="b510b-457">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="b510b-457">Additional sources</span></span>

<span data-ttu-id="b510b-458">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="b510b-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="b510b-459">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="b510b-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="b510b-460">Możesz na przykład potrzebować danych z plików cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="b510b-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="b510b-461">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="b510b-461">To get data from a new source:</span></span>

* <span data-ttu-id="b510b-462">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="b510b-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="b510b-463">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="b510b-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="b510b-464">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b510b-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="b510b-465">Przykładowa aplikacja zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b510b-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="b510b-466">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b510b-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="b510b-467">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="b510b-468">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="b510b-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="b510b-469">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-469">No source for a model property</span></span>

<span data-ttu-id="b510b-470">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="b510b-471">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="b510b-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="b510b-472">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="b510b-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="b510b-473">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="b510b-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="b510b-474">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="b510b-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="b510b-475">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="b510b-476">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="b510b-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="b510b-477">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="b510b-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="b510b-478">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="b510b-479">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="b510b-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="b510b-480">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="b510b-480">Type conversion errors</span></span>

<span data-ttu-id="b510b-481">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="b510b-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="b510b-482">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="b510b-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="b510b-483">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="b510b-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="b510b-484">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="b510b-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="b510b-485">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do Razor formularza stron.</span><span class="sxs-lookup"><span data-stu-id="b510b-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="b510b-486">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="b510b-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="b510b-487">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="b510b-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="b510b-488">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="b510b-489">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="b510b-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="b510b-490">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="b510b-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="b510b-491">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="b510b-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="b510b-492">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="b510b-493">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="b510b-494">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="b510b-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="b510b-495">Typy proste</span><span class="sxs-lookup"><span data-stu-id="b510b-495">Simple types</span></span>

<span data-ttu-id="b510b-496">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="b510b-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="b510b-497">Wartość logiczna</span><span class="sxs-lookup"><span data-stu-id="b510b-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="b510b-498">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="b510b-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="b510b-499">Delikatn</span><span class="sxs-lookup"><span data-stu-id="b510b-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="b510b-500">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="b510b-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="b510b-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="b510b-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="b510b-502">Dokładności</span><span class="sxs-lookup"><span data-stu-id="b510b-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="b510b-503">Double</span><span class="sxs-lookup"><span data-stu-id="b510b-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="b510b-504">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="b510b-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="b510b-505">Ident</span><span class="sxs-lookup"><span data-stu-id="b510b-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="b510b-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="b510b-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="b510b-507">Pojedyncze</span><span class="sxs-lookup"><span data-stu-id="b510b-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="b510b-508">Czasu</span><span class="sxs-lookup"><span data-stu-id="b510b-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="b510b-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="b510b-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="b510b-510">Adresu</span><span class="sxs-lookup"><span data-stu-id="b510b-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="b510b-511">Wersja</span><span class="sxs-lookup"><span data-stu-id="b510b-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="b510b-512">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="b510b-512">Complex types</span></span>

<span data-ttu-id="b510b-513">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="b510b-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="b510b-514">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="b510b-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="b510b-515">Dla każdej właściwości typu złożonego powiązanie modelu przeszukuje źródła dla *prefiksu wzorca nazwy. property_name*.</span><span class="sxs-lookup"><span data-stu-id="b510b-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="b510b-516">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="b510b-517">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="b510b-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="b510b-518">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="b510b-519">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="b510b-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="b510b-520">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="b510b-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="b510b-521">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="b510b-521">Prefix = parameter name</span></span>

<span data-ttu-id="b510b-522">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="b510b-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="b510b-523">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="b510b-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="b510b-524">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="b510b-525">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="b510b-525">Prefix = property name</span></span>

<span data-ttu-id="b510b-526">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="b510b-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="b510b-527">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="b510b-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b510b-528">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="b510b-529">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="b510b-529">Custom prefix</span></span>

<span data-ttu-id="b510b-530">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="b510b-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="b510b-531">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="b510b-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="b510b-532">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="b510b-533">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="b510b-533">Attributes for complex type targets</span></span>

<span data-ttu-id="b510b-534">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="b510b-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="b510b-535">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="b510b-536">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="b510b-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="b510b-537">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="b510b-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="b510b-538">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="b510b-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="b510b-539">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-539">[BindRequired] attribute</span></span>

<span data-ttu-id="b510b-540">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b510b-541">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="b510b-542">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="b510b-543">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-543">[BindNever] attribute</span></span>

<span data-ttu-id="b510b-544">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="b510b-545">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="b510b-546">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="b510b-547">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-547">[Bind] attribute</span></span>

<span data-ttu-id="b510b-548">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="b510b-549">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="b510b-550">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="b510b-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="b510b-551">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="b510b-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="b510b-552">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="b510b-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="b510b-553">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="b510b-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="b510b-554">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="b510b-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="b510b-555">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="b510b-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="b510b-556">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="b510b-556">Collections</span></span>

<span data-ttu-id="b510b-557">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="b510b-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="b510b-558">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b510b-559">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-559">For example:</span></span>

* <span data-ttu-id="b510b-560">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="b510b-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="b510b-561">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="b510b-561">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="b510b-562">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="b510b-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="b510b-563">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="b510b-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b510b-564">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="b510b-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="b510b-565">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="b510b-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="b510b-566">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="b510b-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="b510b-567">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="b510b-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="b510b-568">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="b510b-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="b510b-569">Słowniki</span><span class="sxs-lookup"><span data-stu-id="b510b-569">Dictionaries</span></span>

<span data-ttu-id="b510b-570">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="b510b-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="b510b-571">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="b510b-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="b510b-572">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-572">For example:</span></span>

* <span data-ttu-id="b510b-573">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="b510b-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="b510b-574">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="b510b-574">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="b510b-575">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="b510b-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="b510b-576">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="b510b-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="b510b-577">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="b510b-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="b510b-578">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="b510b-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="b510b-579">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="b510b-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="b510b-580">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="b510b-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="b510b-581">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="b510b-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="b510b-582">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="b510b-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="b510b-583">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="b510b-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="b510b-584">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="b510b-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="b510b-585">Dziedzicz po<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="b510b-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="b510b-586">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="b510b-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="b510b-587">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="b510b-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="b510b-588">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="b510b-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="b510b-589">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="b510b-589">Special data types</span></span>

<span data-ttu-id="b510b-590">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="b510b-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="b510b-591">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="b510b-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="b510b-592">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b510b-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="b510b-593">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="b510b-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="b510b-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="b510b-594">CancellationToken</span></span>

<span data-ttu-id="b510b-595">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="b510b-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="b510b-596">Formularz</span><span class="sxs-lookup"><span data-stu-id="b510b-596">FormCollection</span></span>

<span data-ttu-id="b510b-597">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="b510b-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="b510b-598">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="b510b-598">Input formatters</span></span>

<span data-ttu-id="b510b-599">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="b510b-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="b510b-600">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="b510b-601">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="b510b-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="b510b-602">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="b510b-603">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b510b-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="b510b-604">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="b510b-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="b510b-605">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="b510b-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="b510b-606">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="b510b-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="b510b-607">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="b510b-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="b510b-608">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="b510b-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="b510b-609">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="b510b-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="b510b-610">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="b510b-611">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="b510b-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="b510b-612">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="b510b-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="b510b-613">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="b510b-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="b510b-614">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b510b-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b510b-615">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="b510b-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="b510b-616">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b510b-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b510b-617">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="b510b-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="b510b-618">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-618">Custom model binders</span></span>

<span data-ttu-id="b510b-619">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="b510b-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="b510b-620">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="b510b-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="b510b-621">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="b510b-621">Manual model binding</span></span>

<span data-ttu-id="b510b-622">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="b510b-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="b510b-623">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="b510b-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="b510b-624">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="b510b-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="b510b-625">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="b510b-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="b510b-626">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b510b-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="b510b-627">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="b510b-627">[FromServices] attribute</span></span>

<span data-ttu-id="b510b-628">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="b510b-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="b510b-629">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="b510b-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="b510b-630">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="b510b-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b510b-631">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="b510b-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b510b-632">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b510b-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
