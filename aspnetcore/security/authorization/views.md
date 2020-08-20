---
title: Autoryzacja na podstawie widoku w ASP.NET Core MVC
author: rick-anderson
description: W tym dokumencie pokazano, jak wstrzyknąć i wykorzystać usługę autoryzacji w Razor widoku ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 775ebdffe2b0753de18bf07d9ff1193235a45b17
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629889"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="a7923-103">Autoryzacja na podstawie widoku w ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a7923-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="a7923-104">Deweloper często chce pokazać, ukryć lub zmodyfikować inny interfejs użytkownika w oparciu o bieżącą tożsamość użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a7923-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="a7923-105">Dostęp do usługi autoryzacji można uzyskać w widokach MVC za pośrednictwem [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a7923-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a7923-106">Aby wstrzyknąć usługę autoryzacji do Razor widoku, użyj `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="a7923-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="a7923-107">Jeśli chcesz, aby usługa autoryzacji była w każdym widoku, umieść `@inject` dyrektywę w pliku *_ViewImports. cshtml* w katalogu *widoków* .</span><span class="sxs-lookup"><span data-stu-id="a7923-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="a7923-108">Aby uzyskać więcej informacji, zobacz [iniekcja zależności w widokach](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a7923-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="a7923-109">Użyj wstrzykniętej usługi autoryzacji do wywołania `AuthorizeAsync` w taki sam sposób, jak w przypadku [autoryzacji opartej na zasobach](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="a7923-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="a7923-110">W niektórych przypadkach zasób będzie modelem widoku.</span><span class="sxs-lookup"><span data-stu-id="a7923-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="a7923-111">Wywołaj `AuthorizeAsync` w taki sam sposób, jak podczas [autoryzacji opartej na zasobach](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="a7923-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="a7923-112">W poprzednim kodzie model jest przenoszona jako zasób, a Ocena zasad powinna być uwzględniana.</span><span class="sxs-lookup"><span data-stu-id="a7923-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="a7923-113">Nie należy polegać na przełączaniu widoczności elementów interfejsu użytkownika aplikacji jako pojedynczej kontroli autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a7923-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="a7923-114">Ukrycie elementu interfejsu użytkownika może nie całkowicie uniemożliwić dostęp do jego skojarzonej akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a7923-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="a7923-115">Rozważmy na przykład przycisk w powyższym fragmencie kodu.</span><span class="sxs-lookup"><span data-stu-id="a7923-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="a7923-116">Użytkownik może wywołać `Edit` metodę akcji, jeśli wie, że adres URL zasobu względnego to */Document/Edit/1*.</span><span class="sxs-lookup"><span data-stu-id="a7923-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="a7923-117">Z tego powodu `Edit` Metoda działania powinna wykonywać własne sprawdzanie autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a7923-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
