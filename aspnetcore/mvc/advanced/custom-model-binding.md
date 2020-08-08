---
title: Niestandardowe powiązanie modelu w ASP.NET Core
author: ardalis
description: Dowiedz się, jak powiązanie modelu umożliwia akcjom kontrolera bezpośrednie działanie z typami modeli w ASP.NET Core.
ms.author: riande
ms.date: 01/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 6e52d9c19a3d9c16f980021000db0bdecca5ed58
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018913"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="f566b-103">Niestandardowe powiązanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f566b-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f566b-104">[Steve Kowalski](https://ardalis.com/) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f566b-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="f566b-105">Powiązanie modelu umożliwia działanie kontrolera do pracy bezpośrednio z typami modeli (przekazane jako argumenty metod), a nie żądaniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="f566b-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="f566b-106">Mapowanie między danymi żądań przychodzących a modelami aplikacji jest obsługiwane przez program Binders.</span><span class="sxs-lookup"><span data-stu-id="f566b-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="f566b-107">Deweloperzy mogą rozszerzać wbudowaną funkcję powiązania modelu, implementując niestandardowe powiązania modelu (zazwyczaj nie trzeba pisać własnego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="f566b-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="f566b-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f566b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="f566b-109">Domyślne ograniczenia spinacza modelu</span><span class="sxs-lookup"><span data-stu-id="f566b-109">Default model binder limitations</span></span>

<span data-ttu-id="f566b-110">Domyślne powiązania modeli obsługują większość wspólnych typów danych .NET Core i powinny spełniać większość potrzeb deweloperów.</span><span class="sxs-lookup"><span data-stu-id="f566b-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="f566b-111">Oczekują one powiązania danych tekstowych na podstawie żądania bezpośrednio z typami modeli.</span><span class="sxs-lookup"><span data-stu-id="f566b-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="f566b-112">Może być konieczne przekształcenie danych wejściowych przed ich powiązaniem.</span><span class="sxs-lookup"><span data-stu-id="f566b-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="f566b-113">Na przykład, jeśli masz klucz, którego można użyć do wyszukiwania danych modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="f566b-114">Do pobierania danych opartych na kluczu można użyć spinacza modelu niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="f566b-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="f566b-115">Przegląd powiązań modelu</span><span class="sxs-lookup"><span data-stu-id="f566b-115">Model binding review</span></span>

<span data-ttu-id="f566b-116">Powiązanie modelu używa określonych definicji dla typów, w których działa.</span><span class="sxs-lookup"><span data-stu-id="f566b-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="f566b-117">*Typ prosty* jest konwertowany z pojedynczego ciągu w danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="f566b-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="f566b-118">*Typ złożony* jest konwertowany z wielu wartości wejściowych.</span><span class="sxs-lookup"><span data-stu-id="f566b-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="f566b-119">Struktura określa różnice w zależności od istnienia `TypeConverter` .</span><span class="sxs-lookup"><span data-stu-id="f566b-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="f566b-120">Zalecamy utworzenie konwertera typów, jeśli istnieje proste `string`  ->  `SomeType` Mapowanie, które nie wymaga zasobów zewnętrznych.</span><span class="sxs-lookup"><span data-stu-id="f566b-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="f566b-121">Przed utworzeniem własnego spinacza modelu niestandardowego warto przejrzeć sposób implementacji istniejących spinaczy modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="f566b-122">Rozważ, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> który może być używany do konwertowania ciągów zakodowanych algorytmem Base64 na tablice bajtowe.</span><span class="sxs-lookup"><span data-stu-id="f566b-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="f566b-123">Tablice bajtowe są często przechowywane jako pliki lub pola obiektów BLOB bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f566b-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="f566b-124">Praca z ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="f566b-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="f566b-125">Ciągi kodowane algorytmem Base64 mogą służyć do reprezentowania danych binarnych.</span><span class="sxs-lookup"><span data-stu-id="f566b-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="f566b-126">Na przykład obraz może być zakodowany jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="f566b-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="f566b-127">Przykład zawiera obraz jako ciąg zakodowany algorytmem Base64 w [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="f566b-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="f566b-128">ASP.NET Core MVC może przyjmować ciąg zakodowany algorytmem Base64 i używać go, `ByteArrayModelBinder` Aby przekonwertować go na tablicę bajtów.</span><span class="sxs-lookup"><span data-stu-id="f566b-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="f566b-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider>Mapowania `byte[]` argumentów do `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="f566b-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="f566b-130">Podczas tworzenia własnego spinacza modelu niestandardowego można zaimplementować własny `IModelBinderProvider` Typ lub użyć <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="f566b-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="f566b-131">Poniższy przykład pokazuje, jak użyć `ByteArrayModelBinder` do przekonwertowania ciągu zakodowanego algorytmem Base64 na `byte[]` a i zapisać wynik do pliku:</span><span class="sxs-lookup"><span data-stu-id="f566b-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f566b-132">Można OPUBLIKOWAĆ ciąg zakodowany algorytmem Base64 w tej metodzie interfejsu API za pomocą narzędzia, takiego jak [Poster](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="f566b-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="f566b-133">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="f566b-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="f566b-134">Tak długo, jak spinacz może powiązać dane żądania do odpowiednio nazwanych właściwości lub argumentów, powiązanie modelu zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="f566b-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="f566b-135">Poniższy przykład przedstawia sposób użycia `ByteArrayModelBinder` z modelem widoku:</span><span class="sxs-lookup"><span data-stu-id="f566b-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="f566b-136">Przykładowy spinacz modelu niestandardowego</span><span class="sxs-lookup"><span data-stu-id="f566b-136">Custom model binder sample</span></span>

<span data-ttu-id="f566b-137">W tej sekcji zaimplementujemy niestandardowy spinacz modelu, który:</span><span class="sxs-lookup"><span data-stu-id="f566b-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="f566b-138">Konwertuje przychodzące dane żądania na argumenty klucza o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="f566b-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="f566b-139">Używa Entity Framework Core, aby pobrać skojarzoną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="f566b-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="f566b-140">Przekazuje skojarzoną jednostkę jako argument do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="f566b-141">Poniższy przykład używa `ModelBinder` atrybutu w `Author` modelu:</span><span class="sxs-lookup"><span data-stu-id="f566b-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="f566b-142">W poprzednim kodzie `ModelBinder` atrybut określa typ `IModelBinder` , który powinien być używany do wiązania `Author` parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="f566b-143">Następująca `AuthorEntityBinder` Klasa wiąże `Author` parametr przez pobranie jednostki ze źródła danych przy użyciu Entity Framework Core i `authorId` :</span><span class="sxs-lookup"><span data-stu-id="f566b-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="f566b-144">Poprzednia `AuthorEntityBinder` Klasa jest przeznaczona do zilustrowania niestandardowego spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="f566b-145">Klasa nie jest przeznaczona do zilustrowania najlepszych rozwiązań dotyczących scenariusza wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f566b-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="f566b-146">W celu wyszukania należy powiązać `authorId` bazę danych i zbadać ją w metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="f566b-147">To podejście oddziela błędy powiązań modelu z `NotFound` przypadków.</span><span class="sxs-lookup"><span data-stu-id="f566b-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="f566b-148">Poniższy kod pokazuje, jak używać `AuthorEntityBinder` w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="f566b-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="f566b-149">Ten `ModelBinder` atrybut może służyć do stosowania `AuthorEntityBinder` parametrów do, które nie używają Konwencji domyślnych:</span><span class="sxs-lookup"><span data-stu-id="f566b-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="f566b-150">W tym przykładzie, ponieważ nazwa argumentu nie jest wartością domyślną, jest `authorId` określona w parametrze przy użyciu `ModelBinder` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f566b-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="f566b-151">Zarówno kontroler, jak i Metoda akcji są uproszczone w porównaniu z wyszukiwaniem jednostki w metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="f566b-152">Logika pobierania autora przy użyciu Entity Framework Core jest przenoszona do spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="f566b-153">Może to być znaczące uproszczenie, gdy istnieje kilka metod, które są powiązane z `Author` modelem.</span><span class="sxs-lookup"><span data-stu-id="f566b-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="f566b-154">Można zastosować `ModelBinder` atrybut do poszczególnych właściwości modelu (na przykład na ViewModel) lub do parametrów metody akcji, aby określić określony spinacz modelu lub nazwę modelu dla tylko tego typu lub akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="f566b-155">Implementowanie elementu ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="f566b-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="f566b-156">Zamiast stosować atrybut, można zaimplementować `IModelBinderProvider` .</span><span class="sxs-lookup"><span data-stu-id="f566b-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="f566b-157">Jest to sposób implementacji wbudowanych powiązań struktury.</span><span class="sxs-lookup"><span data-stu-id="f566b-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="f566b-158">Po określeniu typu, na którym działa Twój spinacz, należy określić typ argumentu, który produkuje, a **nie** dane wejściowe zaakceptowane przez spinacz.</span><span class="sxs-lookup"><span data-stu-id="f566b-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="f566b-159">Następujący dostawca programu Binder współpracuje z `AuthorEntityBinder` .</span><span class="sxs-lookup"><span data-stu-id="f566b-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="f566b-160">Po dodaniu do kolekcji dostawców MVC nie trzeba używać `ModelBinder` atrybutów z `Author` `Author` parametrami lub typem.</span><span class="sxs-lookup"><span data-stu-id="f566b-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="f566b-161">Uwaga: Poprzedni kod zwraca wartość `BinderTypeModelBinder` .</span><span class="sxs-lookup"><span data-stu-id="f566b-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="f566b-162">`BinderTypeModelBinder`działa jako fabryka dla segregatorów modelu i zapewnia iniekcję zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="f566b-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="f566b-163">`AuthorEntityBinder`Program wymaga dostępu EF Core.</span><span class="sxs-lookup"><span data-stu-id="f566b-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="f566b-164">Użyj, `BinderTypeModelBinder` Jeśli model spinacza wymaga usług z di.</span><span class="sxs-lookup"><span data-stu-id="f566b-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="f566b-165">Aby użyć niestandardowego dostawcy segregatorów modelu, Dodaj go w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f566b-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="f566b-166">Podczas oceniania powiązań modelu kolekcja dostawców jest sprawdzana w podanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="f566b-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="f566b-167">Używany jest pierwszy dostawca zwracający spinacz.</span><span class="sxs-lookup"><span data-stu-id="f566b-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="f566b-168">Dodanie swojego dostawcy do końca kolekcji może spowodować wywołanie wbudowanego spinacza modelu, zanim niestandardowy spinacz będzie miał szansę.</span><span class="sxs-lookup"><span data-stu-id="f566b-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="f566b-169">W tym przykładzie niestandardowy dostawca zostanie dodany do początku kolekcji, aby upewnić się, że jest on używany dla `Author` argumentów akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="f566b-170">Powiązanie modelu polimorficznego</span><span class="sxs-lookup"><span data-stu-id="f566b-170">Polymorphic model binding</span></span>

<span data-ttu-id="f566b-171">Powiązanie z różnymi modelami typów pochodnych jest znane jako powiązanie modelu polimorficzne.</span><span class="sxs-lookup"><span data-stu-id="f566b-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="f566b-172">Powiązanie modelu niestandardowego polimorficzne jest wymagane, gdy wartość żądania musi być powiązana z konkretnym typem modelu pochodnego.</span><span class="sxs-lookup"><span data-stu-id="f566b-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="f566b-173">Powiązanie modelu polimorficznego:</span><span class="sxs-lookup"><span data-stu-id="f566b-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="f566b-174">Nie jest typowy dla interfejsu API REST, który jest przeznaczony do współpracy ze wszystkimi językami.</span><span class="sxs-lookup"><span data-stu-id="f566b-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="f566b-175">Utrudniają powody dotyczące modeli powiązanych.</span><span class="sxs-lookup"><span data-stu-id="f566b-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="f566b-176">Jeśli jednak aplikacja wymaga powiązania modelu polimorficznego, implementacja może wyglądać podobnie do następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="f566b-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="f566b-177">Zalecenia i najlepsze rozwiązania</span><span class="sxs-lookup"><span data-stu-id="f566b-177">Recommendations and best practices</span></span>

<span data-ttu-id="f566b-178">Niestandardowe powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="f566b-178">Custom model binders:</span></span>

- <span data-ttu-id="f566b-179">Nie należy próbować ustawiać kodów stanu ani zwracać wyników (na przykład nie znaleziono 404).</span><span class="sxs-lookup"><span data-stu-id="f566b-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="f566b-180">Jeśli powiązanie modelu nie powiedzie się, [Filtr akcji](xref:mvc/controllers/filters) lub logika w samej metodzie akcji powinien obsłużyć błąd.</span><span class="sxs-lookup"><span data-stu-id="f566b-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="f566b-181">Są najbardziej przydatne do eliminowania powtarzających się kodu i krzyżowego obaw z metod akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="f566b-182">Zwykle nie należy używać do konwertowania ciągu na typ niestandardowy, a <xref:System.ComponentModel.TypeConverter> jest zazwyczaj lepszym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="f566b-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f566b-183">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f566b-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f566b-184">Powiązanie modelu umożliwia działanie kontrolera do pracy bezpośrednio z typami modeli (przekazane jako argumenty metod), a nie żądaniami HTTP.</span><span class="sxs-lookup"><span data-stu-id="f566b-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="f566b-185">Mapowanie między danymi żądań przychodzących a modelami aplikacji jest obsługiwane przez program Binders.</span><span class="sxs-lookup"><span data-stu-id="f566b-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="f566b-186">Deweloperzy mogą rozszerzać wbudowaną funkcję powiązania modelu, implementując niestandardowe powiązania modelu (zazwyczaj nie trzeba pisać własnego dostawcy).</span><span class="sxs-lookup"><span data-stu-id="f566b-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="f566b-187">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f566b-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="f566b-188">Domyślne ograniczenia spinacza modelu</span><span class="sxs-lookup"><span data-stu-id="f566b-188">Default model binder limitations</span></span>

<span data-ttu-id="f566b-189">Domyślne powiązania modeli obsługują większość wspólnych typów danych .NET Core i powinny spełniać większość potrzeb deweloperów.</span><span class="sxs-lookup"><span data-stu-id="f566b-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="f566b-190">Oczekują one powiązania danych tekstowych na podstawie żądania bezpośrednio z typami modeli.</span><span class="sxs-lookup"><span data-stu-id="f566b-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="f566b-191">Może być konieczne przekształcenie danych wejściowych przed ich powiązaniem.</span><span class="sxs-lookup"><span data-stu-id="f566b-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="f566b-192">Na przykład, jeśli masz klucz, którego można użyć do wyszukiwania danych modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="f566b-193">Do pobierania danych opartych na kluczu można użyć spinacza modelu niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="f566b-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="f566b-194">Przegląd powiązań modelu</span><span class="sxs-lookup"><span data-stu-id="f566b-194">Model binding review</span></span>

<span data-ttu-id="f566b-195">Powiązanie modelu używa określonych definicji dla typów, w których działa.</span><span class="sxs-lookup"><span data-stu-id="f566b-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="f566b-196">*Typ prosty* jest konwertowany z pojedynczego ciągu w danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="f566b-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="f566b-197">*Typ złożony* jest konwertowany z wielu wartości wejściowych.</span><span class="sxs-lookup"><span data-stu-id="f566b-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="f566b-198">Struktura określa różnice w zależności od istnienia `TypeConverter` .</span><span class="sxs-lookup"><span data-stu-id="f566b-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="f566b-199">Zalecamy utworzenie konwertera typów, jeśli istnieje proste `string`  ->  `SomeType` Mapowanie, które nie wymaga zasobów zewnętrznych.</span><span class="sxs-lookup"><span data-stu-id="f566b-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="f566b-200">Przed utworzeniem własnego spinacza modelu niestandardowego warto przejrzeć sposób implementacji istniejących spinaczy modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="f566b-201">Rozważ, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> który może być używany do konwertowania ciągów zakodowanych algorytmem Base64 na tablice bajtowe.</span><span class="sxs-lookup"><span data-stu-id="f566b-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="f566b-202">Tablice bajtowe są często przechowywane jako pliki lub pola obiektów BLOB bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f566b-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="f566b-203">Praca z ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="f566b-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="f566b-204">Ciągi kodowane algorytmem Base64 mogą służyć do reprezentowania danych binarnych.</span><span class="sxs-lookup"><span data-stu-id="f566b-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="f566b-205">Na przykład obraz może być zakodowany jako ciąg.</span><span class="sxs-lookup"><span data-stu-id="f566b-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="f566b-206">Przykład zawiera obraz jako ciąg zakodowany algorytmem Base64 w [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="f566b-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="f566b-207">ASP.NET Core MVC może przyjmować ciąg zakodowany algorytmem Base64 i używać go, `ByteArrayModelBinder` Aby przekonwertować go na tablicę bajtów.</span><span class="sxs-lookup"><span data-stu-id="f566b-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="f566b-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider>Mapowania `byte[]` argumentów do `ByteArrayModelBinder` :</span><span class="sxs-lookup"><span data-stu-id="f566b-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="f566b-209">Podczas tworzenia własnego spinacza modelu niestandardowego można zaimplementować własny `IModelBinderProvider` Typ lub użyć <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> .</span><span class="sxs-lookup"><span data-stu-id="f566b-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="f566b-210">Poniższy przykład pokazuje, jak użyć `ByteArrayModelBinder` do przekonwertowania ciągu zakodowanego algorytmem Base64 na `byte[]` a i zapisać wynik do pliku:</span><span class="sxs-lookup"><span data-stu-id="f566b-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="f566b-211">Można OPUBLIKOWAĆ ciąg zakodowany algorytmem Base64 w tej metodzie interfejsu API za pomocą narzędzia, takiego jak [Poster](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="f566b-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="f566b-212">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="f566b-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="f566b-213">Tak długo, jak spinacz może powiązać dane żądania do odpowiednio nazwanych właściwości lub argumentów, powiązanie modelu zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="f566b-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="f566b-214">Poniższy przykład przedstawia sposób użycia `ByteArrayModelBinder` z modelem widoku:</span><span class="sxs-lookup"><span data-stu-id="f566b-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="f566b-215">Przykładowy spinacz modelu niestandardowego</span><span class="sxs-lookup"><span data-stu-id="f566b-215">Custom model binder sample</span></span>

<span data-ttu-id="f566b-216">W tej sekcji zaimplementujemy niestandardowy spinacz modelu, który:</span><span class="sxs-lookup"><span data-stu-id="f566b-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="f566b-217">Konwertuje przychodzące dane żądania na argumenty klucza o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="f566b-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="f566b-218">Używa Entity Framework Core, aby pobrać skojarzoną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="f566b-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="f566b-219">Przekazuje skojarzoną jednostkę jako argument do metody akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="f566b-220">Poniższy przykład używa `ModelBinder` atrybutu w `Author` modelu:</span><span class="sxs-lookup"><span data-stu-id="f566b-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="f566b-221">W poprzednim kodzie `ModelBinder` atrybut określa typ `IModelBinder` , który powinien być używany do wiązania `Author` parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="f566b-222">Następująca `AuthorEntityBinder` Klasa wiąże `Author` parametr przez pobranie jednostki ze źródła danych przy użyciu Entity Framework Core i `authorId` :</span><span class="sxs-lookup"><span data-stu-id="f566b-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="f566b-223">Poprzednia `AuthorEntityBinder` Klasa jest przeznaczona do zilustrowania niestandardowego spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="f566b-224">Klasa nie jest przeznaczona do zilustrowania najlepszych rozwiązań dotyczących scenariusza wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f566b-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="f566b-225">W celu wyszukania należy powiązać `authorId` bazę danych i zbadać ją w metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="f566b-226">To podejście oddziela błędy powiązań modelu z `NotFound` przypadków.</span><span class="sxs-lookup"><span data-stu-id="f566b-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="f566b-227">Poniższy kod pokazuje, jak używać `AuthorEntityBinder` w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="f566b-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="f566b-228">Ten `ModelBinder` atrybut może służyć do stosowania `AuthorEntityBinder` parametrów do, które nie używają Konwencji domyślnych:</span><span class="sxs-lookup"><span data-stu-id="f566b-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="f566b-229">W tym przykładzie, ponieważ nazwa argumentu nie jest wartością domyślną, jest `authorId` określona w parametrze przy użyciu `ModelBinder` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="f566b-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="f566b-230">Zarówno kontroler, jak i Metoda akcji są uproszczone w porównaniu z wyszukiwaniem jednostki w metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="f566b-231">Logika pobierania autora przy użyciu Entity Framework Core jest przenoszona do spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="f566b-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="f566b-232">Może to być znaczące uproszczenie, gdy istnieje kilka metod, które są powiązane z `Author` modelem.</span><span class="sxs-lookup"><span data-stu-id="f566b-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="f566b-233">Można zastosować `ModelBinder` atrybut do poszczególnych właściwości modelu (na przykład na ViewModel) lub do parametrów metody akcji, aby określić określony spinacz modelu lub nazwę modelu dla tylko tego typu lub akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="f566b-234">Implementowanie elementu ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="f566b-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="f566b-235">Zamiast stosować atrybut, można zaimplementować `IModelBinderProvider` .</span><span class="sxs-lookup"><span data-stu-id="f566b-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="f566b-236">Jest to sposób implementacji wbudowanych powiązań struktury.</span><span class="sxs-lookup"><span data-stu-id="f566b-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="f566b-237">Po określeniu typu, na którym działa Twój spinacz, należy określić typ argumentu, który produkuje, a **nie** dane wejściowe zaakceptowane przez spinacz.</span><span class="sxs-lookup"><span data-stu-id="f566b-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="f566b-238">Następujący dostawca programu Binder współpracuje z `AuthorEntityBinder` .</span><span class="sxs-lookup"><span data-stu-id="f566b-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="f566b-239">Po dodaniu do kolekcji dostawców MVC nie trzeba używać `ModelBinder` atrybutów z `Author` `Author` parametrami lub typem.</span><span class="sxs-lookup"><span data-stu-id="f566b-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="f566b-240">Uwaga: Poprzedni kod zwraca wartość `BinderTypeModelBinder` .</span><span class="sxs-lookup"><span data-stu-id="f566b-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="f566b-241">`BinderTypeModelBinder`działa jako fabryka dla segregatorów modelu i zapewnia iniekcję zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="f566b-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="f566b-242">`AuthorEntityBinder`Program wymaga dostępu EF Core.</span><span class="sxs-lookup"><span data-stu-id="f566b-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="f566b-243">Użyj, `BinderTypeModelBinder` Jeśli model spinacza wymaga usług z di.</span><span class="sxs-lookup"><span data-stu-id="f566b-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="f566b-244">Aby użyć niestandardowego dostawcy segregatorów modelu, Dodaj go w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f566b-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="f566b-245">Podczas oceniania powiązań modelu kolekcja dostawców jest sprawdzana w podanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="f566b-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="f566b-246">Używany jest pierwszy dostawca zwracający spinacz.</span><span class="sxs-lookup"><span data-stu-id="f566b-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="f566b-247">Dodanie swojego dostawcy do końca kolekcji może spowodować wywołanie wbudowanego spinacza modelu, zanim niestandardowy spinacz będzie miał szansę.</span><span class="sxs-lookup"><span data-stu-id="f566b-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="f566b-248">W tym przykładzie niestandardowy dostawca zostanie dodany do początku kolekcji, aby upewnić się, że jest on używany dla `Author` argumentów akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="f566b-249">Powiązanie modelu polimorficznego</span><span class="sxs-lookup"><span data-stu-id="f566b-249">Polymorphic model binding</span></span>

<span data-ttu-id="f566b-250">Powiązanie z różnymi modelami typów pochodnych jest znane jako powiązanie modelu polimorficzne.</span><span class="sxs-lookup"><span data-stu-id="f566b-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="f566b-251">Powiązanie modelu niestandardowego polimorficzne jest wymagane, gdy wartość żądania musi być powiązana z konkretnym typem modelu pochodnego.</span><span class="sxs-lookup"><span data-stu-id="f566b-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="f566b-252">Powiązanie modelu polimorficznego:</span><span class="sxs-lookup"><span data-stu-id="f566b-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="f566b-253">Nie jest typowy dla interfejsu API REST, który jest przeznaczony do współpracy ze wszystkimi językami.</span><span class="sxs-lookup"><span data-stu-id="f566b-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="f566b-254">Utrudniają powody dotyczące modeli powiązanych.</span><span class="sxs-lookup"><span data-stu-id="f566b-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="f566b-255">Jeśli jednak aplikacja wymaga powiązania modelu polimorficznego, implementacja może wyglądać podobnie do następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="f566b-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="f566b-256">Zalecenia i najlepsze rozwiązania</span><span class="sxs-lookup"><span data-stu-id="f566b-256">Recommendations and best practices</span></span>

<span data-ttu-id="f566b-257">Niestandardowe powiązania modelu:</span><span class="sxs-lookup"><span data-stu-id="f566b-257">Custom model binders:</span></span>

- <span data-ttu-id="f566b-258">Nie należy próbować ustawiać kodów stanu ani zwracać wyników (na przykład nie znaleziono 404).</span><span class="sxs-lookup"><span data-stu-id="f566b-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="f566b-259">Jeśli powiązanie modelu nie powiedzie się, [Filtr akcji](xref:mvc/controllers/filters) lub logika w samej metodzie akcji powinien obsłużyć błąd.</span><span class="sxs-lookup"><span data-stu-id="f566b-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="f566b-260">Są najbardziej przydatne do eliminowania powtarzających się kodu i krzyżowego obaw z metod akcji.</span><span class="sxs-lookup"><span data-stu-id="f566b-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="f566b-261">Zwykle nie należy używać do konwertowania ciągu na typ niestandardowy, a <xref:System.ComponentModel.TypeConverter> jest zazwyczaj lepszym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="f566b-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
