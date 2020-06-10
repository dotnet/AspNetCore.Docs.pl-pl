---
title: SignalRKlient ASP.NET Core JavaScript
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
ms.openlocfilehash: 4b3a4b2323b7f221d9cd1aab1c56d1d9828eb916
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106458"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="d5490-103">SignalRKlient ASP.NET Core JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5490-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="d5490-104">Autor [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="d5490-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="d5490-105">SignalRBiblioteka klienta ASP.NET Core JavaScript umożliwia deweloperom Wywoływanie kodu centrum po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="d5490-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="d5490-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5490-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="d5490-107">Zainstaluj SignalR pakiet klienta</span><span class="sxs-lookup"><span data-stu-id="d5490-107">Install the SignalR client package</span></span>

<span data-ttu-id="d5490-108">SignalRBiblioteka klienta JavaScript jest dostarczana jako pakiet [npm](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="d5490-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="d5490-109">W poniższych sekcjach opisano różne sposoby instalowania biblioteki klienta programu.</span><span class="sxs-lookup"><span data-stu-id="d5490-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="d5490-110">Zainstaluj za pomocą npm</span><span class="sxs-lookup"><span data-stu-id="d5490-110">Install with npm</span></span>

<span data-ttu-id="d5490-111">W przypadku korzystania z programu Visual Studio Uruchom następujące polecenia z **konsoli Menedżera pakietów** w folderze głównym.</span><span class="sxs-lookup"><span data-stu-id="d5490-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="d5490-112">Aby uzyskać Visual Studio Code, uruchom następujące polecenia w **zintegrowanym terminalu**.</span><span class="sxs-lookup"><span data-stu-id="d5490-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="d5490-113">npm instaluje zawartość pakietu w folderze \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="d5490-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d5490-114">Utwórz nowy folder o nazwie *sygnalizujący* w folderze *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="d5490-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d5490-115">Skopiuj plik *sygnalizującer. js* do folderu *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="d5490-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="d5490-116">npm instaluje zawartość pakietu w folderze \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="d5490-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d5490-117">Utwórz nowy folder o nazwie *sygnalizujący* w folderze *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="d5490-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d5490-118">Skopiuj plik *sygnalizującer. js* do folderu *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="d5490-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="d5490-119">Odwołuje się do SignalR klienta JavaScript w `<script>` elemencie.</span><span class="sxs-lookup"><span data-stu-id="d5490-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="d5490-120">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d5490-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="d5490-121">Użyj Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="d5490-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="d5490-122">Aby korzystać z biblioteki klienta bez wymagania wstępnego npm, odwołując się do kopii biblioteki klienckiej hostowanej w usłudze CDN.</span><span class="sxs-lookup"><span data-stu-id="d5490-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="d5490-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d5490-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="d5490-124">Biblioteka kliencka jest dostępna w następujących sieci CDN:</span><span class="sxs-lookup"><span data-stu-id="d5490-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="d5490-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="d5490-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="d5490-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="d5490-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="d5490-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="d5490-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="d5490-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="d5490-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="d5490-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="d5490-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="d5490-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="d5490-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="d5490-131">Zainstaluj za pomocą LibMan</span><span class="sxs-lookup"><span data-stu-id="d5490-131">Install with LibMan</span></span>

<span data-ttu-id="d5490-132">[LibMan](xref:client-side/libman/index) można użyć do zainstalowania określonych plików biblioteki klienta z biblioteki klienta hostowanej przez sieć CDN.</span><span class="sxs-lookup"><span data-stu-id="d5490-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="d5490-133">Na przykład, Dodaj tylko plik JavaScript zminimalizowanego do projektu.</span><span class="sxs-lookup"><span data-stu-id="d5490-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="d5490-134">Aby uzyskać szczegółowe informacje na temat tego podejścia, zobacz [Dodawanie SignalR biblioteki klienta](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="d5490-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="d5490-135">Nawiązywanie połączenia z centrum</span><span class="sxs-lookup"><span data-stu-id="d5490-135">Connect to a hub</span></span>

<span data-ttu-id="d5490-136">Poniższy kod tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="d5490-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="d5490-137">Nazwa centrum nie uwzględnia wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="d5490-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="d5490-138">Połączenia między źródłami</span><span class="sxs-lookup"><span data-stu-id="d5490-138">Cross-origin connections</span></span>

<span data-ttu-id="d5490-139">Zwykle przeglądarki ładują połączenia z tej samej domeny co żądana strona.</span><span class="sxs-lookup"><span data-stu-id="d5490-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="d5490-140">Zdarza się jednak, że jest wymagane połączenie z inną domeną.</span><span class="sxs-lookup"><span data-stu-id="d5490-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="d5490-141">Aby uniemożliwić złośliwym lokacjom odczytywanie poufnych danych z innej lokacji, [połączenia między źródłami](xref:security/cors) są domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="d5490-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="d5490-142">Aby zezwolić na żądanie między źródłami, włącz je w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="d5490-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="d5490-143">Wywoływanie metod centrów z klienta</span><span class="sxs-lookup"><span data-stu-id="d5490-143">Call hub methods from client</span></span>

<span data-ttu-id="d5490-144">Klienci języka JavaScript wywołują metody publiczne w centrach za pomocą metody [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) elementu [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="d5490-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="d5490-145">`invoke`Metoda akceptuje dwa argumenty:</span><span class="sxs-lookup"><span data-stu-id="d5490-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="d5490-146">Nazwa metody centrum.</span><span class="sxs-lookup"><span data-stu-id="d5490-146">The name of the hub method.</span></span> <span data-ttu-id="d5490-147">W poniższym przykładzie nazwa metody w centrum to `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="d5490-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="d5490-148">Wszystkie argumenty zdefiniowane w metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="d5490-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="d5490-149">W poniższym przykładzie nazwa argumentu to `message` .</span><span class="sxs-lookup"><span data-stu-id="d5490-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="d5490-150">Przykładowy kod używa składni funkcji ze strzałką, która jest obsługiwana w bieżących wersjach wszystkich głównych przeglądarek z wyjątkiem programu Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="d5490-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="d5490-151">W przypadku korzystania z usługi platformy Azure SignalR w *trybie bezserwerowym*nie można wywoływać metod centralnych z poziomu klienta.</span><span class="sxs-lookup"><span data-stu-id="d5490-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="d5490-152">Aby uzyskać więcej informacji, zobacz [ SignalR dokumentację usługi](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="d5490-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="d5490-153">`invoke`Metoda zwraca [obietnicę](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5490-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="d5490-154">`Promise`Zostanie rozwiązany z wartością zwracaną (jeśli istnieje), gdy metoda zwraca serwer.</span><span class="sxs-lookup"><span data-stu-id="d5490-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="d5490-155">Jeśli metoda na serwerze zgłasza błąd, `Promise` zostaje odrzucona z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="d5490-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d5490-156">Użyj `then` metod i `catch` dla `Promise` samej siebie, aby obsłużyć te przypadki (lub `await` składnię).</span><span class="sxs-lookup"><span data-stu-id="d5490-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="d5490-157">`send`Metoda zwraca kod JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="d5490-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="d5490-158">`Promise`Jest rozpoznawany, gdy wiadomość została wysłana na serwer.</span><span class="sxs-lookup"><span data-stu-id="d5490-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="d5490-159">Jeśli wystąpi błąd podczas wysyłania komunikatu, `Promise` zostanie on odrzucony z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="d5490-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d5490-160">Użyj `then` metod i `catch` dla `Promise` samej siebie, aby obsłużyć te przypadki (lub `await` składnię).</span><span class="sxs-lookup"><span data-stu-id="d5490-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="d5490-161">Użycie `send` nie czeka na odebranie komunikatu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="d5490-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="d5490-162">W związku z tym nie można zwrócić danych ani błędów z serwera.</span><span class="sxs-lookup"><span data-stu-id="d5490-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="d5490-163">Wywoływanie metod klienta z centrum</span><span class="sxs-lookup"><span data-stu-id="d5490-163">Call client methods from hub</span></span>

<span data-ttu-id="d5490-164">Aby odbierać komunikaty z centrum, zdefiniuj metodę przy użyciu metody [on](/javascript/api/%40aspnet/signalr/hubconnection#on) `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5490-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="d5490-165">Nazwa metody klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5490-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="d5490-166">W poniższym przykładzie nazwa metody to `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="d5490-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="d5490-167">Argumenty przekazywane przez centrum do metody.</span><span class="sxs-lookup"><span data-stu-id="d5490-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="d5490-168">W poniższym przykładzie wartość argumentu to `message` .</span><span class="sxs-lookup"><span data-stu-id="d5490-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="d5490-169">Poprzedni kod w `connection.on` działa, gdy kod po stronie serwera wywołuje go przy użyciu metody [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="d5490-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="d5490-170">Określa, która metoda klienta ma być wywoływana przez dopasowanie nazwy metody i argumentów zdefiniowanych w `SendAsync` i `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="d5490-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="d5490-171">Najlepszym rozwiązaniem jest wywołanie metody [startowej](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection` po `on` .</span><span class="sxs-lookup"><span data-stu-id="d5490-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="d5490-172">Dzięki temu programy obsługi zostaną zarejestrowane przed odebraniem wszelkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="d5490-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="d5490-173">Obsługa błędów i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="d5490-173">Error handling and logging</span></span>

<span data-ttu-id="d5490-174">Łańcuchuje `catch` metodę na końcu `start` metody w celu obsługi błędów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="d5490-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="d5490-175">Służy `console.error` do wyprowadzania błędów do konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="d5490-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="d5490-176">Skonfiguruj śledzenie dzienników po stronie klienta, przekazując Rejestrator i typ zdarzenia, które będą rejestrowane po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="d5490-177">Komunikaty są rejestrowane z określonym poziomem dziennika i wyższym.</span><span class="sxs-lookup"><span data-stu-id="d5490-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="d5490-178">Dostępne poziomy dzienników są następujące:</span><span class="sxs-lookup"><span data-stu-id="d5490-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="d5490-179">`signalR.LogLevel.Error`: Komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="d5490-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="d5490-180">Rejestruje `Error` tylko komunikaty.</span><span class="sxs-lookup"><span data-stu-id="d5490-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="d5490-181">`signalR.LogLevel.Warning`: Komunikaty ostrzegawcze dotyczące potencjalnych błędów.</span><span class="sxs-lookup"><span data-stu-id="d5490-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="d5490-182">Dzienniki `Warning` i `Error` komunikaty.</span><span class="sxs-lookup"><span data-stu-id="d5490-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d5490-183">`signalR.LogLevel.Information`: Komunikaty o stanie bez błędów.</span><span class="sxs-lookup"><span data-stu-id="d5490-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="d5490-184">Dzienniki `Information` , `Warning` i i `Error` wiadomości.</span><span class="sxs-lookup"><span data-stu-id="d5490-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d5490-185">`signalR.LogLevel.Trace`: Komunikaty śledzenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="d5490-186">Rejestruje wszystko, w tym dane przesyłane między koncentratorem a klientem.</span><span class="sxs-lookup"><span data-stu-id="d5490-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="d5490-187">Użyj metody [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , aby skonfigurować poziom rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="d5490-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="d5490-188">Komunikaty są rejestrowane w konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="d5490-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="d5490-189">Ponowne łączenie klientów</span><span class="sxs-lookup"><span data-stu-id="d5490-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="d5490-190">Automatycznie Połącz ponownie</span><span class="sxs-lookup"><span data-stu-id="d5490-190">Automatically reconnect</span></span>

<span data-ttu-id="d5490-191">Klient JavaScript dla programu SignalR można skonfigurować do automatycznego ponownego nawiązywania połączenia przy użyciu `withAutomaticReconnect` metody w [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5490-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="d5490-192">Domyślnie nie będzie automatycznie ponownie łączyć się.</span><span class="sxs-lookup"><span data-stu-id="d5490-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="d5490-193">Bez żadnych parametrów program `withAutomaticReconnect()` skonfiguruje klienta tak, aby czekał 0, 2, 10 i 30 sekund przed podjęciem próby ponownego połączenia, zatrzymywanie po czterech nieudanych próbach.</span><span class="sxs-lookup"><span data-stu-id="d5490-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="d5490-194">Przed rozpoczęciem dowolnych prób ponownego nawiązania połączenia `HubConnection` nastąpi przejście do `HubConnectionState.Reconnecting` stanu i uruchomienie jego `onreconnecting` wywołań zwrotnych zamiast przejścia do `Disconnected` stanu i wyzwalanie jego `onclose` wywołań zwrotnych, takich jak `HubConnection` bez automatycznego ponownego łączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="d5490-195">Dzięki temu można ostrzec użytkowników, że połączenie zostało utracone i wyłączyć elementy interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d5490-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d5490-196">Jeśli klient pomyślnie ponownie nawiązuje połączenie w ramach pierwszych czterech prób, `HubConnection` nastąpi powrót do `Connected` stanu i wyzwolenie jego `onreconnected` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="d5490-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="d5490-197">Zapewnia to możliwość powiadomienia użytkowników o tym, że połączenie zostało ponownie nawiązane.</span><span class="sxs-lookup"><span data-stu-id="d5490-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="d5490-198">Ponieważ połączenie jest całkowicie nowe dla serwera, `connectionId` zostanie dostarczone nowe `onreconnected` wywołanie zwrotne.</span><span class="sxs-lookup"><span data-stu-id="d5490-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="d5490-199">`onreconnected`Parametr wywołania zwrotnego `connectionId` zostanie niezdefiniowany, jeśli `HubConnection` został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="d5490-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d5490-200">`withAutomaticReconnect()`nie zostanie skonfigurowana `HubConnection` do ponawiania początkowych nieudanych uruchomień, dlatego należy ręcznie obsługiwać błędy uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="d5490-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="d5490-201">Jeśli klient nie będzie mógł ponownie nawiązać połączenia w ramach pierwszych czterech prób, `HubConnection` przejdzie do `Disconnected` stanu i uruchomi wywołania zwrotne jego [zamknięcia](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="d5490-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="d5490-202">Dzięki temu użytkownicy mogą informować, że połączenie zostało trwale utracone i zalecamy odświeżenie strony:</span><span class="sxs-lookup"><span data-stu-id="d5490-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d5490-203">Aby skonfigurować niestandardową liczbę prób ponownego połączenia przed odłączeniem lub zmianą czasu ponownego połączenia, program `withAutomaticReconnect` akceptuje tablicę liczb reprezentujących opóźnienie (w milisekundach) przed rozpoczęciem każdej próby ponownego nawiązania połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="d5490-204">W powyższym przykładzie konfiguruje `HubConnection` się, aby rozpocząć próba ponownego połączenia natychmiast po utracie połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="d5490-205">Dotyczy to również konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="d5490-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="d5490-206">Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia zostanie również uruchomiona natychmiast, a nie w ciągu 2 sekund, tak jak w przypadku konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="d5490-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d5490-207">Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego nawiązania połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="d5490-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="d5490-208">Zachowanie niestandardowe jest następnie niezgodne z zachowaniem domyślnym przez zatrzymanie po trzeciej nieudanej próbie nawiązania połączenia zamiast próby wykonania kolejnej próby ponownego połączenia w ciągu innych 30 sekund, takich jak w przypadku konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="d5490-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d5490-209">Jeśli chcesz jeszcze większą kontrolę nad chronometrażem i liczbą prób automatycznego ponownego połączenia, `withAutomaticReconnect` zaakceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma pojedynczą metodę o nazwie `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="d5490-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="d5490-210">`nextRetryDelayInMilliseconds`przyjmuje jeden argument z typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="d5490-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="d5490-211">`RetryContext`Ma trzy właściwości: `previousRetryCount` , `elapsedMilliseconds` a `retryReason` które są `number` odpowiednio, a `number` i a `Error` .</span><span class="sxs-lookup"><span data-stu-id="d5490-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="d5490-212">Przed pierwszym ponownym połączeniem, oba `previousRetryCount` i `elapsedMilliseconds` będą miały wartość zero, a `retryReason` będzie to błąd, który spowodował utratę połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="d5490-213">Po każdym nieudanej próbie ponowieniu próby `previousRetryCount` zostanie zaktualizowany w `elapsedMilliseconds` celu odzwierciedlenia ilości czasu poświęconego na przełączenie do tej pory w milisekundach, a `retryReason` będzie to błąd, który spowodował, że Ostatnia próba ponownego połączenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="d5490-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="d5490-214">`nextRetryDelayInMilliseconds`musi zwracać liczbę określającą liczbę milisekund oczekiwania przed kolejną próbą ponownego połączenia lub `null` Jeśli `HubConnection` należy zatrzymać Ponowne nawiązywanie połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="d5490-215">Alternatywnie można napisać kod, który ponownie podłącze klienta ręcznie, jak pokazano w [ręcznym ponownym nawiązaniu połączenia](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="d5490-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="d5490-216">Ręczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="d5490-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="d5490-217">W systemach wcześniejszych niż 3,0 klient JavaScript dla programu SignalR nie będzie automatycznie ponownie łączyć się.</span><span class="sxs-lookup"><span data-stu-id="d5490-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="d5490-218">Musisz napisać kod, który ponownie podłącze klienta ręcznie.</span><span class="sxs-lookup"><span data-stu-id="d5490-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="d5490-219">Poniższy kod ilustruje typowe podejście do ponownego łączenia ręcznego:</span><span class="sxs-lookup"><span data-stu-id="d5490-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="d5490-220">Funkcja (w tym przypadku `start` Funkcja) została utworzona w celu uruchomienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="d5490-221">Wywołaj `start` funkcję w `onclose` obsłudze zdarzeń połączenia.</span><span class="sxs-lookup"><span data-stu-id="d5490-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="d5490-222">Implementacja rzeczywista będzie używać wykładniczej kopii zapasowej lub ponowić określoną liczbę razy przed pokazaniem.</span><span class="sxs-lookup"><span data-stu-id="d5490-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5490-223">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d5490-223">Additional resources</span></span>

* [<span data-ttu-id="d5490-224">Dokumentacja interfejsów API języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5490-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="d5490-225">Samouczek JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5490-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d5490-226">Samouczek WebPack i język TypeScript</span><span class="sxs-lookup"><span data-stu-id="d5490-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="d5490-227">Centra</span><span class="sxs-lookup"><span data-stu-id="d5490-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d5490-228">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="d5490-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d5490-229">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="d5490-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="d5490-230">Żądania między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="d5490-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="d5490-231">[SignalRDokumentacja bezserwerowa usługi platformy Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="d5490-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
