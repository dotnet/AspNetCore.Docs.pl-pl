---
title: Powiązanie modelu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak działa powiązanie modelu w ASP.NET Core i jak dostosować jego zachowanie.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/models/model-binding
ms.openlocfilehash: 49300d32096e577db9b13a0510cc310b91ddb51d
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365356"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="fd18a-103">Powiązanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd18a-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fd18a-104">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="fd18a-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fd18a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="fd18a-106">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-106">What is Model binding</span></span>

<span data-ttu-id="fd18a-107">Kontrolery i :::no-loc(Razor)::: strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-107">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="fd18a-108">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="fd18a-109">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="fd18a-110">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="fd18a-110">Model binding automates this process.</span></span> <span data-ttu-id="fd18a-111">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="fd18a-111">The model binding system:</span></span>

* <span data-ttu-id="fd18a-112">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="fd18a-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="fd18a-113">Dostarcza dane do kontrolerów i :::no-loc(Razor)::: stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-113">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="fd18a-114">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="fd18a-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="fd18a-115">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="fd18a-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="fd18a-116">Example</span></span>

<span data-ttu-id="fd18a-117">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="fd18a-118">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="fd18a-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="fd18a-119">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="fd18a-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="fd18a-120">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="fd18a-121">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="fd18a-122">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="fd18a-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="fd18a-123">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="fd18a-124">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="fd18a-125">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="fd18a-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="fd18a-126">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="fd18a-127">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="fd18a-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="fd18a-128">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="fd18a-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="fd18a-129">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="fd18a-130">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="fd18a-131">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="fd18a-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="fd18a-132">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="fd18a-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="fd18a-133">Targets (Obiekty docelowe)</span><span class="sxs-lookup"><span data-stu-id="fd18a-133">Targets</span></span>

<span data-ttu-id="fd18a-134">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="fd18a-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="fd18a-135">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="fd18a-136">Parametry :::no-loc(Razor)::: metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-136">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="fd18a-137">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="fd18a-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="fd18a-138">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-138">[BindProperty] attribute</span></span>

<span data-ttu-id="fd18a-139">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="fd18a-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="fd18a-140">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-140">[BindProperties] attribute</span></span>

<span data-ttu-id="fd18a-141">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="fd18a-142">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="fd18a-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="fd18a-143">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="fd18a-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="fd18a-144">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="fd18a-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="fd18a-145">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="fd18a-146">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="fd18a-147">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="fd18a-148">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="fd18a-149">Źródła</span><span class="sxs-lookup"><span data-stu-id="fd18a-149">Sources</span></span>

<span data-ttu-id="fd18a-150">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="fd18a-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="fd18a-151">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="fd18a-151">Form fields</span></span>
1. <span data-ttu-id="fd18a-152">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="fd18a-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="fd18a-153">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="fd18a-153">Route data</span></span>
1. <span data-ttu-id="fd18a-154">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="fd18a-154">Query string parameters</span></span>
1. <span data-ttu-id="fd18a-155">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="fd18a-155">Uploaded files</span></span>

<span data-ttu-id="fd18a-156">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="fd18a-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="fd18a-157">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="fd18a-157">There are a few exceptions:</span></span>

* <span data-ttu-id="fd18a-158">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="fd18a-159">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="fd18a-160">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="fd18a-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="fd18a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="fd18a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="fd18a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="fd18a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="fd18a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="fd18a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="fd18a-166">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="fd18a-166">These attributes:</span></span>

* <span data-ttu-id="fd18a-167">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="fd18a-168">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="fd18a-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="fd18a-169">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="fd18a-170">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="fd18a-171">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-171">[FromBody] attribute</span></span>

<span data-ttu-id="fd18a-172">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="fd18a-173">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="fd18a-174">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="fd18a-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="fd18a-175">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="fd18a-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="fd18a-176">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="fd18a-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="fd18a-177">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="fd18a-178">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-178">In the preceding example:</span></span>

* <span data-ttu-id="fd18a-179">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="fd18a-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="fd18a-180">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="fd18a-181">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="fd18a-182">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="fd18a-183">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="fd18a-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="fd18a-184">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="fd18a-185">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="fd18a-185">Additional sources</span></span>

<span data-ttu-id="fd18a-186">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="fd18a-187">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="fd18a-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="fd18a-188">Na przykład możesz potrzebować danych z :::no-loc(cookie)::: lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="fd18a-188">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="fd18a-189">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="fd18a-189">To get data from a new source:</span></span>

* <span data-ttu-id="fd18a-190">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="fd18a-191">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="fd18a-192">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="fd18a-193">Aplikacja Przykładowa zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) , który pobiera wartości z :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="fd18a-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="fd18a-194">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="fd18a-195">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="fd18a-196">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-196">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="fd18a-197">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-197">No source for a model property</span></span>

<span data-ttu-id="fd18a-198">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="fd18a-199">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="fd18a-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="fd18a-200">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="fd18a-201">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="fd18a-202">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="fd18a-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="fd18a-203">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="fd18a-204">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="fd18a-205">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="fd18a-206">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="fd18a-207">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="fd18a-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="fd18a-208">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="fd18a-208">Type conversion errors</span></span>

<span data-ttu-id="fd18a-209">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="fd18a-210">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="fd18a-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="fd18a-211">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="fd18a-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="fd18a-212">Na :::no-loc(Razor)::: stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-212">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="fd18a-213">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do :::no-loc(Razor)::: formularza stron.</span><span class="sxs-lookup"><span data-stu-id="fd18a-213">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="fd18a-214">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="fd18a-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="fd18a-215">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="fd18a-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="fd18a-216">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="fd18a-217">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="fd18a-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="fd18a-218">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="fd18a-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="fd18a-219">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="fd18a-220">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="fd18a-221">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="fd18a-222">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="fd18a-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="fd18a-223">Typy proste</span><span class="sxs-lookup"><span data-stu-id="fd18a-223">Simple types</span></span>

<span data-ttu-id="fd18a-224">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="fd18a-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="fd18a-225">Typu</span><span class="sxs-lookup"><span data-stu-id="fd18a-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="fd18a-226">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="fd18a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="fd18a-227">Delikatn</span><span class="sxs-lookup"><span data-stu-id="fd18a-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="fd18a-228">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="fd18a-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="fd18a-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="fd18a-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="fd18a-230">Dokładności</span><span class="sxs-lookup"><span data-stu-id="fd18a-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="fd18a-231">Double</span><span class="sxs-lookup"><span data-stu-id="fd18a-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="fd18a-232">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="fd18a-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="fd18a-233">Ident</span><span class="sxs-lookup"><span data-stu-id="fd18a-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="fd18a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="fd18a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="fd18a-235">Pojedynczy</span><span class="sxs-lookup"><span data-stu-id="fd18a-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="fd18a-236">Czasu</span><span class="sxs-lookup"><span data-stu-id="fd18a-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="fd18a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="fd18a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="fd18a-238">Adresu</span><span class="sxs-lookup"><span data-stu-id="fd18a-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="fd18a-239">Wersja</span><span class="sxs-lookup"><span data-stu-id="fd18a-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="fd18a-240">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="fd18a-240">Complex types</span></span>

