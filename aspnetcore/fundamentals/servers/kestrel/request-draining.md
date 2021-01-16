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
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="73136-103">Żądanie opróżniania przy użyciu ASP.NET Core serwera sieci Web Kestrel</span><span class="sxs-lookup"><span data-stu-id="73136-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="73136-104">Otwieranie połączeń HTTP jest czasochłonne.</span><span class="sxs-lookup"><span data-stu-id="73136-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="73136-105">W przypadku protokołu HTTPS jest to również czasochłonne zasoby.</span><span class="sxs-lookup"><span data-stu-id="73136-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="73136-106">W związku z tym Kestrel próbuje ponownie użyć połączeń zgodnie z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="73136-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="73136-107">Treść żądania musi być w pełni zużyta, aby umożliwić ponowne użycie połączenia.</span><span class="sxs-lookup"><span data-stu-id="73136-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="73136-108">Aplikacja nie zawsze zużywa treści żądania, na przykład żądania HTTP POST, w których serwer zwraca przekierowanie lub odpowiedź 404.</span><span class="sxs-lookup"><span data-stu-id="73136-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="73136-109">W przypadku przekierowania HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="73136-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="73136-110">Klient mógł już wysłać część danych POST.</span><span class="sxs-lookup"><span data-stu-id="73136-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="73136-111">Serwer zapisuje odpowiedź 301.</span><span class="sxs-lookup"><span data-stu-id="73136-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="73136-112">Nie można użyć połączenia dla nowego żądania, dopóki dane POST z poprzedniej treści żądania nie zostały w pełni odczytane.</span><span class="sxs-lookup"><span data-stu-id="73136-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="73136-113">Kestrel próbuje opróżnić treść żądania.</span><span class="sxs-lookup"><span data-stu-id="73136-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="73136-114">Opróżnianie treści żądania oznacza odczytywanie i odrzucanie danych bez przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="73136-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="73136-115">Proces opróżniania zapewnia kompromis między umożliwieniem ponownego użycia połączenia i czasem potrzebny do opróżnienia wszystkich pozostałych danych:</span><span class="sxs-lookup"><span data-stu-id="73136-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="73136-116">Opróżnianie ma przekroczenie limitu czasu wynoszącego pięć sekund, co nie jest możliwe do skonfigurowania.</span><span class="sxs-lookup"><span data-stu-id="73136-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="73136-117">Jeśli wszystkie dane określone przez `Content-Length` `Transfer-Encoding` nagłówek lub nie zostały odczytane przed upływem limitu czasu, połączenie zostanie zamknięte.</span><span class="sxs-lookup"><span data-stu-id="73136-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="73136-118">Czasami możesz chcieć natychmiast przerwać żądanie przed zapisaniem odpowiedzi lub po jej zapisaniu.</span><span class="sxs-lookup"><span data-stu-id="73136-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="73136-119">Na przykład klienci mogą mieć restrykcyjne limity danych.</span><span class="sxs-lookup"><span data-stu-id="73136-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="73136-120">Ograniczanie przekazanych danych może być priorytetem.</span><span class="sxs-lookup"><span data-stu-id="73136-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="73136-121">W takich przypadkach aby zakończyć żądanie, należy wywołać metodę [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontrolera, Razor strony lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="73136-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="73136-122">Istnieją zastrzeżenia dotyczące wywoływania `Abort` :</span><span class="sxs-lookup"><span data-stu-id="73136-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="73136-123">Tworzenie nowych połączeń może być powolne i kosztowne.</span><span class="sxs-lookup"><span data-stu-id="73136-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="73136-124">Nie ma gwarancji, że klient odczytał odpowiedź przed zamknięciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="73136-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="73136-125">Wywołanie `Abort` powinno być rzadkie i zarezerwowane dla poważnych przypadków błędów, a nie typowych błędów.</span><span class="sxs-lookup"><span data-stu-id="73136-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="73136-126">Należy wywołać tylko `Abort` wtedy, gdy określony problem musi zostać rozwiązany.</span><span class="sxs-lookup"><span data-stu-id="73136-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="73136-127">Na przykład, `Abort` Jeśli złośliwi klienci próbują opublikować dane lub gdy w kodzie klienta występuje usterka powodująca duże lub kilka żądań.</span><span class="sxs-lookup"><span data-stu-id="73136-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="73136-128">Nie wywołuj `Abort` w przypadku typowych sytuacji błędów, takich jak HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="73136-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="73136-129">Wywołanie metody [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) przed wywołaniem `Abort` gwarantuje, że serwer zakończył zapisywanie odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="73136-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="73136-130">Zachowanie klienta nie jest jednak przewidywalne i może nie odczytać odpowiedzi przed przerwaniem połączenia.</span><span class="sxs-lookup"><span data-stu-id="73136-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="73136-131">Ten proces jest inny w przypadku protokołu HTTP/2, ponieważ protokół obsługuje przerywanie pojedynczych strumieni żądań bez zamykania połączenia.</span><span class="sxs-lookup"><span data-stu-id="73136-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="73136-132">Nie ma zastosowania 5-sekundowy limit opróżniania.</span><span class="sxs-lookup"><span data-stu-id="73136-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="73136-133">Jeśli po zakończeniu odpowiedzi istnieją nieodczytane dane treści żądania, serwer wysyła ramkę protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="73136-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="73136-134">Dodatkowe ramki danych treści żądania są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="73136-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="73136-135">Jeśli to możliwe, lepiej, aby klienci używali [oczekiwanego nagłówka żądania: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) i poczekać na odpowiedź serwera przed rozpoczęciem wysyłania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="73136-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="73136-136">Dzięki temu Klient może przeanalizować odpowiedź i przerwać przed wysłaniem niepotrzebnych danych.</span><span class="sxs-lookup"><span data-stu-id="73136-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
