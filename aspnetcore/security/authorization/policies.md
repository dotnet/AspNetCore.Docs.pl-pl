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
# <a name="policy-based-authorization-in-aspnet-core"></a>Autoryzacja oparta na zasadach w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Poniżej obejmuje [autoryzacji opartej na rolach](xref:security/authorization/roles) i [autoryzacji opartej na oświadczeniach](xref:security/authorization/claims) użyć wymagania, obsługi wymagań i wstępnie skonfigurowane zasady. Te bloki konstrukcyjne obsługują wyrażenie ocen autoryzacji w kodzie. Rezultatem jest bogatsza, wielokrotnego, sprawdzalna struktura autoryzacji.

Zasady autoryzacji składają się z co najmniej jednego wymagania. Jest on zarejestrowany jako część konfiguracji usługi `Startup.ConfigureServices` autoryzacji, w metodzie:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

W poprzednim przykładzie tworzona jest zasada "AtLeast21". Ma jeden wymóg,&mdash;że w minimalnym wieku, który jest dostarczany jako parametr do wymogu.

## <a name="iauthorizationservice"></a>Usługa iAuthorizationService 

Usługa podstawowa, która określa, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>czy autoryzacja zakończy się pomyślnie, to:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Powyższy kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>jest usługą znaczników bez metod i mechanizm śledzenia, czy autoryzacja zakończy się pomyślnie.

Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich jest odpowiedzialny za sprawdzenie, czy spełnione są wymagania:
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

Klasa <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> jest to, co program obsługi używa do oznaczenia, czy wymagania zostały spełnione:

```csharp
 context.Succeed(requirement)
```

Poniższy kod przedstawia uproszczoną (i adnotacje z komentarzami) domyślną implementację usługi autoryzacji:

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

