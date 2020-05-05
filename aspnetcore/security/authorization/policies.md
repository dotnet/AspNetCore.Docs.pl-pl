---
title: Autoryzacja oparta na zasadach w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć i używać programów obsługi zasad autoryzacji w celu wymuszania wymagań autoryzacji w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 3b6fcef91355bf22e5aa185652d9489a44998db0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777504"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autoryzacja oparta na zasadach w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Zgodnie z założeniami, [Autoryzacja oparta na rolach](xref:security/authorization/roles) i [Autoryzacja oparta na oświadczeniach](xref:security/authorization/claims) używają wymagań, obsługi wymagań i wstępnie skonfigurowanych zasad. Te bloki konstrukcyjne obsługują wyrażenie oceny autoryzacji w kodzie. Wynikiem jest rozbudowana struktura autoryzacji do wielokrotnego użytku weryfikowalne.

Zasady autoryzacji składają się z co najmniej jednego wymagania. Jest ona zarejestrowana w ramach konfiguracji usługi autoryzacji w ramach `Startup.ConfigureServices` metody:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

W poprzednim przykładzie tworzone są zasady "AtLeast21". Ma jedno wymaganie&mdash;o minimalnym wieku, które jest dostarczane jako parametr do wymagania.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Podstawowa usługa, która określa, czy autoryzacja powiodła się <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Poprzedni kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>to usługa znacznika bez metod i mechanizm śledzenia, czy autoryzacja zakończyła się pomyślnie.

Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> jest odpowiedzialny za sprawdzenie, czy są spełnione wymagania:
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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasa jest wykorzystywana przez program obsługi do oznaczania, czy zostały spełnione wymagania:

```csharp
 context.Succeed(requirement)
```

Poniższy kod przedstawia uproszczoną (i adnotację z komentarzami) domyślną implementację usługi autoryzacji:

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

Poniższy kod przedstawia typowy `ConfigureServices`:

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

Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> lub `[Authorize(Policy = "Something")]` do autoryzacji.

## <a name="applying-policies-to-mvc-controllers"></a>Stosowanie zasad do kontrolerów MVC

