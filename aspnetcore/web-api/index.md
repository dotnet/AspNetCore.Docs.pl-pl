---
title: Tworzenie interfejsów API sieci Web za pomocą ASP.NET Core
author: scottaddie
description: Poznaj podstawy tworzenia internetowego interfejsu API w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666006"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="eb4a3-103">Tworzenie interfejsów API sieci Web za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb4a3-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="eb4a3-104">Przez [Scott Addie](https://github.com/scottaddie) i [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="eb4a3-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="eb4a3-105">Platforma ASP.NET Core obsługuje tworzenie usług RESTful, znanych także jako internetowe interfejsy API, za pomocą języka C#.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="eb4a3-106">Do obsługi żądań internetowy interfejs API używa kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="eb4a3-107">*Kontrolery* w internetowym interfejsie API `ControllerBase`to klasy, które wynikają z programu .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="eb4a3-108">W tym artykule pokazano, jak używać kontrolerów do obsługi żądań interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="eb4a3-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="eb4a3-110">(Jak[pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="eb4a3-111">Klasa ControllerBase</span><span class="sxs-lookup"><span data-stu-id="eb4a3-111">ControllerBase class</span></span>

<span data-ttu-id="eb4a3-112">Internetowy interfejs API składa się z jednej <xref:Microsoft.AspNetCore.Mvc.ControllerBase>lub więcej klas kontrolerów, które wynikają z programu .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="eb4a3-113">Szablon projektu interfejsu API sieci web zapewnia kontroler startowy:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="eb4a3-114">Nie należy tworzyć kontrolera interfejsu API <xref:Microsoft.AspNetCore.Mvc.Controller> sieci web przez wyprowadzanie z klasy.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="eb4a3-115">`Controller`pochodzi z `ControllerBase` i dodaje obsługę widoków, więc jest do obsługi stron sieci web, a nie żądań interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="eb4a3-116">Istnieje wyjątek od tej reguły: jeśli planujesz używać tego samego kontrolera zarówno dla `Controller`widoków, jak i interfejsów API sieci Web, wydziel go z pliku .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="eb4a3-117">Klasa `ControllerBase` zawiera wiele właściwości i metod, które są przydatne do obsługi żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="eb4a3-118">Na przykład `ControllerBase.CreatedAtAction` zwraca kod stanu 201:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="eb4a3-119">Oto kilka przykładów metod, `ControllerBase` które zapewnia.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="eb4a3-120">Metoda</span><span class="sxs-lookup"><span data-stu-id="eb4a3-120">Method</span></span>   |<span data-ttu-id="eb4a3-121">Uwagi</span><span class="sxs-lookup"><span data-stu-id="eb4a3-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="eb4a3-122">Zwraca kod stanu 400.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="eb4a3-123">Zwraca kod stanu 404.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="eb4a3-124">Zwraca plik.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="eb4a3-125">Wywołuje [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="eb4a3-126">Wywołuje [sprawdzanie poprawności modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="eb4a3-127">Aby uzyskać listę wszystkich dostępnych metod <xref:Microsoft.AspNetCore.Mvc.ControllerBase>i właściwości, zobacz .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="eb4a3-128">Atrybuty</span><span class="sxs-lookup"><span data-stu-id="eb4a3-128">Attributes</span></span>

<span data-ttu-id="eb4a3-129">Obszar <xref:Microsoft.AspNetCore.Mvc> nazw zawiera atrybuty, które mogą służyć do konfigurowania zachowania kontrolerów interfejsu API sieci web i metod akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="eb4a3-130">W poniższym przykładzie użyto atrybutów do określenia obsługiwanego zlecenia akcji HTTP i wszelkich znanych kodów stanu HTTP, które mogą zostać zwrócone:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="eb4a3-131">Oto kilka przykładów atrybutów, które są dostępne.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="eb4a3-132">Atrybut</span><span class="sxs-lookup"><span data-stu-id="eb4a3-132">Attribute</span></span>|<span data-ttu-id="eb4a3-133">Uwagi</span><span class="sxs-lookup"><span data-stu-id="eb4a3-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="eb4a3-134">Określa wzorzec adresu URL dla kontrolera lub akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="eb4a3-135">Określa prefiks i właściwości do uwzględnienia dla powiązania modelu.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="eb4a3-136">Identyfikuje akcję, która obsługuje zlecenie akcji HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="eb4a3-137">Określa typy danych akceptowane przez akcję.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="eb4a3-138">Określa typy danych zwracanych przez akcję.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="eb4a3-139">Aby uzyskać listę zawierającą dostępne <xref:Microsoft.AspNetCore.Mvc> atrybuty, zobacz obszar nazw.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="eb4a3-140">Atrybut ApiController</span><span class="sxs-lookup"><span data-stu-id="eb4a3-140">ApiController attribute</span></span>

