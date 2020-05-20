---
<span data-ttu-id="c8332-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-102">'Blazor'</span></span>
- <span data-ttu-id="c8332-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-103">'Identity'</span></span>
- <span data-ttu-id="c8332-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-105">'Razor'</span></span>
- <span data-ttu-id="c8332-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="c8332-107">JsonPatch w interfejsie Web API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8332-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="c8332-108">Autorzy [Dykstra](https://github.com/tdykstra) i [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c8332-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c8332-109">W tym artykule wyjaśniono, jak obsłużyć żądania poprawek w formacie JSON w ASP.NET Core interfejsie API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c8332-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="c8332-110">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="c8332-110">Package installation</span></span>

<span data-ttu-id="c8332-111">Aby włączyć obsługę poprawek JSON w aplikacji, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="c8332-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="c8332-112">Zainstaluj [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8332-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="c8332-113">Zaktualizuj `Startup.ConfigureServices` metodę projektu w celu wywołania <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="c8332-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="c8332-114">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="c8332-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="c8332-115">`AddNewtonsoftJson`jest zgodny z metodami rejestracji usługi MVC:</span><span class="sxs-lookup"><span data-stu-id="c8332-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="c8332-116">Poprawka JSON, AddNewtonsoftJson i system. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="c8332-117">`AddNewtonsoftJson`zastępuje utworzone w oparciu o dane `System.Text.Json` wejściowe i wyjściowe elementy formatujące używane do formatowania **całej** zawartości JSON.</span><span class="sxs-lookup"><span data-stu-id="c8332-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="c8332-118">Aby dodać obsługę poprawki JSON przy użyciu `Newtonsoft.Json` , pozostawiając inne elementy formatujące bez zmian, zaktualizuj `Startup.ConfigureServices` metodę projektu w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="c8332-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="c8332-119">Poprzedzający kod wymaga `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pakietu i następujących `using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="c8332-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="c8332-120">Poprawka metody żądania HTTP</span><span class="sxs-lookup"><span data-stu-id="c8332-120">PATCH HTTP request method</span></span>

