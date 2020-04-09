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
# <a name="overview-of-aspnet-core-security"></a>Omówienie ASP.NET podstawowych zabezpieczeń

ASP.NET Core umożliwia deweloperom łatwe konfigurowanie zabezpieczeń aplikacji i zarządzanie nimi. ASP.NET Core zawiera funkcje zarządzania uwierzytelnianiem, autoryzacją, ochroną danych, wymuszaniem protokołu HTTPS, wpisami tajnymi aplikacji, ochroną przed fałszerskem i zarządzaniem CORS. Te funkcje zabezpieczeń umożliwiają tworzenie niezawodnych, ale bezpiecznych aplikacji ASP.NET Core.

## <a name="aspnet-core-security-features"></a>ASP.NET Podstawowe funkcje zabezpieczeń

ASP.NET Core udostępnia wiele narzędzi i bibliotek do zabezpieczania aplikacji, w tym wbudowanych dostawców tożsamości, ale możesz korzystać z usług tożsamości innych firm, takich jak Facebook, Twitter lub LinkedIn. Dzięki ASP.NET Core można łatwo zarządzać wpisami tajnymi aplikacji, które są sposobem przechowywania i używania poufnych informacji bez konieczności ujawniania ich w kodzie.

## <a name="authentication-vs-authorization"></a>Uwierzytelnianie a autoryzacja

Uwierzytelnianie to proces, w którym użytkownik udostępnia poświadczenia, które są następnie porównywane z poświadczeniami przechowywanymi w systemie operacyjnym, bazie danych, aplikacji lub zasobie. Jeśli są zgodne, użytkownicy uwierzytelniają się pomyślnie, a następnie mogą wykonywać akcje, które są autoryzowane, podczas procesu autoryzacji. Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić.

Innym sposobem myślenia o uwierzytelnianiu jest rozważenie go jako sposobu wprowadzenia miejsca, takiego jak serwer, baza danych, aplikacja lub zasób, podczas gdy autoryzacja polega na tym, które akcje użytkownik może wykonać, do których obiektów w tym miejscu (serwer, baza danych lub aplikacja).

## <a name="common-vulnerabilities-in-software"></a>Typowe luki w oprogramowaniu

ASP.NET Core i EF zawierają funkcje, które pomagają zabezpieczyć aplikacje i zapobiegać naruszeniom zabezpieczeń. Poniższa lista łączy prowadzi do dokumentacji szczegółowo techniki, aby uniknąć najczęstszych luk w zabezpieczeniach w aplikacjach sieci web:

* [Ataki skryptów między witrynami](xref:security/cross-site-scripting)
* [Ataki polegające na iniekcji SQL](/ef/core/querying/raw-sql)
* [Fałszerstwo żądań międzygłównych (CSRF)](xref:security/anti-request-forgery)
* [Otwarte ataki przekierowania](xref:security/preventing-open-redirects)

Istnieje więcej luk w zabezpieczeniach, o których należy pamiętać. Aby uzyskać więcej informacji, zobacz inne artykuły w **sekcji Zabezpieczenia i tożsamość** spisu treści.
