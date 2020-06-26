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
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="ee420-103">Schematy zasad w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee420-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="ee420-104">Schematy zasad uwierzytelniania ułatwiają korzystanie z wielu metod logicznego uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ee420-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="ee420-105">Na przykład schemat zasad może korzystać z uwierzytelniania Google na potrzeby wyzwań i uwierzytelniania plików cookie dla wszystkich innych.</span><span class="sxs-lookup"><span data-stu-id="ee420-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="ee420-106">Systemy zasad uwierzytelniania sprawiają, że:</span><span class="sxs-lookup"><span data-stu-id="ee420-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="ee420-107">Łatwa do przodu jakakolwiek akcja uwierzytelniania w innym schemacie.</span><span class="sxs-lookup"><span data-stu-id="ee420-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="ee420-108">Przekazywanie dynamicznie na podstawie żądania.</span><span class="sxs-lookup"><span data-stu-id="ee420-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="ee420-109">Wszystkie schematy uwierzytelniania używające pochodnych <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> i skojarzonych [ \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="ee420-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="ee420-110">Są automatycznie schematami zasad w ASP.NET Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="ee420-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="ee420-111">Można ją włączyć za pomocą konfiguracji opcji schematu.</span><span class="sxs-lookup"><span data-stu-id="ee420-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="ee420-112">Przykłady</span><span class="sxs-lookup"><span data-stu-id="ee420-112">Examples</span></span>

<span data-ttu-id="ee420-113">Poniższy przykład przedstawia schemat wyższego poziomu, który łączy schematy niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="ee420-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="ee420-114">Uwierzytelnianie Google jest używane na potrzeby wyzwań, a uwierzytelnianie plików cookie jest używane dla wszystkiego innego:</span><span class="sxs-lookup"><span data-stu-id="ee420-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="ee420-115">W poniższym przykładzie jest włączany dynamiczny Wybór schematów dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="ee420-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="ee420-116">Oznacza to, jak mieszać pliki cookie i uwierzytelnianie interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="ee420-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