<span data-ttu-id="eb4a3-141">Atrybut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) można zastosować do klasy kontrolera, aby włączyć następujące opinionated, zachowania specyficzne dla interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="eb4a3-142">Wymaganie routingu atrybutów</span><span class="sxs-lookup"><span data-stu-id="eb4a3-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="eb4a3-143">Automatyczne odpowiedzi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="eb4a3-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="eb4a3-144">Wnioskowanie o parametr źródła wiązania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="eb4a3-145">Wnioskowanie z żądaniami wieloczęściowymi/formularzowymi danymi</span><span class="sxs-lookup"><span data-stu-id="eb4a3-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="eb4a3-146">Szczegóły problemu dla kodów stanu błędów</span><span class="sxs-lookup"><span data-stu-id="eb4a3-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="eb4a3-147">Funkcja *Kody stanu błędu* wymaga wersji zgodności 2.2 lub nowszej. [compatibility version](xref:mvc/compatibility-version)</span><span class="sxs-lookup"><span data-stu-id="eb4a3-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="eb4a3-148">Inne funkcje wymagają wersji zgodności 2.1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="eb4a3-149">Wymaganie routingu atrybutów</span><span class="sxs-lookup"><span data-stu-id="eb4a3-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="eb4a3-150">Automatyczne odpowiedzi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="eb4a3-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="eb4a3-151">Wnioskowanie o parametr źródła wiązania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="eb4a3-152">Wnioskowanie z żądaniami wieloczęściowymi/formularzowymi danymi</span><span class="sxs-lookup"><span data-stu-id="eb4a3-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="eb4a3-153">Te funkcje wymagają [wersji zgodności](xref:mvc/compatibility-version) 2.1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="eb4a3-154">Atrybut na określonych kontrolerach</span><span class="sxs-lookup"><span data-stu-id="eb4a3-154">Attribute on specific controllers</span></span>

<span data-ttu-id="eb4a3-155">Atrybut `[ApiController]` można zastosować do określonych kontrolerów, jak w poniższym przykładzie z szablonu projektu:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="eb4a3-156">Atrybut na wielu kontrolerach</span><span class="sxs-lookup"><span data-stu-id="eb4a3-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="eb4a3-157">Jednym z podejść do używania atrybutu na więcej niż jednym kontrolerze `[ApiController]` jest utworzenie niestandardowej klasy kontrolera podstawowego z adnotacją o tym atrybutem.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="eb4a3-158">W poniższym przykładzie przedstawiono niestandardową klasę podstawową i kontroler, który pochodzi od niej:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="eb4a3-159">Atrybut w zestawie</span><span class="sxs-lookup"><span data-stu-id="eb4a3-159">Attribute on an assembly</span></span>

<span data-ttu-id="eb4a3-160">Jeśli wersja zgodności jest [ustawiona](xref:mvc/compatibility-version) na 2.2 lub nowszą, `[ApiController]` atrybut można zastosować do zestawu.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="eb4a3-161">Adnotacja w ten sposób stosuje zachowanie interfejsu API sieci web do wszystkich kontrolerów w zestawie.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="eb4a3-162">Nie ma możliwości rezygnacji poszczególnych administratorów.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="eb4a3-163">Zastosuj atrybut na poziomie zestawu do deklaracji `Startup` obszaru nazw otaczającej klasę:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="eb4a3-164">Wymaganie routingu atrybutów</span><span class="sxs-lookup"><span data-stu-id="eb4a3-164">Attribute routing requirement</span></span>

