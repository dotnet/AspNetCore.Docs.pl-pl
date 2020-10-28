---
title: :::no-loc(SignalR):::Klient ASP.NET Core JavaScript
author: bradygaster
description: 'Omówienie klienta ASP.NET Core :::no-loc(SignalR)::: JavaScript.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
no-loc:
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
uid: signalr/javascript-client
ms.openlocfilehash: be271272c952487fccc5136307c84fdf49391848
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690643"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="cf16f-103">:::no-loc(SignalR):::Klient ASP.NET Core JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf16f-104">Autor [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="cf16f-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="cf16f-105">:::no-loc(SignalR):::Biblioteka klienta ASP.NET Core JavaScript umożliwia deweloperom Wywoływanie kodu centrum po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="cf16f-105">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="cf16f-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf16f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="cf16f-107">Zainstaluj :::no-loc(SignalR)::: pakiet klienta</span><span class="sxs-lookup"><span data-stu-id="cf16f-107">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="cf16f-108">:::no-loc(SignalR):::Biblioteka klienta JavaScript jest dostarczana jako pakiet [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="cf16f-108">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="cf16f-109">W poniższych sekcjach opisano różne sposoby instalowania biblioteki klienta programu.</span><span class="sxs-lookup"><span data-stu-id="cf16f-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="cf16f-110">Zainstaluj za pomocą npm</span><span class="sxs-lookup"><span data-stu-id="cf16f-110">Install with npm</span></span>

<span data-ttu-id="cf16f-111">W przypadku programu Visual Studio Uruchom następujące polecenia z **konsoli Menedżera pakietów** w folderze głównym.</span><span class="sxs-lookup"><span data-stu-id="cf16f-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="cf16f-112">Aby uzyskać Visual Studio Code, uruchom następujące polecenia w **zintegrowanym terminalu** .</span><span class="sxs-lookup"><span data-stu-id="cf16f-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="cf16f-113">npm instaluje zawartość pakietu w folderze *node_modules \\ @microsoft\signalr\dist\browser* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="cf16f-114">Utwórz nowy folder o nazwie *sygnalizujący* w folderze *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="cf16f-115">Skopiuj plik *signalr.js* do folderu *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="cf16f-116">Odwołuje się do :::no-loc(SignalR)::: klienta JavaScript w `<script>` elemencie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-116">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="cf16f-117">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf16f-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="cf16f-118">Użyj Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="cf16f-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="cf16f-119">Aby korzystać z biblioteki klienta bez wymagania wstępnego npm, odwołując się do kopii biblioteki klienckiej hostowanej w usłudze CDN.</span><span class="sxs-lookup"><span data-stu-id="cf16f-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="cf16f-120">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf16f-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="cf16f-121">Biblioteka kliencka jest dostępna w następujących sieci CDN:</span><span class="sxs-lookup"><span data-stu-id="cf16f-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="cf16f-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="cf16f-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="cf16f-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="cf16f-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="cf16f-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="cf16f-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="cf16f-125">Zainstaluj za pomocą LibMan</span><span class="sxs-lookup"><span data-stu-id="cf16f-125">Install with LibMan</span></span>

<span data-ttu-id="cf16f-126">[LibMan](xref:client-side/libman/index) można użyć do zainstalowania określonych plików biblioteki klienta z biblioteki klienta hostowanej przez sieć CDN.</span><span class="sxs-lookup"><span data-stu-id="cf16f-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="cf16f-127">Na przykład, Dodaj tylko plik JavaScript zminimalizowanego do projektu.</span><span class="sxs-lookup"><span data-stu-id="cf16f-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="cf16f-128">Aby uzyskać szczegółowe informacje na temat tego podejścia, zobacz [Dodawanie :::no-loc(SignalR)::: biblioteki klienta](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="cf16f-128">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="cf16f-129">Nawiązywanie połączenia z centrum</span><span class="sxs-lookup"><span data-stu-id="cf16f-129">Connect to a hub</span></span>

<span data-ttu-id="cf16f-130">Poniższy kod tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="cf16f-131">Nazwa centrum nie uwzględnia wielkości liter:</span><span class="sxs-lookup"><span data-stu-id="cf16f-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="cf16f-132">Połączenia między źródłami</span><span class="sxs-lookup"><span data-stu-id="cf16f-132">Cross-origin connections</span></span>

<span data-ttu-id="cf16f-133">Zwykle przeglądarki ładują połączenia z tej samej domeny co żądana strona.</span><span class="sxs-lookup"><span data-stu-id="cf16f-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="cf16f-134">Zdarza się jednak, że jest wymagane połączenie z inną domeną.</span><span class="sxs-lookup"><span data-stu-id="cf16f-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="cf16f-135">Aby uniemożliwić złośliwym lokacjom odczytywanie poufnych danych z innej lokacji, [połączenia między źródłami](xref:security/cors) są domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="cf16f-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="cf16f-136">Aby zezwolić na żądanie między źródłami, włącz je w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="cf16f-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="cf16f-137">Wywoływanie metod centrów z poziomu klienta</span><span class="sxs-lookup"><span data-stu-id="cf16f-137">Call hub methods from the client</span></span>

<span data-ttu-id="cf16f-138">Klienci języka JavaScript wywołują metody publiczne w centrach za pomocą metody [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) elementu [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="cf16f-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="cf16f-139">`invoke`Metoda akceptuje:</span><span class="sxs-lookup"><span data-stu-id="cf16f-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="cf16f-140">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="cf16f-140">The name of the hub method.</span></span>
* <span data-ttu-id="cf16f-141">Wszystkie argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cf16f-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="cf16f-142">W poniższym przykładzie nazwa metody w centrum to `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="cf16f-143">Drugi i trzeci argument przekazane do `invoke` mapowania na metodę Hub `user` i `message` argumenty:</span><span class="sxs-lookup"><span data-stu-id="cf16f-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="cf16f-144">Wywoływanie metod centrów z poziomu klienta jest obsługiwane tylko w przypadku korzystania z usługi platformy Azure :::no-loc(SignalR)::: w trybie *domyślnym* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-144">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="cf16f-145">Aby uzyskać więcej informacji, zobacz [często zadawane pytania (repozytorium Azure-sygnalizujące GitHub)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="cf16f-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="cf16f-146">`invoke`Metoda zwraca [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cf16f-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="cf16f-147">`Promise`Zostanie rozwiązany z wartością zwracaną (jeśli istnieje), gdy metoda zwraca serwer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="cf16f-148">Jeśli metoda na serwerze zgłasza błąd, `Promise` zostaje odrzucona z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="cf16f-149">Użyj `async` `await` metod i lub `Promise` i `then` , `catch` Aby obsłużyć te przypadki.</span><span class="sxs-lookup"><span data-stu-id="cf16f-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="cf16f-150">Klienci języka JavaScript mogą również wywoływać metody publiczne w centrach [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) za pomocą metody send `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="cf16f-151">W przeciwieństwie do `invoke` metody `send` Metoda nie czeka na odpowiedź z serwera.</span><span class="sxs-lookup"><span data-stu-id="cf16f-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="cf16f-152">`send`Metoda zwraca kod JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="cf16f-153">`Promise`Jest rozpoznawany, gdy wiadomość została wysłana na serwer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="cf16f-154">Jeśli wystąpi błąd podczas wysyłania komunikatu, `Promise` zostanie on odrzucony z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="cf16f-155">Użyj `async` `await` metod i lub `Promise` i `then` , `catch` Aby obsłużyć te przypadki.</span><span class="sxs-lookup"><span data-stu-id="cf16f-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="cf16f-156">Użycie `send` nie czeka na odebranie komunikatu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="cf16f-157">W związku z tym nie można zwrócić danych ani błędów z serwera.</span><span class="sxs-lookup"><span data-stu-id="cf16f-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="cf16f-158">Wywoływanie metod klienta z centrum</span><span class="sxs-lookup"><span data-stu-id="cf16f-158">Call client methods from the hub</span></span>

<span data-ttu-id="cf16f-159">Aby odbierać komunikaty z centrum, zdefiniuj metodę przy użyciu metody [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="cf16f-160">Nazwa metody klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cf16f-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="cf16f-161">Argumenty przekazywane przez centrum do metody.</span><span class="sxs-lookup"><span data-stu-id="cf16f-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="cf16f-162">W poniższym przykładzie nazwa metody to `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="cf16f-163">Nazwy argumentów są `user` `message` następujące:</span><span class="sxs-lookup"><span data-stu-id="cf16f-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="cf16f-164">Poprzedni kod w `connection.on` działa, gdy kod po stronie serwera wywołuje go przy użyciu <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> metody:</span><span class="sxs-lookup"><span data-stu-id="cf16f-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="cf16f-165">:::no-loc(SignalR)::: Określa, która metoda klienta ma być wywoływana przez dopasowanie nazwy metody i argumentów zdefiniowanych w `SendAsync` i `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-165">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="cf16f-166">Najlepszym rozwiązaniem jest wywołanie metody [startowej](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection` po `on` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="cf16f-167">Dzięki temu programy obsługi zostaną zarejestrowane przed odebraniem wszelkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="cf16f-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="cf16f-168">Obsługa błędów i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="cf16f-168">Error handling and logging</span></span>

<span data-ttu-id="cf16f-169">Użyj `try` `catch` metod i with `async` i `await` lub `Promise` , `catch` Aby obsłużyć błędy po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="cf16f-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="cf16f-170">Użyj `console.error` , aby wyprowadzić błędy do konsoli przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="cf16f-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="cf16f-171">Skonfiguruj śledzenie dzienników po stronie klienta, przekazując Rejestrator i typ zdarzenia, które będą rejestrowane po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="cf16f-172">Komunikaty są rejestrowane z określonym poziomem dziennika i wyższym.</span><span class="sxs-lookup"><span data-stu-id="cf16f-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="cf16f-173">Dostępne poziomy dzienników są następujące:</span><span class="sxs-lookup"><span data-stu-id="cf16f-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="cf16f-174">`signalR.LogLevel.Error`: Komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="cf16f-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="cf16f-175">Rejestruje `Error` tylko komunikaty.</span><span class="sxs-lookup"><span data-stu-id="cf16f-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="cf16f-176">`signalR.LogLevel.Warning`: Komunikaty ostrzegawcze dotyczące potencjalnych błędów.</span><span class="sxs-lookup"><span data-stu-id="cf16f-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="cf16f-177">Dzienniki `Warning` i `Error` komunikaty.</span><span class="sxs-lookup"><span data-stu-id="cf16f-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cf16f-178">`signalR.LogLevel.Information`: Komunikaty o stanie bez błędów.</span><span class="sxs-lookup"><span data-stu-id="cf16f-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="cf16f-179">Dzienniki `Information` , `Warning` i i `Error` wiadomości.</span><span class="sxs-lookup"><span data-stu-id="cf16f-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cf16f-180">`signalR.LogLevel.Trace`: Komunikaty śledzenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="cf16f-181">Rejestruje wszystko, w tym dane przesyłane między koncentratorem a klientem.</span><span class="sxs-lookup"><span data-stu-id="cf16f-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="cf16f-182">Użyj metody [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , aby skonfigurować poziom rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cf16f-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="cf16f-183">Komunikaty są rejestrowane w konsoli przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="cf16f-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="cf16f-184">Ponowne łączenie klientów</span><span class="sxs-lookup"><span data-stu-id="cf16f-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="cf16f-185">Automatycznie Połącz ponownie</span><span class="sxs-lookup"><span data-stu-id="cf16f-185">Automatically reconnect</span></span>

<span data-ttu-id="cf16f-186">Klient JavaScript dla programu :::no-loc(SignalR)::: można skonfigurować do automatycznego ponownego nawiązywania połączenia przy użyciu `withAutomaticReconnect` metody w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="cf16f-186">The JavaScript client for :::no-loc(SignalR)::: can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="cf16f-187">Domyślnie nie będzie automatycznie ponownie łączyć się.</span><span class="sxs-lookup"><span data-stu-id="cf16f-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="cf16f-188">Bez żadnych parametrów program `withAutomaticReconnect()` skonfiguruje klienta tak, aby czekał 0, 2, 10 i 30 sekund przed podjęciem próby ponownego połączenia, zatrzymywanie po czterech nieudanych próbach.</span><span class="sxs-lookup"><span data-stu-id="cf16f-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="cf16f-189">Przed rozpoczęciem dowolnych prób ponownego nawiązania połączenia `HubConnection` nastąpi przejście do `HubConnectionState.Reconnecting` stanu i uruchomienie jego `onreconnecting` wywołań zwrotnych zamiast przejścia do `Disconnected` stanu i wyzwalanie jego `onclose` wywołań zwrotnych, takich jak `HubConnection` bez automatycznego ponownego łączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="cf16f-190">Dzięki temu można ostrzec użytkowników, że połączenie zostało utracone i wyłączyć elementy interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cf16f-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="cf16f-191">Jeśli klient pomyślnie ponownie nawiązuje połączenie w ramach pierwszych czterech prób, `HubConnection` nastąpi powrót do `Connected` stanu i wyzwolenie jego `onreconnected` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="cf16f-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="cf16f-192">Zapewnia to możliwość powiadomienia użytkowników o tym, że połączenie zostało ponownie nawiązane.</span><span class="sxs-lookup"><span data-stu-id="cf16f-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="cf16f-193">Ponieważ połączenie jest całkowicie nowe dla serwera, `connectionId` zostanie dostarczone nowe `onreconnected` wywołanie zwrotne.</span><span class="sxs-lookup"><span data-stu-id="cf16f-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="cf16f-194">`onreconnected`Parametr wywołania zwrotnego `connectionId` zostanie niezdefiniowany, jeśli `HubConnection` został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="cf16f-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="cf16f-195">`withAutomaticReconnect()` nie zostanie skonfigurowana `HubConnection` do ponawiania początkowych nieudanych uruchomień, dlatego należy ręcznie obsługiwać błędy uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="cf16f-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log(":::no-loc(SignalR)::: Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="cf16f-196">Jeśli klient nie będzie mógł ponownie nawiązać połączenia w ramach pierwszych czterech prób, `HubConnection` przejdzie do `Disconnected` stanu i uruchomi wywołania zwrotne jego [zamknięcia](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="cf16f-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="cf16f-197">Dzięki temu użytkownicy mogą informować, że połączenie zostało trwale utracone i zalecamy odświeżenie strony:</span><span class="sxs-lookup"><span data-stu-id="cf16f-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="cf16f-198">Aby skonfigurować niestandardową liczbę prób ponownego połączenia przed odłączeniem lub zmianą czasu ponownego połączenia, program `withAutomaticReconnect` akceptuje tablicę liczb reprezentujących opóźnienie (w milisekundach) przed rozpoczęciem każdej próby ponownego nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="cf16f-199">W powyższym przykładzie konfiguruje `HubConnection` się, aby rozpocząć próba ponownego połączenia natychmiast po utracie połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="cf16f-200">Dotyczy to również konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="cf16f-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="cf16f-201">Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia zostanie również uruchomiona natychmiast, a nie w ciągu 2 sekund, tak jak w przypadku konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="cf16f-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="cf16f-202">Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego nawiązania połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="cf16f-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="cf16f-203">Zachowanie niestandardowe jest następnie niezgodne z zachowaniem domyślnym przez zatrzymanie po trzeciej nieudanej próbie nawiązania połączenia zamiast próby wykonania kolejnej próby ponownego połączenia w ciągu innych 30 sekund, takich jak w przypadku konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="cf16f-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="cf16f-204">Jeśli chcesz jeszcze większą kontrolę nad chronometrażem i liczbą prób automatycznego ponownego połączenia, `withAutomaticReconnect` zaakceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma pojedynczą metodę o nazwie `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="cf16f-205">`nextRetryDelayInMilliseconds` przyjmuje jeden argument z typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="cf16f-206">`RetryContext`Ma trzy właściwości: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` które są `number` odpowiednio, a `number` i a `Error` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="cf16f-207">Przed pierwszym ponownym połączeniem, oba `previousRetryCount` i `elapsedMilliseconds` będą miały wartość zero, a `retryReason` będzie to błąd, który spowodował utratę połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="cf16f-208">Po każdym nieudanej próbie ponowieniu próby `previousRetryCount` zostanie zaktualizowany w `elapsedMilliseconds` celu odzwierciedlenia ilości czasu poświęconego na przełączenie do tej pory w milisekundach, a `retryReason` będzie to błąd, który spowodował, że Ostatnia próba ponownego połączenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="cf16f-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="cf16f-209">`nextRetryDelayInMilliseconds` musi zwracać liczbę określającą liczbę milisekund oczekiwania przed kolejną próbą ponownego połączenia lub `null` Jeśli `HubConnection` należy zatrzymać Ponowne nawiązywanie połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

<span data-ttu-id="cf16f-210">Alternatywnie można napisać kod, który ponownie podłącze klienta ręcznie, jak pokazano w [ręcznym ponownym nawiązaniu połączenia](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="cf16f-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="cf16f-211">Ręczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="cf16f-211">Manually reconnect</span></span>

<span data-ttu-id="cf16f-212">Poniższy kod ilustruje typowe podejście do ponownego łączenia ręcznego:</span><span class="sxs-lookup"><span data-stu-id="cf16f-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="cf16f-213">Funkcja (w tym przypadku `start` Funkcja) została utworzona w celu uruchomienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="cf16f-214">Wywołaj `start` funkcję w `onclose` obsłudze zdarzeń połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="cf16f-215">Implementacja rzeczywista będzie używać wykładniczej kopii zapasowej lub ponowić określoną liczbę razy przed pokazaniem.</span><span class="sxs-lookup"><span data-stu-id="cf16f-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf16f-216">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cf16f-216">Additional resources</span></span>

* [<span data-ttu-id="cf16f-217">Dokumentacja interfejsów API języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="cf16f-218">Samouczek JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="cf16f-219">Samouczek WebPack i język TypeScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="cf16f-220">Centra</span><span class="sxs-lookup"><span data-stu-id="cf16f-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="cf16f-221">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="cf16f-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="cf16f-222">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="cf16f-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="cf16f-223">Żądania między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="cf16f-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="cf16f-224">:::no-loc(SignalR):::Dokumentacja bezserwerowa usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="cf16f-224">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="cf16f-225">Rozwiązywanie problemów z błędami połączenia</span><span class="sxs-lookup"><span data-stu-id="cf16f-225">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf16f-226">Autor [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="cf16f-226">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="cf16f-227">:::no-loc(SignalR):::Biblioteka klienta ASP.NET Core JavaScript umożliwia deweloperom Wywoływanie kodu centrum po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="cf16f-227">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="cf16f-228">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf16f-228">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="cf16f-229">Zainstaluj :::no-loc(SignalR)::: pakiet klienta</span><span class="sxs-lookup"><span data-stu-id="cf16f-229">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="cf16f-230">:::no-loc(SignalR):::Biblioteka klienta JavaScript jest dostarczana jako pakiet [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="cf16f-230">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="cf16f-231">W poniższych sekcjach opisano różne sposoby instalowania biblioteki klienta programu.</span><span class="sxs-lookup"><span data-stu-id="cf16f-231">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="cf16f-232">Zainstaluj za pomocą npm</span><span class="sxs-lookup"><span data-stu-id="cf16f-232">Install with npm</span></span>

<span data-ttu-id="cf16f-233">W przypadku korzystania z programu Visual Studio Uruchom następujące polecenia z **konsoli Menedżera pakietów** w folderze głównym.</span><span class="sxs-lookup"><span data-stu-id="cf16f-233">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="cf16f-234">Aby uzyskać Visual Studio Code, uruchom następujące polecenia w **zintegrowanym terminalu** .</span><span class="sxs-lookup"><span data-stu-id="cf16f-234">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="cf16f-235">npm instaluje zawartość pakietu w folderze *node_modules \\ @aspnet\signalr\dist\browser* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-235">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="cf16f-236">Utwórz nowy folder o nazwie *sygnalizujący* w folderze *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-236">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="cf16f-237">Skopiuj plik *signalr.js* do folderu *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-237">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="cf16f-238">Odwołuje się do :::no-loc(SignalR)::: klienta JavaScript w `<script>` elemencie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-238">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="cf16f-239">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf16f-239">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="cf16f-240">Użyj Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="cf16f-240">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="cf16f-241">Aby korzystać z biblioteki klienta bez wymagania wstępnego npm, odwołując się do kopii biblioteki klienckiej hostowanej w usłudze CDN.</span><span class="sxs-lookup"><span data-stu-id="cf16f-241">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="cf16f-242">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cf16f-242">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="cf16f-243">Biblioteka kliencka jest dostępna w następujących sieci CDN:</span><span class="sxs-lookup"><span data-stu-id="cf16f-243">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="cf16f-244">cdnjs</span><span class="sxs-lookup"><span data-stu-id="cf16f-244">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="cf16f-245">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="cf16f-245">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="cf16f-246">unpkg</span><span class="sxs-lookup"><span data-stu-id="cf16f-246">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="cf16f-247">Zainstaluj za pomocą LibMan</span><span class="sxs-lookup"><span data-stu-id="cf16f-247">Install with LibMan</span></span>

<span data-ttu-id="cf16f-248">[LibMan](xref:client-side/libman/index) można użyć do zainstalowania określonych plików biblioteki klienta z biblioteki klienta hostowanej przez sieć CDN.</span><span class="sxs-lookup"><span data-stu-id="cf16f-248">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="cf16f-249">Na przykład, Dodaj tylko plik JavaScript zminimalizowanego do projektu.</span><span class="sxs-lookup"><span data-stu-id="cf16f-249">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="cf16f-250">Aby uzyskać szczegółowe informacje na temat tego podejścia, zobacz [Dodawanie :::no-loc(SignalR)::: biblioteki klienta](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="cf16f-250">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="cf16f-251">Nawiązywanie połączenia z centrum</span><span class="sxs-lookup"><span data-stu-id="cf16f-251">Connect to a hub</span></span>

<span data-ttu-id="cf16f-252">Poniższy kod tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-252">The following code creates and starts a connection.</span></span> <span data-ttu-id="cf16f-253">Nazwa centrum nie uwzględnia wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="cf16f-253">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="cf16f-254">Połączenia między źródłami</span><span class="sxs-lookup"><span data-stu-id="cf16f-254">Cross-origin connections</span></span>

<span data-ttu-id="cf16f-255">Zwykle przeglądarki ładują połączenia z tej samej domeny co żądana strona.</span><span class="sxs-lookup"><span data-stu-id="cf16f-255">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="cf16f-256">Zdarza się jednak, że jest wymagane połączenie z inną domeną.</span><span class="sxs-lookup"><span data-stu-id="cf16f-256">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="cf16f-257">Aby uniemożliwić złośliwym lokacjom odczytywanie poufnych danych z innej lokacji, [połączenia między źródłami](xref:security/cors) są domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="cf16f-257">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="cf16f-258">Aby zezwolić na żądanie między źródłami, włącz je w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-258">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="cf16f-259">Wywoływanie metod centrów z klienta</span><span class="sxs-lookup"><span data-stu-id="cf16f-259">Call hub methods from client</span></span>

<span data-ttu-id="cf16f-260">Klienci języka JavaScript wywołują metody publiczne w centrach za pomocą metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) elementu [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="cf16f-260">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="cf16f-261">`invoke`Metoda akceptuje dwa argumenty:</span><span class="sxs-lookup"><span data-stu-id="cf16f-261">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="cf16f-262">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="cf16f-262">The name of the hub method.</span></span> <span data-ttu-id="cf16f-263">W poniższym przykładzie nazwa metody w centrum to `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-263">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="cf16f-264">Wszystkie argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="cf16f-264">Any arguments defined in the hub method.</span></span> <span data-ttu-id="cf16f-265">W poniższym przykładzie nazwa argumentu to `message` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-265">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="cf16f-266">Przykładowy kod używa składni funkcji ze strzałką, która jest obsługiwana w bieżących wersjach wszystkich głównych przeglądarek z wyjątkiem programu Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-266">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="cf16f-267">Wywoływanie metod centrów z poziomu klienta jest obsługiwane tylko w przypadku korzystania z usługi platformy Azure :::no-loc(SignalR)::: w trybie *domyślnym* .</span><span class="sxs-lookup"><span data-stu-id="cf16f-267">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="cf16f-268">Aby uzyskać więcej informacji, zobacz [często zadawane pytania (repozytorium Azure-sygnalizujące GitHub)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="cf16f-268">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="cf16f-269">`invoke`Metoda zwraca [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cf16f-269">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="cf16f-270">`Promise`Zostanie rozwiązany z wartością zwracaną (jeśli istnieje), gdy metoda zwraca serwer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-270">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="cf16f-271">Jeśli metoda na serwerze zgłasza błąd, `Promise` zostaje odrzucona z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-271">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="cf16f-272">Użyj `then` metod i `catch` dla `Promise` samej siebie, aby obsłużyć te przypadki (lub `await` składnię).</span><span class="sxs-lookup"><span data-stu-id="cf16f-272">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="cf16f-273">`send`Metoda zwraca kod JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-273">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="cf16f-274">`Promise`Jest rozpoznawany, gdy wiadomość została wysłana na serwer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-274">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="cf16f-275">Jeśli wystąpi błąd podczas wysyłania komunikatu, `Promise` zostanie on odrzucony z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-275">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="cf16f-276">Użyj `then` metod i `catch` dla `Promise` samej siebie, aby obsłużyć te przypadki (lub `await` składnię).</span><span class="sxs-lookup"><span data-stu-id="cf16f-276">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="cf16f-277">Użycie `send` nie czeka na odebranie komunikatu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="cf16f-277">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="cf16f-278">W związku z tym nie można zwrócić danych ani błędów z serwera.</span><span class="sxs-lookup"><span data-stu-id="cf16f-278">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="cf16f-279">Wywoływanie metod klienta z centrum</span><span class="sxs-lookup"><span data-stu-id="cf16f-279">Call client methods from hub</span></span>

<span data-ttu-id="cf16f-280">Aby odbierać komunikaty z centrum, zdefiniuj metodę przy użyciu metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-280">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="cf16f-281">Nazwa metody klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cf16f-281">The name of the JavaScript client method.</span></span> <span data-ttu-id="cf16f-282">W poniższym przykładzie nazwa metody to `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-282">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="cf16f-283">Argumenty przekazywane przez centrum do metody.</span><span class="sxs-lookup"><span data-stu-id="cf16f-283">Arguments the hub passes to the method.</span></span> <span data-ttu-id="cf16f-284">W poniższym przykładzie wartość argumentu to `message` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-284">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="cf16f-285">Poprzedni kod w `connection.on` działa, gdy kod po stronie serwera wywołuje go przy użyciu <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="cf16f-285">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="cf16f-286">:::no-loc(SignalR)::: Określa, która metoda klienta ma być wywoływana przez dopasowanie nazwy metody i argumentów zdefiniowanych w `SendAsync` i `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-286">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="cf16f-287">Najlepszym rozwiązaniem jest wywołanie metody [startowej](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection` po `on` .</span><span class="sxs-lookup"><span data-stu-id="cf16f-287">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="cf16f-288">Dzięki temu programy obsługi zostaną zarejestrowane przed odebraniem wszelkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="cf16f-288">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="cf16f-289">Obsługa błędów i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="cf16f-289">Error handling and logging</span></span>

<span data-ttu-id="cf16f-290">Łańcuchuje `catch` metodę na końcu `start` metody w celu obsługi błędów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="cf16f-290">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="cf16f-291">Służy `console.error` do wyprowadzania błędów do konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cf16f-291">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="cf16f-292">Skonfiguruj śledzenie dzienników po stronie klienta, przekazując Rejestrator i typ zdarzenia, które będą rejestrowane po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-292">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="cf16f-293">Komunikaty są rejestrowane z określonym poziomem dziennika i wyższym.</span><span class="sxs-lookup"><span data-stu-id="cf16f-293">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="cf16f-294">Dostępne poziomy dzienników są następujące:</span><span class="sxs-lookup"><span data-stu-id="cf16f-294">Available log levels are as follows:</span></span>

* <span data-ttu-id="cf16f-295">`signalR.LogLevel.Error`: Komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="cf16f-295">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="cf16f-296">Rejestruje `Error` tylko komunikaty.</span><span class="sxs-lookup"><span data-stu-id="cf16f-296">Logs `Error` messages only.</span></span>
* <span data-ttu-id="cf16f-297">`signalR.LogLevel.Warning`: Komunikaty ostrzegawcze dotyczące potencjalnych błędów.</span><span class="sxs-lookup"><span data-stu-id="cf16f-297">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="cf16f-298">Dzienniki `Warning` i `Error` komunikaty.</span><span class="sxs-lookup"><span data-stu-id="cf16f-298">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cf16f-299">`signalR.LogLevel.Information`: Komunikaty o stanie bez błędów.</span><span class="sxs-lookup"><span data-stu-id="cf16f-299">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="cf16f-300">Dzienniki `Information` , `Warning` i i `Error` wiadomości.</span><span class="sxs-lookup"><span data-stu-id="cf16f-300">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cf16f-301">`signalR.LogLevel.Trace`: Komunikaty śledzenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-301">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="cf16f-302">Rejestruje wszystko, w tym dane przesyłane między koncentratorem a klientem.</span><span class="sxs-lookup"><span data-stu-id="cf16f-302">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="cf16f-303">Użyj metody [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , aby skonfigurować poziom rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="cf16f-303">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="cf16f-304">Komunikaty są rejestrowane w konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cf16f-304">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="cf16f-305">Ponowne łączenie klientów</span><span class="sxs-lookup"><span data-stu-id="cf16f-305">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="cf16f-306">Ręczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="cf16f-306">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="cf16f-307">W systemach wcześniejszych niż 3,0 klient JavaScript dla programu :::no-loc(SignalR)::: nie będzie automatycznie ponownie łączyć się.</span><span class="sxs-lookup"><span data-stu-id="cf16f-307">Prior to 3.0, the JavaScript client for :::no-loc(SignalR)::: doesn't automatically reconnect.</span></span> <span data-ttu-id="cf16f-308">Musisz napisać kod, który ponownie podłącze klienta ręcznie.</span><span class="sxs-lookup"><span data-stu-id="cf16f-308">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="cf16f-309">Poniższy kod ilustruje typowe podejście do ponownego łączenia ręcznego:</span><span class="sxs-lookup"><span data-stu-id="cf16f-309">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="cf16f-310">Funkcja (w tym przypadku `start` Funkcja) została utworzona w celu uruchomienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-310">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="cf16f-311">Wywołaj `start` funkcję w `onclose` obsłudze zdarzeń połączenia.</span><span class="sxs-lookup"><span data-stu-id="cf16f-311">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="cf16f-312">Implementacja rzeczywista będzie używać wykładniczej kopii zapasowej lub ponowić określoną liczbę razy przed pokazaniem.</span><span class="sxs-lookup"><span data-stu-id="cf16f-312">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf16f-313">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cf16f-313">Additional resources</span></span>

* [<span data-ttu-id="cf16f-314">Dokumentacja interfejsów API języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-314">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="cf16f-315">Samouczek JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-315">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="cf16f-316">Samouczek WebPack i język TypeScript</span><span class="sxs-lookup"><span data-stu-id="cf16f-316">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="cf16f-317">Centra</span><span class="sxs-lookup"><span data-stu-id="cf16f-317">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="cf16f-318">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="cf16f-318">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="cf16f-319">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="cf16f-319">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="cf16f-320">Żądania między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="cf16f-320">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="cf16f-321">:::no-loc(SignalR):::Dokumentacja bezserwerowa usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="cf16f-321">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
