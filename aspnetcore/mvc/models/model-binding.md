---
title: Powiązanie modelu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak działa powiązanie modelu w ASP.NET Core i jak dostosować jego zachowanie.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ca2f071ccb84fdb2eb06f533fc4d088ad1b1c785
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393889"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="5950c-103">Powiązanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5950c-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5950c-104">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="5950c-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="5950c-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5950c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="5950c-106">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-106">What is Model binding</span></span>

<span data-ttu-id="5950c-107">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="5950c-108">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="5950c-109">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="5950c-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="5950c-110">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="5950c-110">Model binding automates this process.</span></span> <span data-ttu-id="5950c-111">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="5950c-111">The model binding system:</span></span>

* <span data-ttu-id="5950c-112">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="5950c-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="5950c-113">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="5950c-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="5950c-114">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="5950c-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="5950c-115">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="5950c-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="5950c-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="5950c-116">Example</span></span>

<span data-ttu-id="5950c-117">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="5950c-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="5950c-118">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="5950c-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="5950c-119">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="5950c-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="5950c-120">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="5950c-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="5950c-121">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="5950c-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="5950c-122">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="5950c-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="5950c-123">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="5950c-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="5950c-124">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="5950c-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="5950c-125">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="5950c-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="5950c-126">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="5950c-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="5950c-127">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="5950c-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="5950c-128">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="5950c-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="5950c-129">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="5950c-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="5950c-130">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="5950c-131">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="5950c-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="5950c-132">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="5950c-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="5950c-133">Targets (Obiekty docelowe)</span><span class="sxs-lookup"><span data-stu-id="5950c-133">Targets</span></span>

<span data-ttu-id="5950c-134">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="5950c-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="5950c-135">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="5950c-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="5950c-136">Parametry Razor metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="5950c-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="5950c-137">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="5950c-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="5950c-138">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-138">[BindProperty] attribute</span></span>

<span data-ttu-id="5950c-139">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="5950c-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="5950c-140">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-140">[BindProperties] attribute</span></span>

<span data-ttu-id="5950c-141">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="5950c-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="5950c-142">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="5950c-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="5950c-143">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="5950c-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="5950c-144">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5950c-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="5950c-145">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="5950c-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="5950c-146">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="5950c-147">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="5950c-148">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="5950c-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="5950c-149">Źródła</span><span class="sxs-lookup"><span data-stu-id="5950c-149">Sources</span></span>

<span data-ttu-id="5950c-150">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="5950c-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="5950c-151">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="5950c-151">Form fields</span></span>
1. <span data-ttu-id="5950c-152">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="5950c-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="5950c-153">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="5950c-153">Route data</span></span>
1. <span data-ttu-id="5950c-154">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="5950c-154">Query string parameters</span></span>
1. <span data-ttu-id="5950c-155">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="5950c-155">Uploaded files</span></span>

<span data-ttu-id="5950c-156">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="5950c-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="5950c-157">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="5950c-157">There are a few exceptions:</span></span>

* <span data-ttu-id="5950c-158">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="5950c-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="5950c-159">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="5950c-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="5950c-160">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="5950c-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="5950c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="5950c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="5950c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="5950c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="5950c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="5950c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="5950c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="5950c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="5950c-166">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="5950c-166">These attributes:</span></span>

* <span data-ttu-id="5950c-167">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="5950c-168">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="5950c-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="5950c-169">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="5950c-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="5950c-170">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="5950c-171">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-171">[FromBody] attribute</span></span>

<span data-ttu-id="5950c-172">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="5950c-173">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="5950c-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="5950c-174">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5950c-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="5950c-175">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="5950c-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="5950c-176">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="5950c-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="5950c-177">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="5950c-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="5950c-178">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-178">In the preceding example:</span></span>

* <span data-ttu-id="5950c-179">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="5950c-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="5950c-180">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="5950c-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="5950c-181">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="5950c-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="5950c-182">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="5950c-183">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="5950c-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="5950c-184">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="5950c-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="5950c-185">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="5950c-185">Additional sources</span></span>

