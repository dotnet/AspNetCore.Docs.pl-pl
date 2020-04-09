---
title: Obsługa websockets w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak rozpocząć korzystanie z websockets w ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655786"
---
# <a name="websockets-support-in-aspnet-core"></a>Obsługa websockets w ASP.NET Core

Przez [Tom Dykstra](https://github.com/tdykstra) i [Andrew Stanton-Nurse](https://github.com/anurse)

W tym artykule wyjaśniono, jak rozpocząć korzystanie z websockets w ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) to protokół, który umożliwia dwukierunkowe trwałe kanały komunikacji za pośrednictwem połączeń TCP. Jest używany w aplikacjach, które korzystają z szybkiej komunikacji w czasie rzeczywistym, takich jak czat, pulpit nawigacyjny i aplikacje do gier.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) [(jak pobrać).](xref:index#how-to-download-a-sample) [Jak uruchomić](#sample-app).

## <a name="signalr"></a>SignalR

[ASP.NET Core SignalR](xref:signalr/introduction) to biblioteka, która upraszcza dodawanie funkcji sieci Web w czasie rzeczywistym do aplikacji. Używa WebSockets, gdy tylko jest to możliwe.

W przypadku większości aplikacji zalecamy SignalR ponad surowe WebSockets. SignalR zapewnia awaryjne transportu dla środowisk, w których WebSockets nie jest dostępna. Zapewnia również prosty model aplikacji zdalnego wywoływania procedury. W większości scenariuszy SignalR nie ma znaczącej wady wydajności w porównaniu z użyciem surowych websockets.

## <a name="prerequisites"></a>Wymagania wstępne

* ASP.NET Core 1.1 lub nowszym
* Dowolny system operacyjny obsługujący ASP.NET Core:
  
  * Windows 7 / Windows Server 2008 lub nowsze
  * Linux
  * macOS
  
* Jeśli aplikacja działa w systemie Windows z iis:

  * Windows 8 / Windows Server 2012 lub nowsze
  * IIS 8 / IIS 8 Express
  * WebSockets musi być włączona (zobacz sekcję [pomocy technicznej usług IIS/IIS Express.).](#iisiis-express-support)
  
* Jeśli aplikacja działa na [http.sys:](xref:fundamentals/servers/httpsys)

  * Windows 8 / Windows Server 2012 lub nowsze

* Aby uzyskać obsługiwane przeglądarki, zobacz https://caniuse.com/#feat=websockets.

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a>Pakiet NuGet

Zainstaluj pakiet [Microsoft.AspNetCore.WebSockets.](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/)

::: moniker-end

## <a name="configure-the-middleware"></a>Konfigurowanie oprogramowania pośredniczącego


Dodaj oprogramowanie pośredniczące WebSockets `Startup` w metodzie `Configure` klasy:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Można skonfigurować następujące ustawienia:

* `KeepAliveInterval`- Jak często wysyłać "ping" ramki do klienta, aby upewnić się, że serwery proxy zachować połączenie otwarte. Wartość domyślna to dwie minuty.
* `ReceiveBufferSize`- Rozmiar buforu używanego do odbierania danych. Zaawansowani użytkownicy mogą wymagać zmiany tej opcji w celu dostrajania wydajności na podstawie rozmiaru danych. Wartość domyślna to 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Można skonfigurować następujące ustawienia:

* `KeepAliveInterval`- Jak często wysyłać "ping" ramki do klienta, aby upewnić się, że serwery proxy zachować połączenie otwarte. Wartość domyślna to dwie minuty.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>- Rozmiar buforu używanego do odbierania danych. Zaawansowani użytkownicy mogą wymagać zmiany tej opcji w celu dostrajania wydajności na podstawie rozmiaru danych. Wartość domyślna to 4 KB.
* `AllowedOrigins`- Lista dozwolonych wartości nagłówka Origin dla żądań WebSocket. Domyślnie wszystkie źródła są dozwolone. Zobacz "WebSocket ograniczenie pochodzenia" poniżej, aby uzyskać szczegółowe informacje.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Akceptowanie żądań WebSocket

Gdzieś później w cyklu życia `Configure` żądania (później w metodzie lub w metodzie akcji, na przykład) sprawdź, czy jest to żądanie WebSocket i zaakceptuj żądanie WebSocket.

Poniższy przykład jest z `Configure` nowszej metody:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Żądanie WebSocket może pojawić się na dowolnym adresie URL, `/ws`ale ten przykładowy kod akceptuje tylko żądania .

Podczas korzystania z WebSocket, **należy** zachować potok oprogramowania pośredniczącego działa przez czas trwania połączenia. Jeśli spróbujesz wysłać lub odebrać wiadomość WebSocket po zakończeniu potoku oprogramowania pośredniczącego, możesz uzyskać wyjątek podobny do następującego:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Jeśli używasz usługi w tle do zapisywania danych w websocketet, upewnij się, że potok oprogramowania pośredniczącego działa. Zrób to za <xref:System.Threading.Tasks.TaskCompletionSource%601>pomocą pliku . Przekaż `TaskCompletionSource` do usługi w tle <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> i mieć go wywołać po zakończeniu WebSocket. Następnie `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> właściwość podczas żądania, jak pokazano w poniższym przykładzie:

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
Wyjątek zamknięty websocket może również się zdarzyć, jeśli zwrócisz zbyt szybko z metody akcji. Jeśli akceptujesz gniazdo w metodzie akcji, poczekaj na kod, który używa gniazda, aby zakończyć przed zwróceniem z metody akcji.

Nigdy `Task.Wait()`nie `Task.Result`używaj , lub podobne blokowanie wywołań czekać na gniazdo, aby zakończyć, ponieważ może to spowodować poważne problemy z wątkami. Zawsze `await`używaj .

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów

Metoda `AcceptWebSocketAsync` uaktualnia połączenie TCP do połączenia WebSocket i udostępnia obiekt [WebSocket.](/dotnet/core/api/system.net.websockets.websocket) Użyj `WebSocket` obiektu do wysyłania i odbierania wiadomości.

Kod pokazany wcześniej, który akceptuje żądanie WebSocket przekazuje `WebSocket` obiekt do `Echo` metody. Kod odbiera wiadomość i natychmiast odsyła tę samą wiadomość. Wiadomości są wysyłane i odbierane w pętli, dopóki klient nie zamknie połączenia:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Podczas akceptowania połączenia WebSocket przed rozpoczęciem pętli kończy się potok oprogramowania pośredniczącego. Po zamknięciu gniazda potoku odwija się. Oznacza to, że żądanie przestaje iść do przodu w potoku, gdy WebSocket jest akceptowany. Po zakończeniu pętli i zamknięciu gniazda żądanie przechodzi do kopii zapasowej potoku.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Obsługa rozłączenia klientów

Serwer nie jest automatycznie informowany, gdy klient rozłącza się z powodu utraty łączności. Serwer odbiera komunikat rozłączania tylko wtedy, gdy klient wysyła go, co nie może być wykonane w przypadku utraty połączenia internetowego. Jeśli chcesz podjąć pewne działania, gdy tak się stanie, ustaw limit czasu po nic nie jest odbierane od klienta w określonym przedziale czasu.

Jeśli klient nie zawsze wysyła wiadomości i nie chcesz limitu czasu tylko dlatego, że połączenie przechodzi w stan bezczynność, niech klient użyje czasomierza do wysyłania wiadomości ping co X sekund. Na serwerze, jeśli wiadomość nie dotarła\*w ciągu 2 x sekund po poprzednim, zakończ połączenie i zgłoś, że klient rozłączył się. Poczekaj na dwa razy oczekiwany przedział czasu, aby pozostawić dodatkowy czas na opóźnienia sieciowe, które mogą pomieścić komunikat ping.

## <a name="websocket-origin-restriction"></a>Ograniczenie pochodzenia WebSocket

Zabezpieczenia zapewniane przez CORS nie mają zastosowania do WebSockets. Przeglądarki **nie:**

* Wykonywanie żądań CORS przed lotem.
* Przestrzegaj ograniczeń określonych `Access-Control` w nagłówkach podczas tworzenia żądań WebSocket.

Jednak przeglądarki wysyłają `Origin` nagłówek podczas wystawiania żądań WebSocket. Aplikacje powinny być skonfigurowane do sprawdzania poprawności tych nagłówków, aby upewnić się, że tylko WebSockets pochodzących z oczekiwanych źródeł.

Jeśli hostujesz serwer nahttps://server.com" " i hostujesz klienta na "https://client.com", dodaj "https://client.comdo `AllowedOrigins` listy websockets, aby zweryfikować.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> Nagłówek `Origin` jest kontrolowany przez klienta `Referer` i, podobnie jak nagłówek, może być sfałszowany. **Nie** należy używać tych nagłówków jako mechanizmu uwierzytelniania.

::: moniker-end

## <a name="iisiis-express-support"></a>Pomoc techniczna usługi IIS/IIS Express

System Windows Server 2012 lub nowszy oraz Windows 8 lub nowszy z usługami IIS/IIS Express 8 lub nowszymi obsługuje protokół WebSocket.

> [!NOTE]
> WebSockets są zawsze włączone podczas korzystania z usługi IIS Express.

### <a name="enabling-websockets-on-iis"></a>Włączanie websockets w serwisach IIS

Aby włączyć obsługę protokołu WebSocket w systemie Windows Server 2012 lub nowszym:

> [!NOTE]
> Te kroki nie są wymagane podczas korzystania z usługi IIS Express

1. Użyj Kreatora **Dodawania ról i funkcji** z menu **Zarządzanie** lub łącza w **Menedżerze serwera**.
1. Wybierz **pozycję Instalacja oparta na rolach lub funkcjach**. Wybierz **pozycję Dalej**.
1. Wybierz odpowiedni serwer (serwer lokalny jest domyślnie zaznaczony). Wybierz **pozycję Dalej**.
1. Rozwiń węzeł **Web Server (IIS)** w drzewie **Role,** rozwiń węzeł **Serwer sieci Web**, a następnie rozwiń pozycję **Programowy tworzenia aplikacji**.
1. Wybierz **protokół WebSocket**. Wybierz **pozycję Dalej**.
1. Jeśli dodatkowe funkcje nie są potrzebne, wybierz przycisk **Dalej**.
1. Wybierz pozycję **Zainstaluj**.
1. Po zakończeniu instalacji wybierz pozycję **Zamknij,** aby zakończyć pracę kreatora.

Aby włączyć obsługę protokołu WebSocket w systemie Windows 8 lub nowszym:

> [!NOTE]
> Te kroki nie są wymagane podczas korzystania z usługi IIS Express

1. Przejdź do pozycji**Programy i funkcje** >  **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).
1. Otwórz następujące węzły: Funkcje**tworzenia aplikacji**sieci Web w internetowych **usługach informacyjnych** > **.** > 
1. Wybierz funkcję **Protokołu WebSocket.** Kliknij przycisk **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Wyłącz websocket podczas korzystania z socket.io na node.js

Jeśli korzystasz z obsługi WebSocket w [socket.io](https://socket.io/) na [node.js,](https://nodejs.org/)wyłącz domyślny moduł `webSocket` IIS WebSocket przy użyciu elementu w *web.config* lub *applicationHost.config*. Jeśli ten krok nie zostanie wykonany, moduł IIS WebSocket próbuje obsłużyć komunikację WebSocket, a nie Node.js i aplikację.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Przykładowa aplikacja

[Przykładowa aplikacja,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) która towarzyszy w tym artykule jest aplikacja echo. Ma stronę sieci web, która sprawia, że połączenia WebSocket i serwer ponownie wysyła wszystkie wiadomości odbiera z powrotem do klienta. Uruchom aplikację z wiersza polecenia (nie jest skonfigurowany do uruchamiania z programu http://localhost:5000Visual Studio za pomocą programu IIS Express) i przejdź do . Strona internetowa pokazuje stan połączenia w lewym górnym rogu:

![Początkowy stan strony internetowej](websockets/_static/start.png)

Wybierz **pozycję Połącz,** aby wysłać żądanie WebSocket do wyświetlanego adresu URL. Wprowadź wiadomość testową i wybierz pozycję **Wyślij**. Po zakończeniu wybierz pozycję **Zamknij gniazdo**. Sekcja **Dziennik komunikacji** raportuje każdą akcję otwierania, wysyłania i zamykania w miarę jej zbiegu.

![Początkowy stan strony internetowej](websockets/_static/end.png)