<span data-ttu-id="eb4a3-165">Atrybut `[ApiController]` sprawia, że routing atrybutu wymaganie.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="eb4a3-166">Przykład:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="eb4a3-167">Działania są niedostępne za pośrednictwem [konwencjonalnych tras](xref:mvc/controllers/routing#conventional-routing) <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> określonych `Startup.Configure`przez `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>lub w .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="eb4a3-168">Działania są niedostępne za pośrednictwem [konwencjonalnych](xref:mvc/controllers/routing#conventional-routing) <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> tras `Startup.Configure`określonych przez <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> lub w .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="eb4a3-169">Automatyczne odpowiedzi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="eb4a3-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="eb4a3-170">Atrybut `[ApiController]` sprawia, że błędy sprawdzania poprawności modelu automatycznie wyzwalają odpowiedź HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="eb4a3-171">W związku z tym następujący kod jest zbędne w metodzie akcji:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="eb4a3-172">ASP.NET Core MVC używa filtru <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> akcji do wykonania poprzedniego sprawdzenia.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="eb4a3-173">Domyślna odpowiedź BadRequest</span><span class="sxs-lookup"><span data-stu-id="eb4a3-173">Default BadRequest response</span></span>

<span data-ttu-id="eb4a3-174">W wersji zgodności 2.1 domyślnym typem odpowiedzi dla odpowiedzi HTTP <xref:Microsoft.AspNetCore.Mvc.SerializableError>400 jest .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="eb4a3-175">Następująca treść żądania jest przykładem typu serializowanego:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="eb4a3-176">W przypadku wersji zgodności 2.2 lub nowszej domyślnym typem odpowiedzi <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>dla odpowiedzi HTTP 400 jest .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="eb4a3-177">Następująca treść żądania jest przykładem typu serializowanego:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-177">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="eb4a3-178">Typ: `ValidationProblemDetails`</span><span class="sxs-lookup"><span data-stu-id="eb4a3-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="eb4a3-179">Zapewnia format do odczytu maszynowego do określania błędów w odpowiedziach interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="eb4a3-180">Zgodność ze [specyfikacją RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="eb4a3-181">Rejestrowanie automatycznych odpowiedzi 400</span><span class="sxs-lookup"><span data-stu-id="eb4a3-181">Log automatic 400 responses</span></span>

<span data-ttu-id="eb4a3-182">Zobacz [Jak rejestrować automatyczne 400 odpowiedzi na błędy sprawdzania poprawności modelu (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="eb4a3-183">Wyłącz automatyczną odpowiedź 400</span><span class="sxs-lookup"><span data-stu-id="eb4a3-183">Disable automatic 400 response</span></span>

<span data-ttu-id="eb4a3-184">Aby wyłączyć automatyczne zachowanie 400, `true`ustaw właściwość na <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="eb4a3-185">Dodaj następujący wyróżniony `Startup.ConfigureServices`kod w :</span><span class="sxs-lookup"><span data-stu-id="eb4a3-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="eb4a3-186">Wnioskowanie o parametr źródła wiązania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-186">Binding source parameter inference</span></span>

<span data-ttu-id="eb4a3-187">Atrybut źródła powiązania definiuje lokalizację, w której znajduje się wartość parametru akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="eb4a3-188">Istnieją następujące atrybuty źródła powiązania:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="eb4a3-189">Atrybut</span><span class="sxs-lookup"><span data-stu-id="eb4a3-189">Attribute</span></span>|<span data-ttu-id="eb4a3-190">Źródło wiązania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="eb4a3-191">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="eb4a3-192">Dane formularza w treści żądania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="eb4a3-193">Nagłówek żądania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="eb4a3-194">Zażądaj parametru ciągu kwerendy</span><span class="sxs-lookup"><span data-stu-id="eb4a3-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="eb4a3-195">Rozsyłanie danych z bieżącego żądania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="eb4a3-196">Usługa żądania wstrzyknięta jako parametr akcji</span><span class="sxs-lookup"><span data-stu-id="eb4a3-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="eb4a3-197">Nie używaj, `[FromRoute]` gdy `%2f` wartości mogą `/`zawierać (czyli).</span><span class="sxs-lookup"><span data-stu-id="eb4a3-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="eb4a3-198">`%2f`nie będzie unescaped `/`do .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="eb4a3-199">Użyj, `[FromQuery]` jeśli wartość `%2f`może zawierać .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="eb4a3-200">Bez `[ApiController]` atrybutów źródła atrybutów `[FromQuery]`atrybutów, takich jak , ASP.NET Core runtime próbuje użyć spinacza modelu obiektów złożonych.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="eb4a3-201">Spinacz złożonego modelu obiektu pobiera dane od dostawców wartości w zdefiniowanej kolejności.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="eb4a3-202">W poniższym przykładzie `[FromQuery]` atrybut wskazuje, `discontinuedOnly` że wartość parametru znajduje się w ciągu zapytania adresu URL żądania:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="eb4a3-203">Atrybut `[ApiController]` stosuje reguły wnioskowania dla domyślnych źródeł danych parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="eb4a3-204">Te reguły pozwalają zaoszczędzić od konieczności ręcznego identyfikowania źródeł wiązania przez zastosowanie atrybutów do parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="eb4a3-205">Reguły wnioskowania źródła wiązania zachowują się w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="eb4a3-206">`[FromBody]`jest wywnioskowany dla złożonych parametrów typu.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="eb4a3-207">Wyjątkiem od `[FromBody]` reguły wnioskowania jest dowolny złożony, wbudowany typ <xref:Microsoft.AspNetCore.Http.IFormCollection> o <xref:System.Threading.CancellationToken>specjalnym znaczeniu, takim jak i .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="eb4a3-208">Kod wnioskowania źródła powiązania ignoruje te typy specjalne.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="eb4a3-209">`[FromForm]`wywnioskować parametry <xref:Microsoft.AspNetCore.Http.IFormFile> akcji <xref:Microsoft.AspNetCore.Http.IFormFileCollection>typu i .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="eb4a3-210">Nie jest wywnioskowane dla żadnych typów prostych lub zdefiniowanych przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="eb4a3-211">`[FromRoute]`jest wywnioskowany dla dowolnej nazwy parametru akcji pasującej do parametru w szablonie trasy.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="eb4a3-212">Gdy więcej niż jedna trasa pasuje do parametru akcji, uwzględniana `[FromRoute]`jest dowolna wartość trasy.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="eb4a3-213">`[FromQuery]`wywnioskować dla innych parametrów akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="eb4a3-214">Notatki wnioskowania FromBody</span><span class="sxs-lookup"><span data-stu-id="eb4a3-214">FromBody inference notes</span></span>

<span data-ttu-id="eb4a3-215">`[FromBody]`nie jest wywnioskowany `string` dla `int`prostych typów, takich jak lub .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="eb4a3-216">W związku `[FromBody]` z tym atrybut powinien być używany dla typów prostych, gdy ta funkcja jest potrzebna.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="eb4a3-217">Gdy akcja ma więcej niż jeden parametr związany z treści żądania, wyjątek.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="eb4a3-218">Na przykład wszystkie następujące podpisy metody akcji powodują wyjątek:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="eb4a3-219">`[FromBody]`wywnioskować na obu, ponieważ są one złożone typy.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="eb4a3-220">`[FromBody]`atrybut na jednym, wywnioskować na drugim, ponieważ jest to typ złożony.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="eb4a3-221">`[FromBody]`atrybutu na obu.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="eb4a3-222">W ASP.NET Core 2.1 parametry typu kolekcji, takie jak listy i `[FromQuery]`tablice, są niepoprawnie wywnioskowane jako .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="eb4a3-223">Atrybut `[FromBody]` powinien być używany dla tych parametrów, jeśli mają być powiązane z treści żądania.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="eb4a3-224">To zachowanie jest korygowane w ASP.NET Core 2.2 lub nowszym, gdzie parametry typu kolekcji są domyślnie powiązane z treścią.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="eb4a3-225">Wyłączanie reguł wnioskowania</span><span class="sxs-lookup"><span data-stu-id="eb4a3-225">Disable inference rules</span></span>

<span data-ttu-id="eb4a3-226">Aby wyłączyć wnioskowanie o <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> `true`źródle wiązania, ustaw na .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="eb4a3-227">Dodaj następujący kod `Startup.ConfigureServices`w :</span><span class="sxs-lookup"><span data-stu-id="eb4a3-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="eb4a3-228">Wnioskowanie z żądaniami wieloczęściowymi/formularzowymi danymi</span><span class="sxs-lookup"><span data-stu-id="eb4a3-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="eb4a3-229">Atrybut `[ApiController]` stosuje regułę wnioskowania, gdy parametr akcji jest [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) opisywany z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="eb4a3-230">Typ `multipart/form-data` zawartości żądania jest wywnioskowany.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="eb4a3-231">Aby wyłączyć zachowanie domyślne, `true` ustaw `Startup.ConfigureServices`właściwość na: <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters></span><span class="sxs-lookup"><span data-stu-id="eb4a3-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

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

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="eb4a3-232">Szczegóły problemu dla kodów stanu błędów</span><span class="sxs-lookup"><span data-stu-id="eb4a3-232">Problem details for error status codes</span></span>

<span data-ttu-id="eb4a3-233">Gdy wersja zgodności wynosi 2.2 lub nowsze, MVC przekształca wynik błędu (wynik o kodzie stanu <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>400 lub wyższym) na wynik z .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="eb4a3-234">Typ `ProblemDetails` jest oparty na [specyfikacji RFC 7807](https://tools.ietf.org/html/rfc7807) do dostarczania szczegółów błędu do odczytu maszynowego w odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="eb4a3-235">Należy wziąć pod uwagę następujący kod w akcji kontrolera:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="eb4a3-236">Metoda `NotFound` tworzy kod stanu HTTP 404 `ProblemDetails` z treścią.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="eb4a3-237">Przykład:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="eb4a3-238">Wyłącz odpowiedź ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="eb4a3-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="eb4a3-239">Automatyczne tworzenie kodów stanu błędu `ProblemDetails` dla <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> jest wyłączone, `true`gdy właściwość jest ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-239">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="eb4a3-240">Dodaj następujący kod `Startup.ConfigureServices`w :</span><span class="sxs-lookup"><span data-stu-id="eb4a3-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="eb4a3-241">Definiowanie obsługiwanych typów zawartości żądań za pomocą atrybutu [Zużywa]</span><span class="sxs-lookup"><span data-stu-id="eb4a3-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="eb4a3-242">Domyślnie akcja obsługuje wszystkie dostępne typy zawartości żądania.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="eb4a3-243">Jeśli na przykład aplikacja jest skonfigurowana do obsługi [formaterów wejściowych](xref:mvc/models/model-binding#input-formatters)JSON i XML, akcja obsługuje wiele typów zawartości, w tym `application/json` i `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="eb4a3-244">[Atrybut [Zużywa]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umożliwia akcję, aby ograniczyć typy zawartości obsługiwanych żądań.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="eb4a3-245">Zastosuj `[Consumes]` atrybut do akcji lub kontrolera, określając jeden lub więcej typów zawartości:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="eb4a3-246">W poprzednim kodzie `CreateProduct` akcja określa typ `application/xml`zawartości .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="eb4a3-247">Żądania kierowane do tej `Content-Type` akcji `application/xml`muszą określać nagłówek .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="eb4a3-248">Żądania, które nie `Content-Type` określają `application/xml` nagłówka wyniku w [415 nieobsługiwał](https://developer.mozilla.org/docs/Web/HTTP/Status/415) typ nośnika odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="eb4a3-249">Atrybut `[Consumes]` umożliwia również akcję, aby wpłynąć na jego wybór na podstawie typu zawartości żądania przychodzącego przez zastosowanie ograniczenia typu.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="eb4a3-250">Rozważmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="eb4a3-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="eb4a3-251">W poprzednim kodzie `ConsumesController` jest skonfigurowany do obsługi `https://localhost:5001/api/Consumes` żądań wysyłanych do adresu URL.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="eb4a3-252">Obie akcje kontrolera i `PostJson` `PostForm`, obsługiwać żądania POST z tym samym adresem URL.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="eb4a3-253">Bez `[Consumes]` atrybutu stosowania ograniczenia typu, niejednoznaczny wyjątek dopasowania jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="eb4a3-254">Atrybut `[Consumes]` jest stosowany do obu akcji.</span><span class="sxs-lookup"><span data-stu-id="eb4a3-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="eb4a3-255">Akcja `PostJson` obsługuje żądania wysyłane `Content-Type` z `application/json`nagłówkiem .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="eb4a3-256">Akcja `PostForm` obsługuje żądania wysyłane `Content-Type` z `application/x-www-form-urlencoded`nagłówkiem .</span><span class="sxs-lookup"><span data-stu-id="eb4a3-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="eb4a3-257">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="eb4a3-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
