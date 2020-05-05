---
title: Schematy zasad w ASP.NET Core
author: rick-anderson
description: Schematy zasad uwierzytelniania ułatwiają korzystanie z jednego schematu uwierzytelniania logicznego
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767320"
---
# <a name="policy-schemes-in-aspnet-core"></a>Schematy zasad w ASP.NET Core

Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania. Na przykład schemat zasad może korzystać z uwierzytelniania Google na potrzeby wyzwań i uwierzytelniania plików cookie dla wszystkich innych. Systemy zasad uwierzytelniania sprawiają, że:

* Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.
* Przekazywanie dynamicznie na podstawie żądania.

Wszystkie schematy uwierzytelniania wykorzystujące pochodne <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzone [>TOptions\<AuthenticationHandler ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.
* Można ją włączyć za pomocą konfiguracji opcji schematu.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Przykłady

Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu. Uwierzytelnianie Google jest używane na potrzeby wyzwań, a uwierzytelnianie plików cookie jest używane dla wszystkiego innego:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania. Oznacza to, jak mieszać pliki cookie i uwierzytelnianie interfejsu API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
