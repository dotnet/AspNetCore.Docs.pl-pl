---
title: ASP.NET Core SignalR klienta platformy .NET
author: bradygaster
description: Informacje o kliencie ASP.NET Core SignalR .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
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
ms.openlocfilehash: a03598f887d628c8a2b6720d99826d4aef4e52fa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020005"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a><span data-ttu-id="83a3d-103">ASP.NET Core SignalR klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="83a3d-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="83a3d-104">SignalRBiblioteka kliencka ASP.NET Core .NET umożliwia komunikowanie się z SignalR koncentratorami z poziomu aplikacji .NET.</span><span class="sxs-lookup"><span data-stu-id="83a3d-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="83a3d-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83a3d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="83a3d-106">Przykładowy kod w tym artykule jest aplikacją WPF, która używa SignalR klienta programu ASP.NET Core .NET.</span><span class="sxs-lookup"><span data-stu-id="83a3d-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-no-locsignalr-net-client-package"></a><span data-ttu-id="83a3d-107">Zainstaluj SignalR pakiet klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="83a3d-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="83a3d-108">[Microsoft. AspNetCore. SignalR .. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Do nawiązania połączenia z koncentratorami wymagany jest pakiet klienta platformy .NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="83a3d-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a3d-109">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a3d-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83a3d-110">Aby zainstalować bibliotekę kliencką, uruchom następujące polecenie w oknie **konsola Menedżera pakietów** :</span><span class="sxs-lookup"><span data-stu-id="83a3d-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="83a3d-111">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a3d-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="83a3d-112">Aby zainstalować bibliotekę kliencką, uruchom następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="83a3d-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="83a3d-113">Nawiązywanie połączenia z centrum</span><span class="sxs-lookup"><span data-stu-id="83a3d-113">Connect to a hub</span></span>

<span data-ttu-id="83a3d-114">Aby nawiązać połączenie, Utwórz `HubConnectionBuilder` wywołanie i `Build` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="83a3d-115">Podczas tworzenia połączenia można skonfigurować adres URL centrum, protokół, typ transportu, poziom dziennika, nagłówki i inne opcje.</span><span class="sxs-lookup"><span data-stu-id="83a3d-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="83a3d-116">Skonfiguruj wszystkie wymagane opcje, wstawiając dowolne `HubConnectionBuilder` metody do programu `Build` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="83a3d-117">Rozpocznij połączenie z usługą `StartAsync` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="83a3d-118">Obsługa utraconych połączeń</span><span class="sxs-lookup"><span data-stu-id="83a3d-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="83a3d-119">Automatycznie Połącz ponownie</span><span class="sxs-lookup"><span data-stu-id="83a3d-119">Automatically reconnect</span></span>

