---
title: Obsługa obiektów WebSockets w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z usługą WebSockets w ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 1ed586745ba4d678272547785c6ffa77aa841392
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589000"
---
# <a name="websockets-support-in-aspnet-core"></a>Obsługa obiektów WebSockets w ASP.NET Core

Autorzy [Dykstra](https://github.com/tdykstra) i [Andrew Stanton-pielęgniarki](https://github.com/anurse)

W tym artykule wyjaśniono, jak rozpocząć pracę z usługą WebSockets w ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) to protokół, który umożliwia komunikację dwukierunkową trwałych kanałów komunikacji za pośrednictwem połączeń TCP. Jest on używany w aplikacjach, które korzystają z szybkiej komunikacji w czasie rzeczywistym, takiej jak czat, pulpit nawigacyjny i aplikacje do gier.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/websockets/samples) ([jak pobrać](xref:index#how-to-download-a-sample)). [Jak uruchomić](#sample-app).

## SignalR

[ASP.NET Core SignalR ](xref:signalr/introduction) to biblioteka, która upraszcza Dodawanie funkcji sieci Web w czasie rzeczywistym do aplikacji. W miarę możliwości używa obiektów WebSockets.

W przypadku większości aplikacji zalecamy użycie SignalR nieprzetworzonych obiektów WebSockets. SignalR zapewnia rezerwę transportową dla środowisk, w których usługi WebSockets są niedostępne. Udostępnia także podstawowy model aplikacji zdalnego wywoływania procedur. W większości scenariuszy SignalR nie ma znaczącej niekorzystnej wydajności w porównaniu z użyciem nieprzetworzonych gniazd WebSockets.

W przypadku niektórych aplikacji [gRPC na platformie .NET](xref:grpc/index) oferuje alternatywę dla obiektów WebSockets.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolny system operacyjny, który obsługuje ASP.NET Core:  
  * Windows 7/Windows Server 2008 lub nowszy
  * Linux
  * macOS  
* Jeśli aplikacja działa w systemie Windows z usługami IIS:
  * Windows 8/Windows Server 2012 lub nowszy
  * IIS 8/IIS 8 Express
  * Należy włączyć obsługę obiektów WebSockets. Zobacz sekcję [Obsługa usług IIS/IIS Express](#iisiis-express-support) .  
* Jeśli aplikacja działa na [HTTP.sys](xref:fundamentals/servers/httpsys):
  * Windows 8/Windows Server 2012 lub nowszy
* Obsługiwane przeglądarki można znaleźć w temacie https://caniuse.com/#feat=websockets .

## <a name="configure-the-middleware"></a>Konfigurowanie oprogramowania pośredniczącego

Dodaj oprogramowanie pośredniczące obiektów WebSockets do `Configure` metody `Startup` klasy:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

> [!NOTE]
> Jeśli chcesz akceptować żądania protokołu WebSocket w kontrolerze, wywołanie `app.UseWebSockets` musi wystąpić przed `app.UseEndpoints` .

::: moniker range="< aspnetcore-2.2"

Można skonfigurować następujące ustawienia:

* `KeepAliveInterval` -Jak często wysyłać ramki "ping" do klienta, aby upewnić się, że serwer proxy utrzymuje otwarte połączenie. Wartość domyślna to dwie minuty.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Można skonfigurować następujące ustawienia:

* `KeepAliveInterval` -Jak często wysyłać ramki "ping" do klienta, aby upewnić się, że serwer proxy utrzymuje otwarte połączenie. Wartość domyślna to dwie minuty.
* `AllowedOrigins` -Lista dozwolonych wartości nagłówka pierwotnego dla żądań protokołu WebSocket. Domyślnie wszystkie źródła są dozwolone. Aby uzyskać szczegółowe informacje, zobacz sekcję "ograniczenie pochodzenia protokołu WebSocket" poniżej.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Akceptuj żądania protokołu WebSocket

W przyszłości w cyklu życia żądania ( `Configure` na przykład w metodzie lub w metodzie działania) Sprawdź, czy jest to żądanie protokołu WebSocket i zaakceptuj żądanie protokołu WebSocket.

Poniższy przykład znajduje się w dalszej części `Configure` metody:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Żądanie WebSocket może znajdować się na dowolnym adresie URL, ale ten przykładowy kod akceptuje tylko żądania dla `/ws` .

W przypadku korzystania z protokołu WebSocket **należy** zachować potok pośredniczący uruchomiony przez czas trwania połączenia. Jeśli spróbujesz wysłać lub odebrać komunikat protokołu WebSocket po zakończeniu potoku programu pośredniczącego, może wystąpić wyjątek podobny do następującego:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Jeśli używasz usługi w tle do zapisywania danych do protokołu WebSocket, upewnij się, że działa potok pośredniczący. W tym celu należy użyć <xref:System.Threading.Tasks.TaskCompletionSource%601> . Przekaż `TaskCompletionSource` do usługi w tle i Wywołaj ją <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> po zakończeniu z użyciem protokołu WebSocket. Następnie `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> Właściwość w trakcie żądania, jak pokazano w następującym przykładzie:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

Zamknięty wyjątek protokołu WebSocket może również wystąpić, gdy wraca za chwilę z metody akcji. Po zaakceptowaniu gniazda w metodzie akcji poczekaj na wykonanie kodu, który używa gniazda przed powrotem z metody akcji.

Nigdy nie używaj `Task.Wait` , `Task.Result` lub podobnych wywołań blokowania, aby oczekiwać na zakończenie pracy gniazda, co może powodować poważne problemy z wątkami. Zawsze używaj `await` .

## <a name="send-and-receive-messages"></a>Wysyłanie i odbieranie komunikatów

`AcceptWebSocketAsync`Metoda uaktualnia połączenie TCP do połączenia WebSocket i udostępnia obiekt [WebSocket](/dotnet/core/api/system.net.websockets.websocket) . Użyj `WebSocket` obiektu do wysyłania i odbierania wiadomości.

Kod wyświetlany wcześniej, który akceptuje żądanie protokołu WebSocket, przekazuje `WebSocket` obiekt do `Echo` metody. Kod odbiera komunikat i natychmiast wysyła komunikat z powrotem. Komunikaty są wysyłane i odbierane w pętli, dopóki klient nie zamknie połączenia:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Po zaakceptowaniu połączenia z użyciem protokołu WebSocket przed rozpoczęciem pętli potok oprogramowania pośredniczącego zostaje zakończony. Po zamknięciu gniazda potok nie jest zawijany. Oznacza to, że żądanie przestaje poruszać się w potoku po zaakceptowaniu protokołu WebSocket. Gdy pętla zostanie zakończona, a gniazdo jest zamknięte, żądanie wykonuje kopię zapasową potoku.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Obsługa odłączeń klientów

Serwer nie zostanie automatycznie poinformowany, gdy klient odłączy się ze względu na utratę łączności. Serwer odbiera komunikat rozłączenia tylko wtedy, gdy klient wysyła go, którego nie można zrobić w przypadku utraty połączenia z Internetem. Jeśli chcesz wykonać pewne działania, należy ustawić limit czasu po odebraniu niczego z klienta w określonym przedziale czasu.

Jeśli klient nie zawsze wysyła komunikaty i nie chcesz przekroczyć limitu czasu, ponieważ połączenie przechodzi w stan bezczynności, klient wysyła komunikat ping co X s przy użyciu czasomierza. Na serwerze, jeśli wiadomość nie dotarła w ciągu 2 \* X sekund od poprzedniej, Zakończ połączenie i Zgłoś, że klient został odłączony. Poczekaj dwa razy oczekiwany przedział czasu na pozostawienie dodatkowego czasu na opóźnienia sieci, które mogą przytrzymać komunikat ping.

## <a name="websocket-origin-restriction"></a>Ograniczenie pochodzenia obiektu WebSocket

Ochrona zapewniana przez mechanizm CORS nie ma zastosowania do obiektów WebSockets. Przeglądarki **nie**:

* Wykonaj żądania funkcji CORS przed inspekcją.
* Przestrzeganie ograniczeń określonych w `Access-Control` nagłówkach podczas wykonywania żądań WebSocket.

Jednak przeglądarki wysyłają `Origin` nagłówek podczas wystawiania żądań protokołu WebSocket. Aplikacje powinny być skonfigurowane do sprawdzania poprawności tych nagłówków, aby upewnić się, że dozwolone są tylko usługi WebSockets pochodzące z oczekiwanych źródeł.

Jeśli serwer jest hostem "" https://server.com i hostuje klienta na " https://client.com ", Dodaj " https://client.com " do `AllowedOrigins` listy dla obiektów WebSockets do zweryfikowania.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> `Origin`Nagłówek jest kontrolowany przez klienta i, podobnie jak `Referer` nagłówek, może być sfałszowany. Tych nagłówków **nie** należy używać jako mechanizmu uwierzytelniania.

::: moniker-end

## <a name="iisiis-express-support"></a>Obsługa usług IIS/IIS Express

System Windows Server 2012 lub nowszy oraz system Windows 8 lub nowszy z usługami IIS/IIS Express 8 lub nowszym obsługują protokół WebSocket.

> [!NOTE]
> W przypadku korzystania z IIS Express usługi WebSockets są zawsze włączone.

### <a name="enabling-websockets-on-iis"></a>Włączanie obiektów WebSockets w usługach IIS

Aby włączyć obsługę protokołu WebSocket w systemie Windows Server 2012 lub nowszym:

> [!NOTE]
> Te kroki nie są wymagane w przypadku korzystania z IIS Express

1. Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**.
1. Wybierz opcję **Instalacja oparta na rolach lub oparta na funkcjach**. Wybierz opcję **Dalej**.
1. Wybierz odpowiedni serwer (serwer lokalny jest domyślnie wybrany). Wybierz opcję **Dalej**.
1. Rozwiń węzeł **serwer sieci Web (IIS)** w drzewie **role** , rozwiń węzeł **serwer sieci Web**, a następnie rozwiń węzeł **Programowanie aplikacji**.
1. Wybierz pozycję **Protokół WebSocket**. Wybierz opcję **Dalej**.
1. Jeśli nie są potrzebne dodatkowe funkcje, wybierz pozycję **dalej**.
1. Wybierz pozycję **Zainstaluj**.
1. Po zakończeniu instalacji wybierz pozycję **Zamknij** , aby zakończyć pracę kreatora.

Aby włączyć obsługę protokołu WebSocket w systemie Windows 8 lub nowszym:

> [!NOTE]
> Te kroki nie są wymagane w przypadku korzystania z IIS Express

1. Przejdź do pozycji **Panel sterowania**  >  **programy**  >  **programy i funkcje**  >  **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).
1. Otwórz następujące węzły: **Internet Information Services**  >  **World Wide Web Services**  >  **Application Development Features**.
1. Wybierz funkcję **protokołu WebSocket** . Wybierz przycisk **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Wyłącz protokół WebSocket przy użyciu socket.io na Node.js

W przypadku korzystania z obsługi protokołu WebSocket w programie [Socket.IO](https://socket.io/) na [Node.js](https://nodejs.org/)należy wyłączyć domyślny moduł WebSocket usług IIS przy użyciu `webSocket` elementu w *web.config* lub *applicationHost.config*. Jeśli ten krok nie zostanie wykonany, moduł WebSocket usług IIS podejmie próbę obsługi komunikacji z użyciem protokołu WebSocket zamiast Node.js i aplikacji.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Przykładowa aplikacja

[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/websockets/samples) , która jest dołączona do tego artykułu, jest aplikacją echo. Ma ona stronę sieci Web, która udostępnia połączenia protokołu WebSocket, a serwer wysyła wszystkie komunikaty odebrane z powrotem do klienta. Przykładowa aplikacja nie jest skonfigurowana do uruchamiania z programu Visual Studio z IIS Express, więc Uruchom aplikację w powłoce poleceń [`dotnet run`](/dotnet/core/tools/dotnet-run) i przejdź do przeglądarki `http://localhost:5000` . Na stronie sieci Web zostanie wyświetlony stan połączenia:

![Początkowy stan strony sieci Web przed połączeniem z usługą WebSockets](websockets/_static/start.png)

Wybierz pozycję **Połącz** , aby wysłać żądanie protokołu WebSocket do podanego adresu URL. Wprowadź wiadomość testową i wybierz pozycję **Wyślij**. Po zakończeniu wybierz pozycję **Zamknij gniazdo**. Sekcja **dziennika komunikacji** zgłasza poszczególne akcje otwierania, wysyłania i zamykania w miarę ich działania.

![Końcowy stan strony sieci Web po wysłaniu i odebraniu wiadomości testowych połączenia z usługą WebSockets](websockets/_static/end.png)
