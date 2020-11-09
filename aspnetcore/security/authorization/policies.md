---
title: Autoryzacja oparta na zasadach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć i używać programów obsługi zasad autoryzacji w celu wymuszania wymagań autoryzacji w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/policies
ms.openlocfilehash: 286dc3bcc66b86a2a6b7d3cb7b6052bf7b474aff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060212"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="0e46d-103">Autoryzacja oparta na zasadach w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e46d-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e46d-104">Zgodnie z założeniami, [Autoryzacja oparta na rolach](xref:security/authorization/roles) i [Autoryzacja oparta na oświadczeniach](xref:security/authorization/claims) używają wymagań, obsługi wymagań i wstępnie skonfigurowanych zasad.</span><span class="sxs-lookup"><span data-stu-id="0e46d-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="0e46d-105">Te bloki konstrukcyjne obsługują wyrażenie oceny autoryzacji w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="0e46d-106">Wynikiem jest rozbudowana struktura autoryzacji do wielokrotnego użytku weryfikowalne.</span><span class="sxs-lookup"><span data-stu-id="0e46d-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="0e46d-107">Zasady autoryzacji składają się z co najmniej jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="0e46d-108">Jest ona zarejestrowana w ramach konfiguracji usługi autoryzacji w ramach `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="0e46d-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="0e46d-109">W poprzednim przykładzie tworzone są zasady "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="0e46d-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="0e46d-110">Ma jedno wymaganie &mdash; o minimalnym wieku, które jest dostarczane jako parametr do wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="0e46d-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="0e46d-111">IAuthorizationService</span></span>

<span data-ttu-id="0e46d-112">Podstawowa usługa, która określa, czy autoryzacja powiodła się <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="0e46d-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="0e46d-113">Poprzedni kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="0e46d-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="0e46d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> to usługa znacznika bez metod i mechanizm śledzenia, czy autoryzacja zakończyła się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="0e46d-115">Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> jest odpowiedzialny za sprawdzenie, czy są spełnione wymagania:</span><span class="sxs-lookup"><span data-stu-id="0e46d-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="0e46d-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Klasa jest wykorzystywana przez program obsługi do oznaczania, czy zostały spełnione wymagania:</span><span class="sxs-lookup"><span data-stu-id="0e46d-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="0e46d-117">Poniższy kod przedstawia uproszczoną (i adnotację z komentarzami) domyślną implementację usługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="0e46d-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="0e46d-118">Poniższy kod przedstawia typowy `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0e46d-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="0e46d-119">Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> lub `[Authorize(Policy = "Something")]` do autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="0e46d-120">Stosowanie zasad do kontrolerów MVC</span><span class="sxs-lookup"><span data-stu-id="0e46d-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="0e46d-121">Jeśli używasz Razor stron, zobacz [stosowanie zasad do Razor stron](#apply-policies-to-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="0e46d-122">Zasady są stosowane do kontrolerów przy użyciu `[Authorize]` atrybutu z nazwą zasad.</span><span class="sxs-lookup"><span data-stu-id="0e46d-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0e46d-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="0e46d-124">Stosowanie zasad do Razor stron</span><span class="sxs-lookup"><span data-stu-id="0e46d-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="0e46d-125">Zasady są stosowane do Razor stron przy użyciu `[Authorize]` atrybutu z nazwą zasad.</span><span class="sxs-lookup"><span data-stu-id="0e46d-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0e46d-126">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="0e46d-127">Zasady **nie** mogą \* być stosowane na Razor poziomie obsługi strony, muszą być stosowane do strony.</span><span class="sxs-lookup"><span data-stu-id="0e46d-127">Policies can \* **not** _ be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="0e46d-128">Zasady mogą być stosowane do Razor stron przy użyciu [Konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="0e46d-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="0e46d-129">Wymagania</span><span class="sxs-lookup"><span data-stu-id="0e46d-129">Requirements</span></span>

