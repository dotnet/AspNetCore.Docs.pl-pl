---
title: Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core
author: bradygaster
description: Dodaj protokół MessagePack Hub do ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
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
ms.openlocfilehash: 15ae8911e2ffba43c7bb885efd153d8b6803bc2a
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393681"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="3572e-103">Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3572e-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3572e-104">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="3572e-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="3572e-105">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="3572e-105">What is MessagePack?</span></span>

<span data-ttu-id="3572e-106">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="3572e-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="3572e-107">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="3572e-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="3572e-108">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="3572e-109">SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="3572e-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="3572e-110">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="3572e-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="3572e-111">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="3572e-112">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3572e-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-113">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="3572e-113">JSON is enabled by default.</span></span> <span data-ttu-id="3572e-114">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="3572e-115">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="3572e-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="3572e-116">W tym delegatze `SerializerOptions` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="3572e-117">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="3572e-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="3572e-118">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="3572e-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="3572e-119">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="3572e-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="3572e-120">Na przykład wywoływanie `.WithSecurity(MessagePackSecurity.UntrustedData)` podczas zamiany `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="3572e-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="3572e-121">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="3572e-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-122">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3572e-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="3572e-123">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="3572e-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="3572e-124">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="3572e-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="3572e-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3572e-125">.NET client</span></span>

<span data-ttu-id="3572e-126">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3572e-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="3572e-127">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3572e-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="3572e-128">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-128">JavaScript client</span></span>

<span data-ttu-id="3572e-129">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="3572e-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="3572e-130">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3572e-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="3572e-131">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="3572e-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="3572e-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="3572e-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="3572e-133">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3572e-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="3572e-134">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="3572e-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="3572e-135">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="3572e-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-136">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3572e-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="3572e-137">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="3572e-138">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="3572e-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="3572e-139">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="3572e-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="3572e-140">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3572e-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="3572e-141">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="3572e-141">MessagePack quirks</span></span>

<span data-ttu-id="3572e-142">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="3572e-143">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="3572e-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="3572e-144">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3572e-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="3572e-145">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="3572e-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="3572e-146">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3572e-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="3572e-147">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3572e-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="3572e-148">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3572e-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="3572e-149">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="3572e-150">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="3572e-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="3572e-151">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="3572e-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="3572e-152">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="3572e-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="3572e-153">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zostanie przekonwertowany na format UTC, jeśli `DateTime.Kind` jest w `DateTimeKind.Local` przeciwnym razie, nie będzie on dotykał czasu i zostanie przekazany w postaci, w jakiej się znajduje.</span><span class="sxs-lookup"><span data-stu-id="3572e-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="3572e-154">Jeśli pracujesz z `DateTime` wartościami, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="3572e-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="3572e-155">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="3572e-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="3572e-156">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="3572e-157">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="3572e-158">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="3572e-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="3572e-159">Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="3572e-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="3572e-160">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3572e-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="3572e-161">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3572e-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="3572e-162">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="3572e-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="3572e-163">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="3572e-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="3572e-164">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="3572e-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="3572e-165">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="3572e-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="3572e-166">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="3572e-167">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="3572e-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="3572e-168">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="3572e-169">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="3572e-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="3572e-170">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="3572e-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="3572e-171">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="3572e-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="3572e-172">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="3572e-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="3572e-173">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="3572e-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="3572e-174">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="3572e-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="3572e-175">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="3572e-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="3572e-176">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="3572e-176">Related resources</span></span>

