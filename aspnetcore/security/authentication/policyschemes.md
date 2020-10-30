---
title: Schematy zasad w ASP.NET Core
author: rick-anderson
description: Schematy zasad uwierzytelniania ułatwiają korzystanie z jednego schematu uwierzytelniania logicznego
ms.author: riande
ms.date: 12/05/2019
no-loc:
- appsettings.json
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053231"
---
# <a name="policy-schemes-in-aspnet-core"></a>Schematy zasad w ASP.NET Core

Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania. Na przykład schemat zasad może korzystać z usługi Google Authentication na potrzeby wyzwań i cookie uwierzytelniania dla wszystkich innych. Systemy zasad uwierzytelniania sprawiają, że:

* Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.
* Przekazywanie dynamicznie na podstawie żądania.

Wszystkie schematy uwierzytelniania używające pochodnych <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzonych [ \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.
* Można ją włączyć za pomocą konfiguracji opcji schematu.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Przykłady

Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu. Uwierzytelnianie Google jest używane na potrzeby wyzwań, a cookie uwierzytelnianie jest używane dla wszystkiego innego:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania. Oznacza to, jak zmieszać metodę cookie uwierzytelniania interfejsu API i usługi:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
