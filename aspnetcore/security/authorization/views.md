---
title: Autoryzacja na podstawie widoku w ASP.NET Core MVC
author: rick-anderson
description: W tym dokumencie pokazano, jak wstrzyknąć i wykorzystać usługę autoryzacji w widoku ASP.NET Core Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775625"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Autoryzacja na podstawie widoku w ASP.NET Core MVC

Deweloper często chce pokazać, ukryć lub zmodyfikować inny interfejs użytkownika w oparciu o bieżącą tożsamość użytkownika. Dostęp do usługi autoryzacji można uzyskać w widokach MVC za pośrednictwem [iniekcji zależności](xref:fundamentals/dependency-injection). Aby wstrzyknąć usługę autoryzacji do Razor widoku, użyj `@inject` dyrektywy:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Jeśli chcesz, aby usługa autoryzacji była w każdym widoku, umieść `@inject` dyrektywę w pliku *_ViewImports. cshtml* w katalogu *widoków* . Aby uzyskać więcej informacji, zobacz [iniekcja zależności w widokach](xref:mvc/views/dependency-injection).

Użyj wstrzykniętej usługi autoryzacji do wywołania `AuthorizeAsync` w taki sam sposób, jak w przypadku [autoryzacji opartej na zasobach](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

W niektórych przypadkach zasób będzie modelem widoku. Wywołaj `AuthorizeAsync` w taki sam sposób, jak podczas [autoryzacji opartej na zasobach](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

W poprzednim kodzie model jest przenoszona jako zasób, a Ocena zasad powinna być uwzględniana.

> [!WARNING]
> Nie należy polegać na przełączaniu widoczności elementów interfejsu użytkownika aplikacji jako pojedynczej kontroli autoryzacji. Ukrycie elementu interfejsu użytkownika może nie całkowicie uniemożliwić dostęp do jego skojarzonej akcji kontrolera. Rozważmy na przykład przycisk w powyższym fragmencie kodu. Użytkownik może wywołać metodę akcji `Edit` , jeśli wie, że adres URL zasobu względnego to */Document/Edit/1*. Z `Edit` tego powodu metoda działania powinna wykonywać własne sprawdzanie autoryzacji.
