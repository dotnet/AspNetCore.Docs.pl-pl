---
title: Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core
author: bradygaster
description: Dodaj protokół MessagePack Hub do ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024694"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="3de2a-103">Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3de2a-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3de2a-104">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="3de2a-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="3de2a-105">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="3de2a-105">What is MessagePack?</span></span>

<span data-ttu-id="3de2a-106">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="3de2a-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="3de2a-107">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="3de2a-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="3de2a-108">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="3de2a-109">SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="3de2a-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="3de2a-110">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="3de2a-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="3de2a-111">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="3de2a-112">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3de2a-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-113">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="3de2a-113">JSON is enabled by default.</span></span> <span data-ttu-id="3de2a-114">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="3de2a-115">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="3de2a-116">W tym delegatze `SerializerOptions` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="3de2a-117">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="3de2a-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="3de2a-118">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="3de2a-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="3de2a-119">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="3de2a-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="3de2a-120">Na przykład wywoływanie `.WithSecurity(MessagePackSecurity.UntrustedData)` podczas zamiany `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="3de2a-121">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="3de2a-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-122">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="3de2a-123">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="3de2a-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="3de2a-124">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="3de2a-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3de2a-125">.NET client</span></span>

<span data-ttu-id="3de2a-126">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="3de2a-127">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3de2a-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="3de2a-128">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-128">JavaScript client</span></span>

<span data-ttu-id="3de2a-129">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="3de2a-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="3de2a-130">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3de2a-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="3de2a-131">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="3de2a-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="3de2a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="3de2a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="3de2a-133">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3de2a-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="3de2a-134">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="3de2a-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="3de2a-135">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="3de2a-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-136">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3de2a-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="3de2a-137">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="3de2a-138">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="3de2a-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="3de2a-139">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="3de2a-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="3de2a-140">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3de2a-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="3de2a-141">Klient Java</span><span class="sxs-lookup"><span data-stu-id="3de2a-141">Java client</span></span>

<span data-ttu-id="3de2a-142">Aby włączyć MessagePack za pomocą języka Java, zainstaluj `com.microsoft.signalr.messagepack` pakiet.</span><span class="sxs-lookup"><span data-stu-id="3de2a-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="3de2a-143">W przypadku korzystania z Gradle Dodaj następujący wiersz do `dependencies` sekcji pliku *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="3de2a-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="3de2a-144">Korzystając z Maven, Dodaj następujące wiersze wewnątrz `<dependencies>` elementu *pom.xml* pliku:</span><span class="sxs-lookup"><span data-stu-id="3de2a-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="3de2a-145">Wywołanie `withHubProtocol(new MessagePackHubProtocol())` metody `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="3de2a-146">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="3de2a-146">MessagePack quirks</span></span>

<span data-ttu-id="3de2a-147">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="3de2a-148">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="3de2a-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="3de2a-149">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3de2a-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="3de2a-150">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="3de2a-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="3de2a-151">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3de2a-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="3de2a-152">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3de2a-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="3de2a-153">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3de2a-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="3de2a-154">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="3de2a-155">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="3de2a-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="3de2a-156">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="3de2a-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="3de2a-157">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="3de2a-158">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zostanie przekonwertowany na format UTC, jeśli `DateTime.Kind` jest w `DateTimeKind.Local` przeciwnym razie, nie będzie on dotykał czasu i zostanie przekazany w postaci, w jakiej się znajduje.</span><span class="sxs-lookup"><span data-stu-id="3de2a-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="3de2a-159">Jeśli pracujesz z `DateTime` wartościami, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="3de2a-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="3de2a-160">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="3de2a-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="3de2a-161">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="3de2a-162">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="3de2a-163">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="3de2a-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="3de2a-164">Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="3de2a-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="3de2a-165">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3de2a-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="3de2a-166">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3de2a-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="3de2a-167">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="3de2a-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="3de2a-168">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="3de2a-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="3de2a-169">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="3de2a-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="3de2a-170">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="3de2a-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="3de2a-171">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="3de2a-172">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="3de2a-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="3de2a-173">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="3de2a-174">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="3de2a-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="3de2a-175">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="3de2a-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="3de2a-176">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="3de2a-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="3de2a-177">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="3de2a-178">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="3de2a-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="3de2a-179">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="3de2a-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="3de2a-180">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="3de2a-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="3de2a-181">Znaki i ciągi w języku Java</span><span class="sxs-lookup"><span data-stu-id="3de2a-181">Chars and Strings in Java</span></span>

