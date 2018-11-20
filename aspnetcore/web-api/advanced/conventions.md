---
title: Użyj interfejsu API sieci web Konwencji
author: pranavkm
description: Dowiedz się więcej na temat Konwencji interfejsu API sieci web w programie ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: 023b8d09511aa42966e2a7d1c85e407bb6e79b0f
ms.sourcegitcommit: f202864efca81a72ea7120c0692940c40d9d0630
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635365"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="efb1a-103">Użyj interfejsu API sieci web Konwencji</span><span class="sxs-lookup"><span data-stu-id="efb1a-103">Use web API conventions</span></span>

<span data-ttu-id="efb1a-104">ASP.NET Core 2.2 wprowadza sposób wyodrębniania typowe [dokumentacji interfejsu API](xref:tutorials/web-api-help-pages-using-swagger) i zastosować je do wielu akcji kontrolerów i wszystkich kontrolerów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="efb1a-104">ASP.NET Core 2.2 introduces a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="efb1a-105">Konwencje interfejsu API sieci Web są stanowi zastępstwa dla dekoracji poszczególne akcje za pomocą [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="efb1a-105">Web API conventions are a substitute for decorating individual actions with [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span> <span data-ttu-id="efb1a-106">Umożliwia definiowanie najczęściej "konwencjonalne" typy zwracane i kodami stanu, które zwracają z akcję przy użyciu sposób na wybór metody Konwencji, która ma zastosowanie do akcji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-106">It allows you to define the most common "conventional" return types and status codes that you return from your action with a way to select the convention method that applies to an action.</span></span>

<span data-ttu-id="efb1a-107">Domyślnie program ASP.NET Core MVC 2.2 jest dostarczany z zestawem domyślnych Konwencji `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span><span class="sxs-lookup"><span data-stu-id="efb1a-107">By default, ASP.NET Core MVC 2.2 ships with a set of default conventions, `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span></span> <span data-ttu-id="efb1a-108">Konwencje są oparte na kontrolerze, który szkielety mechanizmów platformy ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="efb1a-108">The conventions are based on the controller that ASP.NET Core scaffolds.</span></span> <span data-ttu-id="efb1a-109">Jeśli Twoje działania oparte na wzorcu tworzenia szkieletów generuje, należy pomyślnego używania domyślnych Konwencji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-109">If your actions follow the pattern that scaffolding produces, you should be successful using the default conventions.</span></span>

<span data-ttu-id="efb1a-110">W czasie wykonywania <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumie Konwencji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-110">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="efb1a-111">`ApiExplorer` to Abstrakcja MVC do komunikowania się z generatorów dokumencie interfejsu Open API.</span><span class="sxs-lookup"><span data-stu-id="efb1a-111">`ApiExplorer` is MVC's abstraction to communicate with Open API document generators.</span></span> <span data-ttu-id="efb1a-112">Atrybuty z stosowanych Konwencji z akcji i znajdują się w dokumentacji programu Swagger akcji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-112">Attributes from the applied convention get associated with an action and are included in the action's Swagger documentation.</span></span> <span data-ttu-id="efb1a-113">Interfejs API analizatorów zrozumieć również Konwencji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-113">API analyzers also understand conventions.</span></span> <span data-ttu-id="efb1a-114">Jeśli Twoja akcja jest nietypowe (na przykład zwraca kod stanu, który nie jest udokumentowany zgodnie z Konwencją stosowane), generuje ostrzeżenie zachęcanie do dokumentu go.</span><span class="sxs-lookup"><span data-stu-id="efb1a-114">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), it produces a warning, encouraging you to document it.</span></span>

<span data-ttu-id="efb1a-115">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([sposobu pobierania](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="efb1a-115">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="efb1a-116">Zastosuj konwencje interfejsu API sieci web</span><span class="sxs-lookup"><span data-stu-id="efb1a-116">Apply web API conventions</span></span>

<span data-ttu-id="efb1a-117">Istnieją trzy sposoby, aby zastosować Konwencję.</span><span class="sxs-lookup"><span data-stu-id="efb1a-117">There are three ways to apply a convention.</span></span> <span data-ttu-id="efb1a-118">Konwencje nie tworzą, każde działanie może być skojarzony z dokładnie jednego Konwencji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-118">Conventions don't compose, each action may be associated with exactly one convention.</span></span> <span data-ttu-id="efb1a-119">Bardziej szczegółowe konwencje (szczegóły przedstawiono poniżej) mają pierwszeństwo przed mniej określonych zadań.</span><span class="sxs-lookup"><span data-stu-id="efb1a-119">More specific conventions (detailed below) take precedence over less specific ones.</span></span> <span data-ttu-id="efb1a-120">Zaznaczenie jest deterministyczna, gdy co najmniej dwóch Konwencji ten sam priorytet zastosować do akcji.</span><span class="sxs-lookup"><span data-stu-id="efb1a-120">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="efb1a-121">Istnieją następujące opcje, aby zastosować Konwencję do działania z najbardziej specyficznych do najmniej specyficznych:</span><span class="sxs-lookup"><span data-stu-id="efb1a-121">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="efb1a-122">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Ma zastosowanie do poszczególnych działań i określa typ Konwencji i metody Konwencji, która ma zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="efb1a-122">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span> <span data-ttu-id="efb1a-123">W poniższym przykładzie metoda Konwencji `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` jest stosowany do `Update` akcji:</span><span class="sxs-lookup"><span data-stu-id="efb1a-123">In the following sample, the convention method `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventionmethod&highlight=2-3)]

1. <span data-ttu-id="efb1a-124">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` stosowane do kontrolera &mdash; dotyczy typ Konwencji wszystkich akcji w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="efb1a-124">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the convention type to all actions on the controller.</span></span> <span data-ttu-id="efb1a-125">Konwencja metody są oznaczone za pomocą wskazówek, które określają, jakie akcje będą dotyczyć (szczegóły w ramach tworzenia Konwencji).</span><span class="sxs-lookup"><span data-stu-id="efb1a-125">Convention methods are decorated with hints that determine which actions it would apply to (details as part of authoring conventions).</span></span> <span data-ttu-id="efb1a-126">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="efb1a-126">For example:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventiontypeattribute)]

1. <span data-ttu-id="efb1a-127">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` zastosowany do zestawu &mdash; stosuje typu Konwencji do wszystkich kontrolerów w bieżącym zestawie.</span><span class="sxs-lookup"><span data-stu-id="efb1a-127">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="efb1a-128">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="efb1a-128">For example:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=apiconventiontypeattribute)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="efb1a-129">Tworzenie konwencje interfejsu API sieci web</span><span class="sxs-lookup"><span data-stu-id="efb1a-129">Create web API conventions</span></span>

<span data-ttu-id="efb1a-130">Konwencja jest typu statycznego za pomocą metod.</span><span class="sxs-lookup"><span data-stu-id="efb1a-130">A convention is a static type with methods.</span></span> <span data-ttu-id="efb1a-131">Te metody są oznaczony za pomocą `[ProducesResponseType]` lub `[ProducesDefaultResponseType]` atrybutów.</span><span class="sxs-lookup"><span data-stu-id="efb1a-131">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="efb1a-132">Stosując tę Konwencję do zestawu wyników w metodzie Konwencji, stosując się do dowolnej akcji o nazwie `Find` i o dokładnie jeden parametr o nazwie `id`, tak długo, jak nie mają inne dokładniejszą atrybuty metadanych.</span><span class="sxs-lookup"><span data-stu-id="efb1a-132">Applying this convention to an assembly results in the convention method applying to any action with the name `Find` and having exactly one parameter named `id`, as long as they don't have other more specific metadata attributes.</span></span>

<span data-ttu-id="efb1a-133">Oprócz `[ProducesResponseType]` i `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` i `[ApiConventionTypeMatch]` mogą być stosowane do metody Konwencji, która określa te metody, które odnoszą się do.</span><span class="sxs-lookup"><span data-stu-id="efb1a-133">In addition to `[ProducesResponseType]` and `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` can be applied to the convention method that determines the methods they apply to.</span></span> <span data-ttu-id="efb1a-134">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="efb1a-134">For example:</span></span>

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

* <span data-ttu-id="efb1a-135">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Zastosowany do metody, opcja wskazuje Konwencji może dopasować dowolną akcję tak długo, jak jest prefiksem "Znajdź".</span><span class="sxs-lookup"><span data-stu-id="efb1a-135">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method, indicates that the convention can match any action as long as it's prefixed with "Find".</span></span> <span data-ttu-id="efb1a-136">Obejmuje to metody takie jak `Find`, `FindPet`, lub `FindById`.</span><span class="sxs-lookup"><span data-stu-id="efb1a-136">This includes methods such as `Find`, `FindPet`, or `FindById`.</span></span>
* <span data-ttu-id="efb1a-137">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Zastosowany do parametru wskazuje Konwencji można dopasować metody przy użyciu dokładnie jeden parametr końcówce identyfikatora sufiksu.</span><span class="sxs-lookup"><span data-stu-id="efb1a-137">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention can match methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="efb1a-138">Obejmuje to parametry takie jak `id` lub `petId`.</span><span class="sxs-lookup"><span data-stu-id="efb1a-138">This includes parameters such as `id` or `petId`.</span></span> <span data-ttu-id="efb1a-139">`ApiConventionTypeMatch` można podobnie można zastosować do typów w celu ograniczenia typu parametru.</span><span class="sxs-lookup"><span data-stu-id="efb1a-139">`ApiConventionTypeMatch` can be similarly applied to types to constrain the type of the parameter.</span></span> <span data-ttu-id="efb1a-140">A `params[]` argument może służyć do wskazania pozostałe parametry, których nie trzeba jawnie można dopasować.</span><span class="sxs-lookup"><span data-stu-id="efb1a-140">A `params[]` argument can be used to indicate remaining parameters that don't need to be explicitly matched.</span></span>