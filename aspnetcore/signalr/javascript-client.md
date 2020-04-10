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
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="f260e-103">ASP.NET podstawowy SignalR klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="f260e-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="f260e-104">Przez [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="f260e-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="f260e-105">Biblioteka klienta ASP.NET SignalR Core JavaScript umożliwia deweloperom wywoływanie kodu koncentratora po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="f260e-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="f260e-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f260e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="f260e-107">Instalowanie SignalR pakietu klienta</span><span class="sxs-lookup"><span data-stu-id="f260e-107">Install the SignalR client package</span></span>

<span data-ttu-id="f260e-108">Biblioteka SignalR klienta JavaScript jest dostarczana jako pakiet [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="f260e-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="f260e-109">W poniższych sekcjach opisano różne sposoby instalowania biblioteki klienta.</span><span class="sxs-lookup"><span data-stu-id="f260e-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="f260e-110">Zainstaluj z npm</span><span class="sxs-lookup"><span data-stu-id="f260e-110">Install with npm</span></span>

<span data-ttu-id="f260e-111">Jeśli używasz programu Visual Studio, uruchom następujące polecenia z **konsoli Menedżera pakietów** w folderze głównym.</span><span class="sxs-lookup"><span data-stu-id="f260e-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="f260e-112">W przypadku programu Visual Studio Code uruchom następujące polecenia z **terminala zintegrowanego**.</span><span class="sxs-lookup"><span data-stu-id="f260e-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="f260e-113">npm instaluje zawartość pakietu w folderze \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="f260e-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="f260e-114">Utwórz nowy folder o nazwie *signalr* pod folderem *\\wwwroot lib.*</span><span class="sxs-lookup"><span data-stu-id="f260e-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="f260e-115">Skopiuj plik *signalr.js* do folderu *wwwroot\lib\signalr.*</span><span class="sxs-lookup"><span data-stu-id="f260e-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="f260e-116">npm instaluje zawartość pakietu w folderze \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="f260e-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="f260e-117">Utwórz nowy folder o nazwie *signalr* pod folderem *\\wwwroot lib.*</span><span class="sxs-lookup"><span data-stu-id="f260e-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="f260e-118">Skopiuj plik *signalr.js* do folderu *wwwroot\lib\signalr.*</span><span class="sxs-lookup"><span data-stu-id="f260e-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="f260e-119">Odwołanie SignalR się do klienta `<script>` JavaScript w elemencie.</span><span class="sxs-lookup"><span data-stu-id="f260e-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="f260e-120">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f260e-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="f260e-121">Korzystanie z sieci dostarczania zawartości (CDN)</span><span class="sxs-lookup"><span data-stu-id="f260e-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="f260e-122">Aby użyć biblioteki klienta bez wymagania wstępnego npm, odwołaj się do hostowanego przez sieć CDN kopii biblioteki klienta.</span><span class="sxs-lookup"><span data-stu-id="f260e-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="f260e-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f260e-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="f260e-124">Biblioteka klienta jest dostępna na następujących sieciach CDN:</span><span class="sxs-lookup"><span data-stu-id="f260e-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="f260e-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="f260e-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="f260e-126">jsDelivr ( jsDelivr )</span><span class="sxs-lookup"><span data-stu-id="f260e-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="f260e-127">unpkg (odp.</span><span class="sxs-lookup"><span data-stu-id="f260e-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="f260e-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="f260e-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="f260e-129">jsDelivr ( jsDelivr )</span><span class="sxs-lookup"><span data-stu-id="f260e-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="f260e-130">unpkg (odp.</span><span class="sxs-lookup"><span data-stu-id="f260e-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="f260e-131">Zainstaluj z LibMan</span><span class="sxs-lookup"><span data-stu-id="f260e-131">Install with LibMan</span></span>

<span data-ttu-id="f260e-132">[LibMan](xref:client-side/libman/index) może służyć do instalowania określonych plików biblioteki klienta z biblioteki klienta hostowanego przez usługę CDN.</span><span class="sxs-lookup"><span data-stu-id="f260e-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="f260e-133">Na przykład tylko dodać minified plik JavaScript do projektu.</span><span class="sxs-lookup"><span data-stu-id="f260e-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="f260e-134">Aby uzyskać szczegółowe informacje na temat tego podejścia, zobacz [Dodawanie biblioteki SignalR klienta](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="f260e-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="f260e-135">Łączenie się z koncentratorem</span><span class="sxs-lookup"><span data-stu-id="f260e-135">Connect to a hub</span></span>

<span data-ttu-id="f260e-136">Poniższy kod tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="f260e-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="f260e-137">Nazwa koncentratora jest niewrażliwa na litery.</span><span class="sxs-lookup"><span data-stu-id="f260e-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="f260e-138">Połączenia międzyzierzowe</span><span class="sxs-lookup"><span data-stu-id="f260e-138">Cross-origin connections</span></span>

<span data-ttu-id="f260e-139">Zazwyczaj przeglądarki ładują połączenia z tej samej domeny co żądana strona.</span><span class="sxs-lookup"><span data-stu-id="f260e-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="f260e-140">Istnieją jednak sytuacje, gdy wymagane jest połączenie z inną domeną.</span><span class="sxs-lookup"><span data-stu-id="f260e-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="f260e-141">Aby zapobiec odczytywanie poufnych danych przez złośliwą witrynę z innej [lokacji, połączenia międzybiegomowe](xref:security/cors) są domyślnie wyłączone.</span><span class="sxs-lookup"><span data-stu-id="f260e-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="f260e-142">Aby zezwolić na żądanie cross-origin, należy włączyć go w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="f260e-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="f260e-143">Wywoływanie metod koncentratora z klienta</span><span class="sxs-lookup"><span data-stu-id="f260e-143">Call hub methods from client</span></span>

<span data-ttu-id="f260e-144">Klienci JavaScript dzwonią do metod publicznych w centrach za pomocą metody [wywoływania](/javascript/api/%40aspnet/signalr/hubconnection#invoke) [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="f260e-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="f260e-145">Metoda `invoke` akceptuje dwa argumenty:</span><span class="sxs-lookup"><span data-stu-id="f260e-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="f260e-146">Nazwa metody koncentratora.</span><span class="sxs-lookup"><span data-stu-id="f260e-146">The name of the hub method.</span></span> <span data-ttu-id="f260e-147">W poniższym przykładzie nazwa metody `SendMessage`w centrum jest .</span><span class="sxs-lookup"><span data-stu-id="f260e-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="f260e-148">Wszelkie argumenty zdefiniowane w metodzie koncentratora.</span><span class="sxs-lookup"><span data-stu-id="f260e-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="f260e-149">W poniższym przykładzie nazwa `message`argumentu to .</span><span class="sxs-lookup"><span data-stu-id="f260e-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="f260e-150">Przykładowy kod używa składni funkcji strzałki, która jest obsługiwana w bieżących wersjach wszystkich głównych przeglądarek z wyjątkiem programu Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="f260e-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="f260e-151">Jeśli używasz usługi SignalR Azure w *trybie bezserwerowym,* nie można wywołać metody koncentratora z klienta.</span><span class="sxs-lookup"><span data-stu-id="f260e-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="f260e-152">Aby uzyskać więcej informacji, zobacz [ SignalR dokumentację usługi](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="f260e-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="f260e-153">Metoda `invoke` zwraca JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="f260e-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="f260e-154">Jest `Promise` rozpoznawany z wartością zwracaną (jeśli istnieje) po powrocie metody na serwerze.</span><span class="sxs-lookup"><span data-stu-id="f260e-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="f260e-155">Jeśli metoda na serwerze zgłasza błąd, `Promise` jest odrzucany z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="f260e-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="f260e-156">Użyj `then` i `catch` metody `Promise` na siebie do obsługi `await` tych przypadków (lub składni).</span><span class="sxs-lookup"><span data-stu-id="f260e-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="f260e-157">Metoda `send` zwraca JavaScript `Promise`.</span><span class="sxs-lookup"><span data-stu-id="f260e-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="f260e-158">Jest `Promise` rozpoznawany po wysłaniu wiadomości do serwera.</span><span class="sxs-lookup"><span data-stu-id="f260e-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="f260e-159">Jeśli występuje błąd podczas wysyłania `Promise` wiadomości, jest odrzucany z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="f260e-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="f260e-160">Użyj `then` i `catch` metody `Promise` na siebie do obsługi `await` tych przypadków (lub składni).</span><span class="sxs-lookup"><span data-stu-id="f260e-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="f260e-161">Using `send` nie czeka, aż serwer otrzyma wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f260e-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="f260e-162">W związku z tym nie jest możliwe zwrócenie danych lub błędów z serwera.</span><span class="sxs-lookup"><span data-stu-id="f260e-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="f260e-163">Wywoływanie metod klienta z centrum</span><span class="sxs-lookup"><span data-stu-id="f260e-163">Call client methods from hub</span></span>

<span data-ttu-id="f260e-164">Aby odbierać wiadomości z koncentratora, [on](/javascript/api/%40aspnet/signalr/hubconnection#on) należy zdefiniować metodę przy użyciu metody `HubConnection`on .</span><span class="sxs-lookup"><span data-stu-id="f260e-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="f260e-165">Nazwa metody klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f260e-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="f260e-166">W poniższym przykładzie nazwa `ReceiveMessage`metody to .</span><span class="sxs-lookup"><span data-stu-id="f260e-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="f260e-167">Argumenty koncentratora przechodzi do metody.</span><span class="sxs-lookup"><span data-stu-id="f260e-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="f260e-168">W poniższym przykładzie wartość `message`argumentu jest .</span><span class="sxs-lookup"><span data-stu-id="f260e-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="f260e-169">Poprzedni kod w `connection.on` uruchamia, gdy kod po stronie serwera wywołuje go przy użyciu [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metody.</span><span class="sxs-lookup"><span data-stu-id="f260e-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="f260e-170">określa, którą metodę klienta wywołać, dopasowując nazwę metody i argumenty zdefiniowane w `SendAsync` i `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="f260e-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="f260e-171">Najlepszym rozwiązaniem jest wywołanie metody `HubConnection` start `on`na after . [start](/javascript/api/%40aspnet/signalr/hubconnection#start)</span><span class="sxs-lookup"><span data-stu-id="f260e-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="f260e-172">Dzięki temu programy obsługi są rejestrowane przed odebraniem jakichkolwiek wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f260e-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="f260e-173">Obsługa błędów i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="f260e-173">Error handling and logging</span></span>

<span data-ttu-id="f260e-174">Łańcuch `catch` metody do końca `start` metody do obsługi błędów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="f260e-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="f260e-175">Służy `console.error` do wyprowadzania błędów do konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f260e-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="f260e-176">Konfigurowanie śledzenia dziennika po stronie klienta przez przekazanie rejestratora i typu zdarzenia do rejestrowania po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-176">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="f260e-177">Komunikaty są rejestrowane z określonym poziomem dziennika i wyższym.</span><span class="sxs-lookup"><span data-stu-id="f260e-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="f260e-178">Dostępne poziomy dziennika są następujące:</span><span class="sxs-lookup"><span data-stu-id="f260e-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="f260e-179">`signalR.LogLevel.Error`&ndash; Komunikaty o błędach.</span><span class="sxs-lookup"><span data-stu-id="f260e-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="f260e-180">Rejestruje `Error` tylko komunikaty.</span><span class="sxs-lookup"><span data-stu-id="f260e-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="f260e-181">`signalR.LogLevel.Warning`&ndash; Komunikaty ostrzegawcze o potencjalnych błędach.</span><span class="sxs-lookup"><span data-stu-id="f260e-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="f260e-182">Dzienniki `Warning`i `Error` wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f260e-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="f260e-183">`signalR.LogLevel.Information`&ndash; Komunikaty o stanie bez błędów.</span><span class="sxs-lookup"><span data-stu-id="f260e-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="f260e-184">Dzienniki `Information` `Warning`, `Error` i wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f260e-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="f260e-185">`signalR.LogLevel.Trace`&ndash; Śledzenie komunikatów.</span><span class="sxs-lookup"><span data-stu-id="f260e-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="f260e-186">Rejestruje wszystko, w tym dane przesyłane między koncentratorem a klientem.</span><span class="sxs-lookup"><span data-stu-id="f260e-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="f260e-187">Użyj metody [konfigurowania rejestrowania](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) w [programie HubConnectionBuilder,](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) aby skonfigurować poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="f260e-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="f260e-188">Wiadomości są rejestrowane w konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f260e-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="f260e-189">Łączenie klientów</span><span class="sxs-lookup"><span data-stu-id="f260e-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="f260e-190">Automatyczne ponowne połączenie</span><span class="sxs-lookup"><span data-stu-id="f260e-190">Automatically reconnect</span></span>

<span data-ttu-id="f260e-191">Klient JavaScript SignalR można skonfigurować do automatycznego ponownego `withAutomaticReconnect` łączenia przy użyciu metody na [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="f260e-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="f260e-192">Domyślnie nie zostanie automatycznie ponownie nawiązany.</span><span class="sxs-lookup"><span data-stu-id="f260e-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="f260e-193">Bez żadnych `withAutomaticReconnect()` parametrów konfiguruje klienta czekać 0, 2, 10 i 30 sekund odpowiednio przed wypróbowaniem każdej próby ponownego połączenia, zatrzymując po czterech nieudanych próbach.</span><span class="sxs-lookup"><span data-stu-id="f260e-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="f260e-194">Przed rozpoczęciem wszelkich prób `HubConnection` ponownego połączenia, `HubConnectionState.Reconnecting` będzie przejście `onreconnecting` do stanu i wywołania `Disconnected` zwrotnego zamiast `onclose` przejścia do stanu `HubConnection` i wyzwalania jego wywołania zwrotne jak bez automatycznego ponownego połączenia skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="f260e-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="f260e-195">Umożliwia to ostrzeżenie użytkowników, że połączenie zostało utracone i wyłączenie elementów interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f260e-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="f260e-196">Jeśli klient pomyślnie łączy się ponownie w `HubConnection` ciągu pierwszych czterech `Connected` prób, `onreconnected` będzie przejście z powrotem do stanu i wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="f260e-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="f260e-197">Umożliwia to poinformowanie użytkowników o ponownym nawiązanym połączeniu.</span><span class="sxs-lookup"><span data-stu-id="f260e-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="f260e-198">Ponieważ połączenie wygląda zupełnie nowy na serwerze, nowy `connectionId` `onreconnected` zostanie dostarczony do wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="f260e-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="f260e-199">Parametr `onreconnected` wywołania `connectionId` zwrotnego nie zostanie zdefiniowany, `HubConnection` jeśli został skonfigurowany do [pomijania negocjacji](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="f260e-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="f260e-200">`withAutomaticReconnect()`nie skonfiguruje, `HubConnection` aby ponowić próbę początkowego uruchomienia błędów, więc błędy uruchamiania muszą być obsługiwane ręcznie:</span><span class="sxs-lookup"><span data-stu-id="f260e-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="f260e-201">Jeśli klient nie pomyślnie połączyć się ponownie w `HubConnection` ciągu pierwszych `Disconnected` czterech prób, będzie przejście do stanu i zwolnić [jego](/javascript/api/%40aspnet/signalr/hubconnection#onclose) zamknięcia wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="f260e-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="f260e-202">Umożliwia to poinformowanie użytkowników o trwałej utracie połączenia i zalecenie odświeżenia strony:</span><span class="sxs-lookup"><span data-stu-id="f260e-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="f260e-203">Aby skonfigurować niestandardową liczbę prób ponownego nawiązania połączenia przed `withAutomaticReconnect` odłączeniem lub zmianą czasu ponownego połączenia, akceptuje tablicę liczb przedstawiających opóźnienie w milisekundach, aby poczekać przed rozpoczęciem każdej próby ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="f260e-204">W poprzednim przykładzie `HubConnection` konfiguruje, aby rozpocząć próby ponownego połączenia natychmiast po utracie połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="f260e-205">Dotyczy to również domyślnej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f260e-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="f260e-206">Jeśli pierwsza próba ponownego połączenia nie powiedzie się, druga próba ponownego połączenia również rozpocznie się natychmiast zamiast czekać 2 sekundy, tak jak w konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="f260e-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="f260e-207">Jeśli druga próba ponownego połączenia nie powiedzie się, trzecia próba ponownego połączenia rozpocznie się w ciągu 10 sekund, co jest ponownie podobne do domyślnej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f260e-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="f260e-208">Zachowanie niestandardowe następnie odbiega ponownie od domyślnego zachowania, zatrzymując się po trzecim niepowodzeniu próby ponownego połączenia zamiast próbować jeszcze jednej próby ponownego połączenia w ciągu kolejnych 30 sekund, tak jak w konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="f260e-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="f260e-209">Jeśli chcesz jeszcze większą kontrolę nad czasem i liczbą prób automatycznego ponownego połączenia, `withAutomaticReconnect` akceptuje obiekt implementujący `IRetryPolicy` interfejs, który ma jedną metodę o nazwie `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="f260e-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="f260e-210">`nextRetryDelayInMilliseconds`przyjmuje pojedynczy argument z `RetryContext`typem .</span><span class="sxs-lookup"><span data-stu-id="f260e-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="f260e-211">Ma `RetryContext` trzy właściwości: `previousRetryCount` `elapsedMilliseconds` , `retryReason` i `number`które `number` są `Error` , a i odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="f260e-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="f260e-212">Przed pierwszą próbą `previousRetryCount` ponownego `elapsedMilliseconds` połączenia, zarówno `retryReason` i będzie zero, a będzie błąd, który spowodował utratę połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="f260e-213">Po każdej nieudanej próbie ponowienia zostanie `previousRetryCount` `elapsedMilliseconds` ona zwiększona o jeden, zostanie zaktualizowana w celu odzwierciedlenia ilości `retryReason` czasu spędzonego na ponownym połączeniu w milisekundach, a będzie to błąd, który spowodował niepowodzenie ostatniej próby ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="f260e-214">`nextRetryDelayInMilliseconds`musi zwrócić liczbę reprezentującą liczbę milisekund, aby poczekać przed następną próbą ponownego połączenia lub `null` jeśli `HubConnection` należy zatrzymać ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="f260e-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="f260e-215">Alternatywnie można napisać kod, który spowoduje ponowne połączenie klienta ręcznie, jak pokazano w [Ręcznie ponownie połączyć](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="f260e-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="f260e-216">Ręczne ponowne łączenie</span><span class="sxs-lookup"><span data-stu-id="f260e-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="f260e-217">Przed 3.0, klient SignalR JavaScript dla nie automatycznie ponownie.</span><span class="sxs-lookup"><span data-stu-id="f260e-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="f260e-218">Należy napisać kod, który spowoduje ponowne połączenie klienta ręcznie.</span><span class="sxs-lookup"><span data-stu-id="f260e-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="f260e-219">Poniższy kod pokazuje typowe podejście ręcznego ponownego łączenia:</span><span class="sxs-lookup"><span data-stu-id="f260e-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="f260e-220">Funkcja (w tym przypadku `start` funkcja) jest tworzony do uruchomienia połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="f260e-221">Wywołanie `start` funkcji w programie `onclose` obsługi zdarzeń połączenia.</span><span class="sxs-lookup"><span data-stu-id="f260e-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="f260e-222">Rzeczywistych implementacji będzie używać wykładniczego wycofywania lub ponowić próbę określoną liczbę razy przed rezygnacją.</span><span class="sxs-lookup"><span data-stu-id="f260e-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f260e-223">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f260e-223">Additional resources</span></span>

* [<span data-ttu-id="f260e-224">Dokumentacja interfejsów API języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f260e-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="f260e-225">Poradnik języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="f260e-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f260e-226">WebPack i TypeScript samouczek</span><span class="sxs-lookup"><span data-stu-id="f260e-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="f260e-227">Centra</span><span class="sxs-lookup"><span data-stu-id="f260e-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f260e-228">Klient platformy .NET</span><span class="sxs-lookup"><span data-stu-id="f260e-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f260e-229">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="f260e-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="f260e-230">Żądania między źródłami (CORS)</span><span class="sxs-lookup"><span data-stu-id="f260e-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="f260e-231">[Dokumentacja usługi Azure SignalR Service bezserwerowa](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="f260e-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