Jeśli używasz Razor stron, zobacz [stosowanie zasad do Razor stron](#applying-policies-to-razor-pages) w tym dokumencie.

Zasady są stosowane do kontrolerów przy użyciu `[Authorize]` atrybutu z nazwą zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Stosowanie zasad do Razor stron

Zasady są stosowane do Razor stron przy użyciu `[Authorize]` atrybutu z nazwą zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zasady mogą być również stosowane do Razor stron przy użyciu [Konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Wymagania

Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą użyć do oszacowania bieżącego podmiotu użytkownika. W naszych zasadach "AtLeast21" wymagany jest jeden parametr&mdash;o minimalnym wieku. Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pustym interfejsem znacznika. Minimalny wymóg sparametryzowanego wieku można zaimplementować w następujący sposób:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Jeśli zasady autoryzacji zawierają wiele wymagań autoryzacji, wszystkie wymagania muszą zostać spełnione, aby Ocena zasad powiodła się. Innymi słowy, wiele wymagań autoryzacji dodanych do pojedynczych zasad autoryzacji jest traktowanych **na zasadzie.**

> [!NOTE]
> Wymagania nie muszą mieć danych ani właściwości.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Programy obsługi autoryzacji

Procedura obsługi autoryzacji jest odpowiedzialna za ocenę właściwości wymagania. Procedura obsługi autoryzacji szacuje wymagania w odniesieniu do podanej [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) w celu określenia, czy dostęp jest dozwolony.

Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers). Program obsługi może odziedziczyć [\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` gdzie to wymaganie do obsługi. Alternatywnie program obsługi może zaimplementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) , aby obsłużyć więcej niż jeden typ wymagania.

### <a name="use-a-handler-for-one-requirement"></a>Użyj procedury obsługi dla jednego wymagania

<a name="security-authorization-handler-example"></a>

Poniżej znajduje się przykład relacji jeden-do-jednego, w której program obsługi minimalnych okresów używa jednego wymagania:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Poprzedni kod określa, czy bieżący podmiot użytkownika ma datę żądania urodzenia, który został wystawiony przez znanego i zaufanego wystawcy. Autoryzacja nie może wystąpić w przypadku braku żądania. w takim przypadku zwracane jest zadanie wykonane. W przypadku wystąpienia zgłoszenia jest naliczany wiek użytkownika. Jeśli użytkownik spełni minimalny wiek zdefiniowany przez to wymaganie, autoryzacja zostanie uznana za pomyślnie. Gdy autoryzacja powiedzie `context.Succeed` się, jest wywoływana z wymaganym parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Korzystanie z programu obsługi dla wielu wymagań

Poniżej znajduje się przykład relacji jeden-do-wielu, w której program obsługi uprawnień może obsłużyć trzy różne typy wymagań:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Poprzedni kod przechodzi przez [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;właściwości zawierającej wymagania, które nie są oznaczone jako pomyślne. `ReadPermission` Aby uzyskać dostęp do żądanego zasobu, użytkownik musi być właścicielem lub sponsorem. W przypadku `EditPermission` lub `DeletePermission` wymagania musi być właścicielem, aby uzyskać dostęp do żądanego zasobu.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Rejestracja procedury obsługi

Procedury obsługi są rejestrowane w kolekcji usług podczas konfiguracji. Przykład:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Poprzedni kod rejestruje `MinimumAgeHandler` jako pojedyncze przez wywołanie `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Programy obsługi można zarejestrować przy użyciu dowolnego z wbudowanych [okresów istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co ma zwrócić program obsługi?

Należy zauważyć, `Handle` że metoda w [przykładzie procedury obsługi](#security-authorization-handler-example) nie zwraca żadnej wartości. Jak jest wskazywany stan sukcesu lub niepowodzenia?

* Procedura obsługi wskazuje powodzenie przez wywołanie `context.Succeed(IAuthorizationRequirement requirement)`, przekazanie wymagania, które zostało pomyślnie zweryfikowane.

* Program obsługi nie musi ogólnie obsługiwać błędów, ponieważ inne procedury obsługi tego samego wymagania mogą się powieść.

* W celu zagwarantowania niepowodzenia, nawet w przypadku pomyślnego zajścia innych wymagań `context.Fail`.

Jeśli program obsługi wywołuje `context.Succeed` lub `context.Fail`, wszystkie inne procedury obsługi są nadal wywoływane. Pozwala to na spełnienie wymagań związanych z generowaniem efektów ubocznych, takich jak rejestrowanie, które odbywa się nawet w przypadku pomyślnej weryfikacji lub niepowodzenia przez inną procedurę obsługi. Po ustawieniu na `false`Właściwość [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (dostępna w ASP.NET Core 1,1 i nowszych) krótkie obwody wykonywania programów obsługi po `context.Fail` wywołaniu. `InvokeHandlersAfterFailure`wartość domyślna `true`to, w którym to przypadku są wywoływane wszystkie programy obsługi.

> [!NOTE]
> Procedury obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Dlaczego chcesz mieć wiele programów obsługi wymagań?

W przypadkach, w których Ocena ma być przeprowadzana na podstawie **lub** , należy zaimplementować wiele programów obsługi dla jednego wymagania. Na przykład firma Microsoft ma drzwi, które są otwierane tylko za pomocą kart kluczowych. Jeśli opuścisz kartę kluczową w domu, recepcjonista drukuje tymczasowy naklejkę i otwiera drzwiczki. W tym scenariuszu istnieje jedno wymaganie, *BuildingEntry*, ale wiele programów obsługi, każdy z nich bada pojedyncze wymaganie.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Jeśli jedna z programów obsługi powiedzie się `BuildingEntryRequirement`, gdy zasady oceniają, Ocena zasad zakończy się pomyślnie.

## <a name="using-a-func-to-fulfill-a-policy"></a>Korzystanie z funkcji Func do realizacji zasad

Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażania w kodzie. Podczas konfigurowania zasad przy użyciu konstruktora `Func<AuthorizationHandlerContext, bool>` `RequireAssertion` zasad można podać wartość.

Na przykład poprzedni `BadgeEntryHandler` można napisać ponownie w następujący sposób:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Uzyskiwanie dostępu do kontekstu żądania MVC w programach obsługi

`HandleRequirementAsync` Metoda zaimplementowana w procedurze obsługi autoryzacji ma dwa parametry: `AuthorizationHandlerContext` a i `TRequirement` obsługujące. Struktury, takie jak MVC lub Jabbr, są bezpłatne, aby dodać każdy obiekt `Resource` do właściwości w `AuthorizationHandlerContext` celu przekazania dodatkowych informacji.

Na przykład, MVC przekazuje wystąpienie elementu [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) we `Resource` właściwości. Ta właściwość zapewnia dostęp do `HttpContext`elementów `RouteData`, i innych udostępnianych przez MVC i Razor Pages.

Użycie `Resource` właściwości jest specyficzne dla struktury. Użycie informacji we `Resource` właściwości ogranicza zasady autoryzacji do określonych struktur. Należy rzutować `Resource` Właściwość za pomocą `is` słowa kluczowego, a następnie potwierdzić, że rzutowanie zakończyło się pomyślnie, aby upewnić się, że kod nie ulegnie awarii z `InvalidCastException` uruchomieniem w innych strukturach:

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

Zgodnie z założeniami, [Autoryzacja oparta na rolach](xref:security/authorization/roles) i [Autoryzacja oparta na oświadczeniach](xref:security/authorization/claims) używają wymagań, obsługi wymagań i wstępnie skonfigurowanych zasad. Te bloki konstrukcyjne obsługują wyrażenie oceny autoryzacji w kodzie. Wynikiem jest rozbudowana struktura autoryzacji do wielokrotnego użytku weryfikowalne.

Zasady autoryzacji składają się z co najmniej jednego wymagania. Jest ona zarejestrowana w ramach konfiguracji usługi autoryzacji w ramach `Startup.ConfigureServices` metody:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

W poprzednim przykładzie tworzone są zasady "AtLeast21". Ma jedno wymaganie&mdash;o minimalnym wieku, które jest dostarczane jako parametr do wymagania.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Podstawowa usługa, która określa, czy autoryzacja powiodła się <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Poprzedni kod wyróżnia dwie metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>to usługa znacznika bez metod i mechanizm śledzenia, czy autoryzacja zakończyła się pomyślnie.

Każdy <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> jest odpowiedzialny za sprawdzenie, czy są spełnione wymagania:
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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasa jest wykorzystywana przez program obsługi do oznaczania, czy zostały spełnione wymagania:

```csharp
 context.Succeed(requirement)
```

Poniższy kod przedstawia uproszczoną (i adnotację z komentarzami) domyślną implementację usługi autoryzacji:

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

Poniższy kod przedstawia typowy `ConfigureServices`:

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

Użyj <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> lub `[Authorize(Policy = "Something")]` do autoryzacji.

## <a name="applying-policies-to-mvc-controllers"></a>Stosowanie zasad do kontrolerów MVC

Jeśli używasz Razor stron, zobacz [stosowanie zasad do Razor stron](#applying-policies-to-razor-pages) w tym dokumencie.

Zasady są stosowane do kontrolerów przy użyciu `[Authorize]` atrybutu z nazwą zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Stosowanie zasad do Razor stron

Zasady są stosowane do Razor stron przy użyciu `[Authorize]` atrybutu z nazwą zasad. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zasady mogą być również stosowane do Razor stron przy użyciu [Konwencji autoryzacji](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Wymagania

Wymaganie autoryzacji to zbiór parametrów danych, których zasady mogą użyć do oszacowania bieżącego podmiotu użytkownika. W naszych zasadach "AtLeast21" wymagany jest jeden parametr&mdash;o minimalnym wieku. Wymaganie implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), który jest pustym interfejsem znacznika. Minimalny wymóg sparametryzowanego wieku można zaimplementować w następujący sposób:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Jeśli zasady autoryzacji zawierają wiele wymagań autoryzacji, wszystkie wymagania muszą zostać spełnione, aby Ocena zasad powiodła się. Innymi słowy, wiele wymagań autoryzacji dodanych do pojedynczych zasad autoryzacji jest traktowanych **na zasadzie.**

> [!NOTE]
> Wymagania nie muszą mieć danych ani właściwości.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Programy obsługi autoryzacji

Procedura obsługi autoryzacji jest odpowiedzialna za ocenę właściwości wymagania. Procedura obsługi autoryzacji szacuje wymagania w odniesieniu do podanej [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) w celu określenia, czy dostęp jest dozwolony.

Wymaganie może mieć [wiele programów obsługi](#security-authorization-policies-based-multiple-handlers). Program obsługi może odziedziczyć [\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` gdzie to wymaganie do obsługi. Alternatywnie program obsługi może zaimplementować [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) , aby obsłużyć więcej niż jeden typ wymagania.

### <a name="use-a-handler-for-one-requirement"></a>Użyj procedury obsługi dla jednego wymagania

<a name="security-authorization-handler-example"></a>

Poniżej znajduje się przykład relacji jeden-do-jednego, w której program obsługi minimalnych okresów używa jednego wymagania:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Poprzedni kod określa, czy bieżący podmiot użytkownika ma datę żądania urodzenia, który został wystawiony przez znanego i zaufanego wystawcy. Autoryzacja nie może wystąpić w przypadku braku żądania. w takim przypadku zwracane jest zadanie wykonane. W przypadku wystąpienia zgłoszenia jest naliczany wiek użytkownika. Jeśli użytkownik spełni minimalny wiek zdefiniowany przez to wymaganie, autoryzacja zostanie uznana za pomyślnie. Gdy autoryzacja powiedzie `context.Succeed` się, jest wywoływana z wymaganym parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Korzystanie z programu obsługi dla wielu wymagań

Poniżej znajduje się przykład relacji jeden-do-wielu, w której program obsługi uprawnień może obsłużyć trzy różne typy wymagań:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Poprzedni kod przechodzi przez [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;właściwości zawierającej wymagania, które nie są oznaczone jako pomyślne. `ReadPermission` Aby uzyskać dostęp do żądanego zasobu, użytkownik musi być właścicielem lub sponsorem. W przypadku `EditPermission` lub `DeletePermission` wymagania musi być właścicielem, aby uzyskać dostęp do żądanego zasobu.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Rejestracja procedury obsługi

Procedury obsługi są rejestrowane w kolekcji usług podczas konfiguracji. Przykład:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Poprzedni kod rejestruje `MinimumAgeHandler` jako pojedyncze przez wywołanie `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Programy obsługi można zarejestrować przy użyciu dowolnego z wbudowanych [okresów istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co ma zwrócić program obsługi?

Należy zauważyć, `Handle` że metoda w [przykładzie procedury obsługi](#security-authorization-handler-example) nie zwraca żadnej wartości. Jak jest wskazywany stan sukcesu lub niepowodzenia?

* Procedura obsługi wskazuje powodzenie przez wywołanie `context.Succeed(IAuthorizationRequirement requirement)`, przekazanie wymagania, które zostało pomyślnie zweryfikowane.

* Program obsługi nie musi ogólnie obsługiwać błędów, ponieważ inne procedury obsługi tego samego wymagania mogą się powieść.

* W celu zagwarantowania niepowodzenia, nawet w przypadku pomyślnego zajścia innych wymagań `context.Fail`.

Jeśli program obsługi wywołuje `context.Succeed` lub `context.Fail`, wszystkie inne procedury obsługi są nadal wywoływane. Pozwala to na spełnienie wymagań związanych z generowaniem efektów ubocznych, takich jak rejestrowanie, które odbywa się nawet w przypadku pomyślnej weryfikacji lub niepowodzenia przez inną procedurę obsługi. Po ustawieniu na `false`Właściwość [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (dostępna w ASP.NET Core 1,1 i nowszych) krótkie obwody wykonywania programów obsługi po `context.Fail` wywołaniu. `InvokeHandlersAfterFailure`wartość domyślna `true`to, w którym to przypadku są wywoływane wszystkie programy obsługi.

> [!NOTE]
> Procedury obsługi autoryzacji są wywoływane, nawet jeśli uwierzytelnianie nie powiedzie się.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Dlaczego chcesz mieć wiele programów obsługi wymagań?

W przypadkach, w których Ocena ma być przeprowadzana na podstawie **lub** , należy zaimplementować wiele programów obsługi dla jednego wymagania. Na przykład firma Microsoft ma drzwi, które są otwierane tylko za pomocą kart kluczowych. Jeśli opuścisz kartę kluczową w domu, recepcjonista drukuje tymczasowy naklejkę i otwiera drzwiczki. W tym scenariuszu istnieje jedno wymaganie, *BuildingEntry*, ale wiele programów obsługi, każdy z nich bada pojedyncze wymaganie.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Upewnij się, że oba programy obsługi są [zarejestrowane](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Jeśli jedna z programów obsługi powiedzie się `BuildingEntryRequirement`, gdy zasady oceniają, Ocena zasad zakończy się pomyślnie.

## <a name="using-a-func-to-fulfill-a-policy"></a>Korzystanie z funkcji Func do realizacji zasad

Mogą wystąpić sytuacje, w których spełnienie zasad jest proste do wyrażania w kodzie. Podczas konfigurowania zasad przy użyciu konstruktora `Func<AuthorizationHandlerContext, bool>` `RequireAssertion` zasad można podać wartość.

Na przykład poprzedni `BadgeEntryHandler` można napisać ponownie w następujący sposób:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Uzyskiwanie dostępu do kontekstu żądania MVC w programach obsługi

`HandleRequirementAsync` Metoda zaimplementowana w procedurze obsługi autoryzacji ma dwa parametry: `AuthorizationHandlerContext` a i `TRequirement` obsługujące. Platformy, takie jak MVC lub SignalR , mogą dodawać dowolne obiekty do `Resource` właściwości w `AuthorizationHandlerContext` celu przekazania dodatkowych informacji.

W przypadku korzystania z routingu punktów końcowych Autoryzacja jest zwykle obsługiwana przez oprogramowanie pośredniczące autoryzacji. W tym przypadku `Resource` właściwość jest wystąpieniem <xref:Microsoft.AspNetCore.Http.Endpoint>. Punkt końcowy może służyć do sondowania bazowego zasobu, z którym jest przeprowadzana Routing. Przykład:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Przy użyciu tradycyjnego routingu lub gdy autoryzacja występuje w ramach filtru autoryzacji MVC, wartość `Resource` jest <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> wystąpieniem. Ta właściwość zapewnia dostęp do `HttpContext`elementów `RouteData`, i innych udostępnianych przez MVC i Razor Pages.

Użycie `Resource` właściwości jest specyficzne dla struktury. Użycie informacji we `Resource` właściwości ogranicza zasady autoryzacji do określonych struktur. Należy rzutować `Resource` Właściwość za pomocą `is` słowa kluczowego, a następnie potwierdzić, że rzutowanie zakończyło się pomyślnie, aby upewnić się, że kod nie ulegnie awarii z `InvalidCastException` uruchomieniem w innych strukturach:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
