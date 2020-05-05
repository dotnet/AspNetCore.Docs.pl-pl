---
title: Obsługa obiektów WebSockets w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z usługą WebSockets w ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: da713f22582cf17f60a4deda1b689662a4e4ae06
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775443"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="86b61-103">Obsługa obiektów WebSockets w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86b61-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="86b61-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Andrew Stanton-pielęgniarki](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="86b61-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="86b61-105">W tym artykule wyjaśniono, jak rozpocząć pracę z usługą WebSockets w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="86b61-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="86b61-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) to protokół, który umożliwia komunikację dwukierunkową trwałych kanałów komunikacji za pośrednictwem połączeń TCP.</span><span class="sxs-lookup"><span data-stu-id="86b61-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="86b61-107">Jest on używany w aplikacjach, które korzystają z szybkiej komunikacji w czasie rzeczywistym, takiej jak czat, pulpit nawigacyjny i aplikacje do gier.</span><span class="sxs-lookup"><span data-stu-id="86b61-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="86b61-108">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="86b61-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="86b61-109">[Jak uruchomić](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="86b61-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="86b61-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="86b61-110">SignalR</span></span>

<span data-ttu-id="86b61-111">[ASP.NET Core sygnalizujący](xref:signalr/introduction) to biblioteka, która upraszcza Dodawanie funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="86b61-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="86b61-112">W miarę możliwości używa obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="86b61-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="86b61-113">W przypadku większości aplikacji zaleca się sygnalizowanie za pośrednictwem nieprzetworzonych gniazd WebSockets.</span><span class="sxs-lookup"><span data-stu-id="86b61-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="86b61-114">Sygnalizujący zapewnia rezerwę transportową dla środowisk, w których usługi WebSockets są niedostępne.</span><span class="sxs-lookup"><span data-stu-id="86b61-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="86b61-115">Udostępnia także prosty, zdalny model aplikacji.</span><span class="sxs-lookup"><span data-stu-id="86b61-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="86b61-116">W większości scenariuszy sygnalizujący nie ma znaczącej niekorzystnej wydajności w porównaniu z korzystaniem z nieprzetworzonych gniazd WebSockets.</span><span class="sxs-lookup"><span data-stu-id="86b61-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="86b61-117">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="86b61-117">Prerequisites</span></span>

* <span data-ttu-id="86b61-118">ASP.NET Core 1,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="86b61-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="86b61-119">Dowolny system operacyjny, który obsługuje ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="86b61-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="86b61-120">Windows 7/Windows Server 2008 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="86b61-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="86b61-121">Linux</span><span class="sxs-lookup"><span data-stu-id="86b61-121">Linux</span></span>
  * <span data-ttu-id="86b61-122">macOS</span><span class="sxs-lookup"><span data-stu-id="86b61-122">macOS</span></span>
  
* <span data-ttu-id="86b61-123">Jeśli aplikacja działa w systemie Windows z usługami IIS:</span><span class="sxs-lookup"><span data-stu-id="86b61-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="86b61-124">Windows 8/Windows Server 2012 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="86b61-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="86b61-125">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="86b61-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="86b61-126">Należy włączyć obiekty WebSockets (zobacz sekcję [Obsługa usług IIS/IIS Express](#iisiis-express-support) .).</span><span class="sxs-lookup"><span data-stu-id="86b61-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="86b61-127">Jeśli aplikacja jest uruchamiana na serwerze [http. sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="86b61-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="86b61-128">Windows 8/Windows Server 2012 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="86b61-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="86b61-129">Obsługiwane przeglądarki można znaleźć w https://caniuse.com/#feat=websocketstemacie.</span><span class="sxs-lookup"><span data-stu-id="86b61-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="86b61-130">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="86b61-130">NuGet package</span></span>

<span data-ttu-id="86b61-131">Zainstaluj pakiet [Microsoft. AspNetCore. WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) .</span><span class="sxs-lookup"><span data-stu-id="86b61-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="86b61-132">Konfigurowanie oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="86b61-132">Configure the middleware</span></span>


<span data-ttu-id="86b61-133">Dodaj oprogramowanie pośredniczące obiektów WebSockets do `Configure` metody `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="86b61-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="86b61-134">Można skonfigurować następujące ustawienia:</span><span class="sxs-lookup"><span data-stu-id="86b61-134">The following settings can be configured:</span></span>

* <span data-ttu-id="86b61-135">`KeepAliveInterval`-Jak często wysyłać ramki "ping" do klienta, aby upewnić się, że serwer proxy utrzymuje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="86b61-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="86b61-136">Wartość domyślna to dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="86b61-136">The default is two minutes.</span></span>
* <span data-ttu-id="86b61-137">`ReceiveBufferSize`— Rozmiar buforu używany do odbierania danych.</span><span class="sxs-lookup"><span data-stu-id="86b61-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="86b61-138">Użytkownicy zaawansowani mogą wymagać zmiany wydajności na podstawie rozmiaru danych.</span><span class="sxs-lookup"><span data-stu-id="86b61-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="86b61-139">Wartość domyślna to 4 KB.</span><span class="sxs-lookup"><span data-stu-id="86b61-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="86b61-140">Można skonfigurować następujące ustawienia:</span><span class="sxs-lookup"><span data-stu-id="86b61-140">The following settings can be configured:</span></span>

* <span data-ttu-id="86b61-141">`KeepAliveInterval`-Jak często wysyłać ramki "ping" do klienta, aby upewnić się, że serwer proxy utrzymuje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="86b61-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="86b61-142">Wartość domyślna to dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="86b61-142">The default is two minutes.</span></span>
* <span data-ttu-id="86b61-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>— Rozmiar buforu używany do odbierania danych.</span><span class="sxs-lookup"><span data-stu-id="86b61-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="86b61-144">Użytkownicy zaawansowani mogą wymagać zmiany wydajności na podstawie rozmiaru danych.</span><span class="sxs-lookup"><span data-stu-id="86b61-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="86b61-145">Wartość domyślna to 4 KB.</span><span class="sxs-lookup"><span data-stu-id="86b61-145">The default is 4 KB.</span></span>
* <span data-ttu-id="86b61-146">`AllowedOrigins`-Lista dozwolonych wartości nagłówka pierwotnego dla żądań protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="86b61-147">Domyślnie wszystkie źródła są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="86b61-147">By default, all origins are allowed.</span></span> <span data-ttu-id="86b61-148">Aby uzyskać szczegółowe informacje, zobacz sekcję "ograniczenie pochodzenia protokołu WebSocket" poniżej.</span><span class="sxs-lookup"><span data-stu-id="86b61-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="86b61-149">Akceptuj żądania protokołu WebSocket</span><span class="sxs-lookup"><span data-stu-id="86b61-149">Accept WebSocket requests</span></span>

<span data-ttu-id="86b61-150">W przyszłości w cyklu życia żądania (na przykład w `Configure` metodzie lub w metodzie działania) Sprawdź, czy jest to żądanie protokołu WebSocket i zaakceptuj żądanie protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="86b61-151">Poniższy przykład znajduje się w dalszej części `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="86b61-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="86b61-152">Żądanie WebSocket może znajdować się na dowolnym adresie URL, ale ten przykładowy kod akceptuje tylko `/ws`żądania dla.</span><span class="sxs-lookup"><span data-stu-id="86b61-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="86b61-153">W przypadku korzystania z protokołu WebSocket **należy** zachować potok pośredniczący uruchomiony przez czas trwania połączenia.</span><span class="sxs-lookup"><span data-stu-id="86b61-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="86b61-154">Jeśli spróbujesz wysłać lub odebrać komunikat protokołu WebSocket po zakończeniu potoku programu pośredniczącego, może wystąpić wyjątek podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="86b61-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="86b61-155">Jeśli używasz usługi w tle do zapisywania danych do protokołu WebSocket, upewnij się, że działa potok pośredniczący.</span><span class="sxs-lookup"><span data-stu-id="86b61-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="86b61-156">W tym celu należy użyć <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="86b61-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="86b61-157">Przekaż `TaskCompletionSource` do usługi w tle i Wywołaj <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> ją po zakończeniu z użyciem protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="86b61-158">Następnie `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> właściwość w trakcie żądania, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="86b61-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="86b61-159">Zamknięty wyjątek protokołu WebSocket może również wystąpić, Jeśli powrócisz zbyt szybko z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="86b61-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="86b61-160">Jeśli zaakceptujesz gniazdo w metodzie Action, poczekaj na wykonanie kodu, który używa gniazda przed powrotem z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="86b61-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="86b61-161">Nigdy nie `Task.Wait()`używaj `Task.Result`, lub podobnych wywołań blokowania, aby oczekiwać na zakończenie pracy gniazda, co może powodować poważne problemy z wątkami.</span><span class="sxs-lookup"><span data-stu-id="86b61-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="86b61-162">Zawsze używaj `await`.</span><span class="sxs-lookup"><span data-stu-id="86b61-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="86b61-163">Wysyłanie i odbieranie komunikatów</span><span class="sxs-lookup"><span data-stu-id="86b61-163">Send and receive messages</span></span>

<span data-ttu-id="86b61-164">`AcceptWebSocketAsync` Metoda UAKTUALNIA połączenie TCP do połączenia WebSocket i udostępnia obiekt [WebSocket](/dotnet/core/api/system.net.websockets.websocket) .</span><span class="sxs-lookup"><span data-stu-id="86b61-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="86b61-165">Użyj `WebSocket` obiektu do wysyłania i odbierania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="86b61-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="86b61-166">Kod wyświetlany wcześniej, który akceptuje żądanie protokołu WebSocket, przekazuje `WebSocket` obiekt do `Echo` metody.</span><span class="sxs-lookup"><span data-stu-id="86b61-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="86b61-167">Kod odbiera komunikat i natychmiast wysyła komunikat z powrotem.</span><span class="sxs-lookup"><span data-stu-id="86b61-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="86b61-168">Komunikaty są wysyłane i odbierane w pętli, dopóki klient nie zamknie połączenia:</span><span class="sxs-lookup"><span data-stu-id="86b61-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="86b61-169">Po zaakceptowaniu połączenia z użyciem protokołu WebSocket przed rozpoczęciem pętli potok oprogramowania pośredniczącego zostaje zakończony.</span><span class="sxs-lookup"><span data-stu-id="86b61-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="86b61-170">Po zamknięciu gniazda potok nie jest zawijany.</span><span class="sxs-lookup"><span data-stu-id="86b61-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="86b61-171">Oznacza to, że żądanie przestaje poruszać się w potoku po zaakceptowaniu protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="86b61-172">Gdy pętla zostanie zakończona, a gniazdo jest zamknięte, żądanie wykonuje kopię zapasową potoku.</span><span class="sxs-lookup"><span data-stu-id="86b61-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="86b61-173">Obsługa odłączeń klientów</span><span class="sxs-lookup"><span data-stu-id="86b61-173">Handle client disconnects</span></span>

<span data-ttu-id="86b61-174">Serwer nie zostanie automatycznie poinformowany, gdy klient odłączy się ze względu na utratę łączności.</span><span class="sxs-lookup"><span data-stu-id="86b61-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="86b61-175">Serwer odbiera komunikat rozłączenia tylko wtedy, gdy klient wysyła go, którego nie można zrobić w przypadku utraty połączenia z Internetem.</span><span class="sxs-lookup"><span data-stu-id="86b61-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="86b61-176">Jeśli chcesz wykonać pewne działania, należy ustawić limit czasu po odebraniu niczego z klienta w określonym przedziale czasu.</span><span class="sxs-lookup"><span data-stu-id="86b61-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="86b61-177">Jeśli klient nie zawsze wysyła komunikaty i nie chcesz przekroczyć limitu czasu, ponieważ połączenie przechodzi w stan bezczynności, klient wysyła komunikat ping co X s przy użyciu czasomierza.</span><span class="sxs-lookup"><span data-stu-id="86b61-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="86b61-178">Na serwerze, jeśli wiadomość nie dotarła w ciągu 2\*X sekund od poprzedniej, Zakończ połączenie i Zgłoś, że klient został odłączony.</span><span class="sxs-lookup"><span data-stu-id="86b61-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="86b61-179">Poczekaj dwa razy oczekiwany przedział czasu na pozostawienie dodatkowego czasu na opóźnienia sieci, które mogą przytrzymać komunikat ping.</span><span class="sxs-lookup"><span data-stu-id="86b61-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="86b61-180">Ograniczenie pochodzenia obiektu WebSocket</span><span class="sxs-lookup"><span data-stu-id="86b61-180">WebSocket origin restriction</span></span>

<span data-ttu-id="86b61-181">Ochrona zapewniana przez mechanizm CORS nie ma zastosowania do obiektów WebSockets.</span><span class="sxs-lookup"><span data-stu-id="86b61-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="86b61-182">Przeglądarki **nie**:</span><span class="sxs-lookup"><span data-stu-id="86b61-182">Browsers do **not**:</span></span>

* <span data-ttu-id="86b61-183">Wykonaj żądania funkcji CORS przed inspekcją.</span><span class="sxs-lookup"><span data-stu-id="86b61-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="86b61-184">Przestrzeganie ograniczeń określonych w `Access-Control` nagłówkach podczas wykonywania żądań WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="86b61-185">Jednak przeglądarki wysyłają `Origin` nagłówek podczas wystawiania żądań protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="86b61-186">Aplikacje powinny być skonfigurowane do sprawdzania poprawności tych nagłówków, aby upewnić się, że dozwolone są tylko usługi WebSockets pochodzące z oczekiwanych źródeł.</span><span class="sxs-lookup"><span data-stu-id="86b61-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="86b61-187">Jeśli serwerhttps://server.comjest hostem "" i hostuje klienta na "https://client.com", Dodaj "https://client.com" do `AllowedOrigins` listy dla obiektów WebSockets do zweryfikowania.</span><span class="sxs-lookup"><span data-stu-id="86b61-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="86b61-188">`Origin` Nagłówek jest kontrolowany przez klienta i, podobnie jak `Referer` nagłówek, może być sfałszowany.</span><span class="sxs-lookup"><span data-stu-id="86b61-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="86b61-189">Tych nagłówków **nie** należy używać jako mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="86b61-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="86b61-190">Obsługa usług IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="86b61-190">IIS/IIS Express support</span></span>

<span data-ttu-id="86b61-191">System Windows Server 2012 lub nowszy oraz system Windows 8 lub nowszy z usługami IIS/IIS Express 8 lub nowszym obsługują protokół WebSocket.</span><span class="sxs-lookup"><span data-stu-id="86b61-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="86b61-192">W przypadku korzystania z IIS Express usługi WebSockets są zawsze włączone.</span><span class="sxs-lookup"><span data-stu-id="86b61-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="86b61-193">Włączanie obiektów WebSockets w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="86b61-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="86b61-194">Aby włączyć obsługę protokołu WebSocket w systemie Windows Server 2012 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="86b61-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="86b61-195">Te kroki nie są wymagane w przypadku korzystania z IIS Express</span><span class="sxs-lookup"><span data-stu-id="86b61-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="86b61-196">Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**.</span><span class="sxs-lookup"><span data-stu-id="86b61-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="86b61-197">Wybierz opcję **Instalacja oparta na rolach lub oparta na funkcjach**.</span><span class="sxs-lookup"><span data-stu-id="86b61-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="86b61-198">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="86b61-198">Select **Next**.</span></span>
1. <span data-ttu-id="86b61-199">Wybierz odpowiedni serwer (serwer lokalny jest domyślnie wybrany).</span><span class="sxs-lookup"><span data-stu-id="86b61-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="86b61-200">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="86b61-200">Select **Next**.</span></span>
1. <span data-ttu-id="86b61-201">Rozwiń węzeł **serwer sieci Web (IIS)** w drzewie **role** , rozwiń węzeł **serwer sieci Web**, a następnie rozwiń węzeł **Programowanie aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="86b61-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="86b61-202">Wybierz pozycję **Protokół WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="86b61-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="86b61-203">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="86b61-203">Select **Next**.</span></span>
1. <span data-ttu-id="86b61-204">Jeśli nie są potrzebne dodatkowe funkcje, wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="86b61-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="86b61-205">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="86b61-205">Select **Install**.</span></span>
1. <span data-ttu-id="86b61-206">Po zakończeniu instalacji wybierz pozycję **Zamknij** , aby zakończyć pracę kreatora.</span><span class="sxs-lookup"><span data-stu-id="86b61-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="86b61-207">Aby włączyć obsługę protokołu WebSocket w systemie Windows 8 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="86b61-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="86b61-208">Te kroki nie są wymagane w przypadku korzystania z IIS Express</span><span class="sxs-lookup"><span data-stu-id="86b61-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="86b61-209">Przejdź do pozycji **panel** > sterowania**programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="86b61-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="86b61-210">Otwórz następujące węzły: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="86b61-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="86b61-211">Wybierz funkcję **protokołu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="86b61-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="86b61-212">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="86b61-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="86b61-213">Wyłączenie protokołu WebSocket w przypadku używania socket.io w programie Node. js</span><span class="sxs-lookup"><span data-stu-id="86b61-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="86b61-214">W przypadku korzystania z obsługi protokołu WebSocket w programie [Socket.IO](https://socket.io/) w [węźle Node. js](https://nodejs.org/)należy wyłączyć domyślny moduł WebSocket `webSocket` usług IIS przy użyciu elementu w *pliku Web. config* lub *ApplicationHost. config*. Jeśli ten krok nie zostanie wykonany, moduł WebSocket usług IIS podejmie próbę obsługi komunikacji z użyciem protokołu WebSocket zamiast środowiska Node. js i aplikacji.</span><span class="sxs-lookup"><span data-stu-id="86b61-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="86b61-215">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="86b61-215">Sample app</span></span>

<span data-ttu-id="86b61-216">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) , która jest dołączona do tego artykułu, jest aplikacją echo.</span><span class="sxs-lookup"><span data-stu-id="86b61-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="86b61-217">Ma stronę sieci Web, która udostępnia połączenia protokołu WebSocket, a serwer wysyła wszystkie komunikaty odebrane z powrotem do klienta.</span><span class="sxs-lookup"><span data-stu-id="86b61-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="86b61-218">Uruchom aplikację z wiersza polecenia (nie jest on skonfigurowany do uruchamiania z programu Visual Studio z IIS Express) i przejdź do http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="86b61-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="86b61-219">Na stronie sieci Web zostanie wyświetlony stan połączenia w lewym górnym rogu:</span><span class="sxs-lookup"><span data-stu-id="86b61-219">The web page shows the connection status in the upper left:</span></span>

![Początkowy stan strony sieci Web](websockets/_static/start.png)

<span data-ttu-id="86b61-221">Wybierz pozycję **Połącz** , aby wysłać żądanie protokołu WebSocket do podanego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="86b61-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="86b61-222">Wprowadź wiadomość testową i wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="86b61-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="86b61-223">Po zakończeniu wybierz pozycję **Zamknij gniazdo**.</span><span class="sxs-lookup"><span data-stu-id="86b61-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="86b61-224">Sekcja **dziennika komunikacji** zgłasza poszczególne akcje otwierania, wysyłania i zamykania w miarę ich działania.</span><span class="sxs-lookup"><span data-stu-id="86b61-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Początkowy stan strony sieci Web](websockets/_static/end.png)

