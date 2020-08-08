---
title: Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core
author: bradygaster
description: Dodaj protokół MessagePack Hub do ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8e590c87f75d35cbafde1adbc87dea9c45eac92d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022553"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="b5a9c-103">Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5a9c-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b5a9c-104">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="b5a9c-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b5a9c-105">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b5a9c-105">What is MessagePack?</span></span>

<span data-ttu-id="b5a9c-106">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="b5a9c-107">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b5a9c-108">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b5a9c-109">SignalRProgram ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b5a9c-110">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="b5a9c-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="b5a9c-111">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b5a9c-112">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-113">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-113">JSON is enabled by default.</span></span> <span data-ttu-id="b5a9c-114">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b5a9c-115">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b5a9c-116">W tym delegatze `SerializerOptions` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b5a9c-117">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b5a9c-118">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="b5a9c-119">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="b5a9c-120">Na przykład wywoływanie `.WithSecurity(MessagePackSecurity.UntrustedData)` podczas zamiany `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b5a9c-121">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="b5a9c-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-122">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b5a9c-123">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="b5a9c-124">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b5a9c-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b5a9c-125">.NET client</span></span>

<span data-ttu-id="b5a9c-126">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b5a9c-127">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b5a9c-128">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-128">JavaScript client</span></span>

<span data-ttu-id="b5a9c-129">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="b5a9c-130">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="b5a9c-131">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="b5a9c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="b5a9c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="b5a9c-133">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b5a9c-134">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b5a9c-135">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-136">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b5a9c-137">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b5a9c-138">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b5a9c-139">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b5a9c-140">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b5a9c-141">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="b5a9c-141">MessagePack quirks</span></span>

<span data-ttu-id="b5a9c-142">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b5a9c-143">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="b5a9c-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b5a9c-144">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b5a9c-145">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b5a9c-146">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b5a9c-147">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b5a9c-148">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b5a9c-149">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b5a9c-150">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b5a9c-151">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="b5a9c-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b5a9c-152">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b5a9c-153">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zostanie przekonwertowany na format UTC, jeśli `DateTime.Kind` jest w `DateTimeKind.Local` przeciwnym razie, nie będzie on dotykał czasu i zostanie przekazany w postaci, w jakiej się znajduje.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="b5a9c-154">Jeśli pracujesz z `DateTime` wartościami, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b5a9c-155">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b5a9c-156">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b5a9c-157">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b5a9c-158">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b5a9c-159">Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b5a9c-160">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b5a9c-161">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b5a9c-162">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b5a9c-163">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b5a9c-164">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b5a9c-165">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="b5a9c-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b5a9c-166">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b5a9c-167">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b5a9c-168">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b5a9c-169">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="b5a9c-170">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="b5a9c-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b5a9c-171">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="b5a9c-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b5a9c-172">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b5a9c-173">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b5a9c-174">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="b5a9c-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b5a9c-175">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b5a9c-176">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="b5a9c-176">Related resources</span></span>

