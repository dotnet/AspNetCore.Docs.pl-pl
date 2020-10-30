---
title: Autoryzacja na podstawie widoku w ASP.NET Core MVC
author: rick-anderson
description: 'W tym dokumencie pokazano, jak wstrzyknąć i wykorzystać usługę autoryzacji w :::no-loc(Razor)::: widoku ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061330"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="75e68-103">Autoryzacja na podstawie widoku w ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="75e68-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="75e68-104">Deweloper często chce pokazać, ukryć lub zmodyfikować inny interfejs użytkownika w oparciu o bieżącą tożsamość użytkownika.</span><span class="sxs-lookup"><span data-stu-id="75e68-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="75e68-105">Dostęp do usługi autoryzacji można uzyskać w widokach MVC za pośrednictwem [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75e68-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75e68-106">Aby wstrzyknąć usługę autoryzacji do :::no-loc(Razor)::: widoku, użyj `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="75e68-106">To inject the authorization service into a :::no-loc(Razor)::: view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="75e68-107">Jeśli chcesz, aby usługa autoryzacji była w każdym widoku, umieść `@inject` dyrektywę w pliku *_ViewImports. cshtml* w katalogu *widoków* .</span><span class="sxs-lookup"><span data-stu-id="75e68-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="75e68-108">Aby uzyskać więcej informacji, zobacz [iniekcja zależności w widokach](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75e68-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="75e68-109">Użyj wstrzykniętej usługi autoryzacji do wywołania `AuthorizeAsync` w taki sam sposób, jak w przypadku [autoryzacji opartej na zasobach](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="75e68-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="75e68-110">W niektórych przypadkach zasób będzie modelem widoku.</span><span class="sxs-lookup"><span data-stu-id="75e68-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="75e68-111">Wywołaj `AuthorizeAsync` w taki sam sposób, jak podczas [autoryzacji opartej na zasobach](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="75e68-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="75e68-112">W poprzednim kodzie model jest przenoszona jako zasób, a Ocena zasad powinna być uwzględniana.</span><span class="sxs-lookup"><span data-stu-id="75e68-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="75e68-113">Nie należy polegać na przełączaniu widoczności elementów interfejsu użytkownika aplikacji jako pojedynczej kontroli autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="75e68-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="75e68-114">Ukrycie elementu interfejsu użytkownika może nie całkowicie uniemożliwić dostęp do jego skojarzonej akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="75e68-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="75e68-115">Rozważmy na przykład przycisk w powyższym fragmencie kodu.</span><span class="sxs-lookup"><span data-stu-id="75e68-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="75e68-116">Użytkownik może wywołać `Edit` metodę akcji, jeśli wie, że adres URL zasobu względnego to */Document/Edit/1* .</span><span class="sxs-lookup"><span data-stu-id="75e68-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1* .</span></span> <span data-ttu-id="75e68-117">Z tego powodu `Edit` Metoda działania powinna wykonywać własne sprawdzanie autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="75e68-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
