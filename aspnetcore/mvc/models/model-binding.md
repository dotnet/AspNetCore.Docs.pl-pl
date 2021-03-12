---
title: Powiązanie modelu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak działa powiązanie modelu w ASP.NET Core i jak dostosować jego zachowanie.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 5eaedf6dbe5df59848b9cf8a5bda67add48db2a6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586946"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="a03a4-103">Powiązanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a03a4-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a03a4-104">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="a03a4-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a03a4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="a03a4-106">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-106">What is Model binding</span></span>

<span data-ttu-id="a03a4-107">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="a03a4-108">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="a03a4-109">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="a03a4-110">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="a03a4-110">Model binding automates this process.</span></span> <span data-ttu-id="a03a4-111">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="a03a4-111">The model binding system:</span></span>

* <span data-ttu-id="a03a4-112">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="a03a4-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="a03a4-113">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="a03a4-114">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="a03a4-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="a03a4-115">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="a03a4-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="a03a4-116">Example</span></span>

<span data-ttu-id="a03a4-117">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="a03a4-118">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a03a4-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="a03a4-119">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="a03a4-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="a03a4-120">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="a03a4-121">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="a03a4-122">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="a03a4-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="a03a4-123">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="a03a4-124">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="a03a4-125">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="a03a4-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="a03a4-126">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="a03a4-127">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="a03a4-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="a03a4-128">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="a03a4-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="a03a4-129">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="a03a4-130">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="a03a4-131">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="a03a4-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="a03a4-132">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="a03a4-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="a03a4-133">Targets (Obiekty docelowe)</span><span class="sxs-lookup"><span data-stu-id="a03a4-133">Targets</span></span>

<span data-ttu-id="a03a4-134">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="a03a4-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="a03a4-135">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="a03a4-136">Parametry Razor metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="a03a4-137">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="a03a4-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="a03a4-138">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-138">[BindProperty] attribute</span></span>

<span data-ttu-id="a03a4-139">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="a03a4-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="a03a4-140">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-140">[BindProperties] attribute</span></span>

<span data-ttu-id="a03a4-141">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="a03a4-142">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="a03a4-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="a03a4-143">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="a03a4-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="a03a4-144">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="a03a4-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="a03a4-145">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="a03a4-146">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="a03a4-147">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="a03a4-148">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="a03a4-149">Źródła</span><span class="sxs-lookup"><span data-stu-id="a03a4-149">Sources</span></span>

<span data-ttu-id="a03a4-150">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="a03a4-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="a03a4-151">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="a03a4-151">Form fields</span></span>
1. <span data-ttu-id="a03a4-152">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="a03a4-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="a03a4-153">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="a03a4-153">Route data</span></span>
1. <span data-ttu-id="a03a4-154">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="a03a4-154">Query string parameters</span></span>
1. <span data-ttu-id="a03a4-155">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="a03a4-155">Uploaded files</span></span>

<span data-ttu-id="a03a4-156">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="a03a4-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="a03a4-157">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a03a4-157">There are a few exceptions:</span></span>

* <span data-ttu-id="a03a4-158">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="a03a4-159">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="a03a4-160">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="a03a4-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="a03a4-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="a03a4-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="a03a4-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="a03a4-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="a03a4-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="a03a4-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="a03a4-166">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="a03a4-166">These attributes:</span></span>

* <span data-ttu-id="a03a4-167">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="a03a4-168">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="a03a4-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="a03a4-169">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="a03a4-170">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="a03a4-171">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-171">[FromBody] attribute</span></span>

<span data-ttu-id="a03a4-172">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="a03a4-173">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="a03a4-174">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="a03a4-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="a03a4-175">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="a03a4-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="a03a4-176">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="a03a4-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="a03a4-177">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="a03a4-178">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-178">In the preceding example:</span></span>

* <span data-ttu-id="a03a4-179">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="a03a4-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="a03a4-180">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="a03a4-181">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="a03a4-182">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="a03a4-183">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a03a4-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="a03a4-184">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="a03a4-185">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="a03a4-185">Additional sources</span></span>