<span data-ttu-id="5950c-186">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="5950c-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="5950c-187">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="5950c-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="5950c-188">Na przykład możesz potrzebować danych z cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="5950c-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="5950c-189">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="5950c-189">To get data from a new source:</span></span>

* <span data-ttu-id="5950c-190">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="5950c-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="5950c-191">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="5950c-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="5950c-192">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5950c-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="5950c-193">Aplikacja Przykładowa zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z cookie s.</span><span class="sxs-lookup"><span data-stu-id="5950c-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="5950c-194">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5950c-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="5950c-195">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="5950c-196">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="5950c-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="5950c-197">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-197">No source for a model property</span></span>

<span data-ttu-id="5950c-198">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="5950c-199">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="5950c-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="5950c-200">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="5950c-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="5950c-201">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="5950c-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="5950c-202">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="5950c-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="5950c-203">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="5950c-204">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="5950c-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="5950c-205">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5950c-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="5950c-206">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="5950c-207">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5950c-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="5950c-208">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="5950c-208">Type conversion errors</span></span>

<span data-ttu-id="5950c-209">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="5950c-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="5950c-210">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5950c-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="5950c-211">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="5950c-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="5950c-212">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="5950c-213">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do Razor formularza stron.</span><span class="sxs-lookup"><span data-stu-id="5950c-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="5950c-214">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="5950c-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="5950c-215">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="5950c-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="5950c-216">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="5950c-217">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="5950c-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="5950c-218">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="5950c-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="5950c-219">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="5950c-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="5950c-220">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="5950c-221">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="5950c-222">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="5950c-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="5950c-223">Typy proste</span><span class="sxs-lookup"><span data-stu-id="5950c-223">Simple types</span></span>

<span data-ttu-id="5950c-224">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5950c-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="5950c-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="5950c-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="5950c-226">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="5950c-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="5950c-227">Delikatn</span><span class="sxs-lookup"><span data-stu-id="5950c-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="5950c-228">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="5950c-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="5950c-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="5950c-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="5950c-230">Dokładności</span><span class="sxs-lookup"><span data-stu-id="5950c-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="5950c-231">Double</span><span class="sxs-lookup"><span data-stu-id="5950c-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="5950c-232">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="5950c-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="5950c-233">Ident</span><span class="sxs-lookup"><span data-stu-id="5950c-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="5950c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="5950c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="5950c-235">Pojedynczy</span><span class="sxs-lookup"><span data-stu-id="5950c-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="5950c-236">Czasu</span><span class="sxs-lookup"><span data-stu-id="5950c-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="5950c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="5950c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="5950c-238">Adresu</span><span class="sxs-lookup"><span data-stu-id="5950c-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="5950c-239">Wersja</span><span class="sxs-lookup"><span data-stu-id="5950c-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="5950c-240">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="5950c-240">Complex types</span></span>

<span data-ttu-id="5950c-241">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="5950c-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="5950c-242">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="5950c-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="5950c-243">Dla każdej właściwości typu złożonego powiązanie modelu przeszukuje źródła dla *prefiksu wzorca nazwy. property_name*.</span><span class="sxs-lookup"><span data-stu-id="5950c-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="5950c-244">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="5950c-245">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="5950c-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="5950c-246">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="5950c-247">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="5950c-248">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="5950c-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="5950c-249">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="5950c-249">Prefix = parameter name</span></span>

<span data-ttu-id="5950c-250">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="5950c-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="5950c-251">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="5950c-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="5950c-252">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="5950c-253">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="5950c-253">Prefix = property name</span></span>

<span data-ttu-id="5950c-254">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="5950c-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="5950c-255">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="5950c-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="5950c-256">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="5950c-257">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="5950c-257">Custom prefix</span></span>

<span data-ttu-id="5950c-258">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="5950c-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="5950c-259">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="5950c-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="5950c-260">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="5950c-261">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="5950c-261">Attributes for complex type targets</span></span>

