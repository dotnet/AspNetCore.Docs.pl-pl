---
title: ASP.NET Core SignalR klienta platformy .NET
author: bradygaster
description: Informacje o kliencie ASP.NET Core SignalR .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: 7849322e50aeceda0850d94f9c9bcda79a6051c8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061226"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a>ASP.NET Core SignalR klienta platformy .NET

SignalRBiblioteka kliencka ASP.NET Core .NET umożliwia komunikowanie się z SignalR koncentratorami z poziomu aplikacji .NET.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowy kod w tym artykule jest aplikacją WPF, która używa SignalR klienta programu ASP.NET Core .NET.

## <a name="install-the-no-locsignalr-net-client-package"></a>Zainstaluj SignalR pakiet klienta platformy .NET

[Microsoft. AspNetCore. SignalR .. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Do nawiązania połączenia z koncentratorami wymagany jest pakiet klienta platformy .NET SignalR .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aby zainstalować bibliotekę kliencką, uruchom następujące polecenie w oknie **konsola Menedżera pakietów** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Aby zainstalować bibliotekę kliencką, uruchom następujące polecenie w powłoce poleceń:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Nawiązywanie połączenia z centrum

Aby nawiązać połączenie, Utwórz `HubConnectionBuilder` wywołanie i `Build` . Podczas tworzenia połączenia można skonfigurować adres URL centrum, protokół, typ transportu, poziom dziennika, nagłówki i inne opcje. Skonfiguruj wszystkie wymagane opcje, wstawiając dowolne `HubConnectionBuilder` metody do programu `Build` . Rozpocznij połączenie z usługą `StartAsync` .

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Obsługa utraconych połączeń

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatycznie Połącz ponownie

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Można skonfigurować do automatycznego ponownego łączenia przy użyciu `WithAutomaticReconnect` metody w <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Domyślnie nie będzie automatycznie ponownie łączyć się.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

Bez żadnych parametrów program `WithAutomaticReconnect()` skonfiguruje klienta tak, aby czekał 0, 2, 10 i 30 sekund przed podjęciem próby ponownego połączenia, zatrzymywanie po czterech nieudanych próbach.

Przed rozpoczęciem wszelkich ponownych prób nawiązania połączenia `HubConnection` nastąpi przejście do `HubConnectionState.Reconnecting` stanu i wyzwolenie `Reconnecting` zdarzenia.  Dzięki temu można ostrzec użytkowników, że połączenie zostało utracone i wyłączyć elementy interfejsu użytkownika. Aplikacje nieinteraktywne mogą uruchamiać kolejkowanie lub porzucanie komunikatów.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Jeśli klient pomyślnie ponownie nawiązuje połączenie w ramach pierwszych czterech prób, `HubConnection` nastąpi powrót do `Connected` stanu i wyzwolenie `Reconnected` zdarzenia. Zapewnia to możliwość informowania użytkowników o tym, że połączenie zostało ponownie nawiązane i usuwa wszystkie wiadomości w kolejce.

Ponieważ połączenie jest całkowicie nowe dla serwera, do `ConnectionId` obsługi zdarzeń zostanie udostępniona nowa `Reconnected` .

> [!WARNING]
> `Reconnected`Parametr programu obsługi zdarzeń `connectionId` będzie miał wartość null, jeśli `HubConnection` został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` nie zostanie skonfigurowana `HubConnection` do ponawiania początkowych nieudanych uruchomień, dlatego należy ręcznie obsługiwać błędy uruchamiania:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Jeśli klient nie zostanie pomyślnie ponownie połączony w ramach pierwszych czterech prób, `HubConnection` przejdzie do `Disconnected` stanu i uruchomi <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> zdarzenie. Zapewnia to możliwość próby ponownego uruchomienia połączenia ręcznie lub poinformowanie użytkowników, że połączenie zostało trwale utracone.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Aby skonfigurować niestandardową liczbę prób ponownego połączenia przed odłączeniem lub zmianą czasu ponownego połączenia, program `WithAutomaticReconnect` akceptuje tablicę liczb reprezentujących opóźnienie (w milisekundach) przed rozpoczęciem każdej próby ponownego nawiązania połączenia.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

W powyższym przykładzie konfiguruje `HubConnection` się, aby rozpocząć próba ponownego połączenia natychmiast po utracie połączenia. Dotyczy to również konfiguracji domyślnej.

Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia zostanie również uruchomiona natychmiast, a nie w ciągu 2 sekund, tak jak w przypadku konfiguracji domyślnej.

Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego nawiązania połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do konfiguracji domyślnej.

Zachowanie niestandardowe jest następnie ponownie niezależne od zachowania domyślnego przez zatrzymanie po trzecim nieudanej próbie połączenia. W konfiguracji domyślnej będzie jeszcze jedna kolejna próba ponownego połączenia w ciągu 30 sekund.

Jeśli chcesz jeszcze większą kontrolę nad chronometrażem i liczbą prób automatycznego ponownego połączenia, `WithAutomaticReconnect` zaakceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma pojedynczą metodę o nazwie `NextRetryDelay` .

`NextRetryDelay` przyjmuje jeden argument z typem `RetryContext` . `RetryContext`Ma trzy właściwości: `PreviousRetryCount` , `ElapsedTime` i `RetryReason` , które są, a `long` `TimeSpan` i `Exception` odpowiednio. Przed pierwszym ponownym połączeniem, oba `PreviousRetryCount` i `ElapsedTime` będą miały wartość zero, a `RetryReason` będzie wyjątek, który spowodował utratę połączenia. Po każdym nieudanej próbie ponowieniu próby `PreviousRetryCount` zostanie zaktualizowany w `ElapsedTime` celu odzwierciedlenia czasu, który połączył się do tej pory, i `RetryReason` będzie to wyjątek, który spowodował, że Ostatnia próba ponownego połączenia nie powiedzie się.

`NextRetryDelay` musi zwrócić wartość TimeSpan reprezentującą czas oczekiwania przed kolejną próbą ponownego połączenia lub `null` Jeśli `HubConnection` należy zatrzymać Ponowne nawiązywanie połączenia.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Alternatywnie można napisać kod, który ponownie podłącze klienta ręcznie, jak pokazano w [ręcznym ponownym nawiązaniu połączenia](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ręczne ponowne łączenie

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Przed 3,0 klient platformy .NET dla programu SignalR nie będzie automatycznie ponownie łączyć się. Musisz napisać kod, który ponownie podłącze klienta ręcznie.

::: moniker-end

Użyj <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> zdarzenia, aby odpowiedzieć na utracone połączenie. Na przykład możesz chcieć zautomatyzować ponowne łączenie.

`Closed`Zdarzenie wymaga delegata zwracającego element `Task` , który umożliwia uruchamianie kodu asynchronicznego bez użycia `async void` . Aby spełnić podpis delegata w programie `Closed` obsługi zdarzeń, który jest uruchamiany synchronicznie, zwróć `Task.CompletedTask` :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Główną przyczyną obsługi asynchronicznej jest to, że można ponownie uruchomić połączenie. Rozpoczęcie połączenia jest akcją asynchroniczną.

W programie `Closed` obsługi, który ponownie uruchamia połączenie, rozważ oczekiwanie na losowe opóźnienie, aby zapobiec przeciążeniu serwera, jak pokazano w następującym przykładzie:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Wywoływanie metod centrów z klienta

`InvokeAsync` wywołuje metody w centrum. Przekaż nazwę metody Hub i wszystkie argumenty zdefiniowane w metodzie Hub do `InvokeAsync` . SignalR jest asynchroniczny, więc używaj `async` i `await` podczas wykonywania wywołań.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync`Metoda zwraca, `Task` która kończy się, gdy metoda serwera zwraca wartość. Wartość zwracana, jeśli istnieje, jest podawana jako wynik `Task` . Wszystkie wyjątki zgłoszone przez metodę na serwerze generują błędy `Task` . Użyj `await` składni, aby poczekać na zakończenie i składnię metody serwera, aby `try...catch` obsłużyć błędy.

`SendAsync`Metoda zwraca, `Task` która kończy się, gdy komunikat został wysłany do serwera. Nie podano wartości zwracanej od momentu `Task` zaczekania na zakończenie metody serwera. Wszystkie wyjątki zgłoszone na kliencie podczas wysyłania komunikatu generują błędy `Task` . Użyj `await` składni i, `try...catch` Aby obsłużyć błędy wysyłania.

> [!NOTE]
> Wywoływanie metod centrów z poziomu klienta jest obsługiwane tylko w przypadku korzystania z usługi platformy Azure SignalR w trybie *domyślnym* . Aby uzyskać więcej informacji, zobacz [często zadawane pytania (repozytorium Azure-sygnalizujące GitHub)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Wywoływanie metod klienta z centrum

Zdefiniuj metody, które są używane przez centrum `connection.On` po skompilowaniu, ale przed rozpoczęciem połączenia.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Poprzedni kod w `connection.On` działa, gdy kod po stronie serwera wywołuje go przy użyciu `SendAsync` metody.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Obsługa błędów i rejestrowanie

Obsługa błędów przy użyciu instrukcji try-catch. Zbadaj `Exception` obiekt, aby określić odpowiednią akcję do wykonania po wystąpieniu błędu.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Centra](xref:signalr/hubs)
* [Klient JavaScript](xref:signalr/javascript-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
* [SignalRDokumentacja bezserwerowa usługi platformy Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
