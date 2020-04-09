---
title: Omówienie ASP.NET podstawowych zabezpieczeń
author: rick-anderson
description: Dowiedz się więcej o uwierzytelniania, autoryzacji i podstawach zabezpieczeń w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 0f8e96fb7d5246e746b95f8907745f849de60e24
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656038"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="139cf-103">Omówienie ASP.NET podstawowych zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="139cf-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="139cf-104">ASP.NET Core umożliwia deweloperom łatwe konfigurowanie zabezpieczeń aplikacji i zarządzanie nimi.</span><span class="sxs-lookup"><span data-stu-id="139cf-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="139cf-105">ASP.NET Core zawiera funkcje zarządzania uwierzytelnianiem, autoryzacją, ochroną danych, wymuszaniem protokołu HTTPS, wpisami tajnymi aplikacji, ochroną przed fałszerskem i zarządzaniem CORS.</span><span class="sxs-lookup"><span data-stu-id="139cf-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="139cf-106">Te funkcje zabezpieczeń umożliwiają tworzenie niezawodnych, ale bezpiecznych aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="139cf-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="139cf-107">ASP.NET Podstawowe funkcje zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="139cf-107">ASP.NET Core security features</span></span>

<span data-ttu-id="139cf-108">ASP.NET Core udostępnia wiele narzędzi i bibliotek do zabezpieczania aplikacji, w tym wbudowanych dostawców tożsamości, ale możesz korzystać z usług tożsamości innych firm, takich jak Facebook, Twitter lub LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="139cf-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="139cf-109">Dzięki ASP.NET Core można łatwo zarządzać wpisami tajnymi aplikacji, które są sposobem przechowywania i używania poufnych informacji bez konieczności ujawniania ich w kodzie.</span><span class="sxs-lookup"><span data-stu-id="139cf-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="139cf-110">Uwierzytelnianie a autoryzacja</span><span class="sxs-lookup"><span data-stu-id="139cf-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="139cf-111">Uwierzytelnianie to proces, w którym użytkownik udostępnia poświadczenia, które są następnie porównywane z poświadczeniami przechowywanymi w systemie operacyjnym, bazie danych, aplikacji lub zasobie.</span><span class="sxs-lookup"><span data-stu-id="139cf-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="139cf-112">Jeśli są zgodne, użytkownicy uwierzytelniają się pomyślnie, a następnie mogą wykonywać akcje, które są autoryzowane, podczas procesu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="139cf-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="139cf-113">Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić.</span><span class="sxs-lookup"><span data-stu-id="139cf-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="139cf-114">Innym sposobem myślenia o uwierzytelnianiu jest rozważenie go jako sposobu wprowadzenia miejsca, takiego jak serwer, baza danych, aplikacja lub zasób, podczas gdy autoryzacja polega na tym, które akcje użytkownik może wykonać, do których obiektów w tym miejscu (serwer, baza danych lub aplikacja).</span><span class="sxs-lookup"><span data-stu-id="139cf-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="139cf-115">Typowe luki w oprogramowaniu</span><span class="sxs-lookup"><span data-stu-id="139cf-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="139cf-116">ASP.NET Core i EF zawierają funkcje, które pomagają zabezpieczyć aplikacje i zapobiegać naruszeniom zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="139cf-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="139cf-117">Poniższa lista łączy prowadzi do dokumentacji szczegółowo techniki, aby uniknąć najczęstszych luk w zabezpieczeniach w aplikacjach sieci web:</span><span class="sxs-lookup"><span data-stu-id="139cf-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="139cf-118">Ataki skryptów między witrynami</span><span class="sxs-lookup"><span data-stu-id="139cf-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="139cf-119">Ataki polegające na iniekcji SQL</span><span class="sxs-lookup"><span data-stu-id="139cf-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="139cf-120">Fałszerstwo żądań międzygłównych (CSRF)</span><span class="sxs-lookup"><span data-stu-id="139cf-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="139cf-121">Otwarte ataki przekierowania</span><span class="sxs-lookup"><span data-stu-id="139cf-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="139cf-122">Istnieje więcej luk w zabezpieczeniach, o których należy pamiętać.</span><span class="sxs-lookup"><span data-stu-id="139cf-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="139cf-123">Aby uzyskać więcej informacji, zobacz inne artykuły w **sekcji Zabezpieczenia i tożsamość** spisu treści.</span><span class="sxs-lookup"><span data-stu-id="139cf-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
