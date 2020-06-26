---
title: Przegląd zabezpieczeń ASP.NET Core
author: rick-anderson
description: Informacje na temat uwierzytelniania, autoryzacji i podstaw zabezpieczeń w programie ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: 2da3585df16cbec4b87db32cee1bb9138e6aeb16
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406684"
---
# <a name="overview-of-aspnet-core-security"></a>Przegląd zabezpieczeń ASP.NET Core

ASP.NET Core pozwala deweloperom łatwo konfigurować zabezpieczenia dla swoich aplikacji i zarządzać nimi. ASP.NET Core zawiera funkcje zarządzania uwierzytelnianiem, autoryzacją, ochroną danych, wymuszeniem protokołu HTTPS, wpisami tajnymi aplikacji, ochroną przed fałszowaniem żądań i zarządzaniem CORS. Te funkcje zabezpieczeń umożliwiają tworzenie niezawodnych, jeszcze bezpiecznych aplikacji ASP.NET Core.

## <a name="aspnet-core-security-features"></a>ASP.NET Core funkcje zabezpieczeń

ASP.NET Core udostępnia wiele narzędzi i bibliotek do zabezpieczania aplikacji, w tym wbudowanych dostawców tożsamości, ale można korzystać z usług tożsamości innych firm, takich jak Facebook, Twitter i LinkedIn. Za pomocą ASP.NET Core można łatwo zarządzać wpisami tajnymi aplikacji, które są sposobem przechowywania i używania poufnych informacji bez konieczności ujawniania ich w kodzie.

## <a name="authentication-vs-authorization"></a>Uwierzytelnianie a autoryzacja

Uwierzytelnianie to proces, w którym użytkownik dostarcza poświadczenia, które są porównywane z tymi przechowywanymi w systemie operacyjnym, bazie danych, aplikacji lub zasobie. Jeśli są one zgodne, użytkownicy są uwierzytelniani pomyślnie i mogą wykonywać akcje, dla których są autoryzowane, podczas procesu autoryzacji. Autoryzacja odnosi się do procesu, który określa, co użytkownik może zrobić.

Innym sposobem na wystawienie uwierzytelniania jest rozważenie go w celu wprowadzenia miejsca, takiego jak serwer, baza danych, aplikacja lub zasób, podczas gdy autoryzacja to akcje, które użytkownik może wykonywać, do których obiektów w tym miejscu (serwer, baza danych lub aplikacja).

## <a name="common-vulnerabilities-in-software"></a>Typowe luki w zabezpieczeniach oprogramowania

ASP.NET Core i EF zawierają funkcje, które ułatwiają Zabezpieczanie aplikacji i zapobieganie naruszeniom zabezpieczeń. Poniższa lista linków zawiera szczegółowe informacje o technikach mających na celu uniknięcie najczęstszych luk w zabezpieczeniach w usłudze Web Apps:

* [Ataki skryptów między lokacjami](xref:security/cross-site-scripting)
* [Ataki polegające na iniekcji SQL](/ef/core/querying/raw-sql)
* [Fałszerstwo żądania między lokacjami (CSRF)](xref:security/anti-request-forgery)
* [Otwarte ataki przekierowania](xref:security/preventing-open-redirects)

Istnieje więcej luk w zabezpieczeniach. Aby uzyskać więcej informacji, zapoznaj się z innymi artykułami w sekcji **zabezpieczenia i Identity ** treść spisu treści.
