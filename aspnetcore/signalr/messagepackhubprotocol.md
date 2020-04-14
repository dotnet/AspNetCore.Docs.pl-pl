---
title: Użyj protokołu MessagePack SignalR Hub w dla ASP.NET Core
author: bradygaster
description: Dodaj protokół MessagePack Hub protocol SignalRdo ASP.NET Core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277239"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="0918d-103">Użyj protokołu MessagePack SignalR Hub w dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0918d-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0918d-104">W tym artykule założono, że czytelnik jest zaznajomiony z tematami omówionymi w [temacie Wprowadzenie](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0918d-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0918d-105">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="0918d-105">What is MessagePack?</span></span>

<span data-ttu-id="0918d-106">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="0918d-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0918d-107">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze wiadomości w porównaniu do [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0918d-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0918d-108">Wiadomości binarne są nieczytelne, patrząc na ślady sieci i dzienniki, chyba że bajty są przekazywane przez parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="0918d-109">ma wbudowaną obsługę formatu MessagePack i zapewnia interfejsy API dla klienta i serwera do użycia.</span><span class="sxs-lookup"><span data-stu-id="0918d-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0918d-110">Konfigurowanie pakietu MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="0918d-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="0918d-111">Aby włączyć messagepack hub protokołu na `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` serwerze, należy zainstalować pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0918d-112">W `Startup.ConfigureServices` metodzie `AddMessagePackProtocol` dodaj `AddSignalR` do wywołania, aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="0918d-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-113">JSON jest domyślnie włączona.</span><span class="sxs-lookup"><span data-stu-id="0918d-113">JSON is enabled by default.</span></span> <span data-ttu-id="0918d-114">Dodawanie MessagePack umożliwia obsługę zarówno JSON i MessagePack klientów.</span><span class="sxs-lookup"><span data-stu-id="0918d-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0918d-115">Aby dostosować sposób messagepack będzie `AddMessagePackProtocol` formatować dane, trwa pełnomocnika do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="0918d-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0918d-116">W tym delegata `SerializerOptions` właściwość może służyć do konfigurowania messagepack opcje serializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0918d-117">Aby uzyskać więcej informacji na temat działania programów rozpoznawania nazw, odwiedź bibliotekę MessagePack w [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0918d-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0918d-118">Atrybuty mogą być używane na obiektach, które chcesz serializować, aby zdefiniować sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="0918d-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0918d-119">Zdecydowanie zalecamy zapoznanie się z [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowanie zalecanych plastrów.</span><span class="sxs-lookup"><span data-stu-id="0918d-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0918d-120">Na przykład `.WithSecurity(MessagePackSecurity.UntrustedData)` wywołanie `SerializerOptions`podczas zastępowania pliku .</span><span class="sxs-lookup"><span data-stu-id="0918d-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0918d-121">Konfigurowanie pakietu MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="0918d-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-122">JSON jest domyślnie włączona dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="0918d-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0918d-123">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="0918d-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="0918d-124">Dodanie obsługi messagepack zastąpi wszystkie wcześniej skonfigurowane protokoły.</span><span class="sxs-lookup"><span data-stu-id="0918d-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0918d-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="0918d-125">.NET client</span></span>

<span data-ttu-id="0918d-126">Aby włączyć messagepack w kliencie `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`zainstaluj pakiet i zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="0918d-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0918d-127">To `AddMessagePackProtocol` wywołanie zajmuje pełnomocnika do konfigurowania opcji, podobnie jak serwer.</span><span class="sxs-lookup"><span data-stu-id="0918d-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0918d-128">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-128">JavaScript client</span></span>

<span data-ttu-id="0918d-129">Obsługa messagepack dla klienta JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) jest dostarczana przez pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="0918d-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0918d-130">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="0918d-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0918d-131">Po zainstalowaniu pakietu npm moduł może być używany bezpośrednio za pośrednictwem modułu ładującego JavaScript lub importowany do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="0918d-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0918d-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0918d-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0918d-133">W przeglądarce `msgpack5` należy również odwoływać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="0918d-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0918d-134">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="0918d-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0918d-135">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="0918d-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-136">Podczas korzystania `<script>` z elementu, kolejność jest ważne.</span><span class="sxs-lookup"><span data-stu-id="0918d-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0918d-137">Jeśli *signalr-protocol-msgpack.js* odwołuje się przed *msgpack5.js*, błąd występuje podczas próby połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0918d-138">*signalr.js* jest również wymagane przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="0918d-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0918d-139">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` spowoduje skonfigurowanie klienta do używania protokołu MessagePack podczas łączenia się z serwerem.</span><span class="sxs-lookup"><span data-stu-id="0918d-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0918d-140">W tej chwili nie ma żadnych opcji konfiguracji protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0918d-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0918d-141">MessagePack dziwactwa</span><span class="sxs-lookup"><span data-stu-id="0918d-141">MessagePack quirks</span></span>

<span data-ttu-id="0918d-142">Istnieje kilka problemów, o których należy pamiętać podczas korzystania z protokołu MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="0918d-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0918d-143">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="0918d-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0918d-144">W protokole MessagePack rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0918d-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0918d-145">Rozważmy na przykład następującą klasę języka C#:</span><span class="sxs-lookup"><span data-stu-id="0918d-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0918d-146">Podczas wysyłania z klienta JavaScript, należy użyć `PascalCased` nazwy właściwości, ponieważ wielkość liter musi dokładnie odpowiadać klasy C#.</span><span class="sxs-lookup"><span data-stu-id="0918d-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0918d-147">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0918d-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0918d-148">Przy `camelCased` użyciu nazw nie będzie poprawnie powiązać z klasy C#.</span><span class="sxs-lookup"><span data-stu-id="0918d-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0918d-149">Można obejść ten problem `Key` za pomocą atrybutu, aby określić inną nazwę messagepack właściwości.</span><span class="sxs-lookup"><span data-stu-id="0918d-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0918d-150">Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0918d-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0918d-151">DateTime.Kind nie jest zachowywany podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="0918d-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0918d-152">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="0918d-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0918d-153">W rezultacie podczas deserializacji daty messagepack hub protokół przekonwertuje `DateTime.Kind` do `DateTimeKind.Local` formatu UTC, jeśli jest inaczej nie dotknie czasu i przekazać go w stanie, w jakim jest.</span><span class="sxs-lookup"><span data-stu-id="0918d-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="0918d-154">Jeśli pracujesz z `DateTime` wartościami, zalecamy konwersję na UTC przed ich wysłaniem.</span><span class="sxs-lookup"><span data-stu-id="0918d-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0918d-155">Przekonwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="0918d-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0918d-156">DateTime.MinValue nie jest obsługiwany przez messagepack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0918d-157">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana SignalR przez klienta JavaScript `timestamp96` nie obsługuje typu w pakiecie MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0918d-158">Ten typ jest używany do kodowania bardzo dużych wartości dat (bardzo wcześnie w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="0918d-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0918d-159">Wartość `DateTime.MinValue` jest `January 1, 0001`, które muszą być zakodowane w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="0918d-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0918d-160">Z tego powodu `DateTime.MinValue` wysyłanie do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="0918d-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0918d-161">Po `DateTime.MinValue` odebraniu przez klienta JavaScript zgłaszany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="0918d-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0918d-162">Zazwyczaj `DateTime.MinValue` jest używany do kodowania "brakujące" lub `null` wartość.</span><span class="sxs-lookup"><span data-stu-id="0918d-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0918d-163">Jeśli chcesz zakodować tę wartość w messagepack,`DateTime?`należy użyć wartości `bool` nullable `DateTime` ( ) lub zakodować oddzielną wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="0918d-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0918d-164">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0918d-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0918d-165">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="0918d-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0918d-166">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) używana przez klienta i serwer .NET używa generowania kodu do optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0918d-167">W rezultacie nie jest domyślnie obsługiwana w środowiskach, które używają kompilacji "z wyprzedzeniem" (takich jak Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="0918d-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0918d-168">Jest możliwe użycie MessagePack w tych środowiskach przez "wstępne generowanie" kod serializator/deserializer.</span><span class="sxs-lookup"><span data-stu-id="0918d-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0918d-169">Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="0918d-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="0918d-170">Po wstępnie wygenerowaniu serializatorów można je zarejestrować za `AddMessagePackProtocol`pomocą delegata konfiguracji przekazanego do:</span><span class="sxs-lookup"><span data-stu-id="0918d-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0918d-171">Sprawdzanie typów jest bardziej rygorystyczne w pakiecie MessagePack</span><span class="sxs-lookup"><span data-stu-id="0918d-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0918d-172">Protokół JSON Hub będzie przeprowadzał konwersje typów podczas deserializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0918d-173">Na przykład jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą (`{ foo: 42 }`), `string`ale właściwość w klasie .NET jest typu, wartość zostanie przekonwertowana.</span><span class="sxs-lookup"><span data-stu-id="0918d-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0918d-174">Jednak MessagePack nie wykonuje tej konwersji i zda wyjątek, który można zobaczyć w dziennikach po stronie serwera (i w konsoli):</span><span class="sxs-lookup"><span data-stu-id="0918d-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0918d-175">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0918d-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0918d-176">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="0918d-176">Related resources</span></span>

* [<span data-ttu-id="0918d-177">Rozpocząć</span><span class="sxs-lookup"><span data-stu-id="0918d-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0918d-178">Klient platformy .NET</span><span class="sxs-lookup"><span data-stu-id="0918d-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0918d-179">Klient środowiska JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0918d-180">W tym artykule założono, że czytelnik jest zaznajomiony z tematami omówionymi w [temacie Wprowadzenie](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0918d-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0918d-181">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="0918d-181">What is MessagePack?</span></span>

<span data-ttu-id="0918d-182">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="0918d-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0918d-183">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze wiadomości w porównaniu do [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0918d-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0918d-184">Wiadomości binarne są nieczytelne, patrząc na ślady sieci i dzienniki, chyba że bajty są przekazywane przez parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="0918d-185">ma wbudowaną obsługę formatu MessagePack i zapewnia interfejsy API dla klienta i serwera do użycia.</span><span class="sxs-lookup"><span data-stu-id="0918d-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0918d-186">Konfigurowanie pakietu MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="0918d-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="0918d-187">Aby włączyć messagepack hub protokołu na `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` serwerze, należy zainstalować pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0918d-188">W `Startup.ConfigureServices` metodzie `AddMessagePackProtocol` dodaj `AddSignalR` do wywołania, aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="0918d-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-189">JSON jest domyślnie włączona.</span><span class="sxs-lookup"><span data-stu-id="0918d-189">JSON is enabled by default.</span></span> <span data-ttu-id="0918d-190">Dodawanie MessagePack umożliwia obsługę zarówno JSON i MessagePack klientów.</span><span class="sxs-lookup"><span data-stu-id="0918d-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0918d-191">Aby dostosować sposób messagepack będzie `AddMessagePackProtocol` formatować dane, trwa pełnomocnika do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="0918d-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0918d-192">W tym delegata `FormatterResolvers` właściwość może służyć do konfigurowania messagepack opcje serializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0918d-193">Aby uzyskać więcej informacji na temat działania programów rozpoznawania nazw, odwiedź bibliotekę MessagePack w [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0918d-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0918d-194">Atrybuty mogą być używane na obiektach, które chcesz serializować, aby zdefiniować sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="0918d-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0918d-195">Zdecydowanie zalecamy zapoznanie się z [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowanie zalecanych plastrów.</span><span class="sxs-lookup"><span data-stu-id="0918d-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0918d-196">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="0918d-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0918d-197">Ustawienie `MessagePackSecurity.Active` wymaga ręcznego zainstalowania [wersji 1.9.x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="0918d-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0918d-198">Instalacja `MessagePack` 1.9.x uaktualnień używanej przez wersję. SignalR</span><span class="sxs-lookup"><span data-stu-id="0918d-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0918d-199">Gdy `MessagePackSecurity.Active` nie jest `MessagePackSecurity.UntrustedData`ustawiona na, złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="0918d-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0918d-200">Ustaw `MessagePackSecurity.Active` `Program.Main`w , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="0918d-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0918d-201">Konfigurowanie pakietu MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="0918d-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-202">JSON jest domyślnie włączona dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="0918d-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0918d-203">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="0918d-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="0918d-204">Dodanie obsługi messagepack zastąpi wszystkie wcześniej skonfigurowane protokoły.</span><span class="sxs-lookup"><span data-stu-id="0918d-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0918d-205">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="0918d-205">.NET client</span></span>

<span data-ttu-id="0918d-206">Aby włączyć messagepack w kliencie `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`zainstaluj pakiet i zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="0918d-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0918d-207">To `AddMessagePackProtocol` wywołanie zajmuje pełnomocnika do konfigurowania opcji, podobnie jak serwer.</span><span class="sxs-lookup"><span data-stu-id="0918d-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0918d-208">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-208">JavaScript client</span></span>

<span data-ttu-id="0918d-209">Obsługa messagepack dla klienta JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) jest dostarczana przez pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="0918d-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0918d-210">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="0918d-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="0918d-211">Po zainstalowaniu pakietu npm moduł może być używany bezpośrednio za pośrednictwem modułu ładującego JavaScript lub importowany do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="0918d-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0918d-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0918d-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="0918d-213">W przeglądarce `msgpack5` należy również odwoływać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="0918d-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0918d-214">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="0918d-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0918d-215">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="0918d-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-216">Podczas korzystania `<script>` z elementu, kolejność jest ważne.</span><span class="sxs-lookup"><span data-stu-id="0918d-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0918d-217">Jeśli *signalr-protocol-msgpack.js* odwołuje się przed *msgpack5.js*, błąd występuje podczas próby połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0918d-218">*signalr.js* jest również wymagane przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="0918d-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0918d-219">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` spowoduje skonfigurowanie klienta do używania protokołu MessagePack podczas łączenia się z serwerem.</span><span class="sxs-lookup"><span data-stu-id="0918d-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0918d-220">W tej chwili nie ma żadnych opcji konfiguracji protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0918d-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0918d-221">MessagePack dziwactwa</span><span class="sxs-lookup"><span data-stu-id="0918d-221">MessagePack quirks</span></span>

<span data-ttu-id="0918d-222">Istnieje kilka problemów, o których należy pamiętać podczas korzystania z protokołu MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="0918d-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0918d-223">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="0918d-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0918d-224">W protokole MessagePack rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0918d-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0918d-225">Rozważmy na przykład następującą klasę języka C#:</span><span class="sxs-lookup"><span data-stu-id="0918d-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0918d-226">Podczas wysyłania z klienta JavaScript, należy użyć `PascalCased` nazwy właściwości, ponieważ wielkość liter musi dokładnie odpowiadać klasy C#.</span><span class="sxs-lookup"><span data-stu-id="0918d-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0918d-227">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0918d-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0918d-228">Przy `camelCased` użyciu nazw nie będzie poprawnie powiązać z klasy C#.</span><span class="sxs-lookup"><span data-stu-id="0918d-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0918d-229">Można obejść ten problem `Key` za pomocą atrybutu, aby określić inną nazwę messagepack właściwości.</span><span class="sxs-lookup"><span data-stu-id="0918d-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0918d-230">Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0918d-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0918d-231">DateTime.Kind nie jest zachowywany podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="0918d-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0918d-232">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="0918d-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0918d-233">W rezultacie podczas deserializacji daty protokół MessagePack Hub przyjmuje założenie, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="0918d-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0918d-234">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy konwersję na UTC przed wysłaniem ich.</span><span class="sxs-lookup"><span data-stu-id="0918d-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0918d-235">Przekonwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="0918d-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0918d-236">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="0918d-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0918d-237">DateTime.MinValue nie jest obsługiwany przez messagepack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0918d-238">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana SignalR przez klienta JavaScript `timestamp96` nie obsługuje typu w pakiecie MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0918d-239">Ten typ jest używany do kodowania bardzo dużych wartości dat (bardzo wcześnie w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="0918d-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0918d-240">Wartość `DateTime.MinValue` jest `January 1, 0001`, które muszą być zakodowane w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="0918d-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0918d-241">Z tego powodu `DateTime.MinValue` wysyłanie do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="0918d-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0918d-242">Po `DateTime.MinValue` odebraniu przez klienta JavaScript zgłaszany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="0918d-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0918d-243">Zazwyczaj `DateTime.MinValue` jest używany do kodowania "brakujące" lub `null` wartość.</span><span class="sxs-lookup"><span data-stu-id="0918d-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0918d-244">Jeśli chcesz zakodować tę wartość w messagepack,`DateTime?`należy użyć wartości `bool` nullable `DateTime` ( ) lub zakodować oddzielną wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="0918d-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0918d-245">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0918d-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0918d-246">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="0918d-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0918d-247">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer .NET używa generowania kodu do optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0918d-248">W rezultacie nie jest domyślnie obsługiwana w środowiskach, które używają kompilacji "z wyprzedzeniem" (takich jak Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="0918d-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0918d-249">Jest możliwe użycie MessagePack w tych środowiskach przez "wstępne generowanie" kod serializator/deserializer.</span><span class="sxs-lookup"><span data-stu-id="0918d-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0918d-250">Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="0918d-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0918d-251">Po wstępnie wygenerowaniu serializatorów można je zarejestrować za `AddMessagePackProtocol`pomocą delegata konfiguracji przekazanego do:</span><span class="sxs-lookup"><span data-stu-id="0918d-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0918d-252">Sprawdzanie typów jest bardziej rygorystyczne w pakiecie MessagePack</span><span class="sxs-lookup"><span data-stu-id="0918d-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0918d-253">Protokół JSON Hub będzie przeprowadzał konwersje typów podczas deserializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0918d-254">Na przykład jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą (`{ foo: 42 }`), `string`ale właściwość w klasie .NET jest typu, wartość zostanie przekonwertowana.</span><span class="sxs-lookup"><span data-stu-id="0918d-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0918d-255">Jednak MessagePack nie wykonuje tej konwersji i zda wyjątek, który można zobaczyć w dziennikach po stronie serwera (i w konsoli):</span><span class="sxs-lookup"><span data-stu-id="0918d-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0918d-256">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0918d-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0918d-257">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="0918d-257">Related resources</span></span>

* [<span data-ttu-id="0918d-258">Rozpocząć</span><span class="sxs-lookup"><span data-stu-id="0918d-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0918d-259">Klient platformy .NET</span><span class="sxs-lookup"><span data-stu-id="0918d-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0918d-260">Klient środowiska JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0918d-261">W tym artykule założono, że czytelnik jest zaznajomiony z tematami omówionymi w [temacie Wprowadzenie](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="0918d-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="0918d-262">Co to jest MessagePack?</span><span class="sxs-lookup"><span data-stu-id="0918d-262">What is MessagePack?</span></span>

<span data-ttu-id="0918d-263">[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej.</span><span class="sxs-lookup"><span data-stu-id="0918d-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="0918d-264">Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze wiadomości w porównaniu do [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="0918d-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="0918d-265">Wiadomości binarne są nieczytelne, patrząc na ślady sieci i dzienniki, chyba że bajty są przekazywane przez parser MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="0918d-266">ma wbudowaną obsługę formatu MessagePack i zapewnia interfejsy API dla klienta i serwera do użycia.</span><span class="sxs-lookup"><span data-stu-id="0918d-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="0918d-267">Konfigurowanie pakietu MessagePack na serwerze</span><span class="sxs-lookup"><span data-stu-id="0918d-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="0918d-268">Aby włączyć messagepack hub protokołu na `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` serwerze, należy zainstalować pakiet w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="0918d-269">W `Startup.ConfigureServices` metodzie `AddMessagePackProtocol` dodaj `AddSignalR` do wywołania, aby włączyć obsługę MessagePack na serwerze.</span><span class="sxs-lookup"><span data-stu-id="0918d-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-270">JSON jest domyślnie włączona.</span><span class="sxs-lookup"><span data-stu-id="0918d-270">JSON is enabled by default.</span></span> <span data-ttu-id="0918d-271">Dodawanie MessagePack umożliwia obsługę zarówno JSON i MessagePack klientów.</span><span class="sxs-lookup"><span data-stu-id="0918d-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="0918d-272">Aby dostosować sposób messagepack będzie `AddMessagePackProtocol` formatować dane, trwa pełnomocnika do konfigurowania opcji.</span><span class="sxs-lookup"><span data-stu-id="0918d-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="0918d-273">W tym delegata `FormatterResolvers` właściwość może służyć do konfigurowania messagepack opcje serializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="0918d-274">Aby uzyskać więcej informacji na temat działania programów rozpoznawania nazw, odwiedź bibliotekę MessagePack w [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="0918d-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="0918d-275">Atrybuty mogą być używane na obiektach, które chcesz serializować, aby zdefiniować sposób ich obsługi.</span><span class="sxs-lookup"><span data-stu-id="0918d-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="0918d-276">Zdecydowanie zalecamy zapoznanie się z [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowanie zalecanych plastrów.</span><span class="sxs-lookup"><span data-stu-id="0918d-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="0918d-277">Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="0918d-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="0918d-278">Ustawienie `MessagePackSecurity.Active` wymaga ręcznego zainstalowania [wersji 1.9.x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="0918d-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="0918d-279">Instalacja `MessagePack` 1.9.x uaktualnień używanej przez wersję. SignalR</span><span class="sxs-lookup"><span data-stu-id="0918d-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="0918d-280">Gdy `MessagePackSecurity.Active` nie jest `MessagePackSecurity.UntrustedData`ustawiona na, złośliwy klient może spowodować odmowę usługi.</span><span class="sxs-lookup"><span data-stu-id="0918d-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="0918d-281">Ustaw `MessagePackSecurity.Active` `Program.Main`w , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="0918d-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="0918d-282">Konfigurowanie pakietu MessagePack na kliencie</span><span class="sxs-lookup"><span data-stu-id="0918d-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-283">JSON jest domyślnie włączona dla obsługiwanych klientów.</span><span class="sxs-lookup"><span data-stu-id="0918d-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="0918d-284">Klienci mogą obsługiwać tylko jeden protokół.</span><span class="sxs-lookup"><span data-stu-id="0918d-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="0918d-285">Dodanie obsługi messagepack zastąpi wszystkie wcześniej skonfigurowane protokoły.</span><span class="sxs-lookup"><span data-stu-id="0918d-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="0918d-286">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="0918d-286">.NET client</span></span>

<span data-ttu-id="0918d-287">Aby włączyć messagepack w kliencie `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`zainstaluj pakiet i zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="0918d-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="0918d-288">To `AddMessagePackProtocol` wywołanie zajmuje pełnomocnika do konfigurowania opcji, podobnie jak serwer.</span><span class="sxs-lookup"><span data-stu-id="0918d-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="0918d-289">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-289">JavaScript client</span></span>

<span data-ttu-id="0918d-290">Obsługa messagepack dla klienta JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) jest dostarczana przez pakiet npm.</span><span class="sxs-lookup"><span data-stu-id="0918d-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="0918d-291">Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="0918d-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="0918d-292">Po zainstalowaniu pakietu npm moduł może być używany bezpośrednio za pośrednictwem modułu ładującego JavaScript lub importowany do przeglądarki, odwołując się do następującego pliku:</span><span class="sxs-lookup"><span data-stu-id="0918d-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="0918d-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="0918d-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="0918d-294">W przeglądarce `msgpack5` należy również odwoływać się do biblioteki.</span><span class="sxs-lookup"><span data-stu-id="0918d-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="0918d-295">Użyj `<script>` znacznika, aby utworzyć odwołanie.</span><span class="sxs-lookup"><span data-stu-id="0918d-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="0918d-296">Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="0918d-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="0918d-297">Podczas korzystania `<script>` z elementu, kolejność jest ważne.</span><span class="sxs-lookup"><span data-stu-id="0918d-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="0918d-298">Jeśli *signalr-protocol-msgpack.js* odwołuje się przed *msgpack5.js*, błąd występuje podczas próby połączenia z MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="0918d-299">*signalr.js* jest również wymagane przed *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="0918d-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="0918d-300">Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` spowoduje skonfigurowanie klienta do używania protokołu MessagePack podczas łączenia się z serwerem.</span><span class="sxs-lookup"><span data-stu-id="0918d-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="0918d-301">W tej chwili nie ma żadnych opcji konfiguracji protokołu MessagePack na kliencie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0918d-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="0918d-302">MessagePack dziwactwa</span><span class="sxs-lookup"><span data-stu-id="0918d-302">MessagePack quirks</span></span>

<span data-ttu-id="0918d-303">Istnieje kilka problemów, o których należy pamiętać podczas korzystania z protokołu MessagePack Hub.</span><span class="sxs-lookup"><span data-stu-id="0918d-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="0918d-304">MessagePack jest rozróżniana wielkość liter</span><span class="sxs-lookup"><span data-stu-id="0918d-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="0918d-305">W protokole MessagePack rozróżniana jest wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="0918d-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="0918d-306">Rozważmy na przykład następującą klasę języka C#:</span><span class="sxs-lookup"><span data-stu-id="0918d-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="0918d-307">Podczas wysyłania z klienta JavaScript, należy użyć `PascalCased` nazwy właściwości, ponieważ wielkość liter musi dokładnie odpowiadać klasy C#.</span><span class="sxs-lookup"><span data-stu-id="0918d-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="0918d-308">Przykład:</span><span class="sxs-lookup"><span data-stu-id="0918d-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="0918d-309">Przy `camelCased` użyciu nazw nie będzie poprawnie powiązać z klasy C#.</span><span class="sxs-lookup"><span data-stu-id="0918d-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="0918d-310">Można obejść ten problem `Key` za pomocą atrybutu, aby określić inną nazwę messagepack właściwości.</span><span class="sxs-lookup"><span data-stu-id="0918d-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="0918d-311">Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="0918d-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="0918d-312">DateTime.Kind nie jest zachowywany podczas serializacji/deserializacji</span><span class="sxs-lookup"><span data-stu-id="0918d-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="0918d-313">Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="0918d-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="0918d-314">W rezultacie podczas deserializacji daty protokół MessagePack Hub przyjmuje założenie, że data przychodząca jest w formacie UTC.</span><span class="sxs-lookup"><span data-stu-id="0918d-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="0918d-315">Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy konwersję na UTC przed wysłaniem ich.</span><span class="sxs-lookup"><span data-stu-id="0918d-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="0918d-316">Przekonwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.</span><span class="sxs-lookup"><span data-stu-id="0918d-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="0918d-317">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="0918d-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="0918d-318">DateTime.MinValue nie jest obsługiwany przez messagepack w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="0918d-319">Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana SignalR przez klienta JavaScript `timestamp96` nie obsługuje typu w pakiecie MessagePack.</span><span class="sxs-lookup"><span data-stu-id="0918d-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="0918d-320">Ten typ jest używany do kodowania bardzo dużych wartości dat (bardzo wcześnie w przeszłości lub bardzo daleko w przyszłości).</span><span class="sxs-lookup"><span data-stu-id="0918d-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="0918d-321">Wartość `DateTime.MinValue` jest, `January 1, 0001` który musi być zakodowany w `timestamp96` wartości.</span><span class="sxs-lookup"><span data-stu-id="0918d-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="0918d-322">Z tego powodu `DateTime.MinValue` wysyłanie do klienta JavaScript nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="0918d-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="0918d-323">Po `DateTime.MinValue` odebraniu przez klienta JavaScript zgłaszany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="0918d-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="0918d-324">Zazwyczaj `DateTime.MinValue` jest używany do kodowania "brakujące" lub `null` wartość.</span><span class="sxs-lookup"><span data-stu-id="0918d-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="0918d-325">Jeśli chcesz zakodować tę wartość w messagepack,`DateTime?`należy użyć wartości `bool` nullable `DateTime` ( ) lub zakodować oddzielną wartość wskazującą, czy data jest obecna.</span><span class="sxs-lookup"><span data-stu-id="0918d-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="0918d-326">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="0918d-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="0918d-327">Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"</span><span class="sxs-lookup"><span data-stu-id="0918d-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="0918d-328">Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer .NET używa generowania kodu do optymalizacji serializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="0918d-329">W rezultacie nie jest domyślnie obsługiwana w środowiskach, które używają kompilacji "z wyprzedzeniem" (takich jak Xamarin iOS lub Unity).</span><span class="sxs-lookup"><span data-stu-id="0918d-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="0918d-330">Jest możliwe użycie MessagePack w tych środowiskach przez "wstępne generowanie" kod serializator/deserializer.</span><span class="sxs-lookup"><span data-stu-id="0918d-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="0918d-331">Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="0918d-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="0918d-332">Po wstępnie wygenerowaniu serializatorów można je zarejestrować za `AddMessagePackProtocol`pomocą delegata konfiguracji przekazanego do:</span><span class="sxs-lookup"><span data-stu-id="0918d-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="0918d-333">Sprawdzanie typów jest bardziej rygorystyczne w pakiecie MessagePack</span><span class="sxs-lookup"><span data-stu-id="0918d-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="0918d-334">Protokół JSON Hub będzie przeprowadzał konwersje typów podczas deserializacji.</span><span class="sxs-lookup"><span data-stu-id="0918d-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="0918d-335">Na przykład jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą (`{ foo: 42 }`), `string`ale właściwość w klasie .NET jest typu, wartość zostanie przekonwertowana.</span><span class="sxs-lookup"><span data-stu-id="0918d-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="0918d-336">Jednak MessagePack nie wykonuje tej konwersji i zda wyjątek, który można zobaczyć w dziennikach po stronie serwera (i w konsoli):</span><span class="sxs-lookup"><span data-stu-id="0918d-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="0918d-337">Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="0918d-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="0918d-338">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="0918d-338">Related resources</span></span>

* [<span data-ttu-id="0918d-339">Rozpocząć</span><span class="sxs-lookup"><span data-stu-id="0918d-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0918d-340">Klient platformy .NET</span><span class="sxs-lookup"><span data-stu-id="0918d-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0918d-341">Klient środowiska JavaScript</span><span class="sxs-lookup"><span data-stu-id="0918d-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
