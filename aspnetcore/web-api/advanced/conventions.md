---
title: Korzystanie z Konwencji interfejsu API sieci Web
author: pranavkm
description: Dowiedz się więcej na temat Konwencji interfejsu API sieci Web w ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/conventions
ms.openlocfilehash: f74327cd5bb6a5794c90ffdd3896f2b343e175a6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774889"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="f3662-103">Korzystanie z Konwencji interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="f3662-103">Use web API conventions</span></span>

<span data-ttu-id="f3662-104">Autorzy [Pranav Krishnamoorthy](https://github.com/pranavkm) i [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f3662-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f3662-105">ASP.NET Core 2,2 i nowsze zawierają sposób wyodrębnienia typowej [dokumentacji interfejsu API](xref:tutorials/web-api-help-pages-using-swagger) i zastosowania jej do wielu akcji, kontrolerów lub wszystkich kontrolerów w ramach zestawu.</span><span class="sxs-lookup"><span data-stu-id="f3662-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="f3662-106">Konwencje internetowego interfejsu API stanowią podstawę do dekorowania nazwy poszczególnych [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)akcji z.</span><span class="sxs-lookup"><span data-stu-id="f3662-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="f3662-107">Konwencja umożliwia:</span><span class="sxs-lookup"><span data-stu-id="f3662-107">A convention allows you to:</span></span>

* <span data-ttu-id="f3662-108">Zdefiniuj najpopularniejsze typy zwracane i kody stanu zwrócone przez określony typ akcji.</span><span class="sxs-lookup"><span data-stu-id="f3662-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="f3662-109">Zidentyfikuj akcje odbiegające od zdefiniowanego standardu.</span><span class="sxs-lookup"><span data-stu-id="f3662-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="f3662-110">ASP.NET Core MVC 2,2 i nowsze zawierają zestaw Konwencji domyślnych w programie <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="f3662-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="f3662-111">Konwencje są oparte na kontrolerze (*ValuesController.cs*), który znajduje się w szablonie projektu **interfejsu API** ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3662-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="f3662-112">Jeśli akcje są zgodne ze wzorcami w szablonie, należy pomyślnie korzystać z domyślnych Konwencji.</span><span class="sxs-lookup"><span data-stu-id="f3662-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="f3662-113">Jeśli domyślne konwencje nie spełniają Twoich potrzeb, zobacz [Tworzenie Konwencji internetowego interfejsu API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="f3662-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="f3662-114">W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumie konwencje.</span><span class="sxs-lookup"><span data-stu-id="f3662-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="f3662-115">`ApiExplorer`jest abstrakcją MVC do komunikowania się z [openapi](https://www.openapis.org/) (znanego również jako Swagger) generatorami dokumentów.</span><span class="sxs-lookup"><span data-stu-id="f3662-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="f3662-116">Atrybuty z stosowanej Konwencji są skojarzone z akcją i są zawarte w dokumentacji OpenAPI akcji.</span><span class="sxs-lookup"><span data-stu-id="f3662-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="f3662-117">[Analizatory interfejsów API](xref:web-api/advanced/analyzers) są również zrozumiałe Konwencji.</span><span class="sxs-lookup"><span data-stu-id="f3662-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="f3662-118">Jeśli akcja jest niekonwencjonalny (na przykład zwraca kod stanu, który nie jest udokumentowany przez zastosowana Konwencja), ostrzeżenie zachęca do dokumentowania kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="f3662-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="f3662-119">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3662-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="f3662-120">Zastosuj konwencje internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="f3662-120">Apply web API conventions</span></span>

<span data-ttu-id="f3662-121">Konwencje nie tworzą; Każda akcja może być skojarzona z dokładnie jedną Konwencją.</span><span class="sxs-lookup"><span data-stu-id="f3662-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="f3662-122">Bardziej szczegółowe konwencje mają pierwszeństwo przed określonymi konwencjami.</span><span class="sxs-lookup"><span data-stu-id="f3662-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="f3662-123">Zaznaczenie jest niedeterministyczne, jeśli co najmniej dwie konwencje o takim samym priorytecie mają zastosowanie do akcji.</span><span class="sxs-lookup"><span data-stu-id="f3662-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="f3662-124">Następujące opcje są stosowane w celu zastosowania konwencji do akcji, od najbardziej do najmniej określonych:</span><span class="sxs-lookup"><span data-stu-id="f3662-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="f3662-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash; Stosuje się do poszczególnych akcji i określa typ Konwencji oraz metodę Konwencji, która ma zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="f3662-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="f3662-126">W poniższym przykładzie metoda `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` Konwencji domyślnego typu Konwencji jest stosowana do `Update` akcji:</span><span class="sxs-lookup"><span data-stu-id="f3662-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="f3662-127">Metoda `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` Konwencji stosuje następujące atrybuty do akcji:</span><span class="sxs-lookup"><span data-stu-id="f3662-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="f3662-128">Aby uzyskać więcej informacji `[ProducesDefaultResponseType]`na temat, zobacz [Domyślna odpowiedź](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="f3662-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="f3662-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`zastosowane do kontrolera &mdash; stosuje określony typ Konwencji do wszystkich akcji na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="f3662-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="f3662-130">Metoda Konwencji jest oznaczona przy użyciu wskazówek, które określają akcje, których dotyczy Metoda Konwencji.</span><span class="sxs-lookup"><span data-stu-id="f3662-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="f3662-131">Aby uzyskać więcej informacji na temat wskazówek, zobacz [Tworzenie Konwencji interfejsu API sieci Web](#create-web-api-conventions)).</span><span class="sxs-lookup"><span data-stu-id="f3662-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="f3662-132">W poniższym przykładzie domyślny zestaw Konwencji jest stosowany do wszystkich akcji w *ContactsConventionController*:</span><span class="sxs-lookup"><span data-stu-id="f3662-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="f3662-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`zastosowane do zestawu &mdash; stosuje określony typ Konwencji do wszystkich kontrolerów w bieżącym zestawie.</span><span class="sxs-lookup"><span data-stu-id="f3662-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="f3662-134">Zgodnie z zaleceniem Zastosuj atrybuty na poziomie zestawu w pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="f3662-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="f3662-135">W poniższym przykładzie domyślny zestaw Konwencji jest stosowany do wszystkich kontrolerów w zestawie:</span><span class="sxs-lookup"><span data-stu-id="f3662-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="f3662-136">Tworzenie Konwencji interfejsu API sieci Web</span><span class="sxs-lookup"><span data-stu-id="f3662-136">Create web API conventions</span></span>

<span data-ttu-id="f3662-137">Jeśli domyślne konwencje interfejsów API nie spełniają Twoich potrzeb, należy utworzyć własne konwencje.</span><span class="sxs-lookup"><span data-stu-id="f3662-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="f3662-138">Konwencja:</span><span class="sxs-lookup"><span data-stu-id="f3662-138">A convention is:</span></span>

* <span data-ttu-id="f3662-139">Typ statyczny z metodami.</span><span class="sxs-lookup"><span data-stu-id="f3662-139">A static type with methods.</span></span>
* <span data-ttu-id="f3662-140">Możliwość definiowania [typów odpowiedzi](#response-types) i [wymagań dotyczących nazewnictwa](#naming-requirements) w akcjach.</span><span class="sxs-lookup"><span data-stu-id="f3662-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="f3662-141">Typy odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="f3662-141">Response types</span></span>

<span data-ttu-id="f3662-142">Te metody są opatrzone `[ProducesResponseType]` adnotacją `[ProducesDefaultResponseType]` lub atrybutami.</span><span class="sxs-lookup"><span data-stu-id="f3662-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="f3662-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3662-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="f3662-144">W przypadku braku szczegółowych atrybutów metadanych zastosowanie tej Konwencji do zestawu wymusza, że:</span><span class="sxs-lookup"><span data-stu-id="f3662-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="f3662-145">Metoda Konwencji ma zastosowanie do dowolnej akcji o `Find`nazwie.</span><span class="sxs-lookup"><span data-stu-id="f3662-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="f3662-146">W `Find` akcji występuje `id` parametr o nazwie.</span><span class="sxs-lookup"><span data-stu-id="f3662-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="f3662-147">Wymagania dotyczące nazewnictwa</span><span class="sxs-lookup"><span data-stu-id="f3662-147">Naming requirements</span></span>

<span data-ttu-id="f3662-148">Atrybuty `[ApiConventionNameMatch]` i `[ApiConventionTypeMatch]` mogą być stosowane do metody Konwencji, która określa akcje, do których mają zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="f3662-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="f3662-149">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f3662-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="f3662-150">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="f3662-150">In the preceding example:</span></span>

* <span data-ttu-id="f3662-151">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Opcja stosowana do metody wskazuje, że Konwencja pasuje do każdej akcji poprzedzonej prefiksem "Find".</span><span class="sxs-lookup"><span data-stu-id="f3662-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="f3662-152">Przykłady akcji dopasowania obejmują `Find`, `FindPet`, i. `FindById`</span><span class="sxs-lookup"><span data-stu-id="f3662-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="f3662-153">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Zastosowanie do parametru wskazuje, że Konwencja dopasowuje metody z dokładnie jednym parametrem kończącym się identyfikatorem sufiksu.</span><span class="sxs-lookup"><span data-stu-id="f3662-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="f3662-154">Przykłady obejmują parametry, takie `id` jak `petId`lub.</span><span class="sxs-lookup"><span data-stu-id="f3662-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="f3662-155">`ApiConventionTypeMatch`można w podobny sposób zastosować do typów, aby ograniczyć typ parametru.</span><span class="sxs-lookup"><span data-stu-id="f3662-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="f3662-156">`params[]` Argument wskazuje pozostałe parametry, które nie muszą być jawnie dopasowane.</span><span class="sxs-lookup"><span data-stu-id="f3662-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3662-157">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f3662-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
