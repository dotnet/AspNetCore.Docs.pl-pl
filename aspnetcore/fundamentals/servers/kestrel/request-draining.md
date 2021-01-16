---
title: Żądanie opróżniania przy użyciu ASP.NET Core serwera sieci Web Kestrel
author: rick-anderson
description: Dowiedz się więcej o opróżnianiu żądań za pomocą Kestrel, wieloplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253979"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>Żądanie opróżniania przy użyciu ASP.NET Core serwera sieci Web Kestrel

Otwieranie połączeń HTTP jest czasochłonne. W przypadku protokołu HTTPS jest to również czasochłonne zasoby. W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1. Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia. Aplikacja nie zawsze zużywa treści żądania, na przykład żądania HTTP POST, w których serwer zwraca przekierowanie lub odpowiedź 404. W przypadku przekierowania HTTP POST:

* Klient mógł już wysłać część danych POST.
* Serwer zapisuje odpowiedź 301.
* Nie można użyć połączenia dla nowego żądania, dopóki dane POST z poprzedniej treści żądania nie zostały w pełni odczytane.
* Kestrel próbuje opróżnić treść żądania. Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.

Proces opróżniania zapewnia kompromis między umożliwieniem ponownego użycia połączenia i czasem potrzebny do opróżnienia wszystkich pozostałych danych:

* Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.
* Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.

Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu. Na przykład klienci mogą mieć restrykcyjne limity danych. Ograniczanie przekazanych danych może być priorytetem. W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.

Istnieją zastrzeżenia dotyczące wywoływania `Abort` :

* Tworzenie nowych połączeń może być powolne i kosztowne.
* Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.
* Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.
  * Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany. Na przykład, `Abort` Jeśli złośliwi klienci próbują opublikować dane lub gdy w kodzie klienta występuje usterka powodująca duże lub kilka żądań.
  * Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).

Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi. Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.

Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia. Nie ma zastosowania 5-sekundowy limit opróżniania. Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2. Dodatkowe ramki danych treści żądania są ignorowane.

Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania. Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.