<span data-ttu-id="a03a4-186">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="a03a4-187">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="a03a4-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="a03a4-188">Na przykład możesz potrzebować danych z cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="a03a4-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="a03a4-189">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="a03a4-189">To get data from a new source:</span></span>

* <span data-ttu-id="a03a4-190">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="a03a4-191">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="a03a4-192">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="a03a4-193">Aplikacja Przykładowa zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z cookie s.</span><span class="sxs-lookup"><span data-stu-id="a03a4-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="a03a4-194">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="a03a4-195">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="a03a4-196">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="a03a4-197">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-197">No source for a model property</span></span>

<span data-ttu-id="a03a4-198">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="a03a4-199">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="a03a4-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="a03a4-200">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="a03a4-201">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="a03a4-202">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="a03a4-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="a03a4-203">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="a03a4-204">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="a03a4-205">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="a03a4-206">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="a03a4-207">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="a03a4-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="a03a4-208">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="a03a4-208">Type conversion errors</span></span>

<span data-ttu-id="a03a4-209">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="a03a4-210">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="a03a4-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="a03a4-211">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="a03a4-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="a03a4-212">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="a03a4-213">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do Razor formularza stron.</span><span class="sxs-lookup"><span data-stu-id="a03a4-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="a03a4-214">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="a03a4-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="a03a4-215">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="a03a4-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="a03a4-216">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="a03a4-217">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="a03a4-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="a03a4-218">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="a03a4-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="a03a4-219">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="a03a4-220">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="a03a4-221">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="a03a4-222">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="a03a4-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="a03a4-223">Typy proste</span><span class="sxs-lookup"><span data-stu-id="a03a4-223">Simple types</span></span>

<span data-ttu-id="a03a4-224">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a03a4-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="a03a4-225">Wartość logiczna</span><span class="sxs-lookup"><span data-stu-id="a03a4-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="a03a4-226">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="a03a4-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="a03a4-227">Delikatn</span><span class="sxs-lookup"><span data-stu-id="a03a4-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="a03a4-228">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="a03a4-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="a03a4-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="a03a4-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="a03a4-230">Dokładności</span><span class="sxs-lookup"><span data-stu-id="a03a4-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="a03a4-231">Double</span><span class="sxs-lookup"><span data-stu-id="a03a4-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="a03a4-232">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="a03a4-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="a03a4-233">Ident</span><span class="sxs-lookup"><span data-stu-id="a03a4-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="a03a4-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="a03a4-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="a03a4-235">Pojedynczy</span><span class="sxs-lookup"><span data-stu-id="a03a4-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="a03a4-236">Czasu</span><span class="sxs-lookup"><span data-stu-id="a03a4-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="a03a4-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="a03a4-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="a03a4-238">Adresu</span><span class="sxs-lookup"><span data-stu-id="a03a4-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="a03a4-239">Wersja</span><span class="sxs-lookup"><span data-stu-id="a03a4-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="a03a4-240">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="a03a4-240">Complex types</span></span>

<span data-ttu-id="a03a4-241">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="a03a4-242">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="a03a4-243">Dla każdej właściwości typu złożonego powiązanie modelu wyszukuje źródła nazwy wzorca *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="a03a4-244">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="a03a4-245">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="a03a4-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="a03a4-246">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="a03a4-247">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="a03a4-248">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="a03a4-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="a03a4-249">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="a03a4-249">Prefix = parameter name</span></span>

<span data-ttu-id="a03a4-250">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="a03a4-251">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="a03a4-252">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="a03a4-253">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="a03a4-253">Prefix = property name</span></span>

<span data-ttu-id="a03a4-254">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="a03a4-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="a03a4-255">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a03a4-256">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="a03a4-257">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="a03a4-257">Custom prefix</span></span>

<span data-ttu-id="a03a4-258">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="a03a4-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="a03a4-259">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a03a4-260">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="a03a4-261">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="a03a4-261">Attributes for complex type targets</span></span>

<span data-ttu-id="a03a4-262">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="a03a4-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="a03a4-263">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="a03a4-264">***Nie*** wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="a03a4-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="a03a4-265">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="a03a4-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="a03a4-266">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-266">[Bind] attribute</span></span>