<span data-ttu-id="0e46d-130">Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą użyć do oszacowania bieżącego podmiotu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e46d-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="0e46d-131">W naszych zasadach "AtLeast21" wymagany jest jeden parametr &mdash; o minimalnym wieku.</span><span class="sxs-lookup"><span data-stu-id="0e46d-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="0e46d-132">Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pustym interfejsem znacznika.</span><span class="sxs-lookup"><span data-stu-id="0e46d-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="0e46d-133">Minimalny wymóg sparametryzowanego wieku można zaimplementować w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0e46d-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="0e46d-134">Jeśli zasady autoryzacji zawierają wiele wymagań autoryzacji, wszystkie wymagania muszą zostać spełnione, aby Ocena zasad powiodła się.</span><span class="sxs-lookup"><span data-stu-id="0e46d-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="0e46d-135">Innymi słowy, wiele wymagań autoryzacji dodanych do pojedynczych zasad autoryzacji jest traktowanych na podstawie _ *i* \*.</span><span class="sxs-lookup"><span data-stu-id="0e46d-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an _ *AND* \* basis.</span></span>

> [!NOTE]
> <span data-ttu-id="0e46d-136">Wymagania nie muszą mieć danych ani właściwości.</span><span class="sxs-lookup"><span data-stu-id="0e46d-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="0e46d-137">Programy obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0e46d-137">Authorization handlers</span></span>

<span data-ttu-id="0e46d-138">Procedura obsługi autoryzacji jest odpowiedzialna za ocenę właściwości wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="0e46d-139">Procedura obsługi autoryzacji szacuje wymagania w odniesieniu do podanej [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) w celu określenia, czy dostęp jest dozwolony.</span><span class="sxs-lookup"><span data-stu-id="0e46d-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="0e46d-140">Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="0e46d-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="0e46d-141">Program obsługi może odziedziczyć [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), gdzie `TRequirement` jest wymaganie do obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="0e46d-142">Alternatywnie program obsługi może zaimplementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) , aby obsłużyć więcej niż jeden typ wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="0e46d-143">Użyj procedury obsługi dla jednego wymagania</span><span class="sxs-lookup"><span data-stu-id="0e46d-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="0e46d-144">Poniżej znajduje się przykład relacji jeden-do-jednego, w której program obsługi minimalnych okresów używa jednego wymagania:</span><span class="sxs-lookup"><span data-stu-id="0e46d-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="0e46d-145">Poprzedni kod określa, czy bieżący podmiot użytkownika ma datę żądania urodzenia, który został wystawiony przez znanego i zaufanego wystawcy.</span><span class="sxs-lookup"><span data-stu-id="0e46d-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="0e46d-146">Autoryzacja nie może wystąpić w przypadku braku żądania. w takim przypadku zwracane jest zadanie wykonane.</span><span class="sxs-lookup"><span data-stu-id="0e46d-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="0e46d-147">W przypadku wystąpienia zgłoszenia jest naliczany wiek użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e46d-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="0e46d-148">Jeśli użytkownik spełni minimalny wiek zdefiniowany przez to wymaganie, autoryzacja zostanie uznana za pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="0e46d-149">Gdy autoryzacja powiedzie się, `context.Succeed` jest wywoływana z wymaganym parametrem.</span><span class="sxs-lookup"><span data-stu-id="0e46d-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="0e46d-150">Korzystanie z programu obsługi dla wielu wymagań</span><span class="sxs-lookup"><span data-stu-id="0e46d-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="0e46d-151">Poniżej znajduje się przykład relacji jeden-do-wielu, w której program obsługi uprawnień może obsłużyć trzy różne typy wymagań:</span><span class="sxs-lookup"><span data-stu-id="0e46d-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="0e46d-152">Poprzedni kod przechodzi przez [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; Właściwości zawierającej wymagania, które nie są oznaczone jako pomyślne.</span><span class="sxs-lookup"><span data-stu-id="0e46d-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="0e46d-153">Aby uzyskać `ReadPermission` dostęp do żądanego zasobu, użytkownik musi być właścicielem lub sponsorem.</span><span class="sxs-lookup"><span data-stu-id="0e46d-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="0e46d-154">W przypadku `EditPermission` lub `DeletePermission` wymagania musi być właścicielem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="0e46d-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="0e46d-155">Rejestracja procedury obsługi</span><span class="sxs-lookup"><span data-stu-id="0e46d-155">Handler registration</span></span>