* [<span data-ttu-id="3572e-177">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3572e-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3572e-178">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3572e-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3572e-179">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3572e-180">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="3572e-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="3572e-181">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="3572e-181">What is MessagePack?</span></span>

<span data-ttu-id="3572e-182">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="3572e-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="3572e-183">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="3572e-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="3572e-184">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="3572e-185">SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="3572e-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="3572e-186">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="3572e-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="3572e-187">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="3572e-188">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3572e-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-189">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="3572e-189">JSON is enabled by default.</span></span> <span data-ttu-id="3572e-190">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="3572e-191">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="3572e-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="3572e-192">W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="3572e-193">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="3572e-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="3572e-194">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="3572e-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="3572e-195">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="3572e-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="3572e-196">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="3572e-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="3572e-197">Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="3572e-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="3572e-198">Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie.</span><span class="sxs-lookup"><span data-stu-id="3572e-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="3572e-199">`MessagePack` w wersji 2. x wprowadzono istotne zmiany i są one niezgodne z SignalR wersjami 3,1 i wcześniejszymi.</span><span class="sxs-lookup"><span data-stu-id="3572e-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="3572e-200">Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="3572e-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="3572e-201">Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3572e-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="3572e-202">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="3572e-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-203">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3572e-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="3572e-204">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="3572e-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="3572e-205">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="3572e-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="3572e-206">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3572e-206">.NET client</span></span>

<span data-ttu-id="3572e-207">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3572e-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="3572e-208">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3572e-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="3572e-209">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-209">JavaScript client</span></span>

<span data-ttu-id="3572e-210">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="3572e-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="3572e-211">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3572e-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="3572e-212">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="3572e-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="3572e-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="3572e-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="3572e-214">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3572e-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="3572e-215">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="3572e-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="3572e-216">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="3572e-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-217">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3572e-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="3572e-218">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="3572e-219">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="3572e-219">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="3572e-220">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="3572e-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="3572e-221">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3572e-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="3572e-222">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="3572e-222">MessagePack quirks</span></span>

<span data-ttu-id="3572e-223">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="3572e-224">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="3572e-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="3572e-225">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3572e-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="3572e-226">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="3572e-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="3572e-227">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3572e-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="3572e-228">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3572e-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="3572e-229">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3572e-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="3572e-230">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="3572e-231">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="3572e-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="3572e-232">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="3572e-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="3572e-233">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="3572e-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="3572e-234">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="3572e-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="3572e-235">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="3572e-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="3572e-236">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="3572e-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="3572e-237">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="3572e-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="3572e-238">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="3572e-239">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="3572e-240">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="3572e-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="3572e-241">Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="3572e-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="3572e-242">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3572e-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="3572e-243">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3572e-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="3572e-244">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="3572e-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="3572e-245">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="3572e-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="3572e-246">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="3572e-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="3572e-247">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="3572e-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="3572e-248">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="3572e-249">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="3572e-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="3572e-250">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="3572e-251">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="3572e-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="3572e-252">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="3572e-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="3572e-253">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="3572e-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="3572e-254">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="3572e-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="3572e-255">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="3572e-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="3572e-256">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="3572e-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="3572e-257">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="3572e-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="3572e-258">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="3572e-258">Related resources</span></span>

* [<span data-ttu-id="3572e-259">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3572e-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3572e-260">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3572e-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3572e-261">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3572e-262">W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="3572e-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="3572e-263">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="3572e-263">What is MessagePack?</span></span>

<span data-ttu-id="3572e-264">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="3572e-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="3572e-265">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="3572e-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="3572e-266">Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="3572e-267">SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="3572e-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="3572e-268">Konfigurowanie MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="3572e-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="3572e-269">Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="3572e-270">W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3572e-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-271">KOD JSON jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="3572e-271">JSON is enabled by default.</span></span> <span data-ttu-id="3572e-272">Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="3572e-273">Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="3572e-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="3572e-274">W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="3572e-275">Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="3572e-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="3572e-276">Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="3572e-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="3572e-277">Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek.</span><span class="sxs-lookup"><span data-stu-id="3572e-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="3572e-278">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="3572e-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="3572e-279">Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="3572e-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="3572e-280">Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie.</span><span class="sxs-lookup"><span data-stu-id="3572e-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="3572e-281">Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="3572e-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="3572e-282">Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3572e-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="3572e-283">Konfigurowanie MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="3572e-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-284">KOD JSON jest domyślnie włączony dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="3572e-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="3572e-285">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="3572e-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="3572e-286">Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.</span><span class="sxs-lookup"><span data-stu-id="3572e-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="3572e-287">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3572e-287">.NET client</span></span>

<span data-ttu-id="3572e-288">Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3572e-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="3572e-289">To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3572e-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="3572e-290">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-290">JavaScript client</span></span>

<span data-ttu-id="3572e-291">Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="3572e-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="3572e-292">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3572e-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="3572e-293">Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="3572e-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="3572e-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="3572e-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="3572e-295">W przeglądarce należy `msgpack5` również odwołać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="3572e-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="3572e-296">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="3572e-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="3572e-297">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="3572e-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="3572e-298">W przypadku korzystania z `<script>` elementu kolejność jest ważna.</span><span class="sxs-lookup"><span data-stu-id="3572e-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="3572e-299">Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="3572e-300">*signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="3572e-300">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="3572e-301">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="3572e-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="3572e-302">W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3572e-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="3572e-303">MessagePack osobliwości</span><span class="sxs-lookup"><span data-stu-id="3572e-303">MessagePack quirks</span></span>

<span data-ttu-id="3572e-304">Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="3572e-305">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="3572e-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="3572e-306">W protokole MessagePack jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3572e-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="3572e-307">Rozważmy na przykład następujące klasy języka C#:</span><span class="sxs-lookup"><span data-stu-id="3572e-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="3572e-308">Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3572e-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="3572e-309">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3572e-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="3572e-310">Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#.</span><span class="sxs-lookup"><span data-stu-id="3572e-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="3572e-311">Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="3572e-312">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="3572e-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="3572e-313">Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="3572e-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="3572e-314">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="3572e-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="3572e-315">W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="3572e-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="3572e-316">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="3572e-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="3572e-317">Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="3572e-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="3572e-318">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="3572e-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="3572e-319">Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="3572e-320">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack.</span><span class="sxs-lookup"><span data-stu-id="3572e-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="3572e-321">Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="3572e-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="3572e-322">Wartość jest, `DateTime.MinValue` `January 1, 0001` która musi być zakodowana w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="3572e-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="3572e-323">W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="3572e-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="3572e-324">Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="3572e-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="3572e-325">Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości.</span><span class="sxs-lookup"><span data-stu-id="3572e-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="3572e-326">Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="3572e-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="3572e-327">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="3572e-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="3572e-328">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="3572e-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="3572e-329">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="3572e-330">W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="3572e-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="3572e-331">Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji.</span><span class="sxs-lookup"><span data-stu-id="3572e-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="3572e-332">Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="3572e-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="3572e-333">Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="3572e-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="3572e-334">Kontrole typu są bardziej rygorystyczne w MessagePack</span><span class="sxs-lookup"><span data-stu-id="3572e-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="3572e-335">Podczas deserializacji protokół JSON będzie wykonywał konwersje typów.</span><span class="sxs-lookup"><span data-stu-id="3572e-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="3572e-336">Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana.</span><span class="sxs-lookup"><span data-stu-id="3572e-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="3572e-337">Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):</span><span class="sxs-lookup"><span data-stu-id="3572e-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="3572e-338">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="3572e-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="3572e-339">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="3572e-339">Related resources</span></span>

* [<span data-ttu-id="3572e-340">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="3572e-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3572e-341">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="3572e-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3572e-342">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="3572e-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
