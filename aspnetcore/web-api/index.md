---
title: Tworzenie internetowych interfejsów API za pomocą platformy ASP.NET Core
author: scottaddie
description: Poznaj podstawy tworzenia internetowego interfejsu API w programie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/index
ms.openlocfilehash: 5ea82d36d305b1f6ba58fba1b4c5bb4dca22f912
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776529"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="7a937-103">Tworzenie internetowych interfejsów API za pomocą platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a937-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="7a937-104">Przez [Scott Addie](https://github.com/scottaddie) i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7a937-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="7a937-105">Platforma ASP.NET Core obsługuje tworzenie usług RESTful, znanych także jako internetowe interfejsy API, za pomocą języka C#.</span><span class="sxs-lookup"><span data-stu-id="7a937-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="7a937-106">Aby obsługiwać żądania, interfejs API sieci Web używa kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="7a937-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="7a937-107">*Kontrolery* w INTERNETowym interfejsie API są klasami `ControllerBase`pochodnymi od.</span><span class="sxs-lookup"><span data-stu-id="7a937-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="7a937-108">W tym artykule pokazano, jak używać kontrolerów do obsługi żądań interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7a937-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="7a937-109">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="7a937-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="7a937-110">([Jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7a937-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="7a937-111">Klasa ControllerBase</span><span class="sxs-lookup"><span data-stu-id="7a937-111">ControllerBase class</span></span>