<span data-ttu-id="0e46d-156">Procedury obsługi są rejestrowane w kolekcji usług podczas konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="0e46d-157">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="0e46d-158">Poprzedni kod rejestruje `MinimumAgeHandler` jako pojedyncze przez wywołanie `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="0e46d-159">Programy obsługi można zarejestrować przy użyciu dowolnego z wbudowanych [okresów istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0e46d-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="0e46d-160">Co ma zwrócić program obsługi?</span><span class="sxs-lookup"><span data-stu-id="0e46d-160">What should a handler return?</span></span>

<span data-ttu-id="0e46d-161">Należy zauważyć, że `Handle` Metoda w [przykładzie procedury obsługi](#security-authorization-handler-example) nie zwraca żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="0e46d-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="0e46d-162">Jak jest wskazywany stan sukcesu lub niepowodzenia?</span><span class="sxs-lookup"><span data-stu-id="0e46d-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="0e46d-163">Procedura obsługi wskazuje powodzenie przez wywołanie `context.Succeed(IAuthorizationRequirement requirement)` , przekazanie wymagania, które zostało pomyślnie zweryfikowane.</span><span class="sxs-lookup"><span data-stu-id="0e46d-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="0e46d-164">Program obsługi nie musi ogólnie obsługiwać błędów, ponieważ inne procedury obsługi tego samego wymagania mogą się powieść.</span><span class="sxs-lookup"><span data-stu-id="0e46d-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="0e46d-165">W celu zagwarantowania niepowodzenia, nawet w przypadku pomyślnego zajścia innych wymagań `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="0e46d-166">Jeśli program obsługi wywołuje `context.Succeed` lub `context.Fail` , wszystkie inne procedury obsługi są nadal wywoływane.</span><span class="sxs-lookup"><span data-stu-id="0e46d-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="0e46d-167">Pozwala to na spełnienie wymagań związanych z generowaniem efektów ubocznych, takich jak rejestrowanie, które odbywa się nawet w przypadku pomyślnej weryfikacji lub niepowodzenia przez inną procedurę obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="0e46d-168">Gdy jest ustawiona na `false` , właściwość [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) jest krótka, gdy `context.Fail` zostanie wywołane wykonywanie programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="0e46d-169">`InvokeHandlersAfterFailure` wartość domyślna to `true` , w którym to przypadku są wywoływane wszystkie programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="0e46d-170">Procedury obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="0e46d-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="0e46d-171">Dlaczego chcesz mieć wiele programów obsługi wymagań?</span><span class="sxs-lookup"><span data-stu-id="0e46d-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="0e46d-172">W przypadkach, w których Ocena ma być przeprowadzana na podstawie **lub** , należy zaimplementować wiele programów obsługi dla jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="0e46d-173">Na przykład firma Microsoft ma drzwi, które są otwierane tylko za pomocą kart kluczowych.</span><span class="sxs-lookup"><span data-stu-id="0e46d-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="0e46d-174">Jeśli opuścisz kartę kluczową w domu, recepcjonista drukuje tymczasowy naklejkę i otwiera drzwiczki.</span><span class="sxs-lookup"><span data-stu-id="0e46d-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="0e46d-175">W tym scenariuszu istnieje jedno wymaganie, *BuildingEntry* , ale wiele programów obsługi, każdy z nich bada pojedyncze wymaganie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-175">In this scenario, you'd have a single requirement, *BuildingEntry* , but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="0e46d-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="0e46d-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="0e46d-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0e46d-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="0e46d-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0e46d-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="0e46d-179">Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="0e46d-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="0e46d-180">Jeśli jedna z programów obsługi powiedzie się, gdy zasady oceniają `BuildingEntryRequirement` , Ocena zasad zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="0e46d-181">Używanie funkcji Func do realizacji zasad</span><span class="sxs-lookup"><span data-stu-id="0e46d-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="0e46d-182">Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażania w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="0e46d-183">`Func<AuthorizationHandlerContext, bool>`Podczas konfigurowania zasad przy użyciu konstruktora zasad można podać wartość `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="0e46d-184">Na przykład poprzedni można `BadgeEntryHandler` napisać ponownie w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0e46d-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="0e46d-185">Dostęp do kontekstu żądania MVC w programach obsługi</span><span class="sxs-lookup"><span data-stu-id="0e46d-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="0e46d-186">`HandleRequirementAsync`Metoda zaimplementowana w procedurze obsługi autoryzacji ma dwa parametry: `AuthorizationHandlerContext` a i `TRequirement` obsługujące.</span><span class="sxs-lookup"><span data-stu-id="0e46d-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="0e46d-187">Platformy, takie jak MVC lub, SignalR mogą dodawać dowolne obiekty do `Resource` właściwości w `AuthorizationHandlerContext` celu przekazania dodatkowych informacji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="0e46d-188">W przypadku korzystania z routingu punktów końcowych Autoryzacja jest zwykle obsługiwana przez oprogramowanie pośredniczące autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="0e46d-189">W tym przypadku `Resource` Właściwość jest wystąpieniem <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="0e46d-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="0e46d-190">Punkt końcowy może służyć do sondowania bazowego zasobu, z którym jest przeprowadzana Routing.</span><span class="sxs-lookup"><span data-stu-id="0e46d-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="0e46d-191">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="0e46d-192">Punkt końcowy nie zapewnia dostępu do bieżącego `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="0e46d-193">W przypadku korzystania z routingu punktów końcowych Użyj programu `IHttpContextAcessor` w celu uzyskania dostępu do programu `HttpContext` obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="0e46d-194">Aby uzyskać więcej informacji, zobacz [Korzystanie z elementu HttpContext ze składników niestandardowych](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="0e46d-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="0e46d-195">Przy użyciu tradycyjnego routingu lub gdy autoryzacja występuje w ramach filtru autoryzacji MVC, wartość `Resource` jest <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> wystąpieniem.</span><span class="sxs-lookup"><span data-stu-id="0e46d-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="0e46d-196">Ta właściwość zapewnia dostęp do `HttpContext` `RouteData` elementów, i innych udostępnianych przez MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e46d-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="0e46d-197">Użycie `Resource` właściwości jest specyficzne dla struktury.</span><span class="sxs-lookup"><span data-stu-id="0e46d-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="0e46d-198">Użycie informacji we `Resource` Właściwości ogranicza zasady autoryzacji do określonych struktur.</span><span class="sxs-lookup"><span data-stu-id="0e46d-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="0e46d-199">Należy rzutować `Resource` Właściwość za pomocą `is` słowa kluczowego, a następnie potwierdzić, że rzutowanie zakończyło się pomyślnie, aby upewnić się, że kod nie ulegnie awarii z `InvalidCastException` uruchomieniem w innych strukturach:</span><span class="sxs-lookup"><span data-stu-id="0e46d-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="0e46d-200">Globalnie Wymagaj uwierzytelnienia wszystkich użytkowników</span><span class="sxs-lookup"><span data-stu-id="0e46d-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e46d-201">Zgodnie z założeniami, [Autoryzacja oparta na rolach](xref:security/authorization/roles) i [Autoryzacja oparta na oświadczeniach](xref:security/authorization/claims) używają wymagań, obsługi wymagań i wstępnie skonfigurowanych zasad.</span><span class="sxs-lookup"><span data-stu-id="0e46d-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="0e46d-202">Te bloki konstrukcyjne obsługują wyrażenie oceny autoryzacji w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="0e46d-203">Wynikiem jest rozbudowana struktura autoryzacji do wielokrotnego użytku weryfikowalne.</span><span class="sxs-lookup"><span data-stu-id="0e46d-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="0e46d-204">Zasady autoryzacji składają się z co najmniej jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="0e46d-205">Jest ona zarejestrowana w ramach konfiguracji usługi autoryzacji w ramach `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="0e46d-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="0e46d-206">W poprzednim przykładzie tworzone są zasady "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="0e46d-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="0e46d-207">Ma jedno wymaganie &mdash; o minimalnym wieku, które jest dostarczane jako parametr do wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="0e46d-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="0e46d-208">IAuthorizationService</span></span> 