<span data-ttu-id="a03a4-267">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="a03a4-268">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="a03a4-269">`[Bind]` nie ***ma wpływu na*** wejściowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="a03a4-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="a03a4-270">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="a03a4-271">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="a03a4-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="a03a4-272">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="a03a4-273">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="a03a4-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="a03a4-274">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="a03a4-275">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="a03a4-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="a03a4-276">[ModelBinder] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="a03a4-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> można zastosować do typów, właściwości lub parametrów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="a03a4-278">Umożliwia określenie typu spinacza modelu używanego do powiązania określonego wystąpienia lub typu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="a03a4-279">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="a03a4-280">Ten `[ModelBinder]` atrybut może być również używany do zmiany nazwy właściwości lub parametru, gdy jest on powiązany z modelem:</span><span class="sxs-lookup"><span data-stu-id="a03a4-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="a03a4-281">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-281">[BindRequired] attribute</span></span>

<span data-ttu-id="a03a4-282">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a03a4-283">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="a03a4-284">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="a03a4-285">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="a03a4-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="a03a4-286">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-286">[BindNever] attribute</span></span>

<span data-ttu-id="a03a4-287">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a03a4-288">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="a03a4-289">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="a03a4-290">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="a03a4-290">Collections</span></span>

<span data-ttu-id="a03a4-291">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a03a4-292">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a03a4-293">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-293">For example:</span></span>

* <span data-ttu-id="a03a4-294">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="a03a4-295">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="a03a4-295">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="a03a4-296">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="a03a4-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="a03a4-297">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="a03a4-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a03a4-298">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="a03a4-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="a03a4-299">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="a03a4-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="a03a4-300">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="a03a4-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="a03a4-301">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="a03a4-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="a03a4-302">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="a03a4-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="a03a4-303">Słowniki</span><span class="sxs-lookup"><span data-stu-id="a03a4-303">Dictionaries</span></span>

<span data-ttu-id="a03a4-304">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a03a4-305">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a03a4-306">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-306">For example:</span></span>

* <span data-ttu-id="a03a4-307">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="a03a4-308">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="a03a4-308">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="a03a4-309">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="a03a4-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a03a4-310">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="a03a4-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="a03a4-311">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="a03a4-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="a03a4-312">Typy powiązań konstruktora i rekordów</span><span class="sxs-lookup"><span data-stu-id="a03a4-312">Constructor binding and record types</span></span>

<span data-ttu-id="a03a4-313">Powiązanie modelu wymaga, aby typy złożone miały Konstruktor bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="a03a4-314">Zarówno programowy `System.Text.Json` , jak i `Newtonsoft.Json` oparty na danych wejściowych elementy formatujące obsługują deserializacja klas, które nie mają konstruktora bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="a03a4-315">W języku C# 9 wprowadzono typy rekordów, które są doskonałym sposobem na zwięzłe przedstawianie danych przez sieć.</span><span class="sxs-lookup"><span data-stu-id="a03a4-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="a03a4-316">ASP.NET Core dodaje obsługę powiązania modelu i sprawdzanie poprawności typów rekordów przy użyciu pojedynczego konstruktora:</span><span class="sxs-lookup"><span data-stu-id="a03a4-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

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

<span data-ttu-id="a03a4-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a03a4-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="a03a4-318">Podczas sprawdzania poprawności typów rekordów środowisko uruchomieniowe wyszukuje metadane walidacji w zależności od parametrów, a nie właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="a03a4-319">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="a03a4-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="a03a4-320">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="a03a4-321">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="a03a4-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="a03a4-322">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="a03a4-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="a03a4-323">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="a03a4-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="a03a4-324">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="a03a4-325">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="a03a4-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="a03a4-326">Dziedzicz po <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="a03a4-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="a03a4-327">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="a03a4-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="a03a4-328">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="a03a4-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="a03a4-329">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="a03a4-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="a03a4-330">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="a03a4-330">Special data types</span></span>

<span data-ttu-id="a03a4-331">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="a03a4-332">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="a03a4-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="a03a4-333">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="a03a4-334">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="a03a4-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="a03a4-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="a03a4-335">CancellationToken</span></span>