<span data-ttu-id="7a937-112">Internetowy interfejs API składa się z co najmniej jednej klasy kontrolera, <xref:Microsoft.AspNetCore.Mvc.ControllerBase>która pochodzi od.</span><span class="sxs-lookup"><span data-stu-id="7a937-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="7a937-113">Szablon projektu internetowego interfejsu API zawiera kontroler początkowy:</span><span class="sxs-lookup"><span data-stu-id="7a937-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="7a937-114">Nie twórz kontrolera interfejsu API sieci Web, pobierając z <xref:Microsoft.AspNetCore.Mvc.Controller> klasy.</span><span class="sxs-lookup"><span data-stu-id="7a937-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="7a937-115">`Controller`Program dziedziczy `ControllerBase` z i dodaje obsługę widoków, dlatego służy do obsługi stron sieci Web, a nie żądań interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7a937-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="7a937-116">Wystąpił wyjątek dla tej reguły: Jeśli planujesz używać tego samego kontrolera dla widoków i interfejsów API sieci Web, utwórz go z `Controller`.</span><span class="sxs-lookup"><span data-stu-id="7a937-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="7a937-117">`ControllerBase` Klasa zawiera wiele właściwości i metod, które są przydatne do obsługi żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="7a937-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="7a937-118">Na przykład `ControllerBase.CreatedAtAction` zwraca kod stanu 201:</span><span class="sxs-lookup"><span data-stu-id="7a937-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="7a937-119">Poniżej przedstawiono kilka przykładów metod, które `ControllerBase` zapewnia.</span><span class="sxs-lookup"><span data-stu-id="7a937-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="7a937-120">Metoda</span><span class="sxs-lookup"><span data-stu-id="7a937-120">Method</span></span>   |<span data-ttu-id="7a937-121">Uwagi</span><span class="sxs-lookup"><span data-stu-id="7a937-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="7a937-122">Zwraca kod stanu 400.</span><span class="sxs-lookup"><span data-stu-id="7a937-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="7a937-123">Zwraca kod stanu 404.</span><span class="sxs-lookup"><span data-stu-id="7a937-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="7a937-124">Zwraca plik.</span><span class="sxs-lookup"><span data-stu-id="7a937-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="7a937-125">Wywołuje [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="7a937-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="7a937-126">Wywołuje [walidację modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="7a937-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="7a937-127">Listę wszystkich dostępnych metod i właściwości można znaleźć w temacie <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="7a937-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="7a937-128">Atrybuty</span><span class="sxs-lookup"><span data-stu-id="7a937-128">Attributes</span></span>

<span data-ttu-id="7a937-129"><xref:Microsoft.AspNetCore.Mvc> Przestrzeń nazw zawiera atrybuty, których można użyć do skonfigurowania zachowania kontrolerów internetowego interfejsu API i metod akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="7a937-130">Poniższy przykład używa atrybutów, aby określić obsługiwane zlecenie akcji HTTP i wszystkie znane kody stanu HTTP, które mogą zostać zwrócone:</span><span class="sxs-lookup"><span data-stu-id="7a937-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="7a937-131">Poniżej przedstawiono kilka przykładów dostępnych atrybutów.</span><span class="sxs-lookup"><span data-stu-id="7a937-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="7a937-132">Atrybut</span><span class="sxs-lookup"><span data-stu-id="7a937-132">Attribute</span></span>|<span data-ttu-id="7a937-133">Uwagi</span><span class="sxs-lookup"><span data-stu-id="7a937-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="7a937-134">Określa wzorzec adresu URL dla kontrolera lub akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="7a937-135">Określa prefiks i właściwości, które mają zostać dołączone do powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="7a937-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="7a937-136">Identyfikuje akcję, która obsługuje czasownik HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7a937-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="7a937-137">Określa typy danych, które akcja akceptuje.</span><span class="sxs-lookup"><span data-stu-id="7a937-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="7a937-138">Określa typy danych, które zwraca akcja.</span><span class="sxs-lookup"><span data-stu-id="7a937-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="7a937-139">Aby zapoznać się z listą zawierającą dostępne atrybuty, <xref:Microsoft.AspNetCore.Mvc> Zobacz Przestrzeń nazw.</span><span class="sxs-lookup"><span data-stu-id="7a937-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="7a937-140">ApiController — atrybut</span><span class="sxs-lookup"><span data-stu-id="7a937-140">ApiController attribute</span></span>

<span data-ttu-id="7a937-141">Ten [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut może być stosowany do klasy kontrolera w celu włączenia następujących zachowań ceniona, specyficznych dla interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="7a937-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="7a937-142">Wymagania dotyczące routingu atrybutów</span><span class="sxs-lookup"><span data-stu-id="7a937-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="7a937-143">Automatyczne odpowiedzi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="7a937-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="7a937-144">Wnioskowanie parametru źródła powiązania</span><span class="sxs-lookup"><span data-stu-id="7a937-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="7a937-145">Wieloczęściowe/formularz-wnioskowanie dotyczące danych</span><span class="sxs-lookup"><span data-stu-id="7a937-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="7a937-146">Szczegóły problemu dotyczące kodów stanu błędu</span><span class="sxs-lookup"><span data-stu-id="7a937-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="7a937-147">*Szczegóły problemu dotyczącego kodów stanu błędu* wymagają [wersji](xref:mvc/compatibility-version) 2,2 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="7a937-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="7a937-148">Inne funkcje wymagają wersji 2,1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="7a937-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="7a937-149">Wymagania dotyczące routingu atrybutów</span><span class="sxs-lookup"><span data-stu-id="7a937-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="7a937-150">Automatyczne odpowiedzi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="7a937-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="7a937-151">Wnioskowanie parametru źródła powiązania</span><span class="sxs-lookup"><span data-stu-id="7a937-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="7a937-152">Wieloczęściowe/formularz-wnioskowanie dotyczące danych</span><span class="sxs-lookup"><span data-stu-id="7a937-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="7a937-153">Te funkcje wymagają [wersji](xref:mvc/compatibility-version) 2,1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="7a937-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="7a937-154">Atrybut na określonych kontrolerach</span><span class="sxs-lookup"><span data-stu-id="7a937-154">Attribute on specific controllers</span></span>

<span data-ttu-id="7a937-155">Ten `[ApiController]` atrybut może być stosowany do określonych kontrolerów, jak w poniższym przykładzie z szablonu projektu:</span><span class="sxs-lookup"><span data-stu-id="7a937-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="7a937-156">Atrybut na wielu kontrolerach</span><span class="sxs-lookup"><span data-stu-id="7a937-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="7a937-157">Jednym z metod używania atrybutu na więcej niż jednym kontrolerze jest utworzenie niestandardowej klasy kontrolera podstawowego z adnotacją z `[ApiController]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="7a937-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="7a937-158">Poniższy przykład przedstawia niestandardową klasę bazową i kontroler, który pochodzi od niego:</span><span class="sxs-lookup"><span data-stu-id="7a937-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="7a937-159">Atrybut w zestawie</span><span class="sxs-lookup"><span data-stu-id="7a937-159">Attribute on an assembly</span></span>

<span data-ttu-id="7a937-160">Jeśli [wersja zgodności](xref:mvc/compatibility-version) jest ustawiona na 2,2 lub nowsza, `[ApiController]` atrybut może być stosowany do zestawu.</span><span class="sxs-lookup"><span data-stu-id="7a937-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="7a937-161">Adnotacja w ten sposób stosuje zachowanie internetowego interfejsu API do wszystkich kontrolerów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="7a937-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="7a937-162">Nie ma możliwości rezygnacji z poszczególnych kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="7a937-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="7a937-163">Zastosuj atrybut poziomu zestawu do deklaracji przestrzeni nazw otaczającej `Startup` klasę:</span><span class="sxs-lookup"><span data-stu-id="7a937-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="7a937-164">Wymagania dotyczące routingu atrybutów</span><span class="sxs-lookup"><span data-stu-id="7a937-164">Attribute routing requirement</span></span>

<span data-ttu-id="7a937-165">`[ApiController]` Atrybut powoduje, że atrybut routingu wymaga.</span><span class="sxs-lookup"><span data-stu-id="7a937-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="7a937-166">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7a937-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="7a937-167">Akcje są niedostępne za pośrednictwem [konwencjonalnych tras](xref:mvc/controllers/routing#conventional-routing) `UseEndpoints`zdefiniowanych <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>przez, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> lub `Startup.Configure`w.</span><span class="sxs-lookup"><span data-stu-id="7a937-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="7a937-168">Akcje są niedostępne za pośrednictwem [konwencjonalnych tras](xref:mvc/controllers/routing#conventional-routing) zdefiniowanych <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> przez `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> lub w.</span><span class="sxs-lookup"><span data-stu-id="7a937-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="7a937-169">Automatyczne odpowiedzi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="7a937-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="7a937-170">Ten `[ApiController]` atrybut sprawia, że błędy walidacji modelu automatycznie wyzwalają odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="7a937-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="7a937-171">W związku z tym Poniższy kod jest zbędny w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="7a937-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="7a937-172">ASP.NET Core MVC używa filtru <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> akcji, aby wykonać poprzednią kontrolę.</span><span class="sxs-lookup"><span data-stu-id="7a937-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="7a937-173">Domyślna odpowiedź nieprawidłowego żądania</span><span class="sxs-lookup"><span data-stu-id="7a937-173">Default BadRequest response</span></span>

<span data-ttu-id="7a937-174">W przypadku zgodności z wersją 2,1, domyślny typ odpowiedzi dla odpowiedzi HTTP 400 to <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="7a937-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="7a937-175">Następująca treść żądania jest przykładem serializowanego typu:</span><span class="sxs-lookup"><span data-stu-id="7a937-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7a937-176">W przypadku zgodności z wersją 2,2 lub nowszą domyślny typ odpowiedzi dla odpowiedzi HTTP 400 to <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="7a937-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="7a937-177">Następująca treść żądania jest przykładem serializowanego typu:</span><span class="sxs-lookup"><span data-stu-id="7a937-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="7a937-178">`ValidationProblemDetails` Typ:</span><span class="sxs-lookup"><span data-stu-id="7a937-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="7a937-179">Zapewnia czytelny dla maszyn format służący do określania błędów w odpowiedziach interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7a937-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="7a937-180">Jest zgodna ze [specyfikacją RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="7a937-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="7a937-181">Rejestruj automatyczne odpowiedzi 400</span><span class="sxs-lookup"><span data-stu-id="7a937-181">Log automatic 400 responses</span></span>

<span data-ttu-id="7a937-182">Zobacz [, jak rejestrować 400 automatyczne odpowiedzi na błędy walidacji modelu (ASPNET/AspNetCore. Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="7a937-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="7a937-183">Wyłącz automatyczną odpowiedź 400</span><span class="sxs-lookup"><span data-stu-id="7a937-183">Disable automatic 400 response</span></span>

<span data-ttu-id="7a937-184">Aby wyłączyć zachowanie automatycznego 400, należy ustawić <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> właściwość na. `true`</span><span class="sxs-lookup"><span data-stu-id="7a937-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="7a937-185">Dodaj następujący wyróżniony kod w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a937-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="7a937-186">Wnioskowanie parametru źródła powiązania</span><span class="sxs-lookup"><span data-stu-id="7a937-186">Binding source parameter inference</span></span>

<span data-ttu-id="7a937-187">Atrybut źródłowy powiązania definiuje lokalizację, w której zostanie znaleziona wartość parametru akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="7a937-188">Istnieją następujące atrybuty źródła powiązania:</span><span class="sxs-lookup"><span data-stu-id="7a937-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="7a937-189">Atrybut</span><span class="sxs-lookup"><span data-stu-id="7a937-189">Attribute</span></span>|<span data-ttu-id="7a937-190">Źródło powiązania</span><span class="sxs-lookup"><span data-stu-id="7a937-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="7a937-191">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="7a937-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="7a937-192">Formularz danych w treści żądania</span><span class="sxs-lookup"><span data-stu-id="7a937-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="7a937-193">Nagłówek żądania</span><span class="sxs-lookup"><span data-stu-id="7a937-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="7a937-194">Parametr ciągu zapytania żądania</span><span class="sxs-lookup"><span data-stu-id="7a937-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="7a937-195">Kierowanie danych z bieżącego żądania</span><span class="sxs-lookup"><span data-stu-id="7a937-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="7a937-196">Usługa żądania wstrzykiwana jako parametr akcji</span><span class="sxs-lookup"><span data-stu-id="7a937-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="7a937-197">Nie należy `[FromRoute]` używać, gdy wartości `%2f` mogą zawierać ( `/`to oznacza).</span><span class="sxs-lookup"><span data-stu-id="7a937-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="7a937-198">`%2f`nie zostanie wywyprowadzane do `/`.</span><span class="sxs-lookup"><span data-stu-id="7a937-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="7a937-199">Użyj `[FromQuery]` , jeśli wartość może zawierać `%2f`.</span><span class="sxs-lookup"><span data-stu-id="7a937-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="7a937-200">Bez atrybutów `[ApiController]` źródła atrybutów ani powiązań, takich `[FromQuery]`jak, środowisko uruchomieniowe ASP.NET Core próbuje użyć spinacza modelu obiektów złożonych.</span><span class="sxs-lookup"><span data-stu-id="7a937-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="7a937-201">Segregator modelu obiektów złożonych pobiera dane od dostawców wartości w zdefiniowanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="7a937-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="7a937-202">W poniższym przykładzie `[FromQuery]` atrybut wskazuje, że wartość `discontinuedOnly` parametru jest podana w ciągu zapytania adresu URL żądania:</span><span class="sxs-lookup"><span data-stu-id="7a937-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="7a937-203">Ten `[ApiController]` atrybut stosuje reguły wnioskowania dla domyślnych źródeł danych parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="7a937-204">Te reguły zapisują nie trzeba ręcznie identyfikować źródeł powiązań przez zastosowanie atrybutów do parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="7a937-205">Reguły wnioskowania źródła powiązań zachowują się w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="7a937-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="7a937-206">`[FromBody]`jest wywnioskowany dla parametrów typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="7a937-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="7a937-207">Wyjątek od `[FromBody]` reguły wnioskowania jest dowolnego złożonego, wbudowanego typu z specjalnym znaczeniem, takim jak <xref:Microsoft.AspNetCore.Http.IFormCollection> i. <xref:System.Threading.CancellationToken></span><span class="sxs-lookup"><span data-stu-id="7a937-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="7a937-208">Kod wnioskowania źródła powiązania ignoruje te typy specjalne.</span><span class="sxs-lookup"><span data-stu-id="7a937-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="7a937-209">`[FromForm]`jest wywnioskowany dla parametrów akcji typu <xref:Microsoft.AspNetCore.Http.IFormFile> i. <xref:Microsoft.AspNetCore.Http.IFormFileCollection></span><span class="sxs-lookup"><span data-stu-id="7a937-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="7a937-210">Nie jest wywnioskowane dla żadnego prostego lub zdefiniowanego przez użytkownika typu.</span><span class="sxs-lookup"><span data-stu-id="7a937-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="7a937-211">`[FromRoute]`jest wywnioskowany dla każdej nazwy parametru akcji pasującej do parametru w szablonie trasy.</span><span class="sxs-lookup"><span data-stu-id="7a937-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="7a937-212">W przypadku, gdy więcej niż jedna trasa pasuje do parametru akcji, uwzględniana `[FromRoute]`jest jakakolwiek wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="7a937-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="7a937-213">`[FromQuery]`jest wywnioskowany dla wszystkich innych parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="7a937-214">FromBody informacje o wnioskach</span><span class="sxs-lookup"><span data-stu-id="7a937-214">FromBody inference notes</span></span>

<span data-ttu-id="7a937-215">`[FromBody]`nie jest wywnioskowane dla typów prostych, `string` takich `int`jak lub.</span><span class="sxs-lookup"><span data-stu-id="7a937-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="7a937-216">W związku z `[FromBody]` tym atrybut powinien być używany dla typów prostych, gdy ta funkcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="7a937-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="7a937-217">Gdy akcja ma więcej niż jeden parametr powiązany z treścią żądania, zgłaszany jest wyjątek.</span><span class="sxs-lookup"><span data-stu-id="7a937-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="7a937-218">Na przykład, wszystkie następujące sygnatury metody akcji powodują wyjątek:</span><span class="sxs-lookup"><span data-stu-id="7a937-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="7a937-219">`[FromBody]`wywnioskowane na obu, ponieważ są to typy złożone.</span><span class="sxs-lookup"><span data-stu-id="7a937-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="7a937-220">`[FromBody]`atrybut na jeden, wywnioskowany na drugim, ponieważ jest typem złożonym.</span><span class="sxs-lookup"><span data-stu-id="7a937-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="7a937-221">`[FromBody]`atrybut na obu.</span><span class="sxs-lookup"><span data-stu-id="7a937-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="7a937-222">W ASP.NET Core 2,1 parametry typu kolekcji, takie jak listy i tablice, są nieprawidłowo wnioskowane jako `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="7a937-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="7a937-223">Ten `[FromBody]` atrybut powinien być używany dla tych parametrów, jeśli mają być powiązane z treścią żądania.</span><span class="sxs-lookup"><span data-stu-id="7a937-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="7a937-224">To zachowanie jest korygowane w ASP.NET Core 2,2 lub nowszych, gdzie parametry typu kolekcji są wyrzucane jako powiązane z treścią domyślnie.</span><span class="sxs-lookup"><span data-stu-id="7a937-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="7a937-225">Wyłącz reguły wnioskowania</span><span class="sxs-lookup"><span data-stu-id="7a937-225">Disable inference rules</span></span>

<span data-ttu-id="7a937-226">Aby wyłączyć wnioskowanie źródeł powiązań, ustaw <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> wartość `true`.</span><span class="sxs-lookup"><span data-stu-id="7a937-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="7a937-227">Dodaj następujący kod w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a937-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="7a937-228">Wieloczęściowe/formularz-wnioskowanie dotyczące danych</span><span class="sxs-lookup"><span data-stu-id="7a937-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="7a937-229">Ten `[ApiController]` atrybut stosuje regułę wnioskowania, gdy parametr akcji ma adnotację z [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="7a937-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="7a937-230">Typ `multipart/form-data` zawartości żądania jest wywnioskowany.</span><span class="sxs-lookup"><span data-stu-id="7a937-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="7a937-231">Aby wyłączyć domyślne zachowanie, ustaw <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> właściwość na `true` wartość w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="7a937-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="7a937-232">Szczegóły problemu dotyczące kodów stanu błędu</span><span class="sxs-lookup"><span data-stu-id="7a937-232">Problem details for error status codes</span></span>

<span data-ttu-id="7a937-233">Gdy wersja zgodności to 2,2 lub nowsza, MVC przeprowadzi wynik błędu (wynik z kodem stanu 400 lub nowszym) do wyniku z <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="7a937-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="7a937-234">`ProblemDetails` Typ jest oparty na [specyfikacji RFC 7807](https://tools.ietf.org/html/rfc7807) do udostępniania szczegółowych informacji o błędach w odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="7a937-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="7a937-235">Rozważmy następujący kod w akcji kontrolera:</span><span class="sxs-lookup"><span data-stu-id="7a937-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="7a937-236">`NotFound` Metoda generuje kod stanu HTTP 404 z `ProblemDetails` treścią.</span><span class="sxs-lookup"><span data-stu-id="7a937-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="7a937-237">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7a937-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="7a937-238">Wyłącz odpowiedź ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="7a937-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="7a937-239">Automatyczne tworzenie `ProblemDetails` kodów stanu błędu jest wyłączone, gdy <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> właściwość jest ustawiona na. `true`</span><span class="sxs-lookup"><span data-stu-id="7a937-239">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="7a937-240">Dodaj następujący kod w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a937-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="7a937-241">Zdefiniuj obsługiwane typy zawartości żądania z atrybutem [Requests]</span><span class="sxs-lookup"><span data-stu-id="7a937-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="7a937-242">Domyślnie akcja obsługuje wszystkie dostępne typy zawartości żądania.</span><span class="sxs-lookup"><span data-stu-id="7a937-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="7a937-243">Na przykład jeśli aplikacja jest skonfigurowana do obsługi [danych wejściowych](xref:mvc/models/model-binding#input-formatters)JSON i XML, Akcja obsługuje wiele typów zawartości, w tym `application/json` i. `application/xml`</span><span class="sxs-lookup"><span data-stu-id="7a937-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="7a937-244">Atrybut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) Requests] umożliwia akcja ograniczenia obsługiwanych typów zawartości żądania.</span><span class="sxs-lookup"><span data-stu-id="7a937-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="7a937-245">Zastosuj `[Consumes]` atrybut do akcji lub kontrolera, określając jeden lub więcej typów zawartości:</span><span class="sxs-lookup"><span data-stu-id="7a937-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="7a937-246">W poprzednim kodzie `CreateProduct` akcja określa typ `application/xml`zawartości.</span><span class="sxs-lookup"><span data-stu-id="7a937-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="7a937-247">Żądania kierowane do tej akcji muszą określać `Content-Type` nagłówek `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="7a937-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="7a937-248">Żądania, które nie określają `Content-Type` nagłówka `application/xml` wyniku w [nieobsługiwanej odpowiedzi typu nośnika 415](https://developer.mozilla.org/docs/Web/HTTP/Status/415) .</span><span class="sxs-lookup"><span data-stu-id="7a937-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="7a937-249">Ten `[Consumes]` atrybut umożliwia również akcję, która ma wpływ na wybór w oparciu o typ zawartości przychodzącego żądania przez zastosowanie ograniczenia typu.</span><span class="sxs-lookup"><span data-stu-id="7a937-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="7a937-250">Rozważmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="7a937-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="7a937-251">W poprzednim kodzie program `ConsumesController` jest skonfigurowany do obsługi żądań wysyłanych do `https://localhost:5001/api/Consumes` adresu URL.</span><span class="sxs-lookup"><span data-stu-id="7a937-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="7a937-252">Oba akcje kontrolera `PostJson` i `PostForm`obsługują żądania post z tym samym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="7a937-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="7a937-253">Bez `[Consumes]` atrybutu stosującego ograniczenie typu jest generowany niejednoznaczny wyjątek dopasowania.</span><span class="sxs-lookup"><span data-stu-id="7a937-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="7a937-254">Ten `[Consumes]` atrybut jest stosowany do obu akcji.</span><span class="sxs-lookup"><span data-stu-id="7a937-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="7a937-255">`PostJson` Akcja obsługuje żądania wysyłane z `Content-Type` nagłówkiem `application/json`.</span><span class="sxs-lookup"><span data-stu-id="7a937-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="7a937-256">`PostForm` Akcja obsługuje żądania wysyłane z `Content-Type` nagłówkiem `application/x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="7a937-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="7a937-257">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7a937-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