<span data-ttu-id="3de2a-182">W kliencie Java `char` obiekty są serializowane jako obiekty jednoznakowe `String` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="3de2a-183">Jest to w przeciwieństwie do klienta języka C# i języka JavaScript, który serializować je jako `short` obiekty.</span><span class="sxs-lookup"><span data-stu-id="3de2a-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="3de2a-184">Sama Specyfikacja MessagePack nie definiuje zachowania dla `char` obiektów, dlatego należy do autora biblioteki, aby określić sposób serializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="3de2a-185">Różnica w zachowaniu między naszymi klientami polega na bibliotekach użytych dla naszych implementacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3de2a-186">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="3de2a-186">Related resources</span></span>

* [<span data-ttu-id="3de2a-187">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3de2a-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3de2a-188">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3de2a-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3de2a-189">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3de2a-190">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="3de2a-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="3de2a-191">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="3de2a-191">What is MessagePack?</span></span>

<span data-ttu-id="3de2a-192">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="3de2a-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="3de2a-193">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="3de2a-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="3de2a-194">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="3de2a-195">SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="3de2a-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="3de2a-196">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="3de2a-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="3de2a-197">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="3de2a-198">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3de2a-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-199">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="3de2a-199">JSON is enabled by default.</span></span> <span data-ttu-id="3de2a-200">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="3de2a-201">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="3de2a-202">W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="3de2a-203">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="3de2a-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="3de2a-204">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="3de2a-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="3de2a-205">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="3de2a-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="3de2a-206">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="3de2a-207">Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="3de2a-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="3de2a-208">Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie.</span><span class="sxs-lookup"><span data-stu-id="3de2a-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="3de2a-209">`MessagePack` w wersji 2. x wprowadzono istotne zmiany i są one niezgodne z SignalR wersjami 3,1 i wcześniejszymi.</span><span class="sxs-lookup"><span data-stu-id="3de2a-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="3de2a-210">Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="3de2a-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="3de2a-211">Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3de2a-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="3de2a-212">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="3de2a-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-213">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="3de2a-214">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="3de2a-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="3de2a-215">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="3de2a-216">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3de2a-216">.NET client</span></span>

<span data-ttu-id="3de2a-217">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="3de2a-218">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3de2a-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="3de2a-219">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-219">JavaScript client</span></span>

<span data-ttu-id="3de2a-220">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="3de2a-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="3de2a-221">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3de2a-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="3de2a-222">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="3de2a-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="3de2a-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="3de2a-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="3de2a-224">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3de2a-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="3de2a-225">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="3de2a-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="3de2a-226">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="3de2a-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-227">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3de2a-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="3de2a-228">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="3de2a-229">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="3de2a-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="3de2a-230">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="3de2a-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="3de2a-231">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3de2a-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="3de2a-232">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="3de2a-232">MessagePack quirks</span></span>

<span data-ttu-id="3de2a-233">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="3de2a-234">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="3de2a-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="3de2a-235">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3de2a-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="3de2a-236">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="3de2a-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="3de2a-237">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3de2a-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="3de2a-238">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3de2a-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="3de2a-239">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3de2a-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="3de2a-240">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="3de2a-241">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="3de2a-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="3de2a-242">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="3de2a-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="3de2a-243">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="3de2a-244">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="3de2a-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="3de2a-245">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="3de2a-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="3de2a-246">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="3de2a-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="3de2a-247">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="3de2a-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="3de2a-248">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="3de2a-249">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="3de2a-250">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="3de2a-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="3de2a-251">Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="3de2a-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="3de2a-252">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3de2a-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="3de2a-253">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3de2a-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="3de2a-254">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="3de2a-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="3de2a-255">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="3de2a-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="3de2a-256">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="3de2a-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="3de2a-257">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="3de2a-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="3de2a-258">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="3de2a-259">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="3de2a-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="3de2a-260">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="3de2a-261">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="3de2a-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="3de2a-262">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="3de2a-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="3de2a-263">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="3de2a-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="3de2a-264">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="3de2a-265">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="3de2a-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="3de2a-266">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="3de2a-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="3de2a-267">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="3de2a-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="3de2a-268">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="3de2a-268">Related resources</span></span>

* [<span data-ttu-id="3de2a-269">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3de2a-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3de2a-270">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3de2a-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3de2a-271">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3de2a-272">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="3de2a-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="3de2a-273">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="3de2a-273">What is MessagePack?</span></span>

<span data-ttu-id="3de2a-274">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="3de2a-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="3de2a-275">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="3de2a-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="3de2a-276">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="3de2a-277">SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="3de2a-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="3de2a-278">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="3de2a-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="3de2a-279">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="3de2a-280">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3de2a-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-281">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="3de2a-281">JSON is enabled by default.</span></span> <span data-ttu-id="3de2a-282">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="3de2a-283">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="3de2a-284">W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="3de2a-285">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="3de2a-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="3de2a-286">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="3de2a-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="3de2a-287">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="3de2a-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="3de2a-288">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="3de2a-289">Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="3de2a-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="3de2a-290">Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie.</span><span class="sxs-lookup"><span data-stu-id="3de2a-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="3de2a-291">Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="3de2a-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="3de2a-292">Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3de2a-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="3de2a-293">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="3de2a-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-294">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="3de2a-295">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="3de2a-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="3de2a-296">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="3de2a-297">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3de2a-297">.NET client</span></span>

<span data-ttu-id="3de2a-298">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="3de2a-299">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3de2a-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="3de2a-300">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-300">JavaScript client</span></span>

<span data-ttu-id="3de2a-301">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="3de2a-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="3de2a-302">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3de2a-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="3de2a-303">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="3de2a-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="3de2a-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="3de2a-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="3de2a-305">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3de2a-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="3de2a-306">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="3de2a-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="3de2a-307">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="3de2a-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="3de2a-308">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3de2a-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="3de2a-309">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="3de2a-310">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="3de2a-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="3de2a-311">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="3de2a-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="3de2a-312">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3de2a-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="3de2a-313">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="3de2a-313">MessagePack quirks</span></span>

<span data-ttu-id="3de2a-314">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="3de2a-315">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="3de2a-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="3de2a-316">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3de2a-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="3de2a-317">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="3de2a-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="3de2a-318">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3de2a-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="3de2a-319">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3de2a-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="3de2a-320">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3de2a-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="3de2a-321">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="3de2a-322">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="3de2a-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="3de2a-323">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="3de2a-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="3de2a-324">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="3de2a-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="3de2a-325">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="3de2a-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="3de2a-326">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="3de2a-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="3de2a-327">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="3de2a-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="3de2a-328">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="3de2a-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="3de2a-329">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="3de2a-330">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3de2a-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="3de2a-331">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="3de2a-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="3de2a-332">Wartość jest, `DateTime.MinValue` `January 1, 0001` która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="3de2a-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="3de2a-333">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3de2a-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="3de2a-334">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3de2a-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="3de2a-335">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="3de2a-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="3de2a-336">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="3de2a-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="3de2a-337">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="3de2a-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="3de2a-338">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="3de2a-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="3de2a-339">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="3de2a-340">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="3de2a-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="3de2a-341">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="3de2a-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="3de2a-342">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="3de2a-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="3de2a-343">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="3de2a-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="3de2a-344">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="3de2a-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="3de2a-345">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="3de2a-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="3de2a-346">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="3de2a-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="3de2a-347">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="3de2a-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="3de2a-348">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="3de2a-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="3de2a-349">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="3de2a-349">Related resources</span></span>

* [<span data-ttu-id="3de2a-350">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3de2a-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3de2a-351">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3de2a-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3de2a-352">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3de2a-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
