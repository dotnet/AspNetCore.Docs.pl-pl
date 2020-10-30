---
title: Wprowadzenie do autoryzacji w ASP.NET Core
author: rick-anderson
description: Poznaj podstawowe informacje dotyczące autoryzacji i sposobu działania autoryzacji w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060251"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="a0a65-103">Wprowadzenie do autoryzacji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0a65-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="a0a65-104">Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić.</span><span class="sxs-lookup"><span data-stu-id="a0a65-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="a0a65-105">Na przykład użytkownik administracyjny może utworzyć bibliotekę dokumentów, dodać dokumenty, edytować dokumenty i je usunąć.</span><span class="sxs-lookup"><span data-stu-id="a0a65-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="a0a65-106">Użytkownik niebędący użytkownikiem administracyjnym, który współpracuje z biblioteką, ma uprawnienia tylko do odczytu dokumentów.</span><span class="sxs-lookup"><span data-stu-id="a0a65-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="a0a65-107">Autoryzacja jest prostopadła i niezależna od uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a0a65-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="a0a65-108">Autoryzacja wymaga jednak mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a0a65-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="a0a65-109">Uwierzytelnianie to proces ustalania, kto jest użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="a0a65-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="a0a65-110">Uwierzytelnianie może utworzyć jedną lub więcej tożsamości dla bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a0a65-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="a0a65-111">Aby uzyskać więcej informacji na temat uwierzytelniania w ASP.NET Core, zobacz <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="a0a65-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="a0a65-112">Typy autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a0a65-112">Authorization types</span></span>

<span data-ttu-id="a0a65-113">Autoryzacja ASP.NET Core zapewnia prostą, deklaratywną [rolę](xref:security/authorization/roles) i bogaty model [oparty na zasadach](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="a0a65-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="a0a65-114">Autoryzacja jest wyrażona w wymaganiach, a programy obsługi szacują oświadczenia użytkownika względem wymagań.</span><span class="sxs-lookup"><span data-stu-id="a0a65-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="a0a65-115">Bezwzględne kontrole mogą opierać się na prostych zasadach lub zasadach, które obliczają zarówno tożsamość użytkownika, jak i właściwości zasobu, do którego użytkownik próbuje uzyskać dostęp.</span><span class="sxs-lookup"><span data-stu-id="a0a65-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="a0a65-116">Przestrzenie nazw</span><span class="sxs-lookup"><span data-stu-id="a0a65-116">Namespaces</span></span>

<span data-ttu-id="a0a65-117">`AuthorizeAttribute` `AllowAnonymousAttribute` W przestrzeni nazw znajdują się składniki autoryzacji, w tym atrybuty i `Microsoft.AspNetCore.Authorization` .</span><span class="sxs-lookup"><span data-stu-id="a0a65-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="a0a65-118">Zapoznaj się z dokumentacją dotyczącą [prostej autoryzacji](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="a0a65-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
