---
title: Dostosowywanie zachowania klasy AuthorizationMiddleware
author: pranavkm
ms.author: prkrishn
description: W tym artykule wyjaśniono, jak dostosować obsługę wyników AuthorizationMiddleware.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156785"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="11c6e-103">Dostosowywanie zachowania klasy AuthorizationMiddleware</span><span class="sxs-lookup"><span data-stu-id="11c6e-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="11c6e-104">Aplikacje mogą zarejestrować `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` się, aby dostosować sposób, w jaki oprogramowanie pośredniczące obsługuje wyniki autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="11c6e-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="11c6e-105">Aplikacje mogą korzystać z dostosowanego oprogramowania pośredniczącego w celu:</span><span class="sxs-lookup"><span data-stu-id="11c6e-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="11c6e-106">Zwracaj dostosowane odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11c6e-106">Return customized responses.</span></span>
* <span data-ttu-id="11c6e-107">Zwiększ domyślne wyzwanie lub Zabroń odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="11c6e-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="11c6e-108">Poniższy kod przedstawia przykład procedury obsługi autoryzacji, która zwraca niestandardową odpowiedź dla niektórych rodzajów niepowodzeń autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="11c6e-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="11c6e-109">Zarejestruj `MyAuthorizationMiddlewareResultHandler` się w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="11c6e-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->