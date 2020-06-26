---
title: Schematy zasad w ASP.NET Core
author: rick-anderson
description: Schematy zasad uwierzytelniania ułatwiają korzystanie z jednego schematu uwierzytelniania logicznego
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: a8bde9633f06f41ebcb55480eb2322544db4b4da
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408763"
---
# <a name="policy-schemes-in-aspnet-core"></a>Schematy zasad w ASP.NET Core

Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania. Na przykład schemat zasad może korzystać z uwierzytelniania Google na potrzeby wyzwań i uwierzytelniania plików cookie dla wszystkich innych. Systemy zasad uwierzytelniania sprawiają, że:

* Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.
* Przekazywanie dynamicznie na podstawie żądania.

Wszystkie schematy uwierzytelniania używające pochodnych <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzonych [ \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.
* Można ją włączyć za pomocą konfiguracji opcji schematu.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Przykłady

Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu. Uwierzytelnianie Google jest używane na potrzeby wyzwań, a uwierzytelnianie plików cookie jest używane dla wszystkiego innego:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania. Oznacza to, jak mieszać pliki cookie i uwierzytelnianie interfejsu API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