<span data-ttu-id="c8332-121">Metody PUT i [patch](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu.</span><span class="sxs-lookup"><span data-stu-id="c8332-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="c8332-122">Różnica między nimi polega na tym, że zastępuje cały zasób, podczas gdy poprawka określa tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="c8332-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="c8332-123">Poprawka JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-123">JSON Patch</span></span>

<span data-ttu-id="c8332-124">[Poprawka JSON](https://tools.ietf.org/html/rfc6902) to format służący do określania aktualizacji, które mają zostać zastosowane do zasobu.</span><span class="sxs-lookup"><span data-stu-id="c8332-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="c8332-125">Dokument poprawki JSON zawiera tablicę *operacji*.</span><span class="sxs-lookup"><span data-stu-id="c8332-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="c8332-126">Każda operacja identyfikuje określony typ zmiany.</span><span class="sxs-lookup"><span data-stu-id="c8332-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="c8332-127">Przykłady takich zmian obejmują dodanie elementu tablicy lub zastępowanie wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="c8332-128">Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu oraz wynik zastosowania operacji patch.</span><span class="sxs-lookup"><span data-stu-id="c8332-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="c8332-129">Przykład zasobu</span><span class="sxs-lookup"><span data-stu-id="c8332-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="c8332-130">Przykład poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="c8332-131">W powyższym formacie JSON:</span><span class="sxs-lookup"><span data-stu-id="c8332-131">In the preceding JSON:</span></span>

* <span data-ttu-id="c8332-132">`op`Właściwość wskazuje typ operacji.</span><span class="sxs-lookup"><span data-stu-id="c8332-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="c8332-133">`path`Właściwość wskazuje element do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="c8332-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="c8332-134">`value`Właściwość zawiera nową wartość.</span><span class="sxs-lookup"><span data-stu-id="c8332-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="c8332-135">Zasób po zastosowaniu poprawki</span><span class="sxs-lookup"><span data-stu-id="c8332-135">Resource after patch</span></span>

<span data-ttu-id="c8332-136">Poniżej znajduje się zasób po zastosowaniu poprzedniego dokumentu poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="c8332-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="c8332-137">Zmiany wprowadzone przez zastosowanie dokumentu poprawki JSON do zasobu są niepodzielne.</span><span class="sxs-lookup"><span data-stu-id="c8332-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="c8332-138">Jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.</span><span class="sxs-lookup"><span data-stu-id="c8332-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="c8332-139">Składnia ścieżki</span><span class="sxs-lookup"><span data-stu-id="c8332-139">Path syntax</span></span>

<span data-ttu-id="c8332-140">Właściwość [Path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami.</span><span class="sxs-lookup"><span data-stu-id="c8332-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="c8332-141">Na przykład `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="c8332-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="c8332-142">W celu określenia elementów tablicy są używane indeksy oparte na wartości zero.</span><span class="sxs-lookup"><span data-stu-id="c8332-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="c8332-143">Pierwszy element `addresses` tablicy będzie miał wartość `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="c8332-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="c8332-144">Na `add` koniec tablicy Użyj łącznika ( `-` ), a nie numeru indeksu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="c8332-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="c8332-145">Operacje</span><span class="sxs-lookup"><span data-stu-id="c8332-145">Operations</span></span>

<span data-ttu-id="c8332-146">W poniższej tabeli przedstawiono obsługiwane operacje zgodnie z definicją w [specyfikacji poprawek JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="c8332-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="c8332-147">Operacja</span><span class="sxs-lookup"><span data-stu-id="c8332-147">Operation</span></span>  | <span data-ttu-id="c8332-148">Uwagi</span><span class="sxs-lookup"><span data-stu-id="c8332-148">Notes</span></span> |
|---
<span data-ttu-id="c8332-149">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-150">'Blazor'</span></span>
- <span data-ttu-id="c8332-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-151">'Identity'</span></span>
- <span data-ttu-id="c8332-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-153">'Razor'</span></span>
- <span data-ttu-id="c8332-154">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-155">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-156">'Blazor'</span></span>
- <span data-ttu-id="c8332-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-157">'Identity'</span></span>
- <span data-ttu-id="c8332-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-159">'Razor'</span></span>
- <span data-ttu-id="c8332-160">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-161">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-162">'Blazor'</span></span>
- <span data-ttu-id="c8332-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-163">'Identity'</span></span>
- <span data-ttu-id="c8332-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-165">'Razor'</span></span>
- <span data-ttu-id="c8332-166">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-166">'SignalR' uid:</span></span> 

<span data-ttu-id="c8332-167">------|---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-168">'Blazor'</span></span>
- <span data-ttu-id="c8332-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-169">'Identity'</span></span>
- <span data-ttu-id="c8332-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-171">'Razor'</span></span>
- <span data-ttu-id="c8332-172">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-173">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-174">'Blazor'</span></span>
- <span data-ttu-id="c8332-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-175">'Identity'</span></span>
- <span data-ttu-id="c8332-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-177">'Razor'</span></span>
- <span data-ttu-id="c8332-178">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-179">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-180">'Blazor'</span></span>
- <span data-ttu-id="c8332-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-181">'Identity'</span></span>
- <span data-ttu-id="c8332-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-183">'Razor'</span></span>
- <span data-ttu-id="c8332-184">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-185">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-186">'Blazor'</span></span>
- <span data-ttu-id="c8332-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-187">'Identity'</span></span>
- <span data-ttu-id="c8332-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-189">'Razor'</span></span>
- <span data-ttu-id="c8332-190">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-191">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-192">'Blazor'</span></span>
- <span data-ttu-id="c8332-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-193">'Identity'</span></span>
- <span data-ttu-id="c8332-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-195">'Razor'</span></span>
- <span data-ttu-id="c8332-196">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-197">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-198">'Blazor'</span></span>
- <span data-ttu-id="c8332-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-199">'Identity'</span></span>
- <span data-ttu-id="c8332-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-201">'Razor'</span></span>
- <span data-ttu-id="c8332-202">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-203">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-204">'Blazor'</span></span>
- <span data-ttu-id="c8332-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-205">'Identity'</span></span>
- <span data-ttu-id="c8332-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-207">'Razor'</span></span>
- <span data-ttu-id="c8332-208">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-209">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-210">'Blazor'</span></span>
- <span data-ttu-id="c8332-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-211">'Identity'</span></span>
- <span data-ttu-id="c8332-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-213">'Razor'</span></span>
- <span data-ttu-id="c8332-214">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-215">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-216">'Blazor'</span></span>
- <span data-ttu-id="c8332-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-217">'Identity'</span></span>
- <span data-ttu-id="c8332-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-219">'Razor'</span></span>
- <span data-ttu-id="c8332-220">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-221">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-222">'Blazor'</span></span>
- <span data-ttu-id="c8332-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-223">'Identity'</span></span>
- <span data-ttu-id="c8332-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-225">'Razor'</span></span>
- <span data-ttu-id="c8332-226">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-227">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-228">'Blazor'</span></span>
- <span data-ttu-id="c8332-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-229">'Identity'</span></span>
- <span data-ttu-id="c8332-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-231">'Razor'</span></span>
- <span data-ttu-id="c8332-232">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-233">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-234">'Blazor'</span></span>
- <span data-ttu-id="c8332-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-235">'Identity'</span></span>
- <span data-ttu-id="c8332-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-237">'Razor'</span></span>
- <span data-ttu-id="c8332-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-239">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-240">'Blazor'</span></span>
- <span data-ttu-id="c8332-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-241">'Identity'</span></span>
- <span data-ttu-id="c8332-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-243">'Razor'</span></span>
- <span data-ttu-id="c8332-244">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-245">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-246">'Blazor'</span></span>
- <span data-ttu-id="c8332-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-247">'Identity'</span></span>
- <span data-ttu-id="c8332-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-249">'Razor'</span></span>
- <span data-ttu-id="c8332-250">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-250">'SignalR' uid:</span></span> 

<span data-ttu-id="c8332-251">----------------| | `add`     | Dodaj właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="c8332-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="c8332-252">Dla istniejącej właściwości: Ustaw wartość. | | `remove`  | Usuń właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="c8332-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="c8332-253">| | `replace` | Takie same, jak `remove` następuje `add` w tej samej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="c8332-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="c8332-254">| | `move`    | Takie samo jak `remove` ze źródła, a następnie `add` do miejsca docelowego przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="c8332-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="c8332-255">| | `copy`    | Takie samo jak `add` miejsce docelowe przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="c8332-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="c8332-256">| | `test`    | Zwróć kod stanu sukcesu, jeśli wartość jest równa `path` = podano `value` . |</span><span class="sxs-lookup"><span data-stu-id="c8332-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="c8332-257">Poprawka JSON w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8332-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="c8332-258">ASP.NET Core implementacja poprawki JSON jest dostępna w pakiecie NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="c8332-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="c8332-259">Kod metody akcji</span><span class="sxs-lookup"><span data-stu-id="c8332-259">Action method code</span></span>

<span data-ttu-id="c8332-260">W kontrolerze interfejsu API Metoda akcji dla poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="c8332-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="c8332-261">Ma adnotację z `HttpPatch` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="c8332-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="c8332-262">Akceptuje element `JsonPatchDocument<T>` , zazwyczaj z `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="c8332-263">Wywołuje `ApplyTo` dokument poprawki, aby zastosować zmiany.</span><span class="sxs-lookup"><span data-stu-id="c8332-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="c8332-264">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="c8332-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="c8332-265">Ten kod z przykładowej aplikacji współdziała z następującym `Customer` modelem:</span><span class="sxs-lookup"><span data-stu-id="c8332-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="c8332-266">Przykładowa Metoda akcji:</span><span class="sxs-lookup"><span data-stu-id="c8332-266">The sample action method:</span></span>

* <span data-ttu-id="c8332-267">Konstruuje a `Customer` .</span><span class="sxs-lookup"><span data-stu-id="c8332-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="c8332-268">Stosuje poprawkę.</span><span class="sxs-lookup"><span data-stu-id="c8332-268">Applies the patch.</span></span>
* <span data-ttu-id="c8332-269">Zwraca wynik w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c8332-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="c8332-270">W rzeczywistej aplikacji kod pobiera dane z magazynu, takiego jak baza danych, i aktualizuje bazę danych po zastosowaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="c8332-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="c8332-271">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="c8332-271">Model state</span></span>

<span data-ttu-id="c8332-272">Poprzednia metoda akcji przykład wywołuje Przeciążenie `ApplyTo` , które ma stan modelu jako jeden z jego parametrów.</span><span class="sxs-lookup"><span data-stu-id="c8332-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="c8332-273">W przypadku tej opcji można odbierać komunikaty o błędach w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="c8332-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="c8332-274">Poniższy przykład przedstawia treść nieprawidłowej odpowiedzi na żądanie 400 dla `test` operacji:</span><span class="sxs-lookup"><span data-stu-id="c8332-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="c8332-275">Obiekty dynamiczne</span><span class="sxs-lookup"><span data-stu-id="c8332-275">Dynamic objects</span></span>

<span data-ttu-id="c8332-276">W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego:</span><span class="sxs-lookup"><span data-stu-id="c8332-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="c8332-277">Operacja dodawania</span><span class="sxs-lookup"><span data-stu-id="c8332-277">The add operation</span></span>

* <span data-ttu-id="c8332-278">Jeśli `path` wskazuje element tablicy: wstawia nowy element przed określony przez `path` .</span><span class="sxs-lookup"><span data-stu-id="c8332-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="c8332-279">Jeśli `path` wskazuje Właściwość: ustawia wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="c8332-280">Jeśli `path` wskazuje nieistniejącą lokalizację:</span><span class="sxs-lookup"><span data-stu-id="c8332-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="c8332-281">Jeśli zasób do poprawki jest obiektem dynamicznym: dodaje właściwość.</span><span class="sxs-lookup"><span data-stu-id="c8332-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="c8332-282">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="c8332-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="c8332-283">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i dodaje `Order` obiekt na końcu `Orders` tablicy.</span><span class="sxs-lookup"><span data-stu-id="c8332-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="c8332-284">Operacja usuwania</span><span class="sxs-lookup"><span data-stu-id="c8332-284">The remove operation</span></span>

* <span data-ttu-id="c8332-285">Jeśli `path` wskazuje element tablicy: usuwa element.</span><span class="sxs-lookup"><span data-stu-id="c8332-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="c8332-286">Jeśli `path` wskazuje Właściwość:</span><span class="sxs-lookup"><span data-stu-id="c8332-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="c8332-287">Jeśli zasób do poprawki jest obiektem dynamicznym: usuwa właściwość.</span><span class="sxs-lookup"><span data-stu-id="c8332-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="c8332-288">Jeśli zasób do poprawki jest obiektem statycznym:</span><span class="sxs-lookup"><span data-stu-id="c8332-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="c8332-289">Jeśli właściwość dopuszcza wartość null: ustawia ją na wartość null.</span><span class="sxs-lookup"><span data-stu-id="c8332-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="c8332-290">Jeśli właściwość nie dopuszcza wartości null, ustawia ją na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="c8332-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="c8332-291">Następujący Przykładowa poprawka zestawów dokumentów `CustomerName` do wartości null i usuwa `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="c8332-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="c8332-292">Operacja zamiany</span><span class="sxs-lookup"><span data-stu-id="c8332-292">The replace operation</span></span>

<span data-ttu-id="c8332-293">Ta operacja jest funkcjonalnie taka sama jak `remove` po nim `add` .</span><span class="sxs-lookup"><span data-stu-id="c8332-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="c8332-294">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i zastępuje ją `Orders[0]` nowym `Order` obiektem:</span><span class="sxs-lookup"><span data-stu-id="c8332-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="c8332-295">Operacja przenoszenia</span><span class="sxs-lookup"><span data-stu-id="c8332-295">The move operation</span></span>

* <span data-ttu-id="c8332-296">Jeśli `path` wskazuje element tablicy: kopiuje `from` element do lokalizacji `path` elementu, a następnie uruchamia `remove` operację na `from` elemencie.</span><span class="sxs-lookup"><span data-stu-id="c8332-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="c8332-297">Jeśli `path` wskazuje Właściwość: kopiuje wartość `from` właściwości do `path` właściwości, a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="c8332-298">Jeśli `path` wskazuje na nieistniejącą Właściwość:</span><span class="sxs-lookup"><span data-stu-id="c8332-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="c8332-299">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="c8332-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="c8332-300">Jeśli zasób do poprawki jest obiektem dynamicznym: kopiuje `from` Właściwość do lokalizacji wskazywanej przez `path` , a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="c8332-301">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="c8332-301">The following sample patch document:</span></span>

* <span data-ttu-id="c8332-302">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="c8332-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="c8332-303">Ustawia `Orders[0].OrderName` wartość null.</span><span class="sxs-lookup"><span data-stu-id="c8332-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="c8332-304">Przenosi `Orders[1]` do przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="c8332-305">Operacja kopiowania</span><span class="sxs-lookup"><span data-stu-id="c8332-305">The copy operation</span></span>

<span data-ttu-id="c8332-306">Ta operacja jest funkcjonalnie taka sama jak `move` operacja bez `remove` kroku końcowego.</span><span class="sxs-lookup"><span data-stu-id="c8332-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="c8332-307">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="c8332-307">The following sample patch document:</span></span>

* <span data-ttu-id="c8332-308">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="c8332-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="c8332-309">Wstawia kopię `Orders[1]` przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="c8332-310">Operacja testowa</span><span class="sxs-lookup"><span data-stu-id="c8332-310">The test operation</span></span>

<span data-ttu-id="c8332-311">Jeśli wartość w lokalizacji wskazywanej przez różni `path` się od wartości podanej w `value` , żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="c8332-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="c8332-312">W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki zakończyły się powodzeniem.</span><span class="sxs-lookup"><span data-stu-id="c8332-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="c8332-313">`test`Operacja jest często używana do zapobiegania aktualizacji, gdy występuje konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="c8332-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="c8332-314">Następujący przykładowy dokument poprawek nie ma wpływu, jeśli początkowa wartość `CustomerName` to "Jan", ponieważ test zakończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="c8332-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="c8332-315">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="c8332-315">Get the code</span></span>

<span data-ttu-id="c8332-316">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="c8332-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="c8332-317">([Jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c8332-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c8332-318">Aby przetestować przykład, uruchom aplikację i Wyślij żądania HTTP z następującymi ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="c8332-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="c8332-319">Adres URL:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="c8332-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="c8332-320">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="c8332-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="c8332-321">Nagłówki`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="c8332-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="c8332-322">Treść: Skopiuj i wklej jeden z przykładów dokumentu poprawki JSON z folderu projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="c8332-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8332-323">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c8332-323">Additional resources</span></span>

* [<span data-ttu-id="c8332-324">IETF RFC 5789 Specyfikacja metody poprawek</span><span class="sxs-lookup"><span data-stu-id="c8332-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="c8332-325">IETF RFC 6902 — Specyfikacja poprawek JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="c8332-326">IETF RFC 6901 JSON Format ścieżki poprawek</span><span class="sxs-lookup"><span data-stu-id="c8332-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="c8332-327">[Dokumentacja poprawek JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="c8332-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="c8332-328">Zawiera linki do zasobów do tworzenia dokumentów poprawek JSON.</span><span class="sxs-lookup"><span data-stu-id="c8332-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="c8332-329">ASP.NET Core kod źródłowy poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c8332-330">W tym artykule wyjaśniono, jak obsłużyć żądania poprawek w formacie JSON w ASP.NET Core interfejsie API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c8332-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="c8332-331">Poprawka metody żądania HTTP</span><span class="sxs-lookup"><span data-stu-id="c8332-331">PATCH HTTP request method</span></span>

<span data-ttu-id="c8332-332">Metody PUT i [patch](https://tools.ietf.org/html/rfc5789) są używane do aktualizowania istniejącego zasobu.</span><span class="sxs-lookup"><span data-stu-id="c8332-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="c8332-333">Różnica między nimi polega na tym, że zastępuje cały zasób, podczas gdy poprawka określa tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="c8332-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="c8332-334">Poprawka JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-334">JSON Patch</span></span>

<span data-ttu-id="c8332-335">[Poprawka JSON](https://tools.ietf.org/html/rfc6902) to format służący do określania aktualizacji, które mają zostać zastosowane do zasobu.</span><span class="sxs-lookup"><span data-stu-id="c8332-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="c8332-336">Dokument poprawki JSON zawiera tablicę *operacji*.</span><span class="sxs-lookup"><span data-stu-id="c8332-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="c8332-337">Każda operacja identyfikuje określony typ zmiany, na przykład Dodaj element tablicy lub Zastąp wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="c8332-338">Na przykład następujące dokumenty JSON reprezentują zasób, dokument poprawki JSON dla zasobu oraz wynik zastosowania operacji patch.</span><span class="sxs-lookup"><span data-stu-id="c8332-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="c8332-339">Przykład zasobu</span><span class="sxs-lookup"><span data-stu-id="c8332-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="c8332-340">Przykład poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="c8332-341">W powyższym formacie JSON:</span><span class="sxs-lookup"><span data-stu-id="c8332-341">In the preceding JSON:</span></span>

* <span data-ttu-id="c8332-342">`op`Właściwość wskazuje typ operacji.</span><span class="sxs-lookup"><span data-stu-id="c8332-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="c8332-343">`path`Właściwość wskazuje element do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="c8332-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="c8332-344">`value`Właściwość zawiera nową wartość.</span><span class="sxs-lookup"><span data-stu-id="c8332-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="c8332-345">Zasób po zastosowaniu poprawki</span><span class="sxs-lookup"><span data-stu-id="c8332-345">Resource after patch</span></span>

<span data-ttu-id="c8332-346">Poniżej znajduje się zasób po zastosowaniu poprzedniego dokumentu poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="c8332-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="c8332-347">Zmiany wprowadzone przez zastosowanie dokumentu poprawek JSON do zasobu są niepodzielne: Jeśli jakakolwiek operacja na liście nie powiedzie się, nie zostanie zastosowana żadna operacja na liście.</span><span class="sxs-lookup"><span data-stu-id="c8332-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="c8332-348">Składnia ścieżki</span><span class="sxs-lookup"><span data-stu-id="c8332-348">Path syntax</span></span>

<span data-ttu-id="c8332-349">Właściwość [Path](https://tools.ietf.org/html/rfc6901) obiektu operacji ma ukośniki między poziomami.</span><span class="sxs-lookup"><span data-stu-id="c8332-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="c8332-350">Na przykład `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="c8332-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="c8332-351">W celu określenia elementów tablicy są używane indeksy oparte na wartości zero.</span><span class="sxs-lookup"><span data-stu-id="c8332-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="c8332-352">Pierwszy element `addresses` tablicy będzie miał wartość `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="c8332-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="c8332-353">Na `add` koniec tablicy Użyj łącznika (-), a nie numeru indeksu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="c8332-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="c8332-354">Operacje</span><span class="sxs-lookup"><span data-stu-id="c8332-354">Operations</span></span>

<span data-ttu-id="c8332-355">W poniższej tabeli przedstawiono obsługiwane operacje zgodnie z definicją w [specyfikacji poprawek JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="c8332-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="c8332-356">Operacja</span><span class="sxs-lookup"><span data-stu-id="c8332-356">Operation</span></span>  | <span data-ttu-id="c8332-357">Uwagi</span><span class="sxs-lookup"><span data-stu-id="c8332-357">Notes</span></span> |
|---
<span data-ttu-id="c8332-358">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-359">'Blazor'</span></span>
- <span data-ttu-id="c8332-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-360">'Identity'</span></span>
- <span data-ttu-id="c8332-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-362">'Razor'</span></span>
- <span data-ttu-id="c8332-363">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-364">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-365">'Blazor'</span></span>
- <span data-ttu-id="c8332-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-366">'Identity'</span></span>
- <span data-ttu-id="c8332-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-368">'Razor'</span></span>
- <span data-ttu-id="c8332-369">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-370">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-371">'Blazor'</span></span>
- <span data-ttu-id="c8332-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-372">'Identity'</span></span>
- <span data-ttu-id="c8332-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-374">'Razor'</span></span>
- <span data-ttu-id="c8332-375">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-375">'SignalR' uid:</span></span> 

<span data-ttu-id="c8332-376">------|---
title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-377">'Blazor'</span></span>
- <span data-ttu-id="c8332-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-378">'Identity'</span></span>
- <span data-ttu-id="c8332-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-380">'Razor'</span></span>
- <span data-ttu-id="c8332-381">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-382">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-383">'Blazor'</span></span>
- <span data-ttu-id="c8332-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-384">'Identity'</span></span>
- <span data-ttu-id="c8332-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-386">'Razor'</span></span>
- <span data-ttu-id="c8332-387">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-388">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-389">'Blazor'</span></span>
- <span data-ttu-id="c8332-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-390">'Identity'</span></span>
- <span data-ttu-id="c8332-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-392">'Razor'</span></span>
- <span data-ttu-id="c8332-393">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-394">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-395">'Blazor'</span></span>
- <span data-ttu-id="c8332-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-396">'Identity'</span></span>
- <span data-ttu-id="c8332-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-398">'Razor'</span></span>
- <span data-ttu-id="c8332-399">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-400">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-401">'Blazor'</span></span>
- <span data-ttu-id="c8332-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-402">'Identity'</span></span>
- <span data-ttu-id="c8332-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-404">'Razor'</span></span>
- <span data-ttu-id="c8332-405">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-406">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-407">'Blazor'</span></span>
- <span data-ttu-id="c8332-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-408">'Identity'</span></span>
- <span data-ttu-id="c8332-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-410">'Razor'</span></span>
- <span data-ttu-id="c8332-411">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-412">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-413">'Blazor'</span></span>
- <span data-ttu-id="c8332-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-414">'Identity'</span></span>
- <span data-ttu-id="c8332-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-416">'Razor'</span></span>
- <span data-ttu-id="c8332-417">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-418">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-419">'Blazor'</span></span>
- <span data-ttu-id="c8332-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-420">'Identity'</span></span>
- <span data-ttu-id="c8332-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-422">'Razor'</span></span>
- <span data-ttu-id="c8332-423">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-424">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-425">'Blazor'</span></span>
- <span data-ttu-id="c8332-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-426">'Identity'</span></span>
- <span data-ttu-id="c8332-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-428">'Razor'</span></span>
- <span data-ttu-id="c8332-429">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-430">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-431">'Blazor'</span></span>
- <span data-ttu-id="c8332-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-432">'Identity'</span></span>
- <span data-ttu-id="c8332-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-434">'Razor'</span></span>
- <span data-ttu-id="c8332-435">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-436">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-437">'Blazor'</span></span>
- <span data-ttu-id="c8332-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-438">'Identity'</span></span>
- <span data-ttu-id="c8332-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-440">'Razor'</span></span>
- <span data-ttu-id="c8332-441">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-442">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-443">'Blazor'</span></span>
- <span data-ttu-id="c8332-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-444">'Identity'</span></span>
- <span data-ttu-id="c8332-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-446">'Razor'</span></span>
- <span data-ttu-id="c8332-447">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-448">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-449">'Blazor'</span></span>
- <span data-ttu-id="c8332-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-450">'Identity'</span></span>
- <span data-ttu-id="c8332-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-452">'Razor'</span></span>
- <span data-ttu-id="c8332-453">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8332-454">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c8332-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8332-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8332-455">'Blazor'</span></span>
- <span data-ttu-id="c8332-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8332-456">'Identity'</span></span>
- <span data-ttu-id="c8332-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8332-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8332-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8332-458">'Razor'</span></span>
- <span data-ttu-id="c8332-459">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="c8332-459">'SignalR' uid:</span></span> 

<span data-ttu-id="c8332-460">----------------| | `add`     | Dodaj właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="c8332-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="c8332-461">Dla istniejącej właściwości: Ustaw wartość. | | `remove`  | Usuń właściwość lub element tablicy.</span><span class="sxs-lookup"><span data-stu-id="c8332-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="c8332-462">| | `replace` | Takie same, jak `remove` następuje `add` w tej samej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="c8332-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="c8332-463">| | `move`    | Takie samo jak `remove` ze źródła, a następnie `add` do miejsca docelowego przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="c8332-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="c8332-464">| | `copy`    | Takie samo jak `add` miejsce docelowe przy użyciu wartości ze źródła.</span><span class="sxs-lookup"><span data-stu-id="c8332-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="c8332-465">| | `test`    | Zwróć kod stanu sukcesu, jeśli wartość jest równa `path` = podano `value` . |</span><span class="sxs-lookup"><span data-stu-id="c8332-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="c8332-466">JsonPatch w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8332-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="c8332-467">ASP.NET Core implementacja poprawki JSON jest dostępna w pakiecie NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="c8332-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="c8332-468">Pakiet jest zawarty w pakiecie [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="c8332-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="c8332-469">Kod metody akcji</span><span class="sxs-lookup"><span data-stu-id="c8332-469">Action method code</span></span>

<span data-ttu-id="c8332-470">W kontrolerze interfejsu API Metoda akcji dla poprawki JSON:</span><span class="sxs-lookup"><span data-stu-id="c8332-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="c8332-471">Ma adnotację z `HttpPatch` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="c8332-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="c8332-472">Akceptuje element `JsonPatchDocument<T>` , zazwyczaj z `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="c8332-473">Wywołuje `ApplyTo` dokument poprawki, aby zastosować zmiany.</span><span class="sxs-lookup"><span data-stu-id="c8332-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="c8332-474">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="c8332-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="c8332-475">Ten kod z przykładowej aplikacji współdziała z następującym `Customer` modelem.</span><span class="sxs-lookup"><span data-stu-id="c8332-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="c8332-476">Przykładowa Metoda akcji:</span><span class="sxs-lookup"><span data-stu-id="c8332-476">The sample action method:</span></span>

* <span data-ttu-id="c8332-477">Konstruuje a `Customer` .</span><span class="sxs-lookup"><span data-stu-id="c8332-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="c8332-478">Stosuje poprawkę.</span><span class="sxs-lookup"><span data-stu-id="c8332-478">Applies the patch.</span></span>
* <span data-ttu-id="c8332-479">Zwraca wynik w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="c8332-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="c8332-480">W rzeczywistej aplikacji kod pobiera dane z magazynu, takiego jak baza danych, i aktualizuje bazę danych po zastosowaniu poprawki.</span><span class="sxs-lookup"><span data-stu-id="c8332-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="c8332-481">Stan modelu</span><span class="sxs-lookup"><span data-stu-id="c8332-481">Model state</span></span>

<span data-ttu-id="c8332-482">Poprzednia metoda akcji przykład wywołuje Przeciążenie `ApplyTo` , które ma stan modelu jako jeden z jego parametrów.</span><span class="sxs-lookup"><span data-stu-id="c8332-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="c8332-483">W przypadku tej opcji można odbierać komunikaty o błędach w odpowiedziach.</span><span class="sxs-lookup"><span data-stu-id="c8332-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="c8332-484">Poniższy przykład przedstawia treść nieprawidłowej odpowiedzi na żądanie 400 dla `test` operacji:</span><span class="sxs-lookup"><span data-stu-id="c8332-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="c8332-485">Obiekty dynamiczne</span><span class="sxs-lookup"><span data-stu-id="c8332-485">Dynamic objects</span></span>

<span data-ttu-id="c8332-486">W poniższym przykładzie metody akcji pokazano, jak zastosować poprawkę do obiektu dynamicznego.</span><span class="sxs-lookup"><span data-stu-id="c8332-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="c8332-487">Operacja dodawania</span><span class="sxs-lookup"><span data-stu-id="c8332-487">The add operation</span></span>

* <span data-ttu-id="c8332-488">Jeśli `path` wskazuje element tablicy: wstawia nowy element przed określony przez `path` .</span><span class="sxs-lookup"><span data-stu-id="c8332-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="c8332-489">Jeśli `path` wskazuje Właściwość: ustawia wartość właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="c8332-490">Jeśli `path` wskazuje nieistniejącą lokalizację:</span><span class="sxs-lookup"><span data-stu-id="c8332-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="c8332-491">Jeśli zasób do poprawki jest obiektem dynamicznym: dodaje właściwość.</span><span class="sxs-lookup"><span data-stu-id="c8332-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="c8332-492">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="c8332-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="c8332-493">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i dodaje `Order` obiekt na końcu `Orders` tablicy.</span><span class="sxs-lookup"><span data-stu-id="c8332-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="c8332-494">Operacja usuwania</span><span class="sxs-lookup"><span data-stu-id="c8332-494">The remove operation</span></span>

* <span data-ttu-id="c8332-495">Jeśli `path` wskazuje element tablicy: usuwa element.</span><span class="sxs-lookup"><span data-stu-id="c8332-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="c8332-496">Jeśli `path` wskazuje Właściwość:</span><span class="sxs-lookup"><span data-stu-id="c8332-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="c8332-497">Jeśli zasób do poprawki jest obiektem dynamicznym: usuwa właściwość.</span><span class="sxs-lookup"><span data-stu-id="c8332-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="c8332-498">Jeśli zasób do poprawki jest obiektem statycznym:</span><span class="sxs-lookup"><span data-stu-id="c8332-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="c8332-499">Jeśli właściwość dopuszcza wartość null: ustawia ją na wartość null.</span><span class="sxs-lookup"><span data-stu-id="c8332-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="c8332-500">Jeśli właściwość nie dopuszcza wartości null, ustawia ją na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="c8332-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="c8332-501">Poniższy Przykładowa poprawka zestawów dokumentów `CustomerName` do wartości null i usuwa `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="c8332-502">Operacja zamiany</span><span class="sxs-lookup"><span data-stu-id="c8332-502">The replace operation</span></span>

<span data-ttu-id="c8332-503">Ta operacja jest funkcjonalnie taka sama jak `remove` po nim `add` .</span><span class="sxs-lookup"><span data-stu-id="c8332-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="c8332-504">Następujący przykładowy dokument poprawek ustawia wartość `CustomerName` i zastępuje ją `Orders[0]` nowym `Order` obiektem.</span><span class="sxs-lookup"><span data-stu-id="c8332-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="c8332-505">Operacja przenoszenia</span><span class="sxs-lookup"><span data-stu-id="c8332-505">The move operation</span></span>

* <span data-ttu-id="c8332-506">Jeśli `path` wskazuje element tablicy: kopiuje `from` element do lokalizacji `path` elementu, a następnie uruchamia `remove` operację na `from` elemencie.</span><span class="sxs-lookup"><span data-stu-id="c8332-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="c8332-507">Jeśli `path` wskazuje Właściwość: kopiuje wartość `from` właściwości do `path` właściwości, a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="c8332-508">Jeśli `path` wskazuje na nieistniejącą Właściwość:</span><span class="sxs-lookup"><span data-stu-id="c8332-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="c8332-509">Jeśli zasób do poprawki jest obiektem statycznym: żądanie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="c8332-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="c8332-510">Jeśli zasób do poprawki jest obiektem dynamicznym: kopiuje `from` Właściwość do lokalizacji wskazywanej przez `path` , a następnie uruchamia `remove` operację na `from` właściwości.</span><span class="sxs-lookup"><span data-stu-id="c8332-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="c8332-511">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="c8332-511">The following sample patch document:</span></span>

* <span data-ttu-id="c8332-512">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="c8332-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="c8332-513">Ustawia `Orders[0].OrderName` wartość null.</span><span class="sxs-lookup"><span data-stu-id="c8332-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="c8332-514">Przenosi `Orders[1]` do przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="c8332-515">Operacja kopiowania</span><span class="sxs-lookup"><span data-stu-id="c8332-515">The copy operation</span></span>

<span data-ttu-id="c8332-516">Ta operacja jest funkcjonalnie taka sama jak `move` operacja bez `remove` kroku końcowego.</span><span class="sxs-lookup"><span data-stu-id="c8332-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="c8332-517">Następujący przykładowy dokument poprawek:</span><span class="sxs-lookup"><span data-stu-id="c8332-517">The following sample patch document:</span></span>

* <span data-ttu-id="c8332-518">Kopiuje wartość `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="c8332-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="c8332-519">Wstawia kopię `Orders[1]` przed `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="c8332-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="c8332-520">Operacja testowa</span><span class="sxs-lookup"><span data-stu-id="c8332-520">The test operation</span></span>

<span data-ttu-id="c8332-521">Jeśli wartość w lokalizacji wskazywanej przez różni `path` się od wartości podanej w `value` , żądanie kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="c8332-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="c8332-522">W takim przypadku całe żądanie PATCH kończy się niepowodzeniem, nawet jeśli wszystkie inne operacje w dokumencie poprawki zakończyły się powodzeniem.</span><span class="sxs-lookup"><span data-stu-id="c8332-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="c8332-523">`test`Operacja jest często używana do zapobiegania aktualizacji, gdy występuje konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="c8332-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="c8332-524">Następujący przykładowy dokument poprawek nie ma wpływu, jeśli początkowa wartość `CustomerName` to "Jan", ponieważ test zakończy się niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="c8332-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="c8332-525">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="c8332-525">Get the code</span></span>

<span data-ttu-id="c8332-526">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="c8332-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="c8332-527">([Jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c8332-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c8332-528">Aby przetestować przykład, uruchom aplikację i Wyślij żądania HTTP z następującymi ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="c8332-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="c8332-529">Adres URL:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="c8332-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="c8332-530">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="c8332-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="c8332-531">Nagłówki`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="c8332-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="c8332-532">Treść: Skopiuj i wklej jeden z przykładów dokumentu poprawki JSON z folderu projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="c8332-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8332-533">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c8332-533">Additional resources</span></span>

* [<span data-ttu-id="c8332-534">IETF RFC 5789 Specyfikacja metody poprawek</span><span class="sxs-lookup"><span data-stu-id="c8332-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="c8332-535">IETF RFC 6902 — Specyfikacja poprawek JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="c8332-536">IETF RFC 6901 JSON Format ścieżki poprawek</span><span class="sxs-lookup"><span data-stu-id="c8332-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="c8332-537">[Dokumentacja poprawek JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="c8332-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="c8332-538">Zawiera linki do zasobów do tworzenia dokumentów poprawek JSON.</span><span class="sxs-lookup"><span data-stu-id="c8332-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="c8332-539">ASP.NET Core kod źródłowy poprawki JSON</span><span class="sxs-lookup"><span data-stu-id="c8332-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