<span data-ttu-id="fd18a-241">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="fd18a-242">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="fd18a-243">Dla każdej właściwości typu złożonego powiązanie modelu wyszukuje źródła nazwy wzorca *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="fd18a-244">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="fd18a-245">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="fd18a-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="fd18a-246">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="fd18a-247">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="fd18a-248">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="fd18a-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="fd18a-249">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="fd18a-249">Prefix = parameter name</span></span>

<span data-ttu-id="fd18a-250">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="fd18a-251">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="fd18a-252">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="fd18a-253">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="fd18a-253">Prefix = property name</span></span>

<span data-ttu-id="fd18a-254">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="fd18a-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="fd18a-255">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="fd18a-256">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="fd18a-257">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="fd18a-257">Custom prefix</span></span>

<span data-ttu-id="fd18a-258">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="fd18a-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="fd18a-259">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="fd18a-260">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="fd18a-261">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="fd18a-261">Attributes for complex type targets</span></span>

<span data-ttu-id="fd18a-262">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="fd18a-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="fd18a-263">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="fd18a-264">**Nie mają** one wpływu na wejściowe elementy formatujące, które przetwarzają ogłoszone treści JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="fd18a-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="fd18a-265">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="fd18a-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="fd18a-266">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-266">[Bind] attribute</span></span>

<span data-ttu-id="fd18a-267">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="fd18a-268">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="fd18a-269">`[Bind]` nie _*_ma wpływu na_*_ wejściowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="fd18a-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="fd18a-270">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="fd18a-271">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="fd18a-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="fd18a-272">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach _create \*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="fd18a-273">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="fd18a-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="fd18a-274">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="fd18a-275">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="fd18a-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="fd18a-276">[ModelBinder] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="fd18a-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> można zastosować do typów, właściwości lub parametrów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="fd18a-278">Umożliwia określenie typu spinacza modelu używanego do powiązania określonego wystąpienia lub typu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="fd18a-279">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="fd18a-280">Ten `[ModelBinder]` atrybut może być również używany do zmiany nazwy właściwości lub parametru, gdy jest on powiązany z modelem:</span><span class="sxs-lookup"><span data-stu-id="fd18a-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="fd18a-281">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-281">[BindRequired] attribute</span></span>

<span data-ttu-id="fd18a-282">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="fd18a-283">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="fd18a-284">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="fd18a-285">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="fd18a-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="fd18a-286">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-286">[BindNever] attribute</span></span>

<span data-ttu-id="fd18a-287">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="fd18a-288">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="fd18a-289">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="fd18a-290">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="fd18a-290">Collections</span></span>

<span data-ttu-id="fd18a-291">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="fd18a-292">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="fd18a-293">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-293">For example:</span></span>

* <span data-ttu-id="fd18a-294">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="fd18a-295">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="fd18a-295">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="fd18a-296">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="fd18a-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="fd18a-297">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="fd18a-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="fd18a-298">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="fd18a-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="fd18a-299">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="fd18a-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="fd18a-300">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="fd18a-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="fd18a-301">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="fd18a-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="fd18a-302">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="fd18a-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="fd18a-303">Słowniki</span><span class="sxs-lookup"><span data-stu-id="fd18a-303">Dictionaries</span></span>

<span data-ttu-id="fd18a-304">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="fd18a-305">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="fd18a-306">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-306">For example:</span></span>

* <span data-ttu-id="fd18a-307">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="fd18a-308">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="fd18a-308">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="fd18a-309">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="fd18a-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="fd18a-310">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="fd18a-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="fd18a-311">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="fd18a-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="fd18a-312">Typy powiązań konstruktora i rekordów</span><span class="sxs-lookup"><span data-stu-id="fd18a-312">Constructor binding and record types</span></span>

<span data-ttu-id="fd18a-313">Powiązanie modelu wymaga, aby typy złożone miały Konstruktor bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="fd18a-314">Zarówno programowy `System.Text.Json` , jak i `Newtonsoft.Json` oparty na danych wejściowych elementy formatujące obsługują deserializacja klas, które nie mają konstruktora bez parametrów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="fd18a-315">W języku C# 9 wprowadzono typy rekordów, które są doskonałym sposobem na zwięzłe przedstawianie danych przez sieć.</span><span class="sxs-lookup"><span data-stu-id="fd18a-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="fd18a-316">ASP.NET Core dodaje obsługę powiązania modelu i sprawdzanie poprawności typów rekordów przy użyciu pojedynczego konstruktora:</span><span class="sxs-lookup"><span data-stu-id="fd18a-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

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

<span data-ttu-id="fd18a-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="fd18a-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="fd18a-318">Podczas sprawdzania poprawności typów rekordów środowisko uruchomieniowe wyszukuje metadane walidacji w zależności od parametrów, a nie właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="fd18a-319">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="fd18a-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="fd18a-320">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="fd18a-321">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="fd18a-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="fd18a-322">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="fd18a-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="fd18a-323">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="fd18a-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="fd18a-324">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="fd18a-325">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="fd18a-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="fd18a-326">Dziedzicz po <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="fd18a-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="fd18a-327">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="fd18a-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="fd18a-328">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="fd18a-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="fd18a-329">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="fd18a-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="fd18a-330">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="fd18a-330">Special data types</span></span>

<span data-ttu-id="fd18a-331">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="fd18a-332">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="fd18a-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="fd18a-333">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="fd18a-334">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="fd18a-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="fd18a-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="fd18a-335">CancellationToken</span></span>

<span data-ttu-id="fd18a-336">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-336">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="fd18a-337">Formularz</span><span class="sxs-lookup"><span data-stu-id="fd18a-337">FormCollection</span></span>

<span data-ttu-id="fd18a-338">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="fd18a-338">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="fd18a-339">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="fd18a-339">Input formatters</span></span>

<span data-ttu-id="fd18a-340">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="fd18a-340">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="fd18a-341">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-341">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="fd18a-342">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="fd18a-342">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="fd18a-343">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-343">You can add other formatters for other content types.</span></span>

<span data-ttu-id="fd18a-344">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="fd18a-344">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="fd18a-345">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="fd18a-345">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="fd18a-346">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="fd18a-346">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="fd18a-347">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="fd18a-347">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="fd18a-348">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="fd18a-348">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="fd18a-349">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-349">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="fd18a-350">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="fd18a-350">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="fd18a-351">Dostosowywanie powiązania modelu z użyciem danych wejściowych</span><span class="sxs-lookup"><span data-stu-id="fd18a-351">Customize model binding with input formatters</span></span>

<span data-ttu-id="fd18a-352">Wejściowy program formatujący pobiera pełną odpowiedzialność za odczytywanie danych z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-352">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="fd18a-353">Aby dostosować ten proces, należy skonfigurować interfejsy API używane przez dane wejściowe programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-353">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="fd18a-354">W tej sekcji opisano sposób dostosowywania programu `System.Text.Json` formatującego wejściowego opartego na danych wejściowych w celu zrozumienia niestandardowego typu o nazwie `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-354">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="fd18a-355">Rozważmy następujący model, który zawiera właściwość niestandardową `ObjectId` o nazwie `Id` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-355">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="fd18a-356">Aby dostosować proces powiązania modelu przy użyciu `System.Text.Json` , Utwórz klasę pochodną <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="fd18a-356">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="fd18a-357">Aby użyć niestandardowego konwertera, Zastosuj <xref:System.Text.Json.Serialization.JsonConverterAttribute> atrybut do typu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-357">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="fd18a-358">W poniższym przykładzie `ObjectId` Typ jest skonfigurowany z użyciem `ObjectIdConverter` jako konwertera niestandardowego:</span><span class="sxs-lookup"><span data-stu-id="fd18a-358">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="fd18a-359">Aby uzyskać więcej informacji, zobacz [jak pisać konwertery niestandardowe](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="fd18a-359">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="fd18a-360">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-360">Exclude specified types from model binding</span></span>

<span data-ttu-id="fd18a-361">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="fd18a-361">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="fd18a-362">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="fd18a-362">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="fd18a-363">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-363">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="fd18a-364">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-364">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fd18a-365">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-365">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="fd18a-366">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-366">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fd18a-367">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-367">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="fd18a-368">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-368">Custom model binders</span></span>

<span data-ttu-id="fd18a-369">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-369">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="fd18a-370">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="fd18a-370">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="fd18a-371">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-371">Manual model binding</span></span> 

<span data-ttu-id="fd18a-372">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-372">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="fd18a-373">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-373">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="fd18a-374">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="fd18a-374">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="fd18a-375">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="fd18a-375">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="fd18a-376">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-376">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="fd18a-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  używa dostawców wartości do pobierania danych z formularza, ciągu zapytania i danych tras.</span><span class="sxs-lookup"><span data-stu-id="fd18a-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="fd18a-378">`TryUpdateModelAsync` zwykle:</span><span class="sxs-lookup"><span data-stu-id="fd18a-378">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="fd18a-379">Używany ze :::no-loc(Razor)::: stronami i aplikacjami MVC korzystającymi z kontrolerów i widoków, aby zapobiec nadmiernemu księgowaniu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-379">Used with :::no-loc(Razor)::: Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="fd18a-380">Nieużywany z interfejsem API sieci Web, chyba że jest używane z danych formularza, ciągów zapytań i danych tras.</span><span class="sxs-lookup"><span data-stu-id="fd18a-380">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="fd18a-381">Punkty końcowe interfejsu API sieci Web, które [używają formatu JSON, do](#input-formatters) deserializacji treści żądania do obiektu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-381">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="fd18a-382">Aby uzyskać więcej informacji, zobacz [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="fd18a-382">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="fd18a-383">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-383">[FromServices] attribute</span></span>

<span data-ttu-id="fd18a-384">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-384">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="fd18a-385">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-385">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="fd18a-386">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="fd18a-386">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fd18a-387">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="fd18a-387">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd18a-388">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fd18a-388">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fd18a-389">W tym artykule wyjaśniono, co to jest powiązanie modelu, jak to działa i jak dostosować jego zachowanie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-389">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="fd18a-390">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fd18a-390">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="fd18a-391">Co to jest powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-391">What is Model binding</span></span>

<span data-ttu-id="fd18a-392">Kontrolery i :::no-loc(Razor)::: strony współpracują z danymi, które pochodzą z żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-392">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="fd18a-393">Na przykład dane trasy mogą dostarczyć klucz rekordu, a pola ogłoszone formularza mogą podawać wartości właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-393">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="fd18a-394">Pisanie kodu w celu pobrania każdej z tych wartości i przekonwertowania ich z ciągów na typy .NET byłoby żmudnym i podatne na błędy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-394">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="fd18a-395">Powiązanie modelu automatyzuje ten proces.</span><span class="sxs-lookup"><span data-stu-id="fd18a-395">Model binding automates this process.</span></span> <span data-ttu-id="fd18a-396">System powiązań modelu:</span><span class="sxs-lookup"><span data-stu-id="fd18a-396">The model binding system:</span></span>

* <span data-ttu-id="fd18a-397">Pobiera dane z różnych źródeł, takich jak dane tras, pola formularzy i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="fd18a-397">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="fd18a-398">Dostarcza dane do kontrolerów i :::no-loc(Razor)::: stron w parametrach metod i właściwościach publicznych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-398">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="fd18a-399">Konwertuje dane ciągu na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="fd18a-399">Converts string data to .NET types.</span></span>
* <span data-ttu-id="fd18a-400">Aktualizuje właściwości typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-400">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="fd18a-401">Przykład</span><span class="sxs-lookup"><span data-stu-id="fd18a-401">Example</span></span>

<span data-ttu-id="fd18a-402">Załóżmy, że masz następującą metodę działania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-402">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="fd18a-403">A aplikacja odbiera żądanie przy użyciu tego adresu URL:</span><span class="sxs-lookup"><span data-stu-id="fd18a-403">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="fd18a-404">Powiązanie modelu przechodzi przez następujące kroki, gdy system routingu wybierze metodę akcji:</span><span class="sxs-lookup"><span data-stu-id="fd18a-404">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="fd18a-405">Znajduje pierwszy parametr z `GetByID` , liczba całkowita o nazwie `id` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-405">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="fd18a-406">Wyszukuje dostępne źródła w żądaniu HTTP i odnajduje `id` = "2" w danych trasy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-406">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="fd18a-407">Konwertuje ciąg "2" na liczbę całkowitą 2.</span><span class="sxs-lookup"><span data-stu-id="fd18a-407">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="fd18a-408">Znajduje następny parametr elementu `GetByID` , wartość logiczna o nazwie `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-408">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="fd18a-409">Wyszukuje źródła i wyszukuje ciąg "DogsOnly = true" w ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-409">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="fd18a-410">W dopasowaniu nazw nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="fd18a-410">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="fd18a-411">Konwertuje ciąg "true" na wartość logiczną `true` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-411">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="fd18a-412">Struktura następnie wywołuje `GetById` metodę, przekazując wartość 2 dla `id` parametru i `true` dla `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="fd18a-412">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="fd18a-413">W poprzednim przykładzie elementy docelowe powiązań modelu to parametry metody, które są typami prostymi.</span><span class="sxs-lookup"><span data-stu-id="fd18a-413">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="fd18a-414">Elementy docelowe mogą być również właściwościami typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-414">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="fd18a-415">Po pomyślnym powiązaniu każdej właściwości [Walidacja modelu](xref:mvc/models/validation) jest wykonywana dla tej właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-415">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="fd18a-416">Rekord danych powiązanych z modelem oraz wszelkie błędy powiązań lub walidacji są przechowywane w [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) lub [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="fd18a-416">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="fd18a-417">Aby dowiedzieć się, czy ten proces zakończył się pomyślnie, aplikacja sprawdza flagę [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="fd18a-417">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="fd18a-418">Targets (Obiekty docelowe)</span><span class="sxs-lookup"><span data-stu-id="fd18a-418">Targets</span></span>