<span data-ttu-id="83a3d-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Można skonfigurować do automatycznego ponownego łączenia przy użyciu `WithAutomaticReconnect` metody w <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="83a3d-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="83a3d-121">Domyślnie nie będzie automatycznie ponownie łączyć się.</span><span class="sxs-lookup"><span data-stu-id="83a3d-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="83a3d-122">Bez żadnych parametrów program `WithAutomaticReconnect()` skonfiguruje klienta tak, aby czekał 0, 2, 10 i 30 sekund przed podjęciem próby ponownego połączenia, zatrzymywanie po czterech nieudanych próbach.</span><span class="sxs-lookup"><span data-stu-id="83a3d-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="83a3d-123">Przed rozpoczęciem wszelkich ponownych prób nawiązania połączenia `HubConnection` nastąpi przejście do `HubConnectionState.Reconnecting` stanu i wyzwolenie `Reconnecting` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="83a3d-124">Dzięki temu można ostrzec użytkowników, że połączenie zostało utracone i wyłączyć elementy interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83a3d-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="83a3d-125">Aplikacje nieinteraktywne mogą uruchamiać kolejkowanie lub porzucanie komunikatów.</span><span class="sxs-lookup"><span data-stu-id="83a3d-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="83a3d-126">Jeśli klient pomyślnie ponownie nawiązuje połączenie w ramach pierwszych czterech prób, `HubConnection` nastąpi powrót do `Connected` stanu i wyzwolenie `Reconnected` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="83a3d-127">Zapewnia to możliwość informowania użytkowników o tym, że połączenie zostało ponownie nawiązane i usuwa wszystkie wiadomości w kolejce.</span><span class="sxs-lookup"><span data-stu-id="83a3d-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="83a3d-128">Ponieważ połączenie jest całkowicie nowe dla serwera, do `ConnectionId` obsługi zdarzeń zostanie udostępniona nowa `Reconnected` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="83a3d-129">`Reconnected`Parametr programu obsługi zdarzeń `connectionId` będzie miał wartość null, jeśli `HubConnection` został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="83a3d-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="83a3d-130">`WithAutomaticReconnect()`nie zostanie skonfigurowana `HubConnection` do ponawiania początkowych nieudanych uruchomień, dlatego należy ręcznie obsługiwać błędy uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="83a3d-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="83a3d-131">Jeśli klient nie zostanie pomyślnie ponownie połączony w ramach pierwszych czterech prób, `HubConnection` przejdzie do `Disconnected` stanu i uruchomi <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> zdarzenie.</span><span class="sxs-lookup"><span data-stu-id="83a3d-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="83a3d-132">Zapewnia to możliwość próby ponownego uruchomienia połączenia ręcznie lub poinformowanie użytkowników, że połączenie zostało trwale utracone.</span><span class="sxs-lookup"><span data-stu-id="83a3d-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="83a3d-133">Aby skonfigurować niestandardową liczbę prób ponownego połączenia przed odłączeniem lub zmianą czasu ponownego połączenia, program `WithAutomaticReconnect` akceptuje tablicę liczb reprezentujących opóźnienie (w milisekundach) przed rozpoczęciem każdej próby ponownego nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="83a3d-134">W powyższym przykładzie konfiguruje `HubConnection` się, aby rozpocząć próba ponownego połączenia natychmiast po utracie połączenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="83a3d-135">Dotyczy to również konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="83a3d-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="83a3d-136">Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia zostanie również uruchomiona natychmiast, a nie w ciągu 2 sekund, tak jak w przypadku konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="83a3d-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="83a3d-137">Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego nawiązania połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="83a3d-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="83a3d-138">Zachowanie niestandardowe jest następnie ponownie niezależne od zachowania domyślnego przez zatrzymanie po trzecim nieudanej próbie połączenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="83a3d-139">W konfiguracji domyślnej będzie jeszcze jedna kolejna próba ponownego połączenia w ciągu 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="83a3d-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="83a3d-140">Jeśli chcesz jeszcze większą kontrolę nad chronometrażem i liczbą prób automatycznego ponownego połączenia, `WithAutomaticReconnect` zaakceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma pojedynczą metodę o nazwie `NextRetryDelay` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="83a3d-141">`NextRetryDelay`przyjmuje jeden argument z typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="83a3d-142">`RetryContext`Ma trzy właściwości: `PreviousRetryCount` , `ElapsedTime` i `RetryReason` , które są, a `long` `TimeSpan` i `Exception` odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="83a3d-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="83a3d-143">Przed pierwszym ponownym połączeniem, oba `PreviousRetryCount` i `ElapsedTime` będą miały wartość zero, a `RetryReason` będzie wyjątek, który spowodował utratę połączenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="83a3d-144">Po każdym nieudanej próbie ponowieniu próby `PreviousRetryCount` zostanie zaktualizowany w `ElapsedTime` celu odzwierciedlenia czasu, który połączył się do tej pory, i `RetryReason` będzie to wyjątek, który spowodował, że Ostatnia próba ponownego połączenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="83a3d-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="83a3d-145">`NextRetryDelay`musi zwrócić wartość TimeSpan reprezentującą czas oczekiwania przed kolejną próbą ponownego połączenia lub `null` Jeśli `HubConnection` należy zatrzymać Ponowne nawiązywanie połączenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="83a3d-146">Alternatywnie można napisać kod, który ponownie podłącze klienta ręcznie, jak pokazano w [ręcznym ponownym nawiązaniu połączenia](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="83a3d-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="83a3d-147">Ręczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="83a3d-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="83a3d-148">Przed 3,0 klient platformy .NET dla programu SignalR nie będzie automatycznie ponownie łączyć się.</span><span class="sxs-lookup"><span data-stu-id="83a3d-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="83a3d-149">Musisz napisać kod, który ponownie podłącze klienta ręcznie.</span><span class="sxs-lookup"><span data-stu-id="83a3d-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="83a3d-150">Użyj <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> zdarzenia, aby odpowiedzieć na utracone połączenie.</span><span class="sxs-lookup"><span data-stu-id="83a3d-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="83a3d-151">Na przykład możesz chcieć zautomatyzować ponowne łączenie.</span><span class="sxs-lookup"><span data-stu-id="83a3d-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="83a3d-152">`Closed`Zdarzenie wymaga delegata zwracającego element `Task` , który umożliwia uruchamianie kodu asynchronicznego bez użycia `async void` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="83a3d-153">Aby spełnić podpis delegata w programie `Closed` obsługi zdarzeń, który jest uruchamiany synchronicznie, zwróć `Task.CompletedTask` :</span><span class="sxs-lookup"><span data-stu-id="83a3d-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="83a3d-154">Główną przyczyną obsługi asynchronicznej jest to, że można ponownie uruchomić połączenie.</span><span class="sxs-lookup"><span data-stu-id="83a3d-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="83a3d-155">Rozpoczęcie połączenia jest akcją asynchroniczną.</span><span class="sxs-lookup"><span data-stu-id="83a3d-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="83a3d-156">W programie `Closed` obsługi, który ponownie uruchamia połączenie, rozważ oczekiwanie na losowe opóźnienie, aby zapobiec przeciążeniu serwera, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="83a3d-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="83a3d-157">Wywoływanie metod centrów z klienta</span><span class="sxs-lookup"><span data-stu-id="83a3d-157">Call hub methods from client</span></span>

<span data-ttu-id="83a3d-158">`InvokeAsync`wywołuje metody w centrum.</span><span class="sxs-lookup"><span data-stu-id="83a3d-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="83a3d-159">Przekaż nazwę metody Hub i wszystkie argumenty zdefiniowane w metodzie Hub do `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="83a3d-160">SignalRjest asynchroniczny, więc używaj `async` i `await` podczas wykonywania wywołań.</span><span class="sxs-lookup"><span data-stu-id="83a3d-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="83a3d-161">`InvokeAsync`Metoda zwraca, `Task` która kończy się, gdy metoda serwera zwraca wartość.</span><span class="sxs-lookup"><span data-stu-id="83a3d-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="83a3d-162">Wartość zwracana, jeśli istnieje, jest podawana jako wynik `Task` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="83a3d-163">Wszystkie wyjątki zgłoszone przez metodę na serwerze generują błędy `Task` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="83a3d-164">Użyj `await` składni, aby poczekać na zakończenie i składnię metody serwera, aby `try...catch` obsłużyć błędy.</span><span class="sxs-lookup"><span data-stu-id="83a3d-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="83a3d-165">`SendAsync`Metoda zwraca, `Task` która kończy się, gdy komunikat został wysłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="83a3d-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="83a3d-166">Nie podano wartości zwracanej od momentu `Task` zaczekania na zakończenie metody serwera.</span><span class="sxs-lookup"><span data-stu-id="83a3d-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="83a3d-167">Wszystkie wyjątki zgłoszone na kliencie podczas wysyłania komunikatu generują błędy `Task` .</span><span class="sxs-lookup"><span data-stu-id="83a3d-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="83a3d-168">Użyj `await` składni i, `try...catch` Aby obsłużyć błędy wysyłania.</span><span class="sxs-lookup"><span data-stu-id="83a3d-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="83a3d-169">W przypadku korzystania z usługi platformy Azure SignalR w *trybie bezserwerowym*nie można wywoływać metod centralnych z poziomu klienta.</span><span class="sxs-lookup"><span data-stu-id="83a3d-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="83a3d-170">Aby uzyskać więcej informacji, zobacz [ SignalR dokumentację usługi](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="83a3d-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="83a3d-171">Wywoływanie metod klienta z centrum</span><span class="sxs-lookup"><span data-stu-id="83a3d-171">Call client methods from hub</span></span>

<span data-ttu-id="83a3d-172">Zdefiniuj metody, które są używane przez centrum `connection.On` po skompilowaniu, ale przed rozpoczęciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="83a3d-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="83a3d-173">Poprzedni kod w `connection.On` działa, gdy kod po stronie serwera wywołuje go przy użyciu `SendAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="83a3d-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="83a3d-174">Obsługa błędów i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="83a3d-174">Error handling and logging</span></span>

<span data-ttu-id="83a3d-175">Obsługa błędów przy użyciu instrukcji try-catch.</span><span class="sxs-lookup"><span data-stu-id="83a3d-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="83a3d-176">Zbadaj `Exception` obiekt, aby określić odpowiednią akcję do wykonania po wystąpieniu błędu.</span><span class="sxs-lookup"><span data-stu-id="83a3d-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="83a3d-177">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="83a3d-177">Additional resources</span></span>

* [<span data-ttu-id="83a3d-178">Centra</span><span class="sxs-lookup"><span data-stu-id="83a3d-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="83a3d-179">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="83a3d-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="83a3d-180">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="83a3d-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="83a3d-181">SignalRDokumentacja bezserwerowa usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="83a3d-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
