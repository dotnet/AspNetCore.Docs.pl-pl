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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Dostosowywanie zachowania klasy AuthorizationMiddleware

Aplikacje mogą zarejestrować `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` się, aby dostosować sposób, w jaki oprogramowanie pośredniczące obsługuje wyniki autoryzacji. Aplikacje mogą korzystać z dostosowanego oprogramowania pośredniczącego w celu:

* Zwracaj dostosowane odpowiedzi.
* Zwiększ domyślne wyzwanie lub Zabroń odpowiedzi.

Poniższy kod przedstawia przykład procedury obsługi autoryzacji, która zwraca niestandardową odpowiedź dla niektórych rodzajów niepowodzeń autoryzacji:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Zarejestruj `MyAuthorizationMiddlewareResultHandler` się w `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->