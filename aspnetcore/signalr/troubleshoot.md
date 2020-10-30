---
title: :::no-loc(SignalR):::Rozwiązywanie problemów z ASP.NET Core połączenia
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: Rozwiązywanie problemów z połączeniem.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059614"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="8f211-103">Rozwiązywanie problemów z błędami połączenia</span><span class="sxs-lookup"><span data-stu-id="8f211-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="8f211-104">Ta sekcja zawiera informacje o błędach, które mogą wystąpić podczas próby nawiązania połączenia z :::no-loc(SignalR)::: centrum ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8f211-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core :::no-loc(SignalR)::: hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="8f211-105">Kod odpowiedzi 404</span><span class="sxs-lookup"><span data-stu-id="8f211-105">Response code 404</span></span>

<span data-ttu-id="8f211-106">W przypadku korzystania z usługi WebSockets i `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="8f211-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="8f211-107">W przypadku korzystania z wielu serwerów bez sesji programu Sticky czas połączenia można uruchomić na jednym serwerze, a następnie przełączyć na inny serwer.</span><span class="sxs-lookup"><span data-stu-id="8f211-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="8f211-108">Inny serwer nie wie o poprzednim połączeniu.</span><span class="sxs-lookup"><span data-stu-id="8f211-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="8f211-109">Sprawdź, czy klient nawiązuje połączenie z prawidłowym punktem końcowym.</span><span class="sxs-lookup"><span data-stu-id="8f211-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="8f211-110">Na przykład serwer jest hostowany w `http://127.0.0.1:5000/hub/myHub` usłudze i klient próbuje nawiązać połączenie `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="8f211-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="8f211-111">Jeśli połączenie używa tego identyfikatora i trwa zbyt długo, aby wysłać żądanie do serwera po wynegocjowaniu, serwer:</span><span class="sxs-lookup"><span data-stu-id="8f211-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="8f211-112">Usuwa identyfikator.</span><span class="sxs-lookup"><span data-stu-id="8f211-112">Deletes the ID.</span></span>
  * <span data-ttu-id="8f211-113">Zwraca 404.</span><span class="sxs-lookup"><span data-stu-id="8f211-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="8f211-114">Kod odpowiedzi 400 lub 503</span><span class="sxs-lookup"><span data-stu-id="8f211-114">Response code 400 or 503</span></span>

<span data-ttu-id="8f211-115">Dla następującego błędu:</span><span class="sxs-lookup"><span data-stu-id="8f211-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="8f211-116">Ten błąd jest zwykle spowodowany przez klienta korzystającego tylko z transportu usługi WebSockets, ale na serwerze nie jest włączony protokół WebSockets.</span><span class="sxs-lookup"><span data-stu-id="8f211-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="8f211-117">Kod odpowiedzi 307</span><span class="sxs-lookup"><span data-stu-id="8f211-117">Response code 307</span></span>

<span data-ttu-id="8f211-118">W przypadku korzystania z usługi WebSockets i `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="8f211-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="8f211-119">Ten błąd może również wystąpić podczas żądania negocjowania.</span><span class="sxs-lookup"><span data-stu-id="8f211-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="8f211-120">Częsta przyczyna:</span><span class="sxs-lookup"><span data-stu-id="8f211-120">Common cause:</span></span>
* <span data-ttu-id="8f211-121">Aplikacja jest skonfigurowana do wymuszania protokołu HTTPS przez wywołanie `UseHttpsRedirection` w `Startup` lub wymuszenie reguły ponownego zapisu protokołu HTTPS za pomocą adresu URL.</span><span class="sxs-lookup"><span data-stu-id="8f211-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="8f211-122">Możliwe rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="8f211-122">Possible solution:</span></span>
* <span data-ttu-id="8f211-123">Zmień adres URL po stronie klienta z "http" na "https".</span><span class="sxs-lookup"><span data-stu-id="8f211-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="8f211-124">Kod odpowiedzi 405</span><span class="sxs-lookup"><span data-stu-id="8f211-124">Response code 405</span></span>

<span data-ttu-id="8f211-125">Kod stanu HTTP 405 — Metoda niedozwolona</span><span class="sxs-lookup"><span data-stu-id="8f211-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="8f211-126">Aplikacja nie ma włączonego mechanizmu [CORS](xref:signalr/security#cross-origin-resource-sharing)</span><span class="sxs-lookup"><span data-stu-id="8f211-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="8f211-127">Kod odpowiedzi 0</span><span class="sxs-lookup"><span data-stu-id="8f211-127">Response code 0</span></span>

<span data-ttu-id="8f211-128">Kod stanu HTTP 0 — zwykle jest to problem [CORS](xref:signalr/security#cross-origin-resource-sharing) , nie podano kodu stanu</span><span class="sxs-lookup"><span data-stu-id="8f211-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="8f211-129">Dodaj oczekiwane źródła do `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="8f211-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="8f211-130">Dodaj `.AllowCredentials()` do zasad CORS.</span><span class="sxs-lookup"><span data-stu-id="8f211-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="8f211-131">Nie można użyć `.AllowAnyOrigin()` lub `.WithOrigins("*")` z tą opcją</span><span class="sxs-lookup"><span data-stu-id="8f211-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="8f211-132">Kod odpowiedzi 413</span><span class="sxs-lookup"><span data-stu-id="8f211-132">Response code 413</span></span>

<span data-ttu-id="8f211-133">Kod stanu HTTP 413 — za duży ładunek</span><span class="sxs-lookup"><span data-stu-id="8f211-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="8f211-134">Jest to często spowodowane tym, że występuje token dostępu przekraczający 4K.</span><span class="sxs-lookup"><span data-stu-id="8f211-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="8f211-135">W przypadku korzystania z :::no-loc(SignalR)::: usługi platformy Azure Zmniejsz rozmiar tokenu przez dostosowanie oświadczeń wysyłanych za pośrednictwem usługi przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="8f211-135">If using the Azure :::no-loc(SignalR)::: Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzure:::no-loc(SignalR):::(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="8f211-136">Przejściowe awarie sieci</span><span class="sxs-lookup"><span data-stu-id="8f211-136">Transient network failures</span></span>

<span data-ttu-id="8f211-137">Przejściowe awarie sieci mogą spowodować zamknięcie :::no-loc(SignalR)::: połączenia.</span><span class="sxs-lookup"><span data-stu-id="8f211-137">Transient network failures may close the :::no-loc(SignalR)::: connection.</span></span> <span data-ttu-id="8f211-138">Serwer może interpretować zamknięte połączenie jako łagodne rozłączenie z klientem.</span><span class="sxs-lookup"><span data-stu-id="8f211-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="8f211-139">Aby uzyskać więcej informacji na temat tego, dlaczego klient rozłączył się w tych przypadkach [, Zbierz dzienniki z klienta i serwera](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="8f211-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>