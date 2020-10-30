---
title: SignalRRozwiązywanie problemów z ASP.NET Core połączenia
author: bradygaster
description: ASP.NET Core SignalR Rozwiązywanie problemów z połączeniem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059614"
---
# <a name="troubleshoot-connection-errors"></a>Rozwiązywanie problemów z błędami połączenia

Ta sekcja zawiera informacje o błędach, które mogą wystąpić podczas próby nawiązania połączenia z SignalR centrum ASP.NET Core.

### <a name="response-code-404"></a>Kod odpowiedzi 404

W przypadku korzystania z usługi WebSockets i `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* W przypadku korzystania z wielu serwerów bez sesji programu Sticky czas połączenia można uruchomić na jednym serwerze, a następnie przełączyć na inny serwer. Inny serwer nie wie o poprzednim połączeniu.
* Sprawdź, czy klient nawiązuje połączenie z prawidłowym punktem końcowym. Na przykład serwer jest hostowany w `http://127.0.0.1:5000/hub/myHub` usłudze i klient próbuje nawiązać połączenie `http://127.0.0.1:5000/myHub` .
* Jeśli połączenie używa tego identyfikatora i trwa zbyt długo, aby wysłać żądanie do serwera po wynegocjowaniu, serwer:

  * Usuwa identyfikator.
  * Zwraca 404.

### <a name="response-code-400-or-503"></a>Kod odpowiedzi 400 lub 503

Dla następującego błędu:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Ten błąd jest zwykle spowodowany przez klienta korzystającego tylko z transportu usługi WebSockets, ale na serwerze nie jest włączony protokół WebSockets.

### <a name="response-code-307"></a>Kod odpowiedzi 307

W przypadku korzystania z usługi WebSockets i `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Ten błąd może również wystąpić podczas żądania negocjowania.

Częsta przyczyna:
* Aplikacja jest skonfigurowana do wymuszania protokołu HTTPS przez wywołanie `UseHttpsRedirection` w `Startup` lub wymuszenie reguły ponownego zapisu protokołu HTTPS za pomocą adresu URL.

Możliwe rozwiązanie:
* Zmień adres URL po stronie klienta z "http" na "https". `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Kod odpowiedzi 405

Kod stanu HTTP 405 — Metoda niedozwolona

* Aplikacja nie ma włączonego mechanizmu [CORS](xref:signalr/security#cross-origin-resource-sharing)

### <a name="response-code-0"></a>Kod odpowiedzi 0

Kod stanu HTTP 0 — zwykle jest to problem [CORS](xref:signalr/security#cross-origin-resource-sharing) , nie podano kodu stanu

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Dodaj oczekiwane źródła do `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Dodaj `.AllowCredentials()` do zasad CORS. Nie można użyć `.AllowAnyOrigin()` lub `.WithOrigins("*")` z tą opcją

### <a name="response-code-413"></a>Kod odpowiedzi 413

Kod stanu HTTP 413 — za duży ładunek

Jest to często spowodowane tym, że występuje token dostępu przekraczający 4K.

* W przypadku korzystania z SignalR usługi platformy Azure Zmniejsz rozmiar tokenu przez dostosowanie oświadczeń wysyłanych za pośrednictwem usługi przy użyciu:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Przejściowe awarie sieci

Przejściowe awarie sieci mogą spowodować zamknięcie SignalR połączenia. Serwer może interpretować zamknięte połączenie jako łagodne rozłączenie z klientem. Aby uzyskać więcej informacji na temat tego, dlaczego klient rozłączył się w tych przypadkach [, Zbierz dzienniki z klienta i serwera](xref:signalr/diagnostics).