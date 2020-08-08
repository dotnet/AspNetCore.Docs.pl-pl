---
title: Schematy zasad w ASP.NET Core
author: rick-anderson
description: Schematy zasad uwierzytelniania ułatwiają korzystanie z jednego schematu uwierzytelniania logicznego
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: ddee613bf9c603542f17adf59a835a2ddbdc25a3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017808"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="0edab-103">Schematy zasad w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0edab-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="0edab-104">Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="0edab-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="0edab-105">Na przykład schemat zasad może korzystać z usługi Google Authentication na potrzeby wyzwań i cookie uwierzytelniania dla wszystkich innych.</span><span class="sxs-lookup"><span data-stu-id="0edab-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="0edab-106">Systemy zasad uwierzytelniania sprawiają, że:</span><span class="sxs-lookup"><span data-stu-id="0edab-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="0edab-107">Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.</span><span class="sxs-lookup"><span data-stu-id="0edab-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="0edab-108">Przekazywanie dynamicznie na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="0edab-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="0edab-109">Wszystkie schematy uwierzytelniania używające pochodnych <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzonych [ \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="0edab-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="0edab-110">Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="0edab-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="0edab-111">Można ją włączyć za pomocą konfiguracji opcji schematu.</span><span class="sxs-lookup"><span data-stu-id="0edab-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="0edab-112">Przykłady</span><span class="sxs-lookup"><span data-stu-id="0edab-112">Examples</span></span>

<span data-ttu-id="0edab-113">Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="0edab-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="0edab-114">Uwierzytelnianie Google jest używane na potrzeby wyzwań, a cookie uwierzytelnianie jest używane dla wszystkiego innego:</span><span class="sxs-lookup"><span data-stu-id="0edab-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="0edab-115">W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="0edab-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="0edab-116">Oznacza to, jak zmieszać metodę cookie uwierzytelniania interfejsu API i usługi:</span><span class="sxs-lookup"><span data-stu-id="0edab-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