<span data-ttu-id="0e46d-209">Podstawowa usługa, która określa, czy autoryzacja powiodła się <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="0e46d-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="0e46d-210">Poprzedni kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="0e46d-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="0e46d-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> to usługa znacznika bez metod i mechanizm śledzenia, czy autoryzacja zakończyła się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="0e46d-212">Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> jest odpowiedzialny za sprawdzenie, czy są spełnione wymagania:</span><span class="sxs-lookup"><span data-stu-id="0e46d-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="0e46d-213"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Klasa jest wykorzystywana przez program obsługi do oznaczania, czy zostały spełnione wymagania:</span><span class="sxs-lookup"><span data-stu-id="0e46d-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="0e46d-214">Poniższy kod przedstawia uproszczoną (i adnotację z komentarzami) domyślną implementację usługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="0e46d-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="0e46d-215">Poniższy kod przedstawia typowy `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0e46d-215">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="0e46d-216">Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> lub `[Authorize(Policy = "Something")]` do autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="0e46d-217">Stosowanie zasad do kontrolerów MVC</span><span class="sxs-lookup"><span data-stu-id="0e46d-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="0e46d-218">Jeśli używasz Razor stron, zobacz [stosowanie zasad do Razor stron](#apply-policies-to-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="0e46d-219">Zasady są stosowane do kontrolerów przy użyciu `[Authorize]` atrybutu z nazwą zasad.</span><span class="sxs-lookup"><span data-stu-id="0e46d-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0e46d-220">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="0e46d-221">Stosowanie zasad do Razor stron</span><span class="sxs-lookup"><span data-stu-id="0e46d-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="0e46d-222">Zasady są stosowane do Razor stron przy użyciu `[Authorize]` atrybutu z nazwą zasad.</span><span class="sxs-lookup"><span data-stu-id="0e46d-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0e46d-223">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="0e46d-224">Zasady mogą być również stosowane do Razor stron przy użyciu [Konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="0e46d-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="0e46d-225">Wymagania</span><span class="sxs-lookup"><span data-stu-id="0e46d-225">Requirements</span></span>

<span data-ttu-id="0e46d-226">Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą użyć do oszacowania bieżącego podmiotu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e46d-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="0e46d-227">W naszych zasadach "AtLeast21" wymagany jest jeden parametr &mdash; o minimalnym wieku.</span><span class="sxs-lookup"><span data-stu-id="0e46d-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="0e46d-228">Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pustym interfejsem znacznika.</span><span class="sxs-lookup"><span data-stu-id="0e46d-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="0e46d-229">Minimalny wymóg sparametryzowanego wieku można zaimplementować w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0e46d-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="0e46d-230">Jeśli zasady autoryzacji zawierają wiele wymagań autoryzacji, wszystkie wymagania muszą zostać spełnione, aby Ocena zasad powiodła się.</span><span class="sxs-lookup"><span data-stu-id="0e46d-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="0e46d-231">Innymi słowy, wiele wymagań autoryzacji dodanych do pojedynczych zasad autoryzacji jest traktowanych **na zasadzie.**</span><span class="sxs-lookup"><span data-stu-id="0e46d-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="0e46d-232">Wymagania nie muszą mieć danych ani właściwości.</span><span class="sxs-lookup"><span data-stu-id="0e46d-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="0e46d-233">Programy obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0e46d-233">Authorization handlers</span></span>

<span data-ttu-id="0e46d-234">Procedura obsługi autoryzacji jest odpowiedzialna za ocenę właściwości wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="0e46d-235">Procedura obsługi autoryzacji szacuje wymagania w odniesieniu do podanej [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) w celu określenia, czy dostęp jest dozwolony.</span><span class="sxs-lookup"><span data-stu-id="0e46d-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="0e46d-236">Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="0e46d-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="0e46d-237">Program obsługi może odziedziczyć [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), gdzie `TRequirement` jest wymaganie do obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="0e46d-238">Alternatywnie program obsługi może zaimplementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) , aby obsłużyć więcej niż jeden typ wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="0e46d-239">Użyj procedury obsługi dla jednego wymagania</span><span class="sxs-lookup"><span data-stu-id="0e46d-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="0e46d-240">Poniżej znajduje się przykład relacji jeden-do-jednego, w której program obsługi minimalnych okresów używa jednego wymagania:</span><span class="sxs-lookup"><span data-stu-id="0e46d-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="0e46d-241">Poprzedni kod określa, czy bieżący podmiot użytkownika ma datę żądania urodzenia, który został wystawiony przez znanego i zaufanego wystawcy.</span><span class="sxs-lookup"><span data-stu-id="0e46d-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="0e46d-242">Autoryzacja nie może wystąpić w przypadku braku żądania. w takim przypadku zwracane jest zadanie wykonane.</span><span class="sxs-lookup"><span data-stu-id="0e46d-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="0e46d-243">W przypadku wystąpienia zgłoszenia jest naliczany wiek użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0e46d-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="0e46d-244">Jeśli użytkownik spełni minimalny wiek zdefiniowany przez to wymaganie, autoryzacja zostanie uznana za pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="0e46d-245">Gdy autoryzacja powiedzie się, `context.Succeed` jest wywoływana z wymaganym parametrem.</span><span class="sxs-lookup"><span data-stu-id="0e46d-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="0e46d-246">Korzystanie z programu obsługi dla wielu wymagań</span><span class="sxs-lookup"><span data-stu-id="0e46d-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="0e46d-247">Poniżej znajduje się przykład relacji jeden-do-wielu, w której program obsługi uprawnień może obsłużyć trzy różne typy wymagań:</span><span class="sxs-lookup"><span data-stu-id="0e46d-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="0e46d-248">Poprzedni kod przechodzi przez [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; Właściwości zawierającej wymagania, które nie są oznaczone jako pomyślne.</span><span class="sxs-lookup"><span data-stu-id="0e46d-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="0e46d-249">Aby uzyskać `ReadPermission` dostęp do żądanego zasobu, użytkownik musi być właścicielem lub sponsorem.</span><span class="sxs-lookup"><span data-stu-id="0e46d-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="0e46d-250">W przypadku `EditPermission` lub `DeletePermission` wymagania musi być właścicielem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="0e46d-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="0e46d-251">Rejestracja procedury obsługi</span><span class="sxs-lookup"><span data-stu-id="0e46d-251">Handler registration</span></span>

<span data-ttu-id="0e46d-252">Procedury obsługi są rejestrowane w kolekcji usług podczas konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="0e46d-253">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0e46d-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="0e46d-254">Poprzedni kod rejestruje `MinimumAgeHandler` jako pojedyncze przez wywołanie `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="0e46d-255">Programy obsługi można zarejestrować przy użyciu dowolnego z wbudowanych [okresów istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0e46d-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="0e46d-256">Co ma zwrócić program obsługi?</span><span class="sxs-lookup"><span data-stu-id="0e46d-256">What should a handler return?</span></span>

<span data-ttu-id="0e46d-257">Należy zauważyć, że `Handle` Metoda w [przykładzie procedury obsługi](#security-authorization-handler-example) nie zwraca żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="0e46d-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="0e46d-258">Jak jest wskazywany stan sukcesu lub niepowodzenia?</span><span class="sxs-lookup"><span data-stu-id="0e46d-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="0e46d-259">Procedura obsługi wskazuje powodzenie przez wywołanie `context.Succeed(IAuthorizationRequirement requirement)` , przekazanie wymagania, które zostało pomyślnie zweryfikowane.</span><span class="sxs-lookup"><span data-stu-id="0e46d-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="0e46d-260">Program obsługi nie musi ogólnie obsługiwać błędów, ponieważ inne procedury obsługi tego samego wymagania mogą się powieść.</span><span class="sxs-lookup"><span data-stu-id="0e46d-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="0e46d-261">W celu zagwarantowania niepowodzenia, nawet w przypadku pomyślnego zajścia innych wymagań `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="0e46d-262">Jeśli program obsługi wywołuje `context.Succeed` lub `context.Fail` , wszystkie inne procedury obsługi są nadal wywoływane.</span><span class="sxs-lookup"><span data-stu-id="0e46d-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="0e46d-263">Pozwala to na spełnienie wymagań związanych z generowaniem efektów ubocznych, takich jak rejestrowanie, które odbywa się nawet w przypadku pomyślnej weryfikacji lub niepowodzenia przez inną procedurę obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="0e46d-264">Gdy jest ustawiona na `false` , właściwość [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) jest krótka, gdy `context.Fail` zostanie wywołane wykonywanie programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="0e46d-265">`InvokeHandlersAfterFailure` wartość domyślna to `true` , w którym to przypadku są wywoływane wszystkie programy obsługi.</span><span class="sxs-lookup"><span data-stu-id="0e46d-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="0e46d-266">Procedury obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="0e46d-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="0e46d-267">Dlaczego chcesz mieć wiele programów obsługi wymagań?</span><span class="sxs-lookup"><span data-stu-id="0e46d-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="0e46d-268">W przypadkach, w których Ocena ma być przeprowadzana na podstawie **lub** , należy zaimplementować wiele programów obsługi dla jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="0e46d-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="0e46d-269">Na przykład firma Microsoft ma drzwi, które są otwierane tylko za pomocą kart kluczowych.</span><span class="sxs-lookup"><span data-stu-id="0e46d-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="0e46d-270">Jeśli opuścisz kartę kluczową w domu, recepcjonista drukuje tymczasowy naklejkę i otwiera drzwiczki.</span><span class="sxs-lookup"><span data-stu-id="0e46d-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="0e46d-271">W tym scenariuszu istnieje jedno wymaganie, *BuildingEntry* , ale wiele programów obsługi, każdy z nich bada pojedyncze wymaganie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-271">In this scenario, you'd have a single requirement, *BuildingEntry* , but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="0e46d-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="0e46d-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="0e46d-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0e46d-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="0e46d-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0e46d-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="0e46d-275">Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="0e46d-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="0e46d-276">Jeśli jedna z programów obsługi powiedzie się, gdy zasady oceniają `BuildingEntryRequirement` , Ocena zasad zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="0e46d-277">Używanie funkcji Func do realizacji zasad</span><span class="sxs-lookup"><span data-stu-id="0e46d-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="0e46d-278">Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażania w kodzie.</span><span class="sxs-lookup"><span data-stu-id="0e46d-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="0e46d-279">`Func<AuthorizationHandlerContext, bool>`Podczas konfigurowania zasad przy użyciu konstruktora zasad można podać wartość `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="0e46d-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="0e46d-280">Na przykład poprzedni można `BadgeEntryHandler` napisać ponownie w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0e46d-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="0e46d-281">Dostęp do kontekstu żądania MVC w programach obsługi</span><span class="sxs-lookup"><span data-stu-id="0e46d-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="0e46d-282">`HandleRequirementAsync`Metoda zaimplementowana w procedurze obsługi autoryzacji ma dwa parametry: `AuthorizationHandlerContext` a i `TRequirement` obsługujące.</span><span class="sxs-lookup"><span data-stu-id="0e46d-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="0e46d-283">Platformy, takie jak MVC lub, SignalR mogą dodawać dowolne obiekty do `Resource` właściwości w `AuthorizationHandlerContext` celu przekazania dodatkowych informacji.</span><span class="sxs-lookup"><span data-stu-id="0e46d-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="0e46d-284">Na przykład, MVC przekazuje wystąpienie elementu [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) we `Resource` właściwości.</span><span class="sxs-lookup"><span data-stu-id="0e46d-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="0e46d-285">Ta właściwość zapewnia dostęp do `HttpContext` `RouteData` elementów, i innych udostępnianych przez MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e46d-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="0e46d-286">Użycie `Resource` właściwości jest specyficzne dla struktury.</span><span class="sxs-lookup"><span data-stu-id="0e46d-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="0e46d-287">Użycie informacji we `Resource` Właściwości ogranicza zasady autoryzacji do określonych struktur.</span><span class="sxs-lookup"><span data-stu-id="0e46d-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="0e46d-288">Należy rzutować `Resource` Właściwość za pomocą `is` słowa kluczowego, a następnie potwierdzić, że rzutowanie zakończyło się pomyślnie, aby upewnić się, że kod nie ulegnie awarii z `InvalidCastException` uruchomieniem w innych strukturach:</span><span class="sxs-lookup"><span data-stu-id="0e46d-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
