---
title: Przegląd zabezpieczeń ASP.NET Core
author: rick-anderson
description: Informacje na temat uwierzytelniania, autoryzacji i podstaw zabezpieczeń w programie ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: 413b4cf786c31ac3a538c8663014b53502d90d0c
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "83838295"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="a7ce2-103">Przegląd zabezpieczeń ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7ce2-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="a7ce2-104">ASP.NET Core pozwala deweloperom łatwo konfigurować zabezpieczenia dla swoich aplikacji i zarządzać nimi.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="a7ce2-105">ASP.NET Core zawiera funkcje zarządzania uwierzytelnianiem, autoryzacją, ochroną danych, wymuszeniem protokołu HTTPS, wpisami tajnymi aplikacji, ochroną przed fałszowaniem żądań i zarządzaniem CORS.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="a7ce2-106">Te funkcje zabezpieczeń umożliwiają tworzenie niezawodnych, jeszcze bezpiecznych aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="a7ce2-107">ASP.NET Core funkcje zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="a7ce2-107">ASP.NET Core security features</span></span>

<span data-ttu-id="a7ce2-108">ASP.NET Core udostępnia wiele narzędzi i bibliotek do zabezpieczania aplikacji, w tym wbudowanych dostawców tożsamości, ale można korzystać z usług tożsamości innych firm, takich jak Facebook, Twitter i LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="a7ce2-109">Za pomocą ASP.NET Core można łatwo zarządzać wpisami tajnymi aplikacji, które są sposobem przechowywania i używania poufnych informacji bez konieczności ujawniania ich w kodzie.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="a7ce2-110">Uwierzytelnianie a autoryzacja</span><span class="sxs-lookup"><span data-stu-id="a7ce2-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="a7ce2-111">Uwierzytelnianie to proces, w którym użytkownik dostarcza poświadczenia, które są porównywane z tymi przechowywanymi w systemie operacyjnym, bazie danych, aplikacji lub zasobie.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="a7ce2-112">Jeśli są one zgodne, użytkownicy są uwierzytelniani pomyślnie i mogą wykonywać akcje, dla których są autoryzowane, podczas procesu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="a7ce2-113">Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="a7ce2-114">Innym sposobem na wystawienie uwierzytelniania jest rozważenie go w celu wprowadzenia miejsca, takiego jak serwer, baza danych, aplikacja lub zasób, podczas gdy autoryzacja to akcje, które użytkownik może wykonywać, do których obiektów w tym miejscu (serwer, baza danych lub aplikacja).</span><span class="sxs-lookup"><span data-stu-id="a7ce2-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="a7ce2-115">Typowe luki w zabezpieczeniach oprogramowania</span><span class="sxs-lookup"><span data-stu-id="a7ce2-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="a7ce2-116">ASP.NET Core i EF zawierają funkcje, które ułatwiają Zabezpieczanie aplikacji i zapobieganie naruszeniom zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="a7ce2-117">Poniższa lista linków zawiera szczegółowe informacje o technikach mających na celu uniknięcie najczęstszych luk w zabezpieczeniach w usłudze Web Apps:</span><span class="sxs-lookup"><span data-stu-id="a7ce2-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="a7ce2-118">Ataki skryptów między lokacjami</span><span class="sxs-lookup"><span data-stu-id="a7ce2-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="a7ce2-119">Ataki polegające na iniekcji SQL</span><span class="sxs-lookup"><span data-stu-id="a7ce2-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="a7ce2-120">Fałszerstwo żądania między lokacjami (CSRF)</span><span class="sxs-lookup"><span data-stu-id="a7ce2-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="a7ce2-121">Otwarte ataki przekierowania</span><span class="sxs-lookup"><span data-stu-id="a7ce2-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="a7ce2-122">Istnieje więcej luk w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="a7ce2-123">Aby uzyskać więcej informacji, zapoznaj się z innymi artykułami w sekcji \*\*zabezpieczenia i Identity \*\* treść spisu treści.</span><span class="sxs-lookup"><span data-stu-id="a7ce2-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