<span data-ttu-id="a03a4-336">Akcje można opcjonalnie powiązać `CancellationToken` jako parametr.</span><span class="sxs-lookup"><span data-stu-id="a03a4-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="a03a4-337">To wiąże <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> się z tym, że w przypadku przerwania połączenia z żądaniem http zostało przerwane.</span><span class="sxs-lookup"><span data-stu-id="a03a4-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="a03a4-338">Akcje mogą używać tego parametru, aby anulować długotrwałe operacje asynchroniczne, które są wykonywane w ramach akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a03a4-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="a03a4-339">Formularz</span><span class="sxs-lookup"><span data-stu-id="a03a4-339">FormCollection</span></span>

<span data-ttu-id="a03a4-340">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="a03a4-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="a03a4-341">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="a03a4-341">Input formatters</span></span>

<span data-ttu-id="a03a4-342">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="a03a4-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="a03a4-343">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="a03a4-344">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="a03a4-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="a03a4-345">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="a03a4-346">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="a03a4-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="a03a4-347">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="a03a4-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="a03a4-348">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="a03a4-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="a03a4-349">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="a03a4-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="a03a4-350">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="a03a4-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="a03a4-351">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="a03a4-352">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="a03a4-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="a03a4-353">Dostosowywanie powiązania modelu z użyciem danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="a03a4-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="a03a4-354">Wejściowy program formatujący pobiera pełną odpowiedzialność za odczytywanie danych z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="a03a4-355">Aby dostosować ten proces, należy skonfigurować interfejsy API używane przez dane wejściowe programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="a03a4-356">W tej sekcji opisano sposób dostosowywania programu `System.Text.Json` formatującego wejściowego opartego na danych wejściowych w celu zrozumienia niestandardowego typu o nazwie `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="a03a4-357">Rozważmy następujący model, który zawiera właściwość niestandardową `ObjectId` o nazwie `Id` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="a03a4-358">Aby dostosować proces powiązania modelu przy użyciu `System.Text.Json` , Utwórz klasę pochodną <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="a03a4-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="a03a4-359">Aby użyć niestandardowego konwertera, Zastosuj <xref:System.Text.Json.Serialization.JsonConverterAttribute> atrybut do typu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="a03a4-360">W poniższym przykładzie `ObjectId` Typ jest skonfigurowany z użyciem `ObjectIdConverter` jako konwertera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="a03a4-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="a03a4-361">Aby uzyskać więcej informacji, zobacz [jak pisać konwertery niestandardowe](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="a03a4-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="a03a4-362">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="a03a4-363">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="a03a4-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="a03a4-364">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="a03a4-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="a03a4-365">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="a03a4-366">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a03a4-367">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="a03a4-368">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a03a4-369">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="a03a4-370">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-370">Custom model binders</span></span>

<span data-ttu-id="a03a4-371">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="a03a4-372">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="a03a4-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="a03a4-373">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-373">Manual model binding</span></span> 

