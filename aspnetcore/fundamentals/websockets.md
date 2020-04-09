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
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="ffeab-103">Obsługa websockets w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ffeab-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="ffeab-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ffeab-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="ffeab-105">W tym artykule wyjaśniono, jak rozpocząć korzystanie z websockets w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ffeab-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="ffeab-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) to protokół, który umożliwia dwukierunkowe trwałe kanały komunikacji za pośrednictwem połączeń TCP.</span><span class="sxs-lookup"><span data-stu-id="ffeab-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="ffeab-107">Jest używany w aplikacjach, które korzystają z szybkiej komunikacji w czasie rzeczywistym, takich jak czat, pulpit nawigacyjny i aplikacje do gier.</span><span class="sxs-lookup"><span data-stu-id="ffeab-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="ffeab-108">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) [(jak pobrać).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ffeab-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="ffeab-109">[Jak uruchomić](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="ffeab-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="ffeab-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="ffeab-110">SignalR</span></span>

<span data-ttu-id="ffeab-111">[ASP.NET Core SignalR](xref:signalr/introduction) to biblioteka, która upraszcza dodawanie funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ffeab-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="ffeab-112">Używa WebSockets, gdy tylko jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="ffeab-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="ffeab-113">W przypadku większości aplikacji zalecamy SignalR ponad surowe WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ffeab-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="ffeab-114">SignalR zapewnia awaryjne transportu dla środowisk, w których WebSockets nie jest dostępna.</span><span class="sxs-lookup"><span data-stu-id="ffeab-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="ffeab-115">Zapewnia również prosty model aplikacji zdalnego wywoływania procedury.</span><span class="sxs-lookup"><span data-stu-id="ffeab-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="ffeab-116">W większości scenariuszy SignalR nie ma znaczącej wady wydajności w porównaniu z użyciem surowych websockets.</span><span class="sxs-lookup"><span data-stu-id="ffeab-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ffeab-117">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ffeab-117">Prerequisites</span></span>

* <span data-ttu-id="ffeab-118">ASP.NET Core 1.1 lub nowszym</span><span class="sxs-lookup"><span data-stu-id="ffeab-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="ffeab-119">Dowolny system operacyjny obsługujący ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ffeab-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="ffeab-120">Windows 7 / Windows Server 2008 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ffeab-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="ffeab-121">Linux</span><span class="sxs-lookup"><span data-stu-id="ffeab-121">Linux</span></span>
  * <span data-ttu-id="ffeab-122">macOS</span><span class="sxs-lookup"><span data-stu-id="ffeab-122">macOS</span></span>
  
* <span data-ttu-id="ffeab-123">Jeśli aplikacja działa w systemie Windows z iis:</span><span class="sxs-lookup"><span data-stu-id="ffeab-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="ffeab-124">Windows 8 / Windows Server 2012 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ffeab-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="ffeab-125">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="ffeab-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="ffeab-126">WebSockets musi być włączona (zobacz sekcję [pomocy technicznej usług IIS/IIS Express.).](#iisiis-express-support)</span><span class="sxs-lookup"><span data-stu-id="ffeab-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="ffeab-127">Jeśli aplikacja działa na [http.sys:](xref:fundamentals/servers/httpsys)</span><span class="sxs-lookup"><span data-stu-id="ffeab-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="ffeab-128">Windows 8 / Windows Server 2012 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="ffeab-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="ffeab-129">Aby uzyskać obsługiwane przeglądarki, zobacz https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="ffeab-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="ffeab-130">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="ffeab-130">NuGet package</span></span>

<span data-ttu-id="ffeab-131">Zainstaluj pakiet [Microsoft.AspNetCore.WebSockets.](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/)</span><span class="sxs-lookup"><span data-stu-id="ffeab-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="ffeab-132">Konfigurowanie oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="ffeab-132">Configure the middleware</span></span>


<span data-ttu-id="ffeab-133">Dodaj oprogramowanie pośredniczące WebSockets `Startup` w metodzie `Configure` klasy:</span><span class="sxs-lookup"><span data-stu-id="ffeab-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ffeab-134">Można skonfigurować następujące ustawienia:</span><span class="sxs-lookup"><span data-stu-id="ffeab-134">The following settings can be configured:</span></span>

* <span data-ttu-id="ffeab-135">`KeepAliveInterval`- Jak często wysyłać "ping" ramki do klienta, aby upewnić się, że serwery proxy zachować połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="ffeab-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="ffeab-136">Wartość domyślna to dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="ffeab-136">The default is two minutes.</span></span>
* <span data-ttu-id="ffeab-137">`ReceiveBufferSize`- Rozmiar buforu używanego do odbierania danych.</span><span class="sxs-lookup"><span data-stu-id="ffeab-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="ffeab-138">Zaawansowani użytkownicy mogą wymagać zmiany tej opcji w celu dostrajania wydajności na podstawie rozmiaru danych.</span><span class="sxs-lookup"><span data-stu-id="ffeab-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="ffeab-139">Wartość domyślna to 4 KB.</span><span class="sxs-lookup"><span data-stu-id="ffeab-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ffeab-140">Można skonfigurować następujące ustawienia:</span><span class="sxs-lookup"><span data-stu-id="ffeab-140">The following settings can be configured:</span></span>

* <span data-ttu-id="ffeab-141">`KeepAliveInterval`- Jak często wysyłać "ping" ramki do klienta, aby upewnić się, że serwery proxy zachować połączenie otwarte.</span><span class="sxs-lookup"><span data-stu-id="ffeab-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="ffeab-142">Wartość domyślna to dwie minuty.</span><span class="sxs-lookup"><span data-stu-id="ffeab-142">The default is two minutes.</span></span>
* <span data-ttu-id="ffeab-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>- Rozmiar buforu używanego do odbierania danych.</span><span class="sxs-lookup"><span data-stu-id="ffeab-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="ffeab-144">Zaawansowani użytkownicy mogą wymagać zmiany tej opcji w celu dostrajania wydajności na podstawie rozmiaru danych.</span><span class="sxs-lookup"><span data-stu-id="ffeab-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="ffeab-145">Wartość domyślna to 4 KB.</span><span class="sxs-lookup"><span data-stu-id="ffeab-145">The default is 4 KB.</span></span>
* <span data-ttu-id="ffeab-146">`AllowedOrigins`- Lista dozwolonych wartości nagłówka Origin dla żądań WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ffeab-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="ffeab-147">Domyślnie wszystkie źródła są dozwolone.</span><span class="sxs-lookup"><span data-stu-id="ffeab-147">By default, all origins are allowed.</span></span> <span data-ttu-id="ffeab-148">Zobacz "WebSocket ograniczenie pochodzenia" poniżej, aby uzyskać szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="ffeab-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="ffeab-149">Akceptowanie żądań WebSocket</span><span class="sxs-lookup"><span data-stu-id="ffeab-149">Accept WebSocket requests</span></span>

<span data-ttu-id="ffeab-150">Gdzieś później w cyklu życia `Configure` żądania (później w metodzie lub w metodzie akcji, na przykład) sprawdź, czy jest to żądanie WebSocket i zaakceptuj żądanie WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ffeab-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="ffeab-151">Poniższy przykład jest z `Configure` nowszej metody:</span><span class="sxs-lookup"><span data-stu-id="ffeab-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="ffeab-152">Żądanie WebSocket może pojawić się na dowolnym adresie URL, `/ws`ale ten przykładowy kod akceptuje tylko żądania .</span><span class="sxs-lookup"><span data-stu-id="ffeab-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="ffeab-153">Podczas korzystania z WebSocket, **należy** zachować potok oprogramowania pośredniczącego działa przez czas trwania połączenia.</span><span class="sxs-lookup"><span data-stu-id="ffeab-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="ffeab-154">Jeśli spróbujesz wysłać lub odebrać wiadomość WebSocket po zakończeniu potoku oprogramowania pośredniczącego, możesz uzyskać wyjątek podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="ffeab-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="ffeab-155">Jeśli używasz usługi w tle do zapisywania danych w websocketet, upewnij się, że potok oprogramowania pośredniczącego działa.</span><span class="sxs-lookup"><span data-stu-id="ffeab-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="ffeab-156">Zrób to za <xref:System.Threading.Tasks.TaskCompletionSource%601>pomocą pliku .</span><span class="sxs-lookup"><span data-stu-id="ffeab-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="ffeab-157">Przekaż `TaskCompletionSource` do usługi w tle <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> i mieć go wywołać po zakończeniu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ffeab-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="ffeab-158">Następnie `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> właściwość podczas żądania, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ffeab-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="ffeab-159">Wyjątek zamknięty websocket może również się zdarzyć, jeśli zwrócisz zbyt szybko z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ffeab-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="ffeab-160">Jeśli akceptujesz gniazdo w metodzie akcji, poczekaj na kod, który używa gniazda, aby zakończyć przed zwróceniem z metody akcji.</span><span class="sxs-lookup"><span data-stu-id="ffeab-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="ffeab-161">Nigdy `Task.Wait()`nie `Task.Result`używaj , lub podobne blokowanie wywołań czekać na gniazdo, aby zakończyć, ponieważ może to spowodować poważne problemy z wątkami.</span><span class="sxs-lookup"><span data-stu-id="ffeab-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="ffeab-162">Zawsze `await`używaj .</span><span class="sxs-lookup"><span data-stu-id="ffeab-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="ffeab-163">Wysyłanie i odbieranie komunikatów</span><span class="sxs-lookup"><span data-stu-id="ffeab-163">Send and receive messages</span></span>

<span data-ttu-id="ffeab-164">Metoda `AcceptWebSocketAsync` uaktualnia połączenie TCP do połączenia WebSocket i udostępnia obiekt [WebSocket.](/dotnet/core/api/system.net.websockets.websocket)</span><span class="sxs-lookup"><span data-stu-id="ffeab-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="ffeab-165">Użyj `WebSocket` obiektu do wysyłania i odbierania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="ffeab-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="ffeab-166">Kod pokazany wcześniej, który akceptuje żądanie WebSocket przekazuje `WebSocket` obiekt do `Echo` metody.</span><span class="sxs-lookup"><span data-stu-id="ffeab-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="ffeab-167">Kod odbiera wiadomość i natychmiast odsyła tę samą wiadomość.</span><span class="sxs-lookup"><span data-stu-id="ffeab-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="ffeab-168">Wiadomości są wysyłane i odbierane w pętli, dopóki klient nie zamknie połączenia:</span><span class="sxs-lookup"><span data-stu-id="ffeab-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="ffeab-169">Podczas akceptowania połączenia WebSocket przed rozpoczęciem pętli kończy się potok oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ffeab-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="ffeab-170">Po zamknięciu gniazda potoku odwija się.</span><span class="sxs-lookup"><span data-stu-id="ffeab-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="ffeab-171">Oznacza to, że żądanie przestaje iść do przodu w potoku, gdy WebSocket jest akceptowany.</span><span class="sxs-lookup"><span data-stu-id="ffeab-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="ffeab-172">Po zakończeniu pętli i zamknięciu gniazda żądanie przechodzi do kopii zapasowej potoku.</span><span class="sxs-lookup"><span data-stu-id="ffeab-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="ffeab-173">Obsługa rozłączenia klientów</span><span class="sxs-lookup"><span data-stu-id="ffeab-173">Handle client disconnects</span></span>

<span data-ttu-id="ffeab-174">Serwer nie jest automatycznie informowany, gdy klient rozłącza się z powodu utraty łączności.</span><span class="sxs-lookup"><span data-stu-id="ffeab-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="ffeab-175">Serwer odbiera komunikat rozłączania tylko wtedy, gdy klient wysyła go, co nie może być wykonane w przypadku utraty połączenia internetowego.</span><span class="sxs-lookup"><span data-stu-id="ffeab-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="ffeab-176">Jeśli chcesz podjąć pewne działania, gdy tak się stanie, ustaw limit czasu po nic nie jest odbierane od klienta w określonym przedziale czasu.</span><span class="sxs-lookup"><span data-stu-id="ffeab-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="ffeab-177">Jeśli klient nie zawsze wysyła wiadomości i nie chcesz limitu czasu tylko dlatego, że połączenie przechodzi w stan bezczynność, niech klient użyje czasomierza do wysyłania wiadomości ping co X sekund.</span><span class="sxs-lookup"><span data-stu-id="ffeab-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="ffeab-178">Na serwerze, jeśli wiadomość nie dotarła\*w ciągu 2 x sekund po poprzednim, zakończ połączenie i zgłoś, że klient rozłączył się.</span><span class="sxs-lookup"><span data-stu-id="ffeab-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="ffeab-179">Poczekaj na dwa razy oczekiwany przedział czasu, aby pozostawić dodatkowy czas na opóźnienia sieciowe, które mogą pomieścić komunikat ping.</span><span class="sxs-lookup"><span data-stu-id="ffeab-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="ffeab-180">Ograniczenie pochodzenia WebSocket</span><span class="sxs-lookup"><span data-stu-id="ffeab-180">WebSocket origin restriction</span></span>

<span data-ttu-id="ffeab-181">Zabezpieczenia zapewniane przez CORS nie mają zastosowania do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ffeab-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="ffeab-182">Przeglądarki **nie:**</span><span class="sxs-lookup"><span data-stu-id="ffeab-182">Browsers do **not**:</span></span>

* <span data-ttu-id="ffeab-183">Wykonywanie żądań CORS przed lotem.</span><span class="sxs-lookup"><span data-stu-id="ffeab-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="ffeab-184">Przestrzegaj ograniczeń określonych `Access-Control` w nagłówkach podczas tworzenia żądań WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ffeab-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="ffeab-185">Jednak przeglądarki wysyłają `Origin` nagłówek podczas wystawiania żądań WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ffeab-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="ffeab-186">Aplikacje powinny być skonfigurowane do sprawdzania poprawności tych nagłówków, aby upewnić się, że tylko WebSockets pochodzących z oczekiwanych źródeł.</span><span class="sxs-lookup"><span data-stu-id="ffeab-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="ffeab-187">Jeśli hostujesz serwer nahttps://server.com" " i hostujesz klienta na "https://client.com", dodaj "https://client.comdo `AllowedOrigins` listy websockets, aby zweryfikować.</span><span class="sxs-lookup"><span data-stu-id="ffeab-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="ffeab-188">Nagłówek `Origin` jest kontrolowany przez klienta `Referer` i, podobnie jak nagłówek, może być sfałszowany.</span><span class="sxs-lookup"><span data-stu-id="ffeab-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="ffeab-189">**Nie** należy używać tych nagłówków jako mechanizmu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="ffeab-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="ffeab-190">Pomoc techniczna usługi IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="ffeab-190">IIS/IIS Express support</span></span>

<span data-ttu-id="ffeab-191">System Windows Server 2012 lub nowszy oraz Windows 8 lub nowszy z usługami IIS/IIS Express 8 lub nowszymi obsługuje protokół WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ffeab-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="ffeab-192">WebSockets są zawsze włączone podczas korzystania z usługi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ffeab-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="ffeab-193">Włączanie websockets w serwisach IIS</span><span class="sxs-lookup"><span data-stu-id="ffeab-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="ffeab-194">Aby włączyć obsługę protokołu WebSocket w systemie Windows Server 2012 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="ffeab-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="ffeab-195">Te kroki nie są wymagane podczas korzystania z usługi IIS Express</span><span class="sxs-lookup"><span data-stu-id="ffeab-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="ffeab-196">Użyj Kreatora **Dodawania ról i funkcji** z menu **Zarządzanie** lub łącza w **Menedżerze serwera**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="ffeab-197">Wybierz **pozycję Instalacja oparta na rolach lub funkcjach**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="ffeab-198">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-198">Select **Next**.</span></span>
1. <span data-ttu-id="ffeab-199">Wybierz odpowiedni serwer (serwer lokalny jest domyślnie zaznaczony).</span><span class="sxs-lookup"><span data-stu-id="ffeab-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="ffeab-200">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-200">Select **Next**.</span></span>
1. <span data-ttu-id="ffeab-201">Rozwiń węzeł **Web Server (IIS)** w drzewie **Role,** rozwiń węzeł **Serwer sieci Web**, a następnie rozwiń pozycję **Programowy tworzenia aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="ffeab-202">Wybierz **protokół WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="ffeab-203">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-203">Select **Next**.</span></span>
1. <span data-ttu-id="ffeab-204">Jeśli dodatkowe funkcje nie są potrzebne, wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="ffeab-205">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-205">Select **Install**.</span></span>
1. <span data-ttu-id="ffeab-206">Po zakończeniu instalacji wybierz pozycję **Zamknij,** aby zakończyć pracę kreatora.</span><span class="sxs-lookup"><span data-stu-id="ffeab-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="ffeab-207">Aby włączyć obsługę protokołu WebSocket w systemie Windows 8 lub nowszym:</span><span class="sxs-lookup"><span data-stu-id="ffeab-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="ffeab-208">Te kroki nie są wymagane podczas korzystania z usługi IIS Express</span><span class="sxs-lookup"><span data-stu-id="ffeab-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="ffeab-209">Przejdź do pozycji**Programy i funkcje** >  **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="ffeab-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ffeab-210">Otwórz następujące węzły: Funkcje**tworzenia aplikacji**sieci Web w internetowych **usługach informacyjnych** > **.** > </span><span class="sxs-lookup"><span data-stu-id="ffeab-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="ffeab-211">Wybierz funkcję **Protokołu WebSocket.**</span><span class="sxs-lookup"><span data-stu-id="ffeab-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ffeab-212">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="ffeab-213">Wyłącz websocket podczas korzystania z socket.io na node.js</span><span class="sxs-lookup"><span data-stu-id="ffeab-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="ffeab-214">Jeśli korzystasz z obsługi WebSocket w [socket.io](https://socket.io/) na [node.js,](https://nodejs.org/)wyłącz domyślny moduł `webSocket` IIS WebSocket przy użyciu elementu w *web.config* lub *applicationHost.config*. Jeśli ten krok nie zostanie wykonany, moduł IIS WebSocket próbuje obsłużyć komunikację WebSocket, a nie Node.js i aplikację.</span><span class="sxs-lookup"><span data-stu-id="ffeab-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="ffeab-215">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="ffeab-215">Sample app</span></span>

<span data-ttu-id="ffeab-216">[Przykładowa aplikacja,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) która towarzyszy w tym artykule jest aplikacja echo.</span><span class="sxs-lookup"><span data-stu-id="ffeab-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="ffeab-217">Ma stronę sieci web, która sprawia, że połączenia WebSocket i serwer ponownie wysyła wszystkie wiadomości odbiera z powrotem do klienta.</span><span class="sxs-lookup"><span data-stu-id="ffeab-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="ffeab-218">Uruchom aplikację z wiersza polecenia (nie jest skonfigurowany do uruchamiania z programu http://localhost:5000Visual Studio za pomocą programu IIS Express) i przejdź do .</span><span class="sxs-lookup"><span data-stu-id="ffeab-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="ffeab-219">Strona internetowa pokazuje stan połączenia w lewym górnym rogu:</span><span class="sxs-lookup"><span data-stu-id="ffeab-219">The web page shows the connection status in the upper left:</span></span>

![Początkowy stan strony internetowej](websockets/_static/start.png)

<span data-ttu-id="ffeab-221">Wybierz **pozycję Połącz,** aby wysłać żądanie WebSocket do wyświetlanego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="ffeab-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="ffeab-222">Wprowadź wiadomość testową i wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="ffeab-223">Po zakończeniu wybierz pozycję **Zamknij gniazdo**.</span><span class="sxs-lookup"><span data-stu-id="ffeab-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="ffeab-224">Sekcja **Dziennik komunikacji** raportuje każdą akcję otwierania, wysyłania i zamykania w miarę jej zbiegu.</span><span class="sxs-lookup"><span data-stu-id="ffeab-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Początkowy stan strony internetowej](websockets/_static/end.png)