<span data-ttu-id="5950c-262">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="5950c-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="5950c-263">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="5950c-264">***Nie*** wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="5950c-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="5950c-265">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5950c-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="5950c-266">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-266">[Bind] attribute</span></span>

<span data-ttu-id="5950c-267">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="5950c-268">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="5950c-269">`[Bind]` nie ***ma wpływu na*** wejściowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="5950c-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="5950c-270">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="5950c-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="5950c-271">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="5950c-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="5950c-272">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="5950c-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="5950c-273">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="5950c-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="5950c-274">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5950c-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="5950c-275">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="5950c-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="5950c-276">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-276">[BindRequired] attribute</span></span>

<span data-ttu-id="5950c-277">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="5950c-278">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="5950c-279">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="5950c-280">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="5950c-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="5950c-281">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-281">[BindNever] attribute</span></span>

<span data-ttu-id="5950c-282">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="5950c-283">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="5950c-284">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="5950c-285">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="5950c-285">Collections</span></span>

<span data-ttu-id="5950c-286">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="5950c-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="5950c-287">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="5950c-288">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-288">For example:</span></span>

* <span data-ttu-id="5950c-289">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="5950c-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="5950c-290">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="5950c-290">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="5950c-291">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="5950c-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="5950c-292">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="5950c-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="5950c-293">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="5950c-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="5950c-294">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="5950c-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="5950c-295">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="5950c-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="5950c-296">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="5950c-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="5950c-297">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="5950c-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="5950c-298">Słowniki</span><span class="sxs-lookup"><span data-stu-id="5950c-298">Dictionaries</span></span>

<span data-ttu-id="5950c-299">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="5950c-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="5950c-300">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="5950c-301">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-301">For example:</span></span>

* <span data-ttu-id="5950c-302">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="5950c-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="5950c-303">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="5950c-303">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="5950c-304">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="5950c-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="5950c-305">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="5950c-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="5950c-306">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="5950c-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="5950c-307">Typy powiązań konstruktora i rekordów</span><span class="sxs-lookup"><span data-stu-id="5950c-307">Constructor binding and record types</span></span>

<span data-ttu-id="5950c-308">Powiązanie modelu wymaga, aby typy złożone miały Konstruktor bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="5950c-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="5950c-309">Zarówno programowy `System.Text.Json` , jak i `Newtonsoft.Json` oparty na danych wejściowych elementy formatujące obsługują deserializacja klas, które nie mają konstruktora bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="5950c-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="5950c-310">W języku C# 9 wprowadzono typy rekordów, które są doskonałym sposobem na zwięzłe przedstawianie danych przez sieć.</span><span class="sxs-lookup"><span data-stu-id="5950c-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="5950c-311">ASP.NET Core dodaje obsługę powiązania modelu i sprawdzanie poprawności typów rekordów przy użyciu pojedynczego konstruktora:</span><span class="sxs-lookup"><span data-stu-id="5950c-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="5950c-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="5950c-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="5950c-313">Podczas sprawdzania poprawności typów rekordów środowisko uruchomieniowe wyszukuje metadane walidacji w zależności od parametrów, a nie właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="5950c-314">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="5950c-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="5950c-315">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="5950c-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="5950c-316">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="5950c-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="5950c-317">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="5950c-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="5950c-318">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="5950c-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="5950c-319">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="5950c-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="5950c-320">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="5950c-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="5950c-321">Dziedzicz po <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="5950c-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="5950c-322">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="5950c-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="5950c-323">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="5950c-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="5950c-324">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="5950c-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="5950c-325">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="5950c-325">Special data types</span></span>

<span data-ttu-id="5950c-326">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="5950c-327">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="5950c-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="5950c-328">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="5950c-329">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="5950c-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="5950c-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="5950c-330">CancellationToken</span></span>

<span data-ttu-id="5950c-331">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="5950c-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="5950c-332">Formularz</span><span class="sxs-lookup"><span data-stu-id="5950c-332">FormCollection</span></span>

