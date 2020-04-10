---
title: ASP.NET podstawowy SignalR klient JavaScript
author: bradygaster
description: Omówienie klienta ASP.NET SignalR Core JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994583"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET podstawowy SignalR klient JavaScript

Przez [Rachel Appel](https://twitter.com/rachelappel)

Biblioteka klienta ASP.NET SignalR Core JavaScript umożliwia deweloperom wywoływanie kodu koncentratora po stronie serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Instalowanie SignalR pakietu klienta

Biblioteka SignalR klienta JavaScript jest dostarczana jako pakiet [npm.](https://www.npmjs.com/) W poniższych sekcjach opisano różne sposoby instalowania biblioteki klienta.

### <a name="install-with-npm"></a>Zainstaluj z npm

Jeśli używasz programu Visual Studio, uruchom następujące polecenia z **konsoli Menedżera pakietów** w folderze głównym. W przypadku programu Visual Studio Code uruchom następujące polecenia z **terminala zintegrowanego**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm instaluje zawartość pakietu w folderze *node_modules.\\ * Utwórz nowy folder o nazwie *signalr* pod folderem *\\wwwroot lib.* Skopiuj plik *signalr.js* do folderu *wwwroot\lib\signalr.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm instaluje zawartość pakietu w folderze *node_modules.\\ * Utwórz nowy folder o nazwie *signalr* pod folderem *\\wwwroot lib.* Skopiuj plik *signalr.js* do folderu *wwwroot\lib\signalr.*

::: moniker-end

Odwołanie SignalR się do klienta `<script>` JavaScript w elemencie. Przykład:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Korzystanie z sieci dostarczania zawartości (CDN)

Aby użyć biblioteki klienta bez wymagania wstępnego npm, odwołaj się do hostowanego przez sieć CDN kopii biblioteki klienta. Przykład:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Biblioteka klienta jest dostępna na następujących sieciach CDN:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr ( jsDelivr )](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg (odp.](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr ( jsDelivr )](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg (odp.](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Zainstaluj z LibMan

[LibMan](xref:client-side/libman/index) może służyć do instalowania określonych plików biblioteki klienta z biblioteki klienta hostowanego przez usługę CDN. Na przykład tylko dodać minified plik JavaScript do projektu. Aby uzyskać szczegółowe informacje na temat tego podejścia, zobacz [Dodawanie biblioteki SignalR klienta](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Łączenie się z koncentratorem

Poniższy kod tworzy i uruchamia połączenie. Nazwa koncentratora jest niewrażliwa na litery.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Połączenia międzyzierzowe

Zazwyczaj przeglądarki ładują połączenia z tej samej domeny co żądana strona. Istnieją jednak sytuacje, gdy wymagane jest połączenie z inną domeną.

Aby zapobiec odczytywanie poufnych danych przez złośliwą witrynę z innej [lokacji, połączenia międzybiegomowe](xref:security/cors) są domyślnie wyłączone. Aby zezwolić na żądanie cross-origin, należy włączyć go w `Startup` klasie.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Wywoływanie metod koncentratora z klienta

Klienci JavaScript dzwonią do metod publicznych w centrach za pomocą metody [wywoływania](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection). Metoda `invoke` akceptuje dwa argumenty:

* Nazwa metody koncentratora. W poniższym przykładzie nazwa metody `SendMessage`w centrum jest .
* Wszelkie argumenty zdefiniowane w metodzie koncentratora. W poniższym przykładzie nazwa `message`argumentu to . Przykładowy kod używa składni funkcji strzałki, która jest obsługiwana w bieżących wersjach wszystkich głównych przeglądarek z wyjątkiem programu Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Jeśli używasz usługi SignalR Azure w *trybie bezserwerowym,* nie można wywołać metody koncentratora z klienta. Aby uzyskać więcej informacji, zobacz [ SignalR dokumentację usługi](/azure/azure-signalr/signalr-concept-serverless-development-config).

Metoda `invoke` zwraca JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). Jest `Promise` rozpoznawany z wartością zwracaną (jeśli istnieje) po powrocie metody na serwerze. Jeśli metoda na serwerze zgłasza błąd, `Promise` jest odrzucany z komunikatem o błędzie. Użyj `then` i `catch` metody `Promise` na siebie do obsługi `await` tych przypadków (lub składni).

Metoda `send` zwraca JavaScript `Promise`. Jest `Promise` rozpoznawany po wysłaniu wiadomości do serwera. Jeśli występuje błąd podczas wysyłania `Promise` wiadomości, jest odrzucany z komunikatem o błędzie. Użyj `then` i `catch` metody `Promise` na siebie do obsługi `await` tych przypadków (lub składni).

> [!NOTE]
> Using `send` nie czeka, aż serwer otrzyma wiadomość. W związku z tym nie jest możliwe zwrócenie danych lub błędów z serwera.

## <a name="call-client-methods-from-hub"></a>Wywoływanie metod klienta z centrum

Aby odbierać wiadomości z koncentratora, [on](/javascript/api/%40aspnet/signalr/hubconnection#on) należy zdefiniować metodę przy użyciu metody `HubConnection`on .

* Nazwa metody klienta JavaScript. W poniższym przykładzie nazwa `ReceiveMessage`metody to .
* Argumenty koncentratora przechodzi do metody. W poniższym przykładzie wartość `message`argumentu jest .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Poprzedni kod w `connection.on` uruchamia, gdy kod po stronie serwera wywołuje go przy użyciu [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metody.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRokreśla, którą metodę klienta wywołać, dopasowując nazwę metody i argumenty zdefiniowane w `SendAsync` i `connection.on`.

> [!NOTE]
> Najlepszym rozwiązaniem jest wywołanie metody `HubConnection` start `on`na after . [start](/javascript/api/%40aspnet/signalr/hubconnection#start) Dzięki temu programy obsługi są rejestrowane przed odebraniem jakichkolwiek wiadomości.

## <a name="error-handling-and-logging"></a>Obsługa błędów i rejestrowanie

Łańcuch `catch` metody do końca `start` metody do obsługi błędów po stronie klienta. Służy `console.error` do wyprowadzania błędów do konsoli przeglądarki.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Konfigurowanie śledzenia dziennika po stronie klienta przez przekazanie rejestratora i typu zdarzenia do rejestrowania po nawiązaniu połączenia. Komunikaty są rejestrowane z określonym poziomem dziennika i wyższym. Dostępne poziomy dziennika są następujące:

* `signalR.LogLevel.Error`&ndash; Komunikaty o błędach. Rejestruje `Error` tylko komunikaty.
* `signalR.LogLevel.Warning`&ndash; Komunikaty ostrzegawcze o potencjalnych błędach. Dzienniki `Warning`i `Error` wiadomości.
* `signalR.LogLevel.Information`&ndash; Komunikaty o stanie bez błędów. Dzienniki `Information` `Warning`, `Error` i wiadomości.
* `signalR.LogLevel.Trace`&ndash; Śledzenie komunikatów. Rejestruje wszystko, w tym dane przesyłane między koncentratorem a klientem.

Użyj metody [konfigurowania rejestrowania](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) w [programie HubConnectionBuilder,](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) aby skonfigurować poziom dziennika. Wiadomości są rejestrowane w konsoli przeglądarki.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Łączenie klientów

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatyczne ponowne połączenie

Klient JavaScript SignalR można skonfigurować do automatycznego ponownego `withAutomaticReconnect` łączenia przy użyciu metody na [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Domyślnie nie zostanie automatycznie ponownie nawiązany.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez żadnych `withAutomaticReconnect()` parametrów konfiguruje klienta czekać 0, 2, 10 i 30 sekund odpowiednio przed wypróbowaniem każdej próby ponownego połączenia, zatrzymując po czterech nieudanych próbach.

Przed rozpoczęciem wszelkich prób `HubConnection` ponownego połączenia, `HubConnectionState.Reconnecting` będzie przejście `onreconnecting` do stanu i wywołania `Disconnected` zwrotnego zamiast `onclose` przejścia do stanu `HubConnection` i wyzwalania jego wywołania zwrotne jak bez automatycznego ponownego połączenia skonfigurowane. Umożliwia to ostrzeżenie użytkowników, że połączenie zostało utracone i wyłączenie elementów interfejsu użytkownika.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Jeśli klient pomyślnie łączy się ponownie w `HubConnection` ciągu pierwszych czterech `Connected` prób, `onreconnected` będzie przejście z powrotem do stanu i wywołania zwrotnego. Umożliwia to poinformowanie użytkowników o ponownym nawiązanym połączeniu.

Ponieważ połączenie wygląda zupełnie nowy na serwerze, nowy `connectionId` `onreconnected` zostanie dostarczony do wywołania zwrotnego.

> [!WARNING]
> Parametr `onreconnected` wywołania `connectionId` zwrotnego nie zostanie zdefiniowany, `HubConnection` jeśli został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`nie skonfiguruje, `HubConnection` aby ponowić próbę początkowego uruchomienia błędów, więc błędy uruchamiania muszą być obsługiwane ręcznie:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Jeśli klient nie pomyślnie połączyć się ponownie w `HubConnection` ciągu pierwszych `Disconnected` czterech prób, będzie przejście do stanu i zwolnić [jego](/javascript/api/%40aspnet/signalr/hubconnection#onclose) zamknięcia wywołania zwrotnego. Umożliwia to poinformowanie użytkowników o trwałej utracie połączenia i zalecenie odświeżenia strony:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Aby skonfigurować niestandardową liczbę prób ponownego nawiązania połączenia przed `withAutomaticReconnect` odłączeniem lub zmianą czasu ponownego połączenia, akceptuje tablicę liczb przedstawiających opóźnienie w milisekundach, aby poczekać przed rozpoczęciem każdej próby ponownego połączenia.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

W poprzednim przykładzie `HubConnection` konfiguruje, aby rozpocząć próby ponownego połączenia natychmiast po utracie połączenia. Dotyczy to również domyślnej konfiguracji.

Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia również rozpocznie się natychmiast zamiast czekać 2 sekundy, tak jak w konfiguracji domyślnej.

Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do domyślnej konfiguracji.

Zachowanie niestandardowe następnie odbiega ponownie od domyślnego zachowania, zatrzymując się po trzecim niepowodzeniu próby ponownego połączenia zamiast próbować jeszcze jednej próby ponownego połączenia w ciągu kolejnych 30 sekund, tak jak w konfiguracji domyślnej.

Jeśli chcesz jeszcze większą kontrolę nad czasem i liczbą prób automatycznego ponownego połączenia, `withAutomaticReconnect` akceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma jedną metodę o nazwie `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds`przyjmuje pojedynczy argument z `RetryContext`typem . Ma `RetryContext` trzy właściwości: `previousRetryCount` `elapsedMilliseconds` , `retryReason` i `number`które `number` są `Error` , a i odpowiednio. Przed pierwszą próbą `previousRetryCount` ponownego `elapsedMilliseconds` połączenia, zarówno `retryReason` i będzie zero, a będzie błąd, który spowodował utratę połączenia. Po każdej nieudanej próbie ponowienia zostanie `previousRetryCount` `elapsedMilliseconds` ona zwiększona o jeden, zostanie zaktualizowana w celu odzwierciedlenia ilości `retryReason` czasu spędzonego na ponownym połączeniu w milisekundach, a będzie to błąd, który spowodował niepowodzenie ostatniej próby ponownego połączenia.

`nextRetryDelayInMilliseconds`musi zwrócić liczbę reprezentującą liczbę milisekund, aby poczekać przed następną próbą ponownego połączenia lub `null` jeśli `HubConnection` należy zatrzymać ponowne połączenie.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Alternatywnie można napisać kod, który spowoduje ponowne połączenie klienta ręcznie, jak pokazano w [Ręcznie ponownie połączyć](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ręczne ponowne łączenie

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Przed 3.0, klient SignalR JavaScript dla nie automatycznie ponownie. Należy napisać kod, który spowoduje ponowne połączenie klienta ręcznie.

::: moniker-end

Poniższy kod pokazuje typowe podejście ręcznego ponownego łączenia:

1. Funkcja (w tym przypadku `start` funkcja) jest tworzony do uruchomienia połączenia.
1. Wywołanie `start` funkcji w programie `onclose` obsługi zdarzeń połączenia.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Rzeczywistych implementacji będzie używać wykładniczego wycofywania lub ponowić próbę określoną liczbę razy przed rezygnacją.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dokumentacja interfejsów API języka JavaScript](/javascript/api/?view=signalr-js-latest)
* [Poradnik języka JavaScript](xref:tutorials/signalr)
* [WebPack i TypeScript samouczek](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient platformy .NET](xref:signalr/dotnet-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
* [Żądania między źródłami (CORS)](xref:security/cors)
* [Dokumentacja usługi Azure SignalR Service bezserwerowa](/azure/azure-signalr/signalr-concept-serverless-development-config)
