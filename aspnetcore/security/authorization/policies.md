---
title: Autoryzacja oparta na zasadach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć i używać programów obsługi zasad autoryzacji do wymuszania wymagań dotyczących autoryzacji w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488764"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="e1bea-103">Autoryzacja oparta na zasadach w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1bea-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1bea-104">Poniżej obejmuje [autoryzacji opartej na rolach](xref:security/authorization/roles) i [autoryzacji opartej na oświadczeniach](xref:security/authorization/claims) użyć wymagania, obsługi wymagań i wstępnie skonfigurowane zasady.</span><span class="sxs-lookup"><span data-stu-id="e1bea-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e1bea-105">Te bloki konstrukcyjne obsługują wyrażenie ocen autoryzacji w kodzie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e1bea-106">Rezultatem jest bogatsza, wielokrotnego, sprawdzalna struktura autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e1bea-107">Zasady autoryzacji składają się z co najmniej jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e1bea-108">Jest on zarejestrowany jako część konfiguracji usługi `Startup.ConfigureServices` autoryzacji, w metodzie:</span><span class="sxs-lookup"><span data-stu-id="e1bea-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="e1bea-109">W poprzednim przykładzie tworzona jest zasada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="e1bea-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e1bea-110">Ma jeden wymóg,&mdash;że w minimalnym wieku, który jest dostarczany jako parametr do wymogu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e1bea-111">Usługa iAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e1bea-111">IAuthorizationService</span></span> 