<span data-ttu-id="fd18a-419">Powiązanie modelu próbuje znaleźć wartości dla następujących rodzajów obiektów docelowych:</span><span class="sxs-lookup"><span data-stu-id="fd18a-419">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="fd18a-420">Parametry metody akcji kontrolera, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-420">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="fd18a-421">Parametry :::no-loc(Razor)::: metody obsługi stron, do której jest kierowane żądanie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-421">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="fd18a-422">Właściwości publiczne kontrolera lub `PageModel` klasy, jeśli są określone przez atrybuty.</span><span class="sxs-lookup"><span data-stu-id="fd18a-422">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="fd18a-423">[BindProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-423">[BindProperty] attribute</span></span>

<span data-ttu-id="fd18a-424">Można zastosować do właściwości publicznej kontrolera lub `PageModel` klasy, aby spowodować powiązanie modelu z celem tej właściwości:</span><span class="sxs-lookup"><span data-stu-id="fd18a-424">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="fd18a-425">[BindProperties] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-425">[BindProperties] attribute</span></span>

<span data-ttu-id="fd18a-426">Dostępne w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-426">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="fd18a-427">Można zastosować do kontrolera lub `PageModel` klasy, aby poinformować powiązanie modelu z elementem docelowym wszystkich właściwości publicznych klasy:</span><span class="sxs-lookup"><span data-stu-id="fd18a-427">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="fd18a-428">Powiązanie modelu dla żądań HTTP GET</span><span class="sxs-lookup"><span data-stu-id="fd18a-428">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="fd18a-429">Domyślnie właściwości nie są powiązane z żądaniami HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="fd18a-429">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="fd18a-430">Zazwyczaj wszystkie potrzebne do żądania GET są parametrem identyfikatora rekordu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-430">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="fd18a-431">Identyfikator rekordu służy do wyszukiwania elementu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-431">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="fd18a-432">W związku z tym nie ma potrzeby powiązania właściwości, która przechowuje wystąpienie modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-432">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="fd18a-433">W scenariuszach, w których właściwości są powiązane z żądaniami GET, należy ustawić `SupportsGet` Właściwość na `true` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-433">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="fd18a-434">Źródła</span><span class="sxs-lookup"><span data-stu-id="fd18a-434">Sources</span></span>

<span data-ttu-id="fd18a-435">Domyślnie powiązanie modelu pobiera dane w postaci par klucz-wartość z następujących źródeł w żądaniu HTTP:</span><span class="sxs-lookup"><span data-stu-id="fd18a-435">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="fd18a-436">Pola formularza</span><span class="sxs-lookup"><span data-stu-id="fd18a-436">Form fields</span></span>
1. <span data-ttu-id="fd18a-437">Treść żądania (dla [kontrolerów, które mają atrybut [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="fd18a-437">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="fd18a-438">Dane trasy</span><span class="sxs-lookup"><span data-stu-id="fd18a-438">Route data</span></span>
1. <span data-ttu-id="fd18a-439">Parametry ciągu zapytania</span><span class="sxs-lookup"><span data-stu-id="fd18a-439">Query string parameters</span></span>
1. <span data-ttu-id="fd18a-440">Przekazane pliki</span><span class="sxs-lookup"><span data-stu-id="fd18a-440">Uploaded files</span></span>

<span data-ttu-id="fd18a-441">Dla każdego parametru lub właściwości docelowej źródła są skanowane w kolejności wskazanej na poprzedniej liście.</span><span class="sxs-lookup"><span data-stu-id="fd18a-441">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="fd18a-442">Istnieje kilka wyjątków:</span><span class="sxs-lookup"><span data-stu-id="fd18a-442">There are a few exceptions:</span></span>

* <span data-ttu-id="fd18a-443">Dane trasy i wartości ciągu zapytania są używane tylko dla typów prostych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-443">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="fd18a-444">Przekazane pliki są powiązane tylko z typami docelowymi implementującymi `IFormFile` lub `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-444">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="fd18a-445">Jeśli źródło domyślne jest niepoprawne, użyj jednego z następujących atrybutów, aby określić Źródło:</span><span class="sxs-lookup"><span data-stu-id="fd18a-445">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="fd18a-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Pobiera wartości z ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="fd18a-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Pobiera wartości z danych trasy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="fd18a-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Pobiera wartości ze opublikowanych pól formularza.</span><span class="sxs-lookup"><span data-stu-id="fd18a-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="fd18a-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Pobiera wartości z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="fd18a-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Pobiera wartości z nagłówków HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="fd18a-451">Następujące atrybuty:</span><span class="sxs-lookup"><span data-stu-id="fd18a-451">These attributes:</span></span>

* <span data-ttu-id="fd18a-452">Są dodawane do właściwości modelu pojedynczo (nie do klasy modelu), jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-452">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="fd18a-453">Opcjonalnie Zaakceptuj wartość nazwy modelu w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="fd18a-453">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="fd18a-454">Ta opcja jest dostępna w przypadku, gdy nazwa właściwości nie jest zgodna z wartością w żądaniu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-454">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="fd18a-455">Na przykład wartość w żądaniu może być nagłówkiem z łącznikiem w nazwie, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-455">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="fd18a-456">[FromBody] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-456">[FromBody] attribute</span></span>

<span data-ttu-id="fd18a-457">Zastosuj `[FromBody]` atrybut do parametru, aby wypełnić jego właściwości z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-457">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="fd18a-458">Środowisko uruchomieniowe ASP.NET Core deleguje odpowiedzialność za odczytanie treści do wejściowego programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-458">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="fd18a-459">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="fd18a-459">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="fd18a-460">Gdy `[FromBody]` jest stosowany do parametru typu złożonego, wszystkie atrybuty źródła powiązań zastosowane do jego właściwości są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="fd18a-460">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="fd18a-461">Na przykład Poniższa `Create` Akcja określa, że jego `pet` parametr jest wypełniany w treści:</span><span class="sxs-lookup"><span data-stu-id="fd18a-461">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="fd18a-462">`Pet`Klasa określa, że jej `Breed` Właściwość jest wypełniana przy użyciu parametru ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-462">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="fd18a-463">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-463">In the preceding example:</span></span>

* <span data-ttu-id="fd18a-464">`[FromQuery]`Atrybut jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="fd18a-464">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="fd18a-465">`Breed`Właściwość nie jest wypełniona z parametru ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-465">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="fd18a-466">W danych wejściowych programu formatującego są odczytywane tylko treści i nie są rozpoznawane atrybuty źródłowe powiązania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-466">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="fd18a-467">Jeśli w treści zostanie znaleziona odpowiednia wartość, ta wartość jest używana do wypełniania `Breed` właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-467">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="fd18a-468">Nie stosuj `[FromBody]` do więcej niż jednego parametru na metodę akcji.</span><span class="sxs-lookup"><span data-stu-id="fd18a-468">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="fd18a-469">Gdy strumień żądania jest odczytywany przez dane wejściowe programu formatującego, nie jest już dostępny do ponownego odczytywania dla powiązań innych `[FromBody]` parametrów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-469">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="fd18a-470">Dodatkowe źródła</span><span class="sxs-lookup"><span data-stu-id="fd18a-470">Additional sources</span></span>

<span data-ttu-id="fd18a-471">Dane źródłowe są dostarczane do systemu powiązań modelu przez *dostawców wartości*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-471">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="fd18a-472">Można napisać i zarejestrować dostawców wartości niestandardowych, którzy pobierają dane dla powiązania modelu z innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="fd18a-472">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="fd18a-473">Na przykład możesz potrzebować danych z :::no-loc(cookie)::: lub stanu sesji.</span><span class="sxs-lookup"><span data-stu-id="fd18a-473">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="fd18a-474">Aby pobrać dane z nowego źródła:</span><span class="sxs-lookup"><span data-stu-id="fd18a-474">To get data from a new source:</span></span>

* <span data-ttu-id="fd18a-475">Utwórz klasę implementującą `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-475">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="fd18a-476">Utwórz klasę implementującą `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-476">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="fd18a-477">Zarejestruj klasę fabryki w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-477">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="fd18a-478">Aplikacja Przykładowa zawiera [dostawcę wartości](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) i przykład [fabryki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) , który pobiera wartości z :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="fd18a-478">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="fd18a-479">Oto kod rejestracji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-479">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="fd18a-480">Pokazany kod umieszcza niestandardowego dostawcę wartości po wszystkich wbudowanych dostawcach wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-480">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="fd18a-481">Aby najpierw utworzyć go na liście, należy wywołać `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` zamiast `Add` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-481">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="fd18a-482">Brak źródła dla właściwości modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-482">No source for a model property</span></span>

<span data-ttu-id="fd18a-483">Domyślnie błąd stanu modelu nie jest tworzony, jeśli dla właściwości modelu nie znaleziono żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-483">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="fd18a-484">Właściwość jest ustawiona na null lub wartość domyślną:</span><span class="sxs-lookup"><span data-stu-id="fd18a-484">The property is set to null or a default value:</span></span>

* <span data-ttu-id="fd18a-485">Typy proste o wartości null są ustawione na wartość `null` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-485">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="fd18a-486">Typy wartości, które nie są dopuszczane do wartości null, są ustawione na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-486">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="fd18a-487">Na przykład parametr `int id` jest ustawiony na 0.</span><span class="sxs-lookup"><span data-stu-id="fd18a-487">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="fd18a-488">W przypadku typów złożonych powiązanie modelu tworzy wystąpienie przy użyciu domyślnego konstruktora bez ustawiania właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-488">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="fd18a-489">Tablice są ustawiane na `Array.Empty<T>()` , z tą różnicą, że `byte[]` tablice są ustawione na `null` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-489">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="fd18a-490">Jeśli stan modelu ma być unieważniony, gdy nic nie zostanie znalezione w polach formularza dla właściwości modelu, użyj [`[BindRequired]`](#bindrequired-attribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-490">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="fd18a-491">Należy zauważyć, że to `[BindRequired]` zachowanie ma zastosowanie do powiązania modelu z ogłoszonych danych formularza, nie do danych JSON ani XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-491">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="fd18a-492">Dane treści żądania są obsługiwane przez elementy [formatujące dane wejściowe](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="fd18a-492">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="fd18a-493">Błędy konwersji typów</span><span class="sxs-lookup"><span data-stu-id="fd18a-493">Type conversion errors</span></span>

<span data-ttu-id="fd18a-494">Jeśli źródło zostanie znalezione, ale nie można go przekonwertować na typ docelowy, stan modelu jest oflagowany jako nieprawidłowy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-494">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="fd18a-495">Parametr lub właściwość docelowa jest ustawiona na null lub wartość domyślną, jak wskazano w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="fd18a-495">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="fd18a-496">W kontrolerze interfejsu API, który ma `[ApiController]` atrybut, nieprawidłowy stan modelu powoduje automatyczne odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="fd18a-496">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="fd18a-497">Na :::no-loc(Razor)::: stronie ponownie Wyświetl stronę z komunikatem o błędzie:</span><span class="sxs-lookup"><span data-stu-id="fd18a-497">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="fd18a-498">Weryfikacja po stronie klienta umożliwia przechwycenie najbardziej nieprawidłowych danych, które w przeciwnym razie zostałyby przesłane do :::no-loc(Razor)::: formularza stron.</span><span class="sxs-lookup"><span data-stu-id="fd18a-498">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="fd18a-499">Ta weryfikacja sprawia, że trudno jest wyzwolić poprzedni wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="fd18a-499">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="fd18a-500">Przykładowa aplikacja zawiera przycisk **Prześlij z nieprawidłowym dniem** , który umieszcza złe dane w polu **Data zatrudnienia** i przesyła formularz.</span><span class="sxs-lookup"><span data-stu-id="fd18a-500">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="fd18a-501">Ten przycisk pokazuje, w jaki sposób kod na potrzeby wyświetlania strony działa po wystąpieniu błędów konwersji danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-501">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="fd18a-502">Gdy strona jest ponownie wyświetlana przez poprzedni kod, nieprawidłowe dane wejściowe nie są wyświetlane w polu formularza.</span><span class="sxs-lookup"><span data-stu-id="fd18a-502">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="fd18a-503">Wynika to z faktu, że właściwość model ma wartość null lub domyślną.</span><span class="sxs-lookup"><span data-stu-id="fd18a-503">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="fd18a-504">Nieprawidłowe dane wejściowe są wyświetlane w komunikacie o błędzie.</span><span class="sxs-lookup"><span data-stu-id="fd18a-504">The invalid input does appear in an error message.</span></span> <span data-ttu-id="fd18a-505">Jeśli jednak chcesz ponownie wyświetlić złe dane w polu formularza, rozważ, że właściwość model jest ciągiem i ręcznie wykonuje konwersję danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-505">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="fd18a-506">Ta sama strategia jest zalecana, jeśli nie chcesz, aby Błędy konwersji typów powodowały błędy stanu modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-506">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="fd18a-507">W takim przypadku należy zmienić wartość właściwości model na ciąg.</span><span class="sxs-lookup"><span data-stu-id="fd18a-507">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="fd18a-508">Typy proste</span><span class="sxs-lookup"><span data-stu-id="fd18a-508">Simple types</span></span>

<span data-ttu-id="fd18a-509">Typy proste, które tworzą spinacz modelu mogą konwertować ciągi źródłowe, w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="fd18a-509">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="fd18a-510">Typu</span><span class="sxs-lookup"><span data-stu-id="fd18a-510">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="fd18a-511">[Byte, bajty](xref:System.ComponentModel.ByteConverter) [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="fd18a-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="fd18a-512">Delikatn</span><span class="sxs-lookup"><span data-stu-id="fd18a-512">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="fd18a-513">Data/godzina</span><span class="sxs-lookup"><span data-stu-id="fd18a-513">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="fd18a-514">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="fd18a-514">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="fd18a-515">Dokładności</span><span class="sxs-lookup"><span data-stu-id="fd18a-515">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="fd18a-516">Double</span><span class="sxs-lookup"><span data-stu-id="fd18a-516">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="fd18a-517">Wyliczenie</span><span class="sxs-lookup"><span data-stu-id="fd18a-517">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="fd18a-518">Ident</span><span class="sxs-lookup"><span data-stu-id="fd18a-518">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="fd18a-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="fd18a-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="fd18a-520">Pojedynczy</span><span class="sxs-lookup"><span data-stu-id="fd18a-520">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="fd18a-521">Czasu</span><span class="sxs-lookup"><span data-stu-id="fd18a-521">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="fd18a-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="fd18a-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="fd18a-523">Adresu</span><span class="sxs-lookup"><span data-stu-id="fd18a-523">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="fd18a-524">Wersja</span><span class="sxs-lookup"><span data-stu-id="fd18a-524">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="fd18a-525">Typy złożone</span><span class="sxs-lookup"><span data-stu-id="fd18a-525">Complex types</span></span>

<span data-ttu-id="fd18a-526">Typ złożony musi mieć publiczny Konstruktor domyślny i publiczne właściwości do zapisu do powiązania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-526">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="fd18a-527">W przypadku wystąpienia powiązania modelu Klasa jest tworzona przy użyciu publicznego konstruktora domyślnego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-527">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="fd18a-528">Dla każdej właściwości typu złożonego powiązanie modelu wyszukuje źródła nazwy wzorca *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-528">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="fd18a-529">Jeśli nic nie zostanie znalezione, szuka tylko *property_name* bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-529">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="fd18a-530">W przypadku powiązania z parametrem prefiks jest nazwą parametru.</span><span class="sxs-lookup"><span data-stu-id="fd18a-530">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="fd18a-531">W przypadku powiązania z `PageModel` właściwością publiczną prefiks jest publiczną nazwą właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-531">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="fd18a-532">Niektóre atrybuty mają `Prefix` Właściwość, która pozwala zastąpić domyślne użycie parametru lub nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-532">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="fd18a-533">Na przykład, Załóżmy, że typ złożony jest następującą `Instructor` klasą:</span><span class="sxs-lookup"><span data-stu-id="fd18a-533">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="fd18a-534">Prefix = Nazwa parametru</span><span class="sxs-lookup"><span data-stu-id="fd18a-534">Prefix = parameter name</span></span>

<span data-ttu-id="fd18a-535">Jeśli modelem, który ma zostać powiązany, jest parametr o nazwie `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-535">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="fd18a-536">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-536">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="fd18a-537">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-537">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="fd18a-538">Prefix = nazwa właściwości</span><span class="sxs-lookup"><span data-stu-id="fd18a-538">Prefix = property name</span></span>

<span data-ttu-id="fd18a-539">Jeśli modelem do powiązania jest właściwość o nazwie `Instructor` kontrolera lub `PageModel` klasy:</span><span class="sxs-lookup"><span data-stu-id="fd18a-539">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="fd18a-540">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-540">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="fd18a-541">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-541">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="fd18a-542">Prefiks niestandardowy</span><span class="sxs-lookup"><span data-stu-id="fd18a-542">Custom prefix</span></span>

<span data-ttu-id="fd18a-543">Jeśli model do powiązania jest parametrem o nazwie `instructorToUpdate` i `Bind` atrybut określa `Instructor` jako prefiks:</span><span class="sxs-lookup"><span data-stu-id="fd18a-543">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="fd18a-544">Powiązanie modelu zaczyna się od przejrzenia źródeł klucza `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-544">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="fd18a-545">Jeśli ta wartość nie zostanie znaleziona, szuka `ID` bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-545">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="fd18a-546">Atrybuty dla obiektów docelowych typu złożonego</span><span class="sxs-lookup"><span data-stu-id="fd18a-546">Attributes for complex type targets</span></span>

<span data-ttu-id="fd18a-547">Dostępne są kilka wbudowanych atrybutów do kontrolowania powiązania modelu typów złożonych:</span><span class="sxs-lookup"><span data-stu-id="fd18a-547">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="fd18a-548">Te atrybuty wpływają na powiązanie modelu, gdy dane formularza ogłoszonego są źródłem wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-548">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="fd18a-549">Nie wpływają one na wejściowe elementy formatujące, które przetwarzają ogłoszone treści kodu JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="fd18a-549">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="fd18a-550">Dane wejściowe są wyjaśnione [w dalszej części tego artykułu](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="fd18a-550">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="fd18a-551">Zobacz również Omówienie `[Required]` atrybutu w [walidacji modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="fd18a-551">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="fd18a-552">[BindRequired] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-552">[BindRequired] attribute</span></span>

<span data-ttu-id="fd18a-553">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-553">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="fd18a-554">Powoduje, że powiązanie modelu umożliwia dodanie błędu stanu modelu, Jeśli powiązanie nie może wystąpić dla właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-554">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="fd18a-555">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-555">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="fd18a-556">[BindNever] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-556">[BindNever] attribute</span></span>

<span data-ttu-id="fd18a-557">Można stosować tylko do właściwości modelu, a nie do parametrów metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-557">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="fd18a-558">Uniemożliwia powiązanie modelu z ustawiania właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-558">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="fd18a-559">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-559">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="fd18a-560">[Bind] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-560">[Bind] attribute</span></span>

<span data-ttu-id="fd18a-561">Można zastosować do klasy lub parametru metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-561">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="fd18a-562">Określa, które właściwości modelu powinny być dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-562">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="fd18a-563">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy wywoływana jest jakakolwiek procedura obsługi lub metoda działania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-563">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="fd18a-564">W poniższym przykładzie tylko określone właściwości `Instructor` modelu są powiązane, gdy `OnPost` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="fd18a-564">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="fd18a-565">Ten `[Bind]` atrybut może służyć do ochrony przed nadużyciem w scenariuszach *tworzenia* scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="fd18a-565">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="fd18a-566">Nie działa prawidłowo w scenariuszach edycji, ponieważ wykluczone właściwości mają ustawioną wartość null lub wartość domyślną, a nie jako pozostawione bez zmian.</span><span class="sxs-lookup"><span data-stu-id="fd18a-566">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="fd18a-567">W celu zapewnienia obrony przed przekroczeniem, zaleca się, aby zamiast `[Bind]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-567">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="fd18a-568">Aby uzyskać więcej informacji, zobacz [temat Security uwagi dotyczący przefinalizowania](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="fd18a-568">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="fd18a-569">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="fd18a-569">Collections</span></span>

<span data-ttu-id="fd18a-570">Dla celów, które są kolekcjami typów prostych, powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-570">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="fd18a-571">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="fd18a-572">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-572">For example:</span></span>

* <span data-ttu-id="fd18a-573">Załóżmy, że parametr, który ma zostać powiązany, jest tablicą o nazwie `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-573">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="fd18a-574">Dane formularza lub ciągu zapytania mogą znajdować się w jednym z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="fd18a-574">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="fd18a-575">Następujący format jest obsługiwany tylko w danych formularza:</span><span class="sxs-lookup"><span data-stu-id="fd18a-575">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="fd18a-576">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje tablicę dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="fd18a-576">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="fd18a-577">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="fd18a-577">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="fd18a-578">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="fd18a-578">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="fd18a-579">Formaty danych używające liczb indeksów dolnych (... [0]... [1]...) należy upewnić się, że są numerowane sekwencyjnie, zaczynając od zera.</span><span class="sxs-lookup"><span data-stu-id="fd18a-579">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="fd18a-580">Jeśli występują luki w numerze indeksu dolnego, wszystkie elementy po przerwie zostaną zignorowane.</span><span class="sxs-lookup"><span data-stu-id="fd18a-580">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="fd18a-581">Na przykład, jeśli indeksy dolne są równe 0 i 2 zamiast 0 i 1, drugi element jest ignorowany.</span><span class="sxs-lookup"><span data-stu-id="fd18a-581">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="fd18a-582">Słowniki</span><span class="sxs-lookup"><span data-stu-id="fd18a-582">Dictionaries</span></span>

<span data-ttu-id="fd18a-583">Dla `Dictionary` elementów docelowych powiązanie modelu wyszukuje dopasowania do *parameter_name* lub *property_name*.</span><span class="sxs-lookup"><span data-stu-id="fd18a-583">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="fd18a-584">Jeśli dopasowanie nie zostanie znalezione, szuka jednego z obsługiwanych formatów bez prefiksu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-584">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="fd18a-585">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-585">For example:</span></span>

* <span data-ttu-id="fd18a-586">Załóżmy, że parametr docelowy jest `Dictionary<int, string>` nazwany `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-586">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="fd18a-587">Ogłoszone dane formularza lub ciągu zapytania mogą wyglądać jak w jednym z następujących przykładów:</span><span class="sxs-lookup"><span data-stu-id="fd18a-587">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="fd18a-588">We wszystkich powyższych formatach przykładowe powiązanie modelu przekazuje słownik dwóch elementów do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="fd18a-588">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="fd18a-589">selectedCourses ["1050"] = "Chemia"</span><span class="sxs-lookup"><span data-stu-id="fd18a-589">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="fd18a-590">selectedCourses ["2000"] = "ekonomia"</span><span class="sxs-lookup"><span data-stu-id="fd18a-590">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="fd18a-591">Zachowanie globalizacji danych tras powiązań modelu i ciągów zapytań</span><span class="sxs-lookup"><span data-stu-id="fd18a-591">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="fd18a-592">Dostawca wartości ASP.NET Core tras i dostawcy wartości ciągu zapytania:</span><span class="sxs-lookup"><span data-stu-id="fd18a-592">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="fd18a-593">Traktuj wartości jako niezmienną kulturę.</span><span class="sxs-lookup"><span data-stu-id="fd18a-593">Treat values as invariant culture.</span></span>
* <span data-ttu-id="fd18a-594">Należy oczekiwać, że adresy URL są kulturą niezmienną.</span><span class="sxs-lookup"><span data-stu-id="fd18a-594">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="fd18a-595">Natomiast wartości pochodzące z danych formularza są przekształcane z uwzględnieniem kultury.</span><span class="sxs-lookup"><span data-stu-id="fd18a-595">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="fd18a-596">Jest to zaprojektowane w taki sposób, aby adresy URL były udostępniane w ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-596">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="fd18a-597">Aby dostawca wartości ASP.NET Core trasy i dostawca wartości ciągu zapytania były poddawane konwersji zależnej od kultury:</span><span class="sxs-lookup"><span data-stu-id="fd18a-597">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="fd18a-598">Dziedzicz po <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="fd18a-598">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="fd18a-599">Skopiuj kod z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) lub [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="fd18a-599">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="fd18a-600">Zastąp [wartość kultury](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) przekazaną do konstruktora dostawcy wartości wartością [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="fd18a-600">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="fd18a-601">Zastąp domyślną fabrykę dostawcy wartości w opcjach MVC nowym:</span><span class="sxs-lookup"><span data-stu-id="fd18a-601">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="fd18a-602">Specjalne typy danych</span><span class="sxs-lookup"><span data-stu-id="fd18a-602">Special data types</span></span>

<span data-ttu-id="fd18a-603">Istnieją specjalne typy danych, które może obsłużyć powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="fd18a-603">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="fd18a-604">IFormFile i IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="fd18a-604">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="fd18a-605">Przekazany plik uwzględniony w żądaniu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd18a-605">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="fd18a-606">Obsługiwane jest również `IEnumerable<IFormFile>` dla wielu plików.</span><span class="sxs-lookup"><span data-stu-id="fd18a-606">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="fd18a-607">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="fd18a-607">CancellationToken</span></span>

<span data-ttu-id="fd18a-608">Służy do anulowania działania w kontrolerach asynchronicznych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-608">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="fd18a-609">Formularz</span><span class="sxs-lookup"><span data-stu-id="fd18a-609">FormCollection</span></span>

<span data-ttu-id="fd18a-610">Służy do pobierania wszystkich wartości z ogłoszonych danych formularza.</span><span class="sxs-lookup"><span data-stu-id="fd18a-610">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="fd18a-611">Wejściowe elementy formatujące</span><span class="sxs-lookup"><span data-stu-id="fd18a-611">Input formatters</span></span>

<span data-ttu-id="fd18a-612">Dane w treści żądania mogą być w formacie JSON, XML lub innym.</span><span class="sxs-lookup"><span data-stu-id="fd18a-612">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="fd18a-613">Aby przeanalizować te dane, powiązanie modelu korzysta z *wejściowego programu formatującego* , który jest skonfigurowany do obsługi określonego typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-613">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="fd18a-614">Domyślnie ASP.NET Core zawiera dane wejściowe w formacie JSON na potrzeby obsługi danych JSON.</span><span class="sxs-lookup"><span data-stu-id="fd18a-614">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="fd18a-615">Można dodać inne elementy formatujące dla innych typów zawartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-615">You can add other formatters for other content types.</span></span>

<span data-ttu-id="fd18a-616">ASP.NET Core wybiera wejściowe elementy formatujące na podstawie atrybutu [użycia](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="fd18a-616">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="fd18a-617">Jeśli atrybut nie jest obecny, używa [nagłówka Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="fd18a-617">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="fd18a-618">Aby użyć wbudowanych elementów formatujących dane wejściowe XML:</span><span class="sxs-lookup"><span data-stu-id="fd18a-618">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="fd18a-619">Zainstaluj `Microsoft.AspNetCore.Mvc.Formatters.Xml` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="fd18a-619">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="fd18a-620">W `Startup.ConfigureServices` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> lub <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="fd18a-620">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="fd18a-621">Zastosuj `Consumes` atrybut do klas kontrolera lub metod akcji, które powinny oczekiwać XML w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="fd18a-621">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="fd18a-622">Aby uzyskać więcej informacji, zobacz [wprowadzenie serializacji XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="fd18a-622">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="fd18a-623">Wyklucz określone typy z powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-623">Exclude specified types from model binding</span></span>

<span data-ttu-id="fd18a-624">Zachowanie modelu powiązań i systemów walidacji jest zależne od [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="fd18a-624">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="fd18a-625">Można dostosować `ModelMetadata` , dodając dostawcę szczegółów do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="fd18a-625">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="fd18a-626">Wbudowane dostawcy szczegółów są dostępne do wyłączenia powiązania modelu lub walidacji dla określonych typów.</span><span class="sxs-lookup"><span data-stu-id="fd18a-626">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="fd18a-627">Aby wyłączyć powiązanie modelu dla wszystkich modeli określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-627">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fd18a-628">Na przykład aby wyłączyć powiązanie modelu dla wszystkich modeli typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-628">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="fd18a-629">Aby wyłączyć walidację właściwości określonego typu, Dodaj element <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> w `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-629">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fd18a-630">Na przykład aby wyłączyć walidację właściwości typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="fd18a-630">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="fd18a-631">Niestandardowe powiązania modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-631">Custom model binders</span></span>

<span data-ttu-id="fd18a-632">Można rozszerzać powiązania modelu, pisząc niestandardowy spinacz modelu i używając `[ModelBinder]` atrybutu, aby wybrać go dla danego elementu docelowego.</span><span class="sxs-lookup"><span data-stu-id="fd18a-632">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="fd18a-633">Dowiedz się więcej na temat [niestandardowego powiązania modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="fd18a-633">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="fd18a-634">Ręczne powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="fd18a-634">Manual model binding</span></span>

<span data-ttu-id="fd18a-635">Powiązanie modelu można wywołać ręcznie przy użyciu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="fd18a-635">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="fd18a-636">Metoda jest zdefiniowana dla obu `ControllerBase` klas i `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="fd18a-636">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="fd18a-637">Przeciążenia metod umożliwiają określenie prefiksu i dostawcy wartości do użycia.</span><span class="sxs-lookup"><span data-stu-id="fd18a-637">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="fd18a-638">Metoda zwraca `false` Jeśli powiązanie modelu nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="fd18a-638">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="fd18a-639">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="fd18a-639">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="fd18a-640">[FromServices] — atrybut</span><span class="sxs-lookup"><span data-stu-id="fd18a-640">[FromServices] attribute</span></span>

<span data-ttu-id="fd18a-641">Nazwa tego atrybutu jest zgodna ze wzorcem atrybutów powiązania modelu, które określają źródło danych.</span><span class="sxs-lookup"><span data-stu-id="fd18a-641">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="fd18a-642">Ale nie informacje o powiązaniu danych od dostawcy wartości.</span><span class="sxs-lookup"><span data-stu-id="fd18a-642">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="fd18a-643">Pobiera wystąpienie typu z kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="fd18a-643">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fd18a-644">Jego celem jest zapewnienie alternatywy dla iniekcji konstruktorów, gdy potrzebna jest usługa tylko wtedy, gdy jest wywoływana konkretna metoda.</span><span class="sxs-lookup"><span data-stu-id="fd18a-644">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd18a-645">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fd18a-645">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