<span data-ttu-id="5950c-333">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="5950c-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="5950c-334">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="5950c-334">Input formatters</span></span>

<span data-ttu-id="5950c-335">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="5950c-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="5950c-336">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="5950c-337">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="5950c-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="5950c-338">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="5950c-339">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="5950c-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="5950c-340">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="5950c-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="5950c-341">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="5950c-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="5950c-342">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="5950c-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="5950c-343">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="5950c-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="5950c-344">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="5950c-345">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="5950c-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="5950c-346">Dostosowywanie powiązania modelu z użyciem danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="5950c-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="5950c-347">Wejściowy program formatujący pobiera pełną odpowiedzialność za odczytywanie danych z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="5950c-348">Aby dostosować ten proces, należy skonfigurować interfejsy API używane przez dane wejściowe programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="5950c-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="5950c-349">W tej sekcji opisano sposób dostosowywania programu `System.Text.Json` formatującego wejściowego opartego na danych wejściowych w celu zrozumienia niestandardowego typu o nazwie `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="5950c-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="5950c-350">Rozważmy następujący model, który zawiera właściwość niestandardową `ObjectId` o nazwie `Id` :</span><span class="sxs-lookup"><span data-stu-id="5950c-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="5950c-351">Aby dostosować proces powiązania modelu przy użyciu `System.Text.Json` , Utwórz klasę pochodną <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="5950c-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="5950c-352">Aby użyć niestandardowego konwertera, Zastosuj <xref:System.Text.Json.Serialization.JsonConverterAttribute> atrybut do typu.</span><span class="sxs-lookup"><span data-stu-id="5950c-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="5950c-353">W poniższym przykładzie `ObjectId` Typ jest skonfigurowany z użyciem `ObjectIdConverter` jako konwertera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="5950c-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="5950c-354">Aby uzyskać więcej informacji, zobacz [jak pisać konwertery niestandardowe](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="5950c-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="5950c-355">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="5950c-356">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="5950c-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="5950c-357">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="5950c-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="5950c-358">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="5950c-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="5950c-359">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5950c-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5950c-360">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="5950c-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="5950c-361">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5950c-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5950c-362">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="5950c-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="5950c-363">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-363">Custom model binders</span></span>

<span data-ttu-id="5950c-364">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="5950c-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="5950c-365">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="5950c-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="5950c-366">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-366">Manual model binding</span></span> 

<span data-ttu-id="5950c-367">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="5950c-368">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="5950c-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="5950c-369">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="5950c-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="5950c-370">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="5950c-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="5950c-371">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="5950c-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  używa dostawców wartości do pobierania danych z formularza, ciągu zapytania i danych tras.</span><span class="sxs-lookup"><span data-stu-id="5950c-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="5950c-373">`TryUpdateModelAsync` zwykle:</span><span class="sxs-lookup"><span data-stu-id="5950c-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="5950c-374">Używany ze Razor stronami i aplikacjami MVC korzystającymi z kontrolerów i widoków, aby zapobiec nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="5950c-374">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="5950c-375">Nieużywany z interfejsem API sieci Web, chyba że jest używane z danych formularza, ciągów zapytań i danych tras.</span><span class="sxs-lookup"><span data-stu-id="5950c-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="5950c-376">Punkty końcowe interfejsu API sieci Web, które [używają formatu JSON, do](#input-formatters) deserializacji treści żądania do obiektu.</span><span class="sxs-lookup"><span data-stu-id="5950c-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="5950c-377">Aby uzyskać więcej informacji, zobacz [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="5950c-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="5950c-378">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-378">[FromServices] attribute</span></span>

<span data-ttu-id="5950c-379">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="5950c-380">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="5950c-381">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="5950c-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5950c-382">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="5950c-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5950c-383">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5950c-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5950c-384">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="5950c-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="5950c-385">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5950c-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="5950c-386">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-386">What is Model binding</span></span>

<span data-ttu-id="5950c-387">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-387">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="5950c-388">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="5950c-389">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="5950c-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="5950c-390">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="5950c-390">Model binding automates this process.</span></span> <span data-ttu-id="5950c-391">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="5950c-391">The model binding system:</span></span>

* <span data-ttu-id="5950c-392">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="5950c-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="5950c-393">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="5950c-393">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="5950c-394">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="5950c-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="5950c-395">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="5950c-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="5950c-396">Przykład</span><span class="sxs-lookup"><span data-stu-id="5950c-396">Example</span></span>

<span data-ttu-id="5950c-397">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="5950c-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="5950c-398">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="5950c-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="5950c-399">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="5950c-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="5950c-400">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="5950c-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="5950c-401">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="5950c-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="5950c-402">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="5950c-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="5950c-403">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="5950c-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="5950c-404">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="5950c-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="5950c-405">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="5950c-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="5950c-406">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="5950c-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="5950c-407">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="5950c-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="5950c-408">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="5950c-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="5950c-409">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="5950c-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="5950c-410">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="5950c-411">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="5950c-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="5950c-412">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="5950c-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="5950c-413">Targets (Obiekty docelowe)</span><span class="sxs-lookup"><span data-stu-id="5950c-413">Targets</span></span>

<span data-ttu-id="5950c-414">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="5950c-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="5950c-415">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="5950c-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="5950c-416">Parametry Razor metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="5950c-416">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="5950c-417">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="5950c-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="5950c-418">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-418">[BindProperty] attribute</span></span>

<span data-ttu-id="5950c-419">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="5950c-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="5950c-420">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-420">[BindProperties] attribute</span></span>

<span data-ttu-id="5950c-421">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="5950c-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="5950c-422">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="5950c-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="5950c-423">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="5950c-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="5950c-424">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5950c-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="5950c-425">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="5950c-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="5950c-426">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="5950c-427">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="5950c-428">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="5950c-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="5950c-429">Źródła</span><span class="sxs-lookup"><span data-stu-id="5950c-429">Sources</span></span>

<span data-ttu-id="5950c-430">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="5950c-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="5950c-431">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="5950c-431">Form fields</span></span>
1. <span data-ttu-id="5950c-432">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="5950c-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="5950c-433">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="5950c-433">Route data</span></span>
1. <span data-ttu-id="5950c-434">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="5950c-434">Query string parameters</span></span>
1. <span data-ttu-id="5950c-435">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="5950c-435">Uploaded files</span></span>

<span data-ttu-id="5950c-436">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="5950c-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="5950c-437">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="5950c-437">There are a few exceptions:</span></span>

* <span data-ttu-id="5950c-438">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="5950c-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="5950c-439">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="5950c-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="5950c-440">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="5950c-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="5950c-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="5950c-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="5950c-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="5950c-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="5950c-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="5950c-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="5950c-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="5950c-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="5950c-446">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="5950c-446">These attributes:</span></span>

* <span data-ttu-id="5950c-447">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="5950c-448">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="5950c-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="5950c-449">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="5950c-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="5950c-450">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="5950c-451">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-451">[FromBody] attribute</span></span>

<span data-ttu-id="5950c-452">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="5950c-453">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="5950c-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="5950c-454">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5950c-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="5950c-455">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="5950c-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="5950c-456">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="5950c-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="5950c-457">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="5950c-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="5950c-458">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-458">In the preceding example:</span></span>

* <span data-ttu-id="5950c-459">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="5950c-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="5950c-460">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="5950c-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="5950c-461">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="5950c-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="5950c-462">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="5950c-463">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="5950c-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="5950c-464">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="5950c-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="5950c-465">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="5950c-465">Additional sources</span></span>

<span data-ttu-id="5950c-466">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="5950c-466">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="5950c-467">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="5950c-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="5950c-468">Na przykład możesz potrzebować danych z cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="5950c-468">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="5950c-469">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="5950c-469">To get data from a new source:</span></span>

* <span data-ttu-id="5950c-470">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="5950c-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="5950c-471">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="5950c-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="5950c-472">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5950c-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="5950c-473">Aplikacja Przykładowa zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z cookie s.</span><span class="sxs-lookup"><span data-stu-id="5950c-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="5950c-474">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5950c-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="5950c-475">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="5950c-476">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="5950c-476">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="5950c-477">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-477">No source for a model property</span></span>

<span data-ttu-id="5950c-478">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="5950c-479">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="5950c-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="5950c-480">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="5950c-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="5950c-481">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="5950c-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="5950c-482">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="5950c-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="5950c-483">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="5950c-484">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="5950c-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="5950c-485">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5950c-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="5950c-486">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="5950c-487">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5950c-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="5950c-488">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="5950c-488">Type conversion errors</span></span>

<span data-ttu-id="5950c-489">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="5950c-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="5950c-490">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="5950c-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="5950c-491">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="5950c-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="5950c-492">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="5950c-492">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="5950c-493">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do Razor formularza stron.</span><span class="sxs-lookup"><span data-stu-id="5950c-493">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="5950c-494">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="5950c-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="5950c-495">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="5950c-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="5950c-496">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="5950c-497">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="5950c-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="5950c-498">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="5950c-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="5950c-499">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="5950c-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="5950c-500">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="5950c-501">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="5950c-502">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="5950c-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="5950c-503">Typy proste</span><span class="sxs-lookup"><span data-stu-id="5950c-503">Simple types</span></span>

<span data-ttu-id="5950c-504">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="5950c-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="5950c-505">Boolean</span><span class="sxs-lookup"><span data-stu-id="5950c-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="5950c-506">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="5950c-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="5950c-507">Delikatn</span><span class="sxs-lookup"><span data-stu-id="5950c-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="5950c-508">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="5950c-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="5950c-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="5950c-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="5950c-510">Dokładności</span><span class="sxs-lookup"><span data-stu-id="5950c-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="5950c-511">Double</span><span class="sxs-lookup"><span data-stu-id="5950c-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="5950c-512">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="5950c-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="5950c-513">Ident</span><span class="sxs-lookup"><span data-stu-id="5950c-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="5950c-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="5950c-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="5950c-515">Pojedynczy</span><span class="sxs-lookup"><span data-stu-id="5950c-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="5950c-516">Czasu</span><span class="sxs-lookup"><span data-stu-id="5950c-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="5950c-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="5950c-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="5950c-518">Adresu</span><span class="sxs-lookup"><span data-stu-id="5950c-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="5950c-519">Wersja</span><span class="sxs-lookup"><span data-stu-id="5950c-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="5950c-520">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="5950c-520">Complex types</span></span>

<span data-ttu-id="5950c-521">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="5950c-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="5950c-522">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="5950c-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="5950c-523">Dla każdej właściwości typu złożonego powiązanie modelu przeszukuje źródła dla *prefiksu wzorca nazwy. property_name*.</span><span class="sxs-lookup"><span data-stu-id="5950c-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="5950c-524">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="5950c-525">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="5950c-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="5950c-526">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="5950c-527">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="5950c-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="5950c-528">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="5950c-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="5950c-529">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="5950c-529">Prefix = parameter name</span></span>

<span data-ttu-id="5950c-530">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="5950c-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="5950c-531">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="5950c-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="5950c-532">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="5950c-533">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="5950c-533">Prefix = property name</span></span>

<span data-ttu-id="5950c-534">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="5950c-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="5950c-535">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="5950c-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="5950c-536">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="5950c-537">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="5950c-537">Custom prefix</span></span>

<span data-ttu-id="5950c-538">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="5950c-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="5950c-539">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="5950c-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="5950c-540">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="5950c-541">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="5950c-541">Attributes for complex type targets</span></span>

<span data-ttu-id="5950c-542">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="5950c-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="5950c-543">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="5950c-544">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="5950c-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="5950c-545">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5950c-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="5950c-546">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="5950c-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="5950c-547">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-547">[BindRequired] attribute</span></span>

<span data-ttu-id="5950c-548">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="5950c-549">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="5950c-550">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="5950c-551">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-551">[BindNever] attribute</span></span>

<span data-ttu-id="5950c-552">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="5950c-553">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="5950c-554">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="5950c-555">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-555">[Bind] attribute</span></span>

<span data-ttu-id="5950c-556">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="5950c-557">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="5950c-558">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="5950c-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="5950c-559">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="5950c-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="5950c-560">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="5950c-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="5950c-561">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="5950c-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="5950c-562">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5950c-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="5950c-563">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="5950c-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="5950c-564">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="5950c-564">Collections</span></span>

<span data-ttu-id="5950c-565">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="5950c-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="5950c-566">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="5950c-567">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-567">For example:</span></span>

* <span data-ttu-id="5950c-568">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="5950c-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="5950c-569">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="5950c-569">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="5950c-570">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="5950c-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="5950c-571">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="5950c-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="5950c-572">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="5950c-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="5950c-573">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="5950c-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="5950c-574">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="5950c-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="5950c-575">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="5950c-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="5950c-576">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="5950c-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="5950c-577">Słowniki</span><span class="sxs-lookup"><span data-stu-id="5950c-577">Dictionaries</span></span>

<span data-ttu-id="5950c-578">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="5950c-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="5950c-579">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="5950c-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="5950c-580">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-580">For example:</span></span>

* <span data-ttu-id="5950c-581">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="5950c-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="5950c-582">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="5950c-582">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="5950c-583">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="5950c-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="5950c-584">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="5950c-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="5950c-585">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="5950c-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="5950c-586">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="5950c-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="5950c-587">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="5950c-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="5950c-588">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="5950c-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="5950c-589">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="5950c-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="5950c-590">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="5950c-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="5950c-591">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="5950c-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="5950c-592">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="5950c-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="5950c-593">Dziedzicz po <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="5950c-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="5950c-594">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="5950c-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="5950c-595">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="5950c-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="5950c-596">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="5950c-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="5950c-597">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="5950c-597">Special data types</span></span>

<span data-ttu-id="5950c-598">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="5950c-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="5950c-599">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="5950c-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="5950c-600">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5950c-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="5950c-601">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="5950c-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="5950c-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="5950c-602">CancellationToken</span></span>

<span data-ttu-id="5950c-603">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="5950c-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="5950c-604">Formularz</span><span class="sxs-lookup"><span data-stu-id="5950c-604">FormCollection</span></span>

<span data-ttu-id="5950c-605">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="5950c-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="5950c-606">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="5950c-606">Input formatters</span></span>

<span data-ttu-id="5950c-607">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="5950c-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="5950c-608">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="5950c-609">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="5950c-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="5950c-610">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="5950c-611">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="5950c-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="5950c-612">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="5950c-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="5950c-613">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="5950c-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="5950c-614">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="5950c-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="5950c-615">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="5950c-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="5950c-616">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="5950c-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="5950c-617">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="5950c-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="5950c-618">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="5950c-619">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="5950c-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="5950c-620">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="5950c-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="5950c-621">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="5950c-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="5950c-622">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5950c-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5950c-623">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="5950c-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="5950c-624">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5950c-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5950c-625">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="5950c-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="5950c-626">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-626">Custom model binders</span></span>

<span data-ttu-id="5950c-627">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="5950c-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="5950c-628">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="5950c-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="5950c-629">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="5950c-629">Manual model binding</span></span>

<span data-ttu-id="5950c-630">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="5950c-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="5950c-631">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="5950c-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="5950c-632">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="5950c-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="5950c-633">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="5950c-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="5950c-634">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="5950c-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="5950c-635">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="5950c-635">[FromServices] attribute</span></span>

<span data-ttu-id="5950c-636">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="5950c-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="5950c-637">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="5950c-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="5950c-638">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="5950c-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5950c-639">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="5950c-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5950c-640">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="5950c-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
