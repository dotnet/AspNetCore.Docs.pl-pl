---
title: Klient SignalR ASP.NET Core JavaScript
author: bradygaster
description: Omówienie klienta ASP.NET Core SignalR JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768945"
---
# <a name="aspnet-core-signalr-javascript-client"></a>Klient SignalR ASP.NET Core JavaScript

Autor [Rachel Appel](https://twitter.com/rachelappel)

Biblioteka klienta SignalR ASP.NET Core JavaScript umożliwia deweloperom Wywoływanie kodu centrum po stronie serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Zainstaluj pakiet SignalR klienta

Biblioteka SignalR klienta JavaScript jest dostarczana jako pakiet [npm](https://www.npmjs.com/) . W poniższych sekcjach opisano różne sposoby instalowania biblioteki klienta programu.

### <a name="install-with-npm"></a>Zainstaluj za pomocą npm

W przypadku korzystania z programu Visual Studio Uruchom następujące polecenia z **konsoli Menedżera pakietów** w folderze głównym. Aby uzyskać Visual Studio Code, uruchom następujące polecenia w **zintegrowanym terminalu**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm instaluje zawartość pakietu w folderze *node_modules\\ * . Utwórz nowy folder o nazwie *sygnalizujący* w folderze *wwwroot\\lib* . Skopiuj plik *sygnalizującer. js* do folderu *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm instaluje zawartość pakietu w folderze *node_modules\\ * . Utwórz nowy folder o nazwie *sygnalizujący* w folderze *wwwroot\\lib* . Skopiuj plik *sygnalizującer. js* do folderu *wwwroot\lib\signalr* .

::: moniker-end

Odwołuje SignalR się do klienta JavaScript `<script>` w elemencie. Przykład:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Użyj Content Delivery Network (CDN)

Aby korzystać z biblioteki klienta bez wymagania wstępnego npm, odwołując się do kopii biblioteki klienckiej hostowanej w usłudze CDN. Przykład:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Biblioteka kliencka jest dostępna w następujących sieci CDN:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Zainstaluj za pomocą LibMan

[LibMan](xref:client-side/libman/index) można użyć do zainstalowania określonych plików biblioteki klienta z biblioteki klienta hostowanej przez sieć CDN. Na przykład, Dodaj tylko plik JavaScript zminimalizowanego do projektu. Aby uzyskać szczegółowe informacje na temat tego podejścia, zobacz [Dodawanie biblioteki SignalR klienta](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Nawiązywanie połączenia z centrum

Poniższy kod tworzy i uruchamia połączenie. Nazwa centrum nie uwzględnia wielkości liter.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Połączenia między źródłami

Zwykle przeglądarki ładują połączenia z tej samej domeny co żądana strona. Zdarza się jednak, że jest wymagane połączenie z inną domeną.

Aby uniemożliwić złośliwym lokacjom odczytywanie poufnych danych z innej lokacji, [połączenia między źródłami](xref:security/cors) są domyślnie wyłączone. Aby zezwolić na żądanie między źródłami, włącz je w `Startup` klasie.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Wywoływanie metod centrów z klienta

Klienci języka JavaScript wywołują metody publiczne w centrach za pomocą metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) elementu [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). `invoke` Metoda akceptuje dwa argumenty:

* Nazwa metody centrum. W poniższym przykładzie nazwa metody w centrum to `SendMessage`.
* Wszystkie argumenty zdefiniowane w metodzie centrum. W poniższym przykładzie nazwa argumentu to `message`. Przykładowy kod używa składni funkcji ze strzałką, która jest obsługiwana w bieżących wersjach wszystkich głównych przeglądarek z wyjątkiem programu Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> W przypadku korzystania z usługi SignalR platformy Azure w *trybie bezserwerowym*nie można wywoływać metod centralnych z poziomu klienta. Aby uzyskać więcej informacji, zobacz [ SignalR dokumentację usługi](/azure/azure-signalr/signalr-concept-serverless-development-config).

`invoke` Metoda zwraca [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)języka JavaScript. `Promise` Zostanie rozwiązany z wartością zwracaną (jeśli istnieje), gdy metoda zwraca serwer. Jeśli metoda na serwerze zgłasza błąd, `Promise` zostaje odrzucona z komunikatem o błędzie. Użyj metod `then` i `catch` dla `Promise` samej siebie, aby obsłużyć te przypadki `await` (lub składnię).

`send` Metoda zwraca kod JavaScript `Promise`. Jest `Promise` rozpoznawany, gdy wiadomość została wysłana na serwer. Jeśli wystąpi błąd podczas wysyłania komunikatu, `Promise` zostanie on odrzucony z komunikatem o błędzie. Użyj metod `then` i `catch` dla `Promise` samej siebie, aby obsłużyć te przypadki `await` (lub składnię).

> [!NOTE]
> Użycie `send` nie czeka na odebranie komunikatu przez serwer. W związku z tym nie można zwrócić danych ani błędów z serwera.

## <a name="call-client-methods-from-hub"></a>Wywoływanie metod klienta z centrum

Aby odbierać komunikaty z centrum, zdefiniuj metodę przy użyciu metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection`.

* Nazwa metody klienta JavaScript. W poniższym przykładzie nazwa metody to `ReceiveMessage`.
* Argumenty przekazywane przez centrum do metody. W poniższym przykładzie wartość argumentu to `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Poprzedni kod w `connection.on` działa, gdy kod po stronie serwera wywołuje go przy użyciu metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalROkreśla, która metoda klienta ma być wywoływana przez dopasowanie nazwy metody i argumentów zdefiniowanych `SendAsync` w `connection.on`i.

> [!NOTE]
> Najlepszym rozwiązaniem jest wywołanie metody [startowej](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection` po `on`. Dzięki temu programy obsługi zostaną zarejestrowane przed odebraniem wszelkich komunikatów.

## <a name="error-handling-and-logging"></a>Obsługa błędów i rejestrowanie

Łańcuchuje `catch` metodę na końcu `start` metody w celu obsługi błędów po stronie klienta. Służy `console.error` do wyprowadzania błędów do konsoli przeglądarki.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Skonfiguruj śledzenie dzienników po stronie klienta, przekazując Rejestrator i typ zdarzenia, które będą rejestrowane po nawiązaniu połączenia. Komunikaty są rejestrowane z określonym poziomem dziennika i wyższym. Dostępne poziomy dzienników są następujące:

* `signalR.LogLevel.Error`&ndash; Komunikaty o błędach. Rejestruje `Error` tylko komunikaty.
* `signalR.LogLevel.Warning`&ndash; Komunikaty ostrzegawcze dotyczące potencjalnych błędów. Dzienniki `Warning`i `Error` komunikaty.
* `signalR.LogLevel.Information`&ndash; Komunikaty o stanie bez błędów. Dzienniki `Information`, `Warning`i i `Error` wiadomości.
* `signalR.LogLevel.Trace`&ndash; Komunikaty śledzenia. Rejestruje wszystko, w tym dane przesyłane między koncentratorem a klientem.

Użyj metody [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , aby skonfigurować poziom rejestrowania. Komunikaty są rejestrowane w konsoli przeglądarki.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Ponowne łączenie klientów

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatycznie Połącz ponownie

Klient JavaScript dla programu SignalR można skonfigurować do automatycznego ponownego nawiązywania połączenia przy `withAutomaticReconnect` użyciu metody w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Domyślnie nie będzie automatycznie ponownie łączyć się.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Bez żadnych parametrów program `withAutomaticReconnect()` skonfiguruje klienta tak, aby czekał 0, 2, 10 i 30 sekund przed podjęciem próby ponownego połączenia, zatrzymywanie po czterech nieudanych próbach.

Przed rozpoczęciem dowolnych prób ponownego `HubConnection` nawiązania połączenia nastąpi `HubConnectionState.Reconnecting` przejście do `onreconnecting` stanu i uruchomienie jego wywołań zwrotnych zamiast `Disconnected` przejścia do stanu i wyzwalanie jego `onclose` wywołań zwrotnych, takich jak `HubConnection` bez automatycznego ponownego łączenia. Dzięki temu można ostrzec użytkowników, że połączenie zostało utracone i wyłączyć elementy interfejsu użytkownika.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Jeśli klient pomyślnie ponownie nawiązuje połączenie w ramach pierwszych czterech prób, `HubConnection` nastąpi powrót do `Connected` stanu i wyzwolenie `onreconnected` jego wywołania zwrotnego. Zapewnia to możliwość powiadomienia użytkowników o tym, że połączenie zostało ponownie nawiązane.

Ponieważ połączenie jest całkowicie nowe dla serwera, zostanie dostarczone nowe `connectionId` `onreconnected` wywołanie zwrotne.

> [!WARNING]
> `connectionId` Parametr `onreconnected` wywołania zwrotnego zostanie niezdefiniowany, jeśli `HubConnection` został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`nie zostanie skonfigurowana `HubConnection` do ponawiania początkowych nieudanych uruchomień, dlatego należy ręcznie obsługiwać błędy uruchamiania:

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

Jeśli klient nie `HubConnection` będzie mógł ponownie nawiązać połączenia w ramach pierwszych czterech prób, przejdzie do `Disconnected` stanu i uruchomi wywołania zwrotne jego [zamknięcia](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . Dzięki temu użytkownicy mogą informować, że połączenie zostało trwale utracone i zalecamy odświeżenie strony:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Aby skonfigurować niestandardową liczbę prób ponownego połączenia przed odłączeniem lub zmianą czasu ponownego połączenia, program akceptuje `withAutomaticReconnect` tablicę liczb reprezentujących opóźnienie (w milisekundach) przed rozpoczęciem każdej próby ponownego nawiązania połączenia.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

W powyższym przykładzie `HubConnection` konfiguruje się, aby rozpocząć próba ponownego połączenia natychmiast po utracie połączenia. Dotyczy to również konfiguracji domyślnej.

Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia zostanie również uruchomiona natychmiast, a nie w ciągu 2 sekund, tak jak w przypadku konfiguracji domyślnej.

Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego nawiązania połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do konfiguracji domyślnej.

Zachowanie niestandardowe jest następnie niezgodne z zachowaniem domyślnym przez zatrzymanie po trzeciej nieudanej próbie nawiązania połączenia zamiast próby wykonania kolejnej próby ponownego połączenia w ciągu innych 30 sekund, takich jak w przypadku konfiguracji domyślnej.

Jeśli chcesz jeszcze większą kontrolę nad chronometrażem i liczbą prób automatycznego ponownego połączenia, `withAutomaticReconnect` zaakceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma pojedynczą metodę o nazwie. `nextRetryDelayInMilliseconds`

`nextRetryDelayInMilliseconds`przyjmuje jeden argument z typem `RetryContext`. `RetryContext` Ma trzy właściwości: `previousRetryCount`, `elapsedMilliseconds` a `retryReason` które są `number` `number` `Error` odpowiednio, a i a. Przed pierwszym ponownym połączeniem, oba `previousRetryCount` i `elapsedMilliseconds` będą miały wartość zero, a `retryReason` będzie to błąd, który spowodował utratę połączenia. Po każdym nieudanej próbie `previousRetryCount` ponowieniu próby zostanie `elapsedMilliseconds` zaktualizowany w celu odzwierciedlenia ilości czasu poświęconego na przełączenie do tej pory w milisekundach, a `retryReason` będzie to błąd, który spowodował, że Ostatnia próba ponownego połączenia nie powiedzie się.

`nextRetryDelayInMilliseconds`musi zwracać liczbę określającą liczbę milisekund oczekiwania przed kolejną próbą ponownego połączenia lub `null` Jeśli `HubConnection` należy zatrzymać Ponowne nawiązywanie połączenia.

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

Alternatywnie można napisać kod, który ponownie podłącze klienta ręcznie, jak pokazano w [ręcznym ponownym nawiązaniu połączenia](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ręczne ponowne łączenie

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> W systemach wcześniejszych niż 3,0 klient JavaScript dla SignalR programu nie będzie automatycznie ponownie łączyć się. Musisz napisać kod, który ponownie podłącze klienta ręcznie.

::: moniker-end

Poniższy kod ilustruje typowe podejście do ponownego łączenia ręcznego:

1. Funkcja (w tym przypadku `start` funkcja) została utworzona w celu uruchomienia połączenia.
1. Wywołaj `start` funkcję w obsłudze `onclose` zdarzeń połączenia.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Implementacja rzeczywista będzie używać wykładniczej kopii zapasowej lub ponowić określoną liczbę razy przed pokazaniem.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Dokumentacja interfejsów API języka JavaScript](/javascript/api/?view=signalr-js-latest)
* [Samouczek JavaScript](xref:tutorials/signalr)
* [Samouczek WebPack i język TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
* [Żądania między źródłami (CORS)](xref:security/cors)
* [Dokumentacja SignalR bezserwerowa usługi platformy Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