Poniższy kod przedstawia `ConfigureServices`typowy:

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

Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` lub do autoryzacji.

## <a name="applying-policies-to-mvc-controllers"></a>Stosowanie zasad do kontrolerów MVC

Jeśli używasz stron Razor, zobacz [Stosowanie zasad do stron Razor](#applying-policies-to-razor-pages) w tym dokumencie.

Zasady są stosowane do kontrolerów `[Authorize]` przy użyciu atrybutu o nazwie zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Stosowanie zasad do stron Razor

Zasady są stosowane do stron Razor przy użyciu atrybutu `[Authorize]` o nazwie zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zasady można również stosować do stron Razor przy użyciu [konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Wymagania

Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą być używane do oceny bieżącego podmiotu zabezpieczeń użytkownika. W naszej polityce "AtLeast21" wymóg jest&mdash;jednym parametrem minimalnym wiekiem. Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pusty interfejs znacznika. Parametryzowane wymagania dotyczące minimalnego wieku mogą być realizowane w następujący sposób:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Jeśli zasady autoryzacji zawiera wiele wymagań autoryzacji, wszystkie wymagania muszą przejść, aby ocena zasad powiodła się. Innymi słowy, wiele wymagań autoryzacji dodanych do zasad pojedynczej autoryzacji są traktowane na podstawie **I.**

> [!NOTE]
> Wymaganie nie musi mieć danych ani właściwości.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Programy obsługi autoryzacji

Program obsługi autoryzacji jest odpowiedzialny za ocenę właściwości wymagania. Program obsługi autoryzacji ocenia wymagania względem [dostarczonej AutoryzacjiHandlerContext,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aby ustalić, czy dostęp jest dozwolony.

Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers). Program obsługi może dziedziczyć [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), gdzie `TRequirement` jest wymagane do obsługi. Alternatywnie program obsługi może implementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) do obsługi więcej niż jeden typ wymagań.

### <a name="use-a-handler-for-one-requirement"></a>Używanie programu obsługi dla jednego wymagania

<a name="security-authorization-handler-example"></a>

Poniżej przedstawiono przykład relacji jeden do jednego, w którym program obsługi minimalnego wieku wykorzystuje pojedyncze wymaganie:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Powyższy kod określa, czy bieżący podmiot zleceniodawca ma datę urodzenia, która została wystawiona przez znanego i zaufanego Emitenta. Autoryzacja nie może wystąpić, gdy brakuje oświadczenia, w którym to przypadku zwracane jest ukończone zadanie. Gdy oświadczenie jest obecne, obliczany jest wiek użytkownika. Jeśli użytkownik spełnia minimalny wiek określony przez wymóg, autoryzacja jest uważana za pomyślną. Gdy autoryzacja `context.Succeed` zakończy się pomyślnie, jest wywoływana z spełnione wymaganie jako jego jedynym parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Używanie programu obsługi dla wielu wymagań

Poniżej przedstawiono przykład relacji jeden do wielu, w którym program obsługi uprawnień może obsługiwać trzy różne typy wymagań:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Poprzedni kod przechodzi przez wymagania&mdash; [oczekujące](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)właściwość zawierająca wymagania nie oznaczone jako pomyślne. W `ReadPermission` przypadku wymagań użytkownik musi być właścicielem lub sponsorem, aby uzyskać dostęp do żądanego zasobu. W przypadku `EditPermission` lub `DeletePermission` wymagania musi on być właścicielem, aby uzyskać dostęp do żądanego zasobu.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Rejestracja obsługi

Programy obsługi są rejestrowane w kolekcji usług podczas konfiguracji. Przykład:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Poprzedni kod rejestruje `MinimumAgeHandler` się jako singleton, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`wywołując . Programy obsługi mogą być rejestrowane przy użyciu dowolnego z [wbudowanych okresów istnienia usługi.](xref:fundamentals/dependency-injection#service-lifetimes)

## <a name="what-should-a-handler-return"></a>Co powinien zwrócić program obsługi?

Należy zauważyć, że `Handle` metoda w [przykładzie programu obsługi](#security-authorization-handler-example) zwraca żadnej wartości. W jaki sposób wskazuje się status sukcesu lub porażki?

* Program obsługi wskazuje sukces `context.Succeed(IAuthorizationRequirement requirement)`przez wywołanie , przekazywanie wymagania, które zostało pomyślnie zatwierdzone.

* Program obsługi nie musi obsługiwać błędów ogólnie, jak inne programy obsługi dla tego samego wymagania może zakończyć się pomyślnie.

* Aby zagwarantować awarię, nawet jeśli inne `context.Fail`programy obsługi wymagań zakończyć się pomyślnie, wywołaj .

Jeśli wywołano `context.Succeed` `context.Fail`program obsługi lub wszystkie inne programy obsługi są nadal wywoływane. Dzięki temu wymagania do tworzenia skutków ubocznych, takich jak rejestrowanie, które odbywa się, nawet jeśli inny program obsługi pomyślnie zweryfikowane lub nie powiodło się wymagania. Gdy ustawiona na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) właściwość (dostępna w ASP.NET Core 1.1 i nowszych) zwęża wykonywanie programów obsługi, gdy `context.Fail` jest wywoływana. `InvokeHandlersAfterFailure`domyślnie `true`, w którym to przypadku wszystkie programy obsługi są wywoływane.

> [!NOTE]
> Programy obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Dlaczego chcę wiele programów obsługi dla wymagania?

W przypadkach, gdy chcesz, aby ocena była na podstawie **OR,** zaimplementuj wiele programów obsługi dla jednego wymagania. Na przykład Microsoft ma drzwi, które otwierają się tylko za pomocą kart kluczowych. Jeśli zostawisz kartę z kluczem w domu, recepcjonistka wydrukuje tymczasową naklejkę i otworzy drzwi dla Ciebie. W tym scenariuszu masz jedno wymaganie, *BuildingEntry*, ale wiele programów obsługi, każdy z nich badania pojedynczego wymagania.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Jeśli którykolwiek z programów obsługi `BuildingEntryRequirement`powiedzie się, gdy zasada ocenia , oceny zasad powiedzie się.

## <a name="using-a-func-to-fulfill-a-policy"></a>Używanie func do realizacji zasad

Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażenia w kodzie. Jest możliwe, aby `Func<AuthorizationHandlerContext, bool>` podać podczas konfigurowania `RequireAssertion` zasad z konstruktorem zasad.

Na przykład poprzedni `BadgeEntryHandler` może być przepisany w następujący sposób:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Uzyskiwanie dostępu do kontekstu żądania MVC w programach obsługi

Metoda `HandleRequirementAsync` zaimplementowana w programie obsługi `AuthorizationHandlerContext` autoryzacji ma dwa parametry: an i `TRequirement` obsługi. Struktury, takie jak MVC lub Jabbr mogą `Resource` dodawać `AuthorizationHandlerContext` dowolny obiekt do właściwości w celu przekazania dodatkowych informacji.

Na przykład MVC przekazuje wystąpienie [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) we `Resource` właściwości. Ta właściwość `HttpContext`zapewnia `RouteData`dostęp do , i wszystko inne dostarczone przez MVC i Razor Pages.

Korzystanie z `Resource` właściwości jest specyficzne dla ram. Korzystanie z `Resource` informacji we właściwości ogranicza zasady autoryzacji do określonych struktur. Należy oddać `Resource` właściwość `is` przy użyciu słowa kluczowego, a następnie potwierdzić, że `InvalidCastException` oddanych udało się upewnić, że kod nie upaść z po uruchomieniu na innych platformach:

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

Poniżej obejmuje [autoryzacji opartej na rolach](xref:security/authorization/roles) i [autoryzacji opartej na oświadczeniach](xref:security/authorization/claims) użyć wymagania, obsługi wymagań i wstępnie skonfigurowane zasady. Te bloki konstrukcyjne obsługują wyrażenie ocen autoryzacji w kodzie. Rezultatem jest bogatsza, wielokrotnego, sprawdzalna struktura autoryzacji.

Zasady autoryzacji składają się z co najmniej jednego wymagania. Jest on zarejestrowany jako część konfiguracji usługi `Startup.ConfigureServices` autoryzacji, w metodzie:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

W poprzednim przykładzie tworzona jest zasada "AtLeast21". Ma jeden wymóg,&mdash;że w minimalnym wieku, który jest dostarczany jako parametr do wymogu.

## <a name="iauthorizationservice"></a>Usługa iAuthorizationService 

Usługa podstawowa, która określa, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>czy autoryzacja zakończy się pomyślnie, to:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Powyższy kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>jest usługą znaczników bez metod i mechanizm śledzenia, czy autoryzacja zakończy się pomyślnie.

Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich jest odpowiedzialny za sprawdzenie, czy spełnione są wymagania:
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

Klasa <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> jest to, co program obsługi używa do oznaczenia, czy wymagania zostały spełnione:

```csharp
 context.Succeed(requirement)
```

Poniższy kod przedstawia uproszczoną (i adnotacje z komentarzami) domyślną implementację usługi autoryzacji:

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

Poniższy kod przedstawia `ConfigureServices`typowy:

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

Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` lub do autoryzacji.

## <a name="applying-policies-to-mvc-controllers"></a>Stosowanie zasad do kontrolerów MVC

Jeśli używasz stron Razor, zobacz [Stosowanie zasad do stron Razor](#applying-policies-to-razor-pages) w tym dokumencie.

Zasady są stosowane do kontrolerów `[Authorize]` przy użyciu atrybutu o nazwie zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Stosowanie zasad do stron Razor

Zasady są stosowane do stron Razor przy użyciu atrybutu `[Authorize]` o nazwie zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zasady można również stosować do stron Razor przy użyciu [konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Wymagania

Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą być używane do oceny bieżącego podmiotu zabezpieczeń użytkownika. W naszej polityce "AtLeast21" wymóg jest&mdash;jednym parametrem minimalnym wiekiem. Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pusty interfejs znacznika. Parametryzowane wymagania dotyczące minimalnego wieku mogą być realizowane w następujący sposób:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Jeśli zasady autoryzacji zawiera wiele wymagań autoryzacji, wszystkie wymagania muszą przejść, aby ocena zasad powiodła się. Innymi słowy, wiele wymagań autoryzacji dodanych do zasad pojedynczej autoryzacji są traktowane na podstawie **I.**

> [!NOTE]
> Wymaganie nie musi mieć danych ani właściwości.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Programy obsługi autoryzacji

Program obsługi autoryzacji jest odpowiedzialny za ocenę właściwości wymagania. Program obsługi autoryzacji ocenia wymagania względem [dostarczonej AutoryzacjiHandlerContext,](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aby ustalić, czy dostęp jest dozwolony.

Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers). Program obsługi może dziedziczyć [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), gdzie `TRequirement` jest wymagane do obsługi. Alternatywnie program obsługi może implementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) do obsługi więcej niż jeden typ wymagań.

### <a name="use-a-handler-for-one-requirement"></a>Używanie programu obsługi dla jednego wymagania

<a name="security-authorization-handler-example"></a>

Poniżej przedstawiono przykład relacji jeden do jednego, w którym program obsługi minimalnego wieku wykorzystuje pojedyncze wymaganie:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Powyższy kod określa, czy bieżący podmiot zleceniodawca ma datę urodzenia, która została wystawiona przez znanego i zaufanego Emitenta. Autoryzacja nie może wystąpić, gdy brakuje oświadczenia, w którym to przypadku zwracane jest ukończone zadanie. Gdy oświadczenie jest obecne, obliczany jest wiek użytkownika. Jeśli użytkownik spełnia minimalny wiek określony przez wymóg, autoryzacja jest uważana za pomyślną. Gdy autoryzacja `context.Succeed` zakończy się pomyślnie, jest wywoływana z spełnione wymaganie jako jego jedynym parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Używanie programu obsługi dla wielu wymagań

Poniżej przedstawiono przykład relacji jeden do wielu, w którym program obsługi uprawnień może obsługiwać trzy różne typy wymagań:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Poprzedni kod przechodzi przez wymagania&mdash; [oczekujące](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)właściwość zawierająca wymagania nie oznaczone jako pomyślne. W `ReadPermission` przypadku wymagań użytkownik musi być właścicielem lub sponsorem, aby uzyskać dostęp do żądanego zasobu. W przypadku `EditPermission` lub `DeletePermission` wymagania musi on być właścicielem, aby uzyskać dostęp do żądanego zasobu.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Rejestracja obsługi

Programy obsługi są rejestrowane w kolekcji usług podczas konfiguracji. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Poprzedni kod rejestruje `MinimumAgeHandler` się jako singleton, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`wywołując . Programy obsługi mogą być rejestrowane przy użyciu dowolnego z [wbudowanych okresów istnienia usługi.](xref:fundamentals/dependency-injection#service-lifetimes)

## <a name="what-should-a-handler-return"></a>Co powinien zwrócić program obsługi?

Należy zauważyć, że `Handle` metoda w [przykładzie programu obsługi](#security-authorization-handler-example) zwraca żadnej wartości. W jaki sposób wskazuje się status sukcesu lub porażki?

* Program obsługi wskazuje sukces `context.Succeed(IAuthorizationRequirement requirement)`przez wywołanie , przekazywanie wymagania, które zostało pomyślnie zatwierdzone.

* Program obsługi nie musi obsługiwać błędów ogólnie, jak inne programy obsługi dla tego samego wymagania może zakończyć się pomyślnie.

* Aby zagwarantować awarię, nawet jeśli inne `context.Fail`programy obsługi wymagań zakończyć się pomyślnie, wywołaj .

Jeśli wywołano `context.Succeed` `context.Fail`program obsługi lub wszystkie inne programy obsługi są nadal wywoływane. Dzięki temu wymagania do tworzenia skutków ubocznych, takich jak rejestrowanie, które odbywa się, nawet jeśli inny program obsługi pomyślnie zweryfikowane lub nie powiodło się wymagania. Gdy ustawiona na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) właściwość (dostępna w ASP.NET Core 1.1 i nowszych) zwęża wykonywanie programów obsługi, gdy `context.Fail` jest wywoływana. `InvokeHandlersAfterFailure`domyślnie `true`, w którym to przypadku wszystkie programy obsługi są wywoływane.

> [!NOTE]
> Programy obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Dlaczego chcę wiele programów obsługi dla wymagania?

W przypadkach, gdy chcesz, aby ocena była na podstawie **OR,** zaimplementuj wiele programów obsługi dla jednego wymagania. Na przykład Microsoft ma drzwi, które otwierają się tylko za pomocą kart kluczowych. Jeśli zostawisz kartę z kluczem w domu, recepcjonistka wydrukuje tymczasową naklejkę i otworzy drzwi dla Ciebie. W tym scenariuszu masz jedno wymaganie, *BuildingEntry*, ale wiele programów obsługi, każdy z nich badania pojedynczego wymagania.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Jeśli którykolwiek z programów obsługi `BuildingEntryRequirement`powiedzie się, gdy zasada ocenia , oceny zasad powiedzie się.

## <a name="using-a-func-to-fulfill-a-policy"></a>Używanie func do realizacji zasad

Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażenia w kodzie. Jest możliwe, aby `Func<AuthorizationHandlerContext, bool>` podać podczas konfigurowania `RequireAssertion` zasad z konstruktorem zasad.

Na przykład poprzedni `BadgeEntryHandler` może być przepisany w następujący sposób:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Uzyskiwanie dostępu do kontekstu żądania MVC w programach obsługi

Metoda `HandleRequirementAsync` zaimplementowana w programie obsługi `AuthorizationHandlerContext` autoryzacji ma dwa parametry: an i `TRequirement` obsługi. Struktury, takie jak MVC lub SignalR mogą `Resource` dodawać `AuthorizationHandlerContext` dowolny obiekt do właściwości na przekazać dodatkowe informacje.

Podczas korzystania z routingu punktu końcowego autoryzacja jest zazwyczaj obsługiwane przez oprogramowanie pośredniczące autoryzacji. W takim przypadku `Resource` właściwość jest <xref:Microsoft.AspNetCore.Http.Endpoint>wystąpieniem . Punkt końcowy może służyć do sondowania zasobu źródłowego, do którego jesteś routingu. Przykład:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

W przypadku tradycyjnego routingu lub gdy autoryzacja odbywa się `Resource` jako <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> część filtru autoryzacji MVC, wartość jest wystąpieniem. Ta właściwość `HttpContext`zapewnia `RouteData`dostęp do , i wszystko inne dostarczone przez MVC i Razor Pages.

Korzystanie z `Resource` właściwości jest specyficzne dla ram. Korzystanie z `Resource` informacji we właściwości ogranicza zasady autoryzacji do określonych struktur. Należy oddać `Resource` właściwość `is` przy użyciu słowa kluczowego, a następnie potwierdzić, że `InvalidCastException` oddanych udało się upewnić, że kod nie upaść z po uruchomieniu na innych platformach:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