* [<span data-ttu-id="b5a9c-177">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="b5a9c-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b5a9c-178">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b5a9c-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b5a9c-179">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b5a9c-180">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="b5a9c-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b5a9c-181">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b5a9c-181">What is MessagePack?</span></span>

<span data-ttu-id="b5a9c-182">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="b5a9c-183">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b5a9c-184">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b5a9c-185">SignalRProgram ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b5a9c-186">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="b5a9c-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="b5a9c-187">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b5a9c-188">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-189">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-189">JSON is enabled by default.</span></span> <span data-ttu-id="b5a9c-190">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b5a9c-191">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b5a9c-192">W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b5a9c-193">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b5a9c-194">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="b5a9c-195">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="b5a9c-196">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="b5a9c-197">Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="b5a9c-198">Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="b5a9c-199">Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="b5a9c-200">Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b5a9c-201">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="b5a9c-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-202">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b5a9c-203">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="b5a9c-204">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b5a9c-205">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b5a9c-205">.NET client</span></span>

<span data-ttu-id="b5a9c-206">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b5a9c-207">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b5a9c-208">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-208">JavaScript client</span></span>

<span data-ttu-id="b5a9c-209">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="b5a9c-210">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="b5a9c-211">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="b5a9c-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="b5a9c-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="b5a9c-213">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b5a9c-214">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b5a9c-215">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-216">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b5a9c-217">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b5a9c-218">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b5a9c-219">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b5a9c-220">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b5a9c-221">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="b5a9c-221">MessagePack quirks</span></span>

<span data-ttu-id="b5a9c-222">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b5a9c-223">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="b5a9c-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b5a9c-224">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b5a9c-225">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b5a9c-226">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b5a9c-227">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b5a9c-228">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b5a9c-229">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b5a9c-230">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b5a9c-231">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="b5a9c-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b5a9c-232">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b5a9c-233">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="b5a9c-234">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b5a9c-235">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="b5a9c-236">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b5a9c-237">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b5a9c-238">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b5a9c-239">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b5a9c-240">Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b5a9c-241">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b5a9c-242">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b5a9c-243">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b5a9c-244">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b5a9c-245">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b5a9c-246">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="b5a9c-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b5a9c-247">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b5a9c-248">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b5a9c-249">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b5a9c-250">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="b5a9c-251">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="b5a9c-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b5a9c-252">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="b5a9c-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b5a9c-253">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b5a9c-254">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b5a9c-255">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="b5a9c-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b5a9c-256">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b5a9c-257">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="b5a9c-257">Related resources</span></span>

* [<span data-ttu-id="b5a9c-258">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="b5a9c-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b5a9c-259">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b5a9c-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b5a9c-260">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5a9c-261">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="b5a9c-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b5a9c-262">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b5a9c-262">What is MessagePack?</span></span>

<span data-ttu-id="b5a9c-263">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="b5a9c-264">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b5a9c-265">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b5a9c-266">SignalRProgram ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b5a9c-267">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="b5a9c-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="b5a9c-268">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b5a9c-269">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-270">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-270">JSON is enabled by default.</span></span> <span data-ttu-id="b5a9c-271">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b5a9c-272">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b5a9c-273">W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b5a9c-274">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b5a9c-275">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="b5a9c-276">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="b5a9c-277">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="b5a9c-278">Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="b5a9c-279">Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="b5a9c-280">Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="b5a9c-281">Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b5a9c-282">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="b5a9c-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-283">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b5a9c-284">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="b5a9c-285">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b5a9c-286">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b5a9c-286">.NET client</span></span>

<span data-ttu-id="b5a9c-287">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b5a9c-288">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b5a9c-289">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-289">JavaScript client</span></span>

<span data-ttu-id="b5a9c-290">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="b5a9c-291">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="b5a9c-292">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="b5a9c-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="b5a9c-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="b5a9c-294">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b5a9c-295">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b5a9c-296">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a9c-297">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b5a9c-298">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b5a9c-299">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b5a9c-300">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b5a9c-301">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b5a9c-302">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="b5a9c-302">MessagePack quirks</span></span>

<span data-ttu-id="b5a9c-303">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b5a9c-304">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="b5a9c-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b5a9c-305">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b5a9c-306">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b5a9c-307">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b5a9c-308">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b5a9c-309">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b5a9c-310">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b5a9c-311">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b5a9c-312">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="b5a9c-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b5a9c-313">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="b5a9c-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b5a9c-314">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="b5a9c-315">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b5a9c-316">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="b5a9c-317">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b5a9c-318">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b5a9c-319">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b5a9c-320">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b5a9c-321">Wartość jest, `DateTime.MinValue` `January 1, 0001` która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b5a9c-322">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b5a9c-323">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="b5a9c-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b5a9c-324">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b5a9c-325">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b5a9c-326">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b5a9c-327">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="b5a9c-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b5a9c-328">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b5a9c-329">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b5a9c-330">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b5a9c-331">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="b5a9c-332">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="b5a9c-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b5a9c-333">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="b5a9c-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b5a9c-334">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b5a9c-335">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="b5a9c-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b5a9c-336">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="b5a9c-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b5a9c-337">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b5a9c-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b5a9c-338">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="b5a9c-338">Related resources</span></span>

* [<span data-ttu-id="b5a9c-339">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="b5a9c-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b5a9c-340">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b5a9c-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b5a9c-341">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="b5a9c-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
