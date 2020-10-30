---
title: Schematy zasad w ASP.NET Core
author: rick-anderson
description: Schematy zasad uwierzytelniania ułatwiają korzystanie z jednego schematu uwierzytelniania logicznego
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053231"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="e4ba1-103">Schematy zasad w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4ba1-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="e4ba1-104">Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="e4ba1-105">Na przykład schemat zasad może korzystać z usługi Google Authentication na potrzeby wyzwań i :::no-loc(cookie)::: uwierzytelniania dla wszystkich innych.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-105">For example, a policy scheme might use Google authentication for challenges, and :::no-loc(cookie)::: authentication for everything else.</span></span> <span data-ttu-id="e4ba1-106">Systemy zasad uwierzytelniania sprawiają, że:</span><span class="sxs-lookup"><span data-stu-id="e4ba1-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="e4ba1-107">Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="e4ba1-108">Przekazywanie dynamicznie na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="e4ba1-109">Wszystkie schematy uwierzytelniania używające pochodnych <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzonych [ \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="e4ba1-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="e4ba1-110">Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="e4ba1-111">Można ją włączyć za pomocą konfiguracji opcji schematu.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="e4ba1-112">Przykłady</span><span class="sxs-lookup"><span data-stu-id="e4ba1-112">Examples</span></span>

<span data-ttu-id="e4ba1-113">Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="e4ba1-114">Uwierzytelnianie Google jest używane na potrzeby wyzwań, a :::no-loc(cookie)::: uwierzytelnianie jest używane dla wszystkiego innego:</span><span class="sxs-lookup"><span data-stu-id="e4ba1-114">Google authentication is used for challenges, and :::no-loc(cookie)::: authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="e4ba1-115">W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="e4ba1-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="e4ba1-116">Oznacza to, jak zmieszać metodę :::no-loc(cookie)::: uwierzytelniania interfejsu API i usługi:</span><span class="sxs-lookup"><span data-stu-id="e4ba1-116">That is, how to mix :::no-loc(cookie):::s and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