<span data-ttu-id="e1bea-112">Usługa podstawowa, która określa, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>czy autoryzacja zakończy się pomyślnie, to:</span><span class="sxs-lookup"><span data-stu-id="e1bea-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="e1bea-113">Powyższy kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="e1bea-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e1bea-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>jest usługą znaczników bez metod i mechanizm śledzenia, czy autoryzacja zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e1bea-115">Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich jest odpowiedzialny za sprawdzenie, czy spełnione są wymagania:</span><span class="sxs-lookup"><span data-stu-id="e1bea-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="e1bea-116">Klasa <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> jest to, co program obsługi używa do oznaczenia, czy wymagania zostały spełnione:</span><span class="sxs-lookup"><span data-stu-id="e1bea-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e1bea-117">Poniższy kod przedstawia uproszczoną (i adnotacje z komentarzami) domyślną implementację usługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="e1bea-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="e1bea-118">Poniższy kod przedstawia `ConfigureServices`typowy:</span><span class="sxs-lookup"><span data-stu-id="e1bea-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="e1bea-119">Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` lub do autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="e1bea-120">Stosowanie zasad do kontrolerów MVC</span><span class="sxs-lookup"><span data-stu-id="e1bea-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="e1bea-121">Jeśli używasz stron Razor, zobacz [Stosowanie zasad do stron Razor](#applying-policies-to-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e1bea-122">Zasady są stosowane do kontrolerów `[Authorize]` przy użyciu atrybutu o nazwie zasad.</span><span class="sxs-lookup"><span data-stu-id="e1bea-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e1bea-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="e1bea-124">Stosowanie zasad do stron Razor</span><span class="sxs-lookup"><span data-stu-id="e1bea-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="e1bea-125">Zasady są stosowane do stron Razor przy użyciu atrybutu `[Authorize]` o nazwie zasad.</span><span class="sxs-lookup"><span data-stu-id="e1bea-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e1bea-126">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e1bea-127">Zasady można również stosować do stron Razor przy użyciu [konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e1bea-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e1bea-128">Wymagania</span><span class="sxs-lookup"><span data-stu-id="e1bea-128">Requirements</span></span>

<span data-ttu-id="e1bea-129">Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą być używane do oceny bieżącego podmiotu zabezpieczeń użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1bea-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e1bea-130">W naszej polityce "AtLeast21" wymóg jest&mdash;jednym parametrem minimalnym wiekiem.</span><span class="sxs-lookup"><span data-stu-id="e1bea-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e1bea-131">Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pusty interfejs znacznika.</span><span class="sxs-lookup"><span data-stu-id="e1bea-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e1bea-132">Parametryzowane wymagania dotyczące minimalnego wieku mogą być realizowane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e1bea-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e1bea-133">Jeśli zasady autoryzacji zawiera wiele wymagań autoryzacji, wszystkie wymagania muszą przejść, aby ocena zasad powiodła się.</span><span class="sxs-lookup"><span data-stu-id="e1bea-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e1bea-134">Innymi słowy, wiele wymagań autoryzacji dodanych do zasad pojedynczej autoryzacji są traktowane na podstawie **I.**</span><span class="sxs-lookup"><span data-stu-id="e1bea-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e1bea-135">Wymaganie nie musi mieć danych ani właściwości.</span><span class="sxs-lookup"><span data-stu-id="e1bea-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e1bea-136">Programy obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="e1bea-136">Authorization handlers</span></span>

<span data-ttu-id="e1bea-137">Program obsługi autoryzacji jest odpowiedzialny za ocenę właściwości wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e1bea-138">Program obsługi autoryzacji ocenia wymagania względem [dostarczonej AutoryzacjiHandlerContext,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aby ustalić, czy dostęp jest dozwolony.</span><span class="sxs-lookup"><span data-stu-id="e1bea-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e1bea-139">Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e1bea-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e1bea-140">Program obsługi może dziedziczyć [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), gdzie `TRequirement` jest wymagane do obsługi.</span><span class="sxs-lookup"><span data-stu-id="e1bea-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e1bea-141">Alternatywnie program obsługi może implementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) do obsługi więcej niż jeden typ wymagań.</span><span class="sxs-lookup"><span data-stu-id="e1bea-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e1bea-142">Używanie programu obsługi dla jednego wymagania</span><span class="sxs-lookup"><span data-stu-id="e1bea-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e1bea-143">Poniżej przedstawiono przykład relacji jeden do jednego, w którym program obsługi minimalnego wieku wykorzystuje pojedyncze wymaganie:</span><span class="sxs-lookup"><span data-stu-id="e1bea-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e1bea-144">Powyższy kod określa, czy bieżący podmiot zleceniodawca ma datę urodzenia, która została wystawiona przez znanego i zaufanego Emitenta.</span><span class="sxs-lookup"><span data-stu-id="e1bea-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e1bea-145">Autoryzacja nie może wystąpić, gdy brakuje oświadczenia, w którym to przypadku zwracane jest ukończone zadanie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e1bea-146">Gdy oświadczenie jest obecne, obliczany jest wiek użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1bea-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e1bea-147">Jeśli użytkownik spełnia minimalny wiek określony przez wymóg, autoryzacja jest uważana za pomyślną.</span><span class="sxs-lookup"><span data-stu-id="e1bea-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e1bea-148">Gdy autoryzacja `context.Succeed` zakończy się pomyślnie, jest wywoływana z spełnione wymaganie jako jego jedynym parametrem.</span><span class="sxs-lookup"><span data-stu-id="e1bea-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e1bea-149">Używanie programu obsługi dla wielu wymagań</span><span class="sxs-lookup"><span data-stu-id="e1bea-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e1bea-150">Poniżej przedstawiono przykład relacji jeden do wielu, w którym program obsługi uprawnień może obsługiwać trzy różne typy wymagań:</span><span class="sxs-lookup"><span data-stu-id="e1bea-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e1bea-151">Poprzedni kod przechodzi przez wymagania&mdash; [oczekujące](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)właściwość zawierająca wymagania nie oznaczone jako pomyślne.</span><span class="sxs-lookup"><span data-stu-id="e1bea-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e1bea-152">W `ReadPermission` przypadku wymagań użytkownik musi być właścicielem lub sponsorem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e1bea-153">W przypadku `EditPermission` lub `DeletePermission` wymagania musi on być właścicielem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e1bea-154">Rejestracja obsługi</span><span class="sxs-lookup"><span data-stu-id="e1bea-154">Handler registration</span></span>

<span data-ttu-id="e1bea-155">Programy obsługi są rejestrowane w kolekcji usług podczas konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e1bea-156">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="e1bea-157">Poprzedni kod rejestruje `MinimumAgeHandler` się jako singleton, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`wywołując .</span><span class="sxs-lookup"><span data-stu-id="e1bea-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e1bea-158">Programy obsługi mogą być rejestrowane przy użyciu dowolnego z [wbudowanych okresów istnienia usługi.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="e1bea-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e1bea-159">Co powinien zwrócić program obsługi?</span><span class="sxs-lookup"><span data-stu-id="e1bea-159">What should a handler return?</span></span>

<span data-ttu-id="e1bea-160">Należy zauważyć, że `Handle` metoda w [przykładzie programu obsługi](#security-authorization-handler-example) zwraca żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="e1bea-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e1bea-161">W jaki sposób wskazuje się status sukcesu lub porażki?</span><span class="sxs-lookup"><span data-stu-id="e1bea-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e1bea-162">Program obsługi wskazuje sukces `context.Succeed(IAuthorizationRequirement requirement)`przez wywołanie , przekazywanie wymagania, które zostało pomyślnie zatwierdzone.</span><span class="sxs-lookup"><span data-stu-id="e1bea-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e1bea-163">Program obsługi nie musi obsługiwać błędów ogólnie, jak inne programy obsługi dla tego samego wymagania może zakończyć się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e1bea-164">Aby zagwarantować awarię, nawet jeśli inne `context.Fail`programy obsługi wymagań zakończyć się pomyślnie, wywołaj .</span><span class="sxs-lookup"><span data-stu-id="e1bea-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e1bea-165">Jeśli wywołano `context.Succeed` `context.Fail`program obsługi lub wszystkie inne programy obsługi są nadal wywoływane.</span><span class="sxs-lookup"><span data-stu-id="e1bea-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e1bea-166">Dzięki temu wymagania do tworzenia skutków ubocznych, takich jak rejestrowanie, które odbywa się, nawet jeśli inny program obsługi pomyślnie zweryfikowane lub nie powiodło się wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e1bea-167">Gdy ustawiona na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) właściwość (dostępna w ASP.NET Core 1.1 i nowszych) zwęża wykonywanie programów obsługi, gdy `context.Fail` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="e1bea-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e1bea-168">`InvokeHandlersAfterFailure`domyślnie `true`, w którym to przypadku wszystkie programy obsługi są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="e1bea-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e1bea-169">Programy obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="e1bea-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e1bea-170">Dlaczego chcę wiele programów obsługi dla wymagania?</span><span class="sxs-lookup"><span data-stu-id="e1bea-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e1bea-171">W przypadkach, gdy chcesz, aby ocena była na podstawie **OR,** zaimplementuj wiele programów obsługi dla jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e1bea-172">Na przykład Microsoft ma drzwi, które otwierają się tylko za pomocą kart kluczowych.</span><span class="sxs-lookup"><span data-stu-id="e1bea-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e1bea-173">Jeśli zostawisz kartę z kluczem w domu, recepcjonistka wydrukuje tymczasową naklejkę i otworzy drzwi dla Ciebie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e1bea-174">W tym scenariuszu masz jedno wymaganie, *BuildingEntry*, ale wiele programów obsługi, każdy z nich badania pojedynczego wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e1bea-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e1bea-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e1bea-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e1bea-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e1bea-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e1bea-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e1bea-178">Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="e1bea-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e1bea-179">Jeśli którykolwiek z programów obsługi `BuildingEntryRequirement`powiedzie się, gdy zasada ocenia , oceny zasad powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="e1bea-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e1bea-180">Używanie func do realizacji zasad</span><span class="sxs-lookup"><span data-stu-id="e1bea-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e1bea-181">Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażenia w kodzie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e1bea-182">Jest możliwe, aby `Func<AuthorizationHandlerContext, bool>` podać podczas konfigurowania `RequireAssertion` zasad z konstruktorem zasad.</span><span class="sxs-lookup"><span data-stu-id="e1bea-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e1bea-183">Na przykład poprzedni `BadgeEntryHandler` może być przepisany w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e1bea-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e1bea-184">Uzyskiwanie dostępu do kontekstu żądania MVC w programach obsługi</span><span class="sxs-lookup"><span data-stu-id="e1bea-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="e1bea-185">Metoda `HandleRequirementAsync` zaimplementowana w programie obsługi `AuthorizationHandlerContext` autoryzacji ma dwa parametry: an i `TRequirement` obsługi.</span><span class="sxs-lookup"><span data-stu-id="e1bea-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e1bea-186">Struktury, takie jak MVC lub Jabbr mogą `Resource` dodawać `AuthorizationHandlerContext` dowolny obiekt do właściwości w celu przekazania dodatkowych informacji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e1bea-187">Na przykład MVC przekazuje wystąpienie [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) we `Resource` właściwości.</span><span class="sxs-lookup"><span data-stu-id="e1bea-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="e1bea-188">Ta właściwość `HttpContext`zapewnia `RouteData`dostęp do , i wszystko inne dostarczone przez MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e1bea-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e1bea-189">Korzystanie z `Resource` właściwości jest specyficzne dla ram.</span><span class="sxs-lookup"><span data-stu-id="e1bea-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e1bea-190">Korzystanie z `Resource` informacji we właściwości ogranicza zasady autoryzacji do określonych struktur.</span><span class="sxs-lookup"><span data-stu-id="e1bea-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e1bea-191">Należy oddać `Resource` właściwość `is` przy użyciu słowa kluczowego, a następnie potwierdzić, że `InvalidCastException` oddanych udało się upewnić, że kod nie upaść z po uruchomieniu na innych platformach:</span><span class="sxs-lookup"><span data-stu-id="e1bea-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1bea-192">Poniżej obejmuje [autoryzacji opartej na rolach](xref:security/authorization/roles) i [autoryzacji opartej na oświadczeniach](xref:security/authorization/claims) użyć wymagania, obsługi wymagań i wstępnie skonfigurowane zasady.</span><span class="sxs-lookup"><span data-stu-id="e1bea-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e1bea-193">Te bloki konstrukcyjne obsługują wyrażenie ocen autoryzacji w kodzie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e1bea-194">Rezultatem jest bogatsza, wielokrotnego, sprawdzalna struktura autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e1bea-195">Zasady autoryzacji składają się z co najmniej jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e1bea-196">Jest on zarejestrowany jako część konfiguracji usługi `Startup.ConfigureServices` autoryzacji, w metodzie:</span><span class="sxs-lookup"><span data-stu-id="e1bea-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="e1bea-197">W poprzednim przykładzie tworzona jest zasada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="e1bea-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e1bea-198">Ma jeden wymóg,&mdash;że w minimalnym wieku, który jest dostarczany jako parametr do wymogu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e1bea-199">Usługa iAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e1bea-199">IAuthorizationService</span></span> 

<span data-ttu-id="e1bea-200">Usługa podstawowa, która określa, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>czy autoryzacja zakończy się pomyślnie, to:</span><span class="sxs-lookup"><span data-stu-id="e1bea-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="e1bea-201">Powyższy kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="e1bea-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e1bea-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>jest usługą znaczników bez metod i mechanizm śledzenia, czy autoryzacja zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e1bea-203">Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich jest odpowiedzialny za sprawdzenie, czy spełnione są wymagania:</span><span class="sxs-lookup"><span data-stu-id="e1bea-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="e1bea-204">Klasa <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> jest to, co program obsługi używa do oznaczenia, czy wymagania zostały spełnione:</span><span class="sxs-lookup"><span data-stu-id="e1bea-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e1bea-205">Poniższy kod przedstawia uproszczoną (i adnotacje z komentarzami) domyślną implementację usługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="e1bea-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="e1bea-206">Poniższy kod przedstawia `ConfigureServices`typowy:</span><span class="sxs-lookup"><span data-stu-id="e1bea-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="e1bea-207">Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` lub do autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="e1bea-208">Stosowanie zasad do kontrolerów MVC</span><span class="sxs-lookup"><span data-stu-id="e1bea-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="e1bea-209">Jeśli używasz stron Razor, zobacz [Stosowanie zasad do stron Razor](#applying-policies-to-razor-pages) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e1bea-210">Zasady są stosowane do kontrolerów `[Authorize]` przy użyciu atrybutu o nazwie zasad.</span><span class="sxs-lookup"><span data-stu-id="e1bea-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e1bea-211">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="e1bea-212">Stosowanie zasad do stron Razor</span><span class="sxs-lookup"><span data-stu-id="e1bea-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="e1bea-213">Zasady są stosowane do stron Razor przy użyciu atrybutu `[Authorize]` o nazwie zasad.</span><span class="sxs-lookup"><span data-stu-id="e1bea-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e1bea-214">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e1bea-215">Zasady można również stosować do stron Razor przy użyciu [konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e1bea-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e1bea-216">Wymagania</span><span class="sxs-lookup"><span data-stu-id="e1bea-216">Requirements</span></span>

<span data-ttu-id="e1bea-217">Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą być używane do oceny bieżącego podmiotu zabezpieczeń użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1bea-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e1bea-218">W naszej polityce "AtLeast21" wymóg jest&mdash;jednym parametrem minimalnym wiekiem.</span><span class="sxs-lookup"><span data-stu-id="e1bea-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e1bea-219">Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pusty interfejs znacznika.</span><span class="sxs-lookup"><span data-stu-id="e1bea-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e1bea-220">Parametryzowane wymagania dotyczące minimalnego wieku mogą być realizowane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e1bea-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e1bea-221">Jeśli zasady autoryzacji zawiera wiele wymagań autoryzacji, wszystkie wymagania muszą przejść, aby ocena zasad powiodła się.</span><span class="sxs-lookup"><span data-stu-id="e1bea-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e1bea-222">Innymi słowy, wiele wymagań autoryzacji dodanych do zasad pojedynczej autoryzacji są traktowane na podstawie **I.**</span><span class="sxs-lookup"><span data-stu-id="e1bea-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e1bea-223">Wymaganie nie musi mieć danych ani właściwości.</span><span class="sxs-lookup"><span data-stu-id="e1bea-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e1bea-224">Programy obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="e1bea-224">Authorization handlers</span></span>

<span data-ttu-id="e1bea-225">Program obsługi autoryzacji jest odpowiedzialny za ocenę właściwości wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e1bea-226">Program obsługi autoryzacji ocenia wymagania względem [dostarczonej AutoryzacjiHandlerContext,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aby ustalić, czy dostęp jest dozwolony.</span><span class="sxs-lookup"><span data-stu-id="e1bea-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e1bea-227">Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e1bea-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e1bea-228">Program obsługi może dziedziczyć [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), gdzie `TRequirement` jest wymagane do obsługi.</span><span class="sxs-lookup"><span data-stu-id="e1bea-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e1bea-229">Alternatywnie program obsługi może implementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) do obsługi więcej niż jeden typ wymagań.</span><span class="sxs-lookup"><span data-stu-id="e1bea-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e1bea-230">Używanie programu obsługi dla jednego wymagania</span><span class="sxs-lookup"><span data-stu-id="e1bea-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e1bea-231">Poniżej przedstawiono przykład relacji jeden do jednego, w którym program obsługi minimalnego wieku wykorzystuje pojedyncze wymaganie:</span><span class="sxs-lookup"><span data-stu-id="e1bea-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e1bea-232">Powyższy kod określa, czy bieżący podmiot zleceniodawca ma datę urodzenia, która została wystawiona przez znanego i zaufanego Emitenta.</span><span class="sxs-lookup"><span data-stu-id="e1bea-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e1bea-233">Autoryzacja nie może wystąpić, gdy brakuje oświadczenia, w którym to przypadku zwracane jest ukończone zadanie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e1bea-234">Gdy oświadczenie jest obecne, obliczany jest wiek użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e1bea-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e1bea-235">Jeśli użytkownik spełnia minimalny wiek określony przez wymóg, autoryzacja jest uważana za pomyślną.</span><span class="sxs-lookup"><span data-stu-id="e1bea-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e1bea-236">Gdy autoryzacja `context.Succeed` zakończy się pomyślnie, jest wywoływana z spełnione wymaganie jako jego jedynym parametrem.</span><span class="sxs-lookup"><span data-stu-id="e1bea-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e1bea-237">Używanie programu obsługi dla wielu wymagań</span><span class="sxs-lookup"><span data-stu-id="e1bea-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e1bea-238">Poniżej przedstawiono przykład relacji jeden do wielu, w którym program obsługi uprawnień może obsługiwać trzy różne typy wymagań:</span><span class="sxs-lookup"><span data-stu-id="e1bea-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e1bea-239">Poprzedni kod przechodzi przez wymagania&mdash; [oczekujące](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)właściwość zawierająca wymagania nie oznaczone jako pomyślne.</span><span class="sxs-lookup"><span data-stu-id="e1bea-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e1bea-240">W `ReadPermission` przypadku wymagań użytkownik musi być właścicielem lub sponsorem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e1bea-241">W przypadku `EditPermission` lub `DeletePermission` wymagania musi on być właścicielem, aby uzyskać dostęp do żądanego zasobu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e1bea-242">Rejestracja obsługi</span><span class="sxs-lookup"><span data-stu-id="e1bea-242">Handler registration</span></span>

<span data-ttu-id="e1bea-243">Programy obsługi są rejestrowane w kolekcji usług podczas konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e1bea-244">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="e1bea-245">Poprzedni kod rejestruje `MinimumAgeHandler` się jako singleton, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`wywołując .</span><span class="sxs-lookup"><span data-stu-id="e1bea-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e1bea-246">Programy obsługi mogą być rejestrowane przy użyciu dowolnego z [wbudowanych okresów istnienia usługi.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="e1bea-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e1bea-247">Co powinien zwrócić program obsługi?</span><span class="sxs-lookup"><span data-stu-id="e1bea-247">What should a handler return?</span></span>

<span data-ttu-id="e1bea-248">Należy zauważyć, że `Handle` metoda w [przykładzie programu obsługi](#security-authorization-handler-example) zwraca żadnej wartości.</span><span class="sxs-lookup"><span data-stu-id="e1bea-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e1bea-249">W jaki sposób wskazuje się status sukcesu lub porażki?</span><span class="sxs-lookup"><span data-stu-id="e1bea-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e1bea-250">Program obsługi wskazuje sukces `context.Succeed(IAuthorizationRequirement requirement)`przez wywołanie , przekazywanie wymagania, które zostało pomyślnie zatwierdzone.</span><span class="sxs-lookup"><span data-stu-id="e1bea-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e1bea-251">Program obsługi nie musi obsługiwać błędów ogólnie, jak inne programy obsługi dla tego samego wymagania może zakończyć się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e1bea-252">Aby zagwarantować awarię, nawet jeśli inne `context.Fail`programy obsługi wymagań zakończyć się pomyślnie, wywołaj .</span><span class="sxs-lookup"><span data-stu-id="e1bea-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e1bea-253">Jeśli wywołano `context.Succeed` `context.Fail`program obsługi lub wszystkie inne programy obsługi są nadal wywoływane.</span><span class="sxs-lookup"><span data-stu-id="e1bea-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e1bea-254">Dzięki temu wymagania do tworzenia skutków ubocznych, takich jak rejestrowanie, które odbywa się, nawet jeśli inny program obsługi pomyślnie zweryfikowane lub nie powiodło się wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e1bea-255">Gdy ustawiona na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) właściwość (dostępna w ASP.NET Core 1.1 i nowszych) zwęża wykonywanie programów obsługi, gdy `context.Fail` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="e1bea-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e1bea-256">`InvokeHandlersAfterFailure`domyślnie `true`, w którym to przypadku wszystkie programy obsługi są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="e1bea-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e1bea-257">Programy obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="e1bea-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e1bea-258">Dlaczego chcę wiele programów obsługi dla wymagania?</span><span class="sxs-lookup"><span data-stu-id="e1bea-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e1bea-259">W przypadkach, gdy chcesz, aby ocena była na podstawie **OR,** zaimplementuj wiele programów obsługi dla jednego wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e1bea-260">Na przykład Microsoft ma drzwi, które otwierają się tylko za pomocą kart kluczowych.</span><span class="sxs-lookup"><span data-stu-id="e1bea-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e1bea-261">Jeśli zostawisz kartę z kluczem w domu, recepcjonistka wydrukuje tymczasową naklejkę i otworzy drzwi dla Ciebie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e1bea-262">W tym scenariuszu masz jedno wymaganie, *BuildingEntry*, ale wiele programów obsługi, każdy z nich badania pojedynczego wymagania.</span><span class="sxs-lookup"><span data-stu-id="e1bea-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e1bea-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e1bea-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e1bea-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e1bea-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e1bea-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e1bea-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e1bea-266">Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="e1bea-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e1bea-267">Jeśli którykolwiek z programów obsługi `BuildingEntryRequirement`powiedzie się, gdy zasada ocenia , oceny zasad powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="e1bea-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e1bea-268">Używanie func do realizacji zasad</span><span class="sxs-lookup"><span data-stu-id="e1bea-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e1bea-269">Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażenia w kodzie.</span><span class="sxs-lookup"><span data-stu-id="e1bea-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e1bea-270">Jest możliwe, aby `Func<AuthorizationHandlerContext, bool>` podać podczas konfigurowania `RequireAssertion` zasad z konstruktorem zasad.</span><span class="sxs-lookup"><span data-stu-id="e1bea-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e1bea-271">Na przykład poprzedni `BadgeEntryHandler` może być przepisany w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e1bea-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e1bea-272">Uzyskiwanie dostępu do kontekstu żądania MVC w programach obsługi</span><span class="sxs-lookup"><span data-stu-id="e1bea-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="e1bea-273">Metoda `HandleRequirementAsync` zaimplementowana w programie obsługi `AuthorizationHandlerContext` autoryzacji ma dwa parametry: an i `TRequirement` obsługi.</span><span class="sxs-lookup"><span data-stu-id="e1bea-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e1bea-274">Struktury, takie jak MVC lub SignalR mogą `Resource` dodawać `AuthorizationHandlerContext` dowolny obiekt do właściwości na przekazać dodatkowe informacje.</span><span class="sxs-lookup"><span data-stu-id="e1bea-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e1bea-275">Podczas korzystania z routingu punktu końcowego autoryzacja jest zazwyczaj obsługiwane przez oprogramowanie pośredniczące autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="e1bea-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="e1bea-276">W takim przypadku `Resource` właściwość jest <xref:Microsoft.AspNetCore.Http.Endpoint>wystąpieniem .</span><span class="sxs-lookup"><span data-stu-id="e1bea-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="e1bea-277">Punkt końcowy może służyć do sondowania zasobu źródłowego, do którego jesteś routingu.</span><span class="sxs-lookup"><span data-stu-id="e1bea-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="e1bea-278">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e1bea-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="e1bea-279">W przypadku tradycyjnego routingu lub gdy autoryzacja odbywa się `Resource` jako <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> część filtru autoryzacji MVC, wartość jest wystąpieniem.</span><span class="sxs-lookup"><span data-stu-id="e1bea-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="e1bea-280">Ta właściwość `HttpContext`zapewnia `RouteData`dostęp do , i wszystko inne dostarczone przez MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e1bea-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e1bea-281">Korzystanie z `Resource` właściwości jest specyficzne dla ram.</span><span class="sxs-lookup"><span data-stu-id="e1bea-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e1bea-282">Korzystanie z `Resource` informacji we właściwości ogranicza zasady autoryzacji do określonych struktur.</span><span class="sxs-lookup"><span data-stu-id="e1bea-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e1bea-283">Należy oddać `Resource` właściwość `is` przy użyciu słowa kluczowego, a następnie potwierdzić, że `InvalidCastException` oddanych udało się upewnić, że kod nie upaść z po uruchomieniu na innych platformach:</span><span class="sxs-lookup"><span data-stu-id="e1bea-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
