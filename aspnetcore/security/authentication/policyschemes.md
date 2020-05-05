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
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="7eef2-103">Schematy zasad w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7eef2-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="7eef2-104">Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7eef2-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="7eef2-105">Na przykład schemat zasad może korzystać z uwierzytelniania Google na potrzeby wyzwań i uwierzytelniania plików cookie dla wszystkich innych.</span><span class="sxs-lookup"><span data-stu-id="7eef2-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="7eef2-106">Systemy zasad uwierzytelniania sprawiają, że:</span><span class="sxs-lookup"><span data-stu-id="7eef2-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="7eef2-107">Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.</span><span class="sxs-lookup"><span data-stu-id="7eef2-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="7eef2-108">Przekazywanie dynamicznie na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="7eef2-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="7eef2-109">Wszystkie schematy uwierzytelniania wykorzystujące pochodne <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzone [>TOptions\<AuthenticationHandler ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="7eef2-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="7eef2-110">Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="7eef2-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="7eef2-111">Można ją włączyć za pomocą konfiguracji opcji schematu.</span><span class="sxs-lookup"><span data-stu-id="7eef2-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="7eef2-112">Przykłady</span><span class="sxs-lookup"><span data-stu-id="7eef2-112">Examples</span></span>

<span data-ttu-id="7eef2-113">Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="7eef2-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="7eef2-114">Uwierzytelnianie Google jest używane na potrzeby wyzwań, a uwierzytelnianie plików cookie jest używane dla wszystkiego innego:</span><span class="sxs-lookup"><span data-stu-id="7eef2-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="7eef2-115">W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="7eef2-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="7eef2-116">Oznacza to, jak mieszać pliki cookie i uwierzytelnianie interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="7eef2-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