<span data-ttu-id="a03a4-374">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="a03a4-375">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="a03a4-376">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="a03a4-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="a03a4-377">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="a03a4-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="a03a4-378">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="a03a4-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  używa dostawców wartości do pobierania danych z formularza, ciągu zapytania i danych tras.</span><span class="sxs-lookup"><span data-stu-id="a03a4-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="a03a4-380">`TryUpdateModelAsync` zwykle:</span><span class="sxs-lookup"><span data-stu-id="a03a4-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="a03a4-381">Używany ze Razor stronami i aplikacjami MVC korzystającymi z kontrolerów i widoków, aby zapobiec nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="a03a4-382">Nieużywany z interfejsem API sieci Web, chyba że jest używane z danych formularza, ciągów zapytań i danych tras.</span><span class="sxs-lookup"><span data-stu-id="a03a4-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="a03a4-383">Punkty końcowe interfejsu API sieci Web, które [używają formatu JSON, do](#input-formatters) deserializacji treści żądania do obiektu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="a03a4-384">Aby uzyskać więcej informacji, zobacz [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="a03a4-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="a03a4-385">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-385">[FromServices] attribute</span></span>

<span data-ttu-id="a03a4-386">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="a03a4-387">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="a03a4-388">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="a03a4-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a03a4-389">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="a03a4-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a03a4-390">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a03a4-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a03a4-391">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="a03a4-392">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a03a4-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="a03a4-393">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-393">What is Model binding</span></span>

<span data-ttu-id="a03a4-394">Kontrolery i Razor strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="a03a4-395">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="a03a4-396">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="a03a4-397">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="a03a4-397">Model binding automates this process.</span></span> <span data-ttu-id="a03a4-398">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="a03a4-398">The model binding system:</span></span>

* <span data-ttu-id="a03a4-399">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="a03a4-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="a03a4-400">Dostarcza dane do kontrolerów i Razor stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="a03a4-401">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="a03a4-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="a03a4-402">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="a03a4-403">Przykład</span><span class="sxs-lookup"><span data-stu-id="a03a4-403">Example</span></span>

<span data-ttu-id="a03a4-404">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="a03a4-405">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a03a4-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="a03a4-406">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="a03a4-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="a03a4-407">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="a03a4-408">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="a03a4-409">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="a03a4-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="a03a4-410">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="a03a4-411">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="a03a4-412">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="a03a4-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="a03a4-413">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="a03a4-414">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="a03a4-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="a03a4-415">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="a03a4-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="a03a4-416">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="a03a4-417">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="a03a4-418">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="a03a4-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="a03a4-419">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="a03a4-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="a03a4-420">Targets (Obiekty docelowe)</span><span class="sxs-lookup"><span data-stu-id="a03a4-420">Targets</span></span>

<span data-ttu-id="a03a4-421">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="a03a4-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="a03a4-422">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="a03a4-423">Parametry Razor metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="a03a4-424">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="a03a4-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="a03a4-425">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-425">[BindProperty] attribute</span></span>

<span data-ttu-id="a03a4-426">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="a03a4-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="a03a4-427">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-427">[BindProperties] attribute</span></span>

<span data-ttu-id="a03a4-428">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="a03a4-429">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="a03a4-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="a03a4-430">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="a03a4-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="a03a4-431">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="a03a4-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="a03a4-432">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="a03a4-433">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="a03a4-434">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="a03a4-435">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="a03a4-436">Źródła</span><span class="sxs-lookup"><span data-stu-id="a03a4-436">Sources</span></span>

<span data-ttu-id="a03a4-437">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="a03a4-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="a03a4-438">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="a03a4-438">Form fields</span></span>
1. <span data-ttu-id="a03a4-439">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="a03a4-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="a03a4-440">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="a03a4-440">Route data</span></span>
1. <span data-ttu-id="a03a4-441">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="a03a4-441">Query string parameters</span></span>
1. <span data-ttu-id="a03a4-442">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="a03a4-442">Uploaded files</span></span>

<span data-ttu-id="a03a4-443">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="a03a4-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="a03a4-444">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="a03a4-444">There are a few exceptions:</span></span>

* <span data-ttu-id="a03a4-445">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="a03a4-446">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="a03a4-447">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="a03a4-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="a03a4-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="a03a4-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="a03a4-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="a03a4-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="a03a4-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="a03a4-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="a03a4-453">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="a03a4-453">These attributes:</span></span>

* <span data-ttu-id="a03a4-454">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="a03a4-455">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="a03a4-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="a03a4-456">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="a03a4-457">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="a03a4-458">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-458">[FromBody] attribute</span></span>

<span data-ttu-id="a03a4-459">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="a03a4-460">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="a03a4-461">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="a03a4-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="a03a4-462">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="a03a4-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="a03a4-463">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="a03a4-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="a03a4-464">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="a03a4-465">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-465">In the preceding example:</span></span>

* <span data-ttu-id="a03a4-466">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="a03a4-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="a03a4-467">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="a03a4-468">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="a03a4-469">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="a03a4-470">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="a03a4-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="a03a4-471">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="a03a4-472">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="a03a4-472">Additional sources</span></span>

<span data-ttu-id="a03a4-473">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="a03a4-474">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="a03a4-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="a03a4-475">Na przykład możesz potrzebować danych z cookie lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="a03a4-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="a03a4-476">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="a03a4-476">To get data from a new source:</span></span>

* <span data-ttu-id="a03a4-477">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="a03a4-478">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="a03a4-479">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="a03a4-480">Aplikacja Przykładowa zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , który pobiera wartości z cookie s.</span><span class="sxs-lookup"><span data-stu-id="a03a4-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="a03a4-481">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="a03a4-482">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="a03a4-483">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new CookieValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="a03a4-484">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-484">No source for a model property</span></span>

<span data-ttu-id="a03a4-485">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="a03a4-486">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="a03a4-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="a03a4-487">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="a03a4-488">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="a03a4-489">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="a03a4-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="a03a4-490">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="a03a4-491">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="a03a4-492">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="a03a4-493">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="a03a4-494">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="a03a4-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="a03a4-495">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="a03a4-495">Type conversion errors</span></span>

<span data-ttu-id="a03a4-496">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="a03a4-497">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="a03a4-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="a03a4-498">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="a03a4-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="a03a4-499">Na Razor stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="a03a4-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="a03a4-500">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do Razor formularza stron.</span><span class="sxs-lookup"><span data-stu-id="a03a4-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="a03a4-501">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="a03a4-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="a03a4-502">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="a03a4-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="a03a4-503">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="a03a4-504">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="a03a4-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="a03a4-505">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="a03a4-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="a03a4-506">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="a03a4-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="a03a4-507">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="a03a4-508">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="a03a4-509">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="a03a4-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="a03a4-510">Typy proste</span><span class="sxs-lookup"><span data-stu-id="a03a4-510">Simple types</span></span>

<span data-ttu-id="a03a4-511">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a03a4-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="a03a4-512">Wartość logiczna</span><span class="sxs-lookup"><span data-stu-id="a03a4-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="a03a4-513">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="a03a4-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="a03a4-514">Delikatn</span><span class="sxs-lookup"><span data-stu-id="a03a4-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="a03a4-515">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="a03a4-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="a03a4-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="a03a4-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="a03a4-517">Dokładności</span><span class="sxs-lookup"><span data-stu-id="a03a4-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="a03a4-518">Double</span><span class="sxs-lookup"><span data-stu-id="a03a4-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="a03a4-519">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="a03a4-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="a03a4-520">Ident</span><span class="sxs-lookup"><span data-stu-id="a03a4-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="a03a4-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="a03a4-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="a03a4-522">Pojedynczy</span><span class="sxs-lookup"><span data-stu-id="a03a4-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="a03a4-523">Czasu</span><span class="sxs-lookup"><span data-stu-id="a03a4-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="a03a4-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="a03a4-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="a03a4-525">Adresu</span><span class="sxs-lookup"><span data-stu-id="a03a4-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="a03a4-526">Wersja</span><span class="sxs-lookup"><span data-stu-id="a03a4-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="a03a4-527">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="a03a4-527">Complex types</span></span>

<span data-ttu-id="a03a4-528">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="a03a4-529">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="a03a4-530">Dla każdej właściwości typu złożonego powiązanie modelu wyszukuje źródła nazwy wzorca *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="a03a4-531">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="a03a4-532">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="a03a4-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="a03a4-533">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="a03a4-534">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="a03a4-535">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="a03a4-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="a03a4-536">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="a03a4-536">Prefix = parameter name</span></span>

<span data-ttu-id="a03a4-537">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="a03a4-538">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="a03a4-539">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="a03a4-540">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="a03a4-540">Prefix = property name</span></span>

<span data-ttu-id="a03a4-541">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="a03a4-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="a03a4-542">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a03a4-543">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="a03a4-544">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="a03a4-544">Custom prefix</span></span>

<span data-ttu-id="a03a4-545">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="a03a4-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="a03a4-546">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a03a4-547">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="a03a4-548">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="a03a4-548">Attributes for complex type targets</span></span>

<span data-ttu-id="a03a4-549">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="a03a4-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="a03a4-550">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="a03a4-551">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="a03a4-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="a03a4-552">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="a03a4-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="a03a4-553">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="a03a4-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="a03a4-554">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-554">[BindRequired] attribute</span></span>

<span data-ttu-id="a03a4-555">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a03a4-556">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="a03a4-557">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="a03a4-558">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-558">[BindNever] attribute</span></span>

<span data-ttu-id="a03a4-559">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a03a4-560">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="a03a4-561">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="a03a4-562">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-562">[Bind] attribute</span></span>

<span data-ttu-id="a03a4-563">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="a03a4-564">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="a03a4-565">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="a03a4-566">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="a03a4-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="a03a4-567">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="a03a4-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="a03a4-568">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="a03a4-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="a03a4-569">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="a03a4-570">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="a03a4-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="a03a4-571">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="a03a4-571">Collections</span></span>

<span data-ttu-id="a03a4-572">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a03a4-573">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a03a4-574">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-574">For example:</span></span>

* <span data-ttu-id="a03a4-575">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="a03a4-576">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="a03a4-576">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="a03a4-577">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="a03a4-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="a03a4-578">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="a03a4-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a03a4-579">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="a03a4-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="a03a4-580">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="a03a4-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="a03a4-581">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="a03a4-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="a03a4-582">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="a03a4-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="a03a4-583">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="a03a4-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="a03a4-584">Słowniki</span><span class="sxs-lookup"><span data-stu-id="a03a4-584">Dictionaries</span></span>

<span data-ttu-id="a03a4-585">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="a03a4-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a03a4-586">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a03a4-587">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-587">For example:</span></span>

* <span data-ttu-id="a03a4-588">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="a03a4-589">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="a03a4-589">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="a03a4-590">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="a03a4-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a03a4-591">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="a03a4-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="a03a4-592">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="a03a4-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="a03a4-593">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="a03a4-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="a03a4-594">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="a03a4-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="a03a4-595">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="a03a4-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="a03a4-596">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="a03a4-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="a03a4-597">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="a03a4-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="a03a4-598">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="a03a4-599">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="a03a4-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="a03a4-600">Dziedzicz po <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="a03a4-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="a03a4-601">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="a03a4-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="a03a4-602">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="a03a4-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="a03a4-603">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="a03a4-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="a03a4-604">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="a03a4-604">Special data types</span></span>

<span data-ttu-id="a03a4-605">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="a03a4-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="a03a4-606">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="a03a4-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="a03a4-607">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a03a4-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="a03a4-608">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="a03a4-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="a03a4-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="a03a4-609">CancellationToken</span></span>

<span data-ttu-id="a03a4-610">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="a03a4-611">Formularz</span><span class="sxs-lookup"><span data-stu-id="a03a4-611">FormCollection</span></span>

<span data-ttu-id="a03a4-612">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="a03a4-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="a03a4-613">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="a03a4-613">Input formatters</span></span>

<span data-ttu-id="a03a4-614">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="a03a4-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="a03a4-615">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="a03a4-616">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="a03a4-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="a03a4-617">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="a03a4-618">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="a03a4-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="a03a4-619">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="a03a4-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="a03a4-620">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="a03a4-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="a03a4-621">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="a03a4-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="a03a4-622">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="a03a4-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="a03a4-623">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="a03a4-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="a03a4-624">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="a03a4-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="a03a4-625">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="a03a4-626">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="a03a4-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="a03a4-627">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="a03a4-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="a03a4-628">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="a03a4-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="a03a4-629">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a03a4-630">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="a03a4-631">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a03a4-632">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="a03a4-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="a03a4-633">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-633">Custom model binders</span></span>

<span data-ttu-id="a03a4-634">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="a03a4-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="a03a4-635">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="a03a4-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="a03a4-636">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="a03a4-636">Manual model binding</span></span>

<span data-ttu-id="a03a4-637">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="a03a4-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="a03a4-638">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="a03a4-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="a03a4-639">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="a03a4-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="a03a4-640">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="a03a4-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="a03a4-641">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="a03a4-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="a03a4-642">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="a03a4-642">[FromServices] attribute</span></span>

<span data-ttu-id="a03a4-643">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="a03a4-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="a03a4-644">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="a03a4-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="a03a4-645">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="a03a4-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a03a4-646">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="a03a4-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a03a4-647">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a03a4-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
