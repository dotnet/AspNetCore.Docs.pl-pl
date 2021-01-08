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
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>Użyj protokołu MessagePack Hub w programie SignalR for ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)

## <a name="what-is-messagepack"></a>Co to jest MessagePack?

[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej. Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/). Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack. SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.

## <a name="configure-messagepack-on-the-server"></a>Konfigurowanie MessagePack na serwerze

Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji. W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.

> [!NOTE]
> KOD JSON jest domyślnie włączony. Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji. W tym delegatze `SerializerOptions` Właściwość może służyć do konfigurowania opcji serializacji MessagePack. Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.

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
> Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek. Na przykład wywoływanie `.WithSecurity(MessagePackSecurity.UntrustedData)` podczas zamiany `SerializerOptions` .

## <a name="configure-messagepack-on-the-client"></a>Konfigurowanie MessagePack na kliencie

> [!NOTE]
> KOD JSON jest domyślnie włączony dla obsługiwanych klientów. Klienci mogą obsługiwać tylko jeden protokół. Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.

### <a name="net-client"></a>Klient .NET

Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.

### <a name="javascript-client"></a>Klient JavaScript

Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm. Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

W przeglądarce należy `msgpack5` również odwołać się do biblioteki. Użyj `<script>` znacznika, aby utworzyć odwołanie. Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> W przypadku korzystania z `<script>` elementu kolejność jest ważna. Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack. *signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.

### <a name="java-client"></a>Klient Java

Aby włączyć MessagePack za pomocą języka Java, zainstaluj `com.microsoft.signalr.messagepack` pakiet. W przypadku korzystania z Gradle Dodaj następujący wiersz do `dependencies` sekcji pliku *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

Korzystając z Maven, Dodaj następujące wiersze wewnątrz `<dependencies>` elementu *pom.xml* pliku:

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

Wywołanie `withHubProtocol(new MessagePackHubProtocol())` metody `HubConnectionBuilder` .

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a>MessagePack osobliwości

Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack jest rozróżniana wielkość liter

W protokole MessagePack jest rozróżniana wielkość liter. Rozważmy na przykład następujące klasy języka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#. Na przykład:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#. Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack. Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji

Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` . W związku z tym podczas deserializacji daty, protokół MessagePack Hub zostanie przekonwertowany na format UTC, jeśli `DateTime.Kind` jest w `DateTimeKind.Local` przeciwnym razie, nie będzie on dotykał czasu i zostanie przekazany w postaci, w jakiej się znajduje. Jeśli pracujesz z `DateTime` wartościami, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem. Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript

Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack. Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości). Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości. W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane. Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości. Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"

Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji. W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity). Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji. Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Kontrole typu są bardziej rygorystyczne w MessagePack

Podczas deserializacji protokół JSON będzie wykonywał konwersje typów. Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana. Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

### <a name="chars-and-strings-in-java"></a>Znaki i ciągi w języku Java

W kliencie Java `char` obiekty są serializowane jako obiekty jednoznakowe `String` . Jest to w przeciwieństwie do klienta języka C# i języka JavaScript, który serializować je jako `short` obiekty. Sama Specyfikacja MessagePack nie definiuje zachowania dla `char` obiektów, dlatego należy do autora biblioteki, aby określić sposób serializacji. Różnica w zachowaniu między naszymi klientami polega na bibliotekach użytych dla naszych implementacji.

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)

## <a name="what-is-messagepack"></a>Co to jest MessagePack?

[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej. Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/). Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack. SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.

## <a name="configure-messagepack-on-the-server"></a>Konfigurowanie MessagePack na serwerze

Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji. W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.

> [!NOTE]
> KOD JSON jest domyślnie włączony. Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji. W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack. Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.

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
> Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek. Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` . Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie. `MessagePack` w wersji 2. x wprowadzono istotne zmiany i są one niezgodne z SignalR wersjami 3,1 i wcześniejszymi. Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi. Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurowanie MessagePack na kliencie

> [!NOTE]
> KOD JSON jest domyślnie włączony dla obsługiwanych klientów. Klienci mogą obsługiwać tylko jeden protokół. Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.

### <a name="net-client"></a>Klient .NET

Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.

### <a name="javascript-client"></a>Klient JavaScript

Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) pakiet npm. Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

W przeglądarce należy `msgpack5` również odwołać się do biblioteki. Użyj `<script>` znacznika, aby utworzyć odwołanie. Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> W przypadku korzystania z `<script>` elementu kolejność jest ważna. Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack. *signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.

## <a name="messagepack-quirks"></a>MessagePack osobliwości

Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack jest rozróżniana wielkość liter

W protokole MessagePack jest rozróżniana wielkość liter. Rozważmy na przykład następujące klasy języka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#. Na przykład:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#. Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack. Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji

Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` . W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC. Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem. Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript

Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack. Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości). Wartość `DateTime.MinValue` is `January 1, 0001` , która musi być zakodowana w `timestamp96` wartości. W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane. Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości. Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"

Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji. W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity). Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji. Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Kontrole typu są bardziej rygorystyczne w MessagePack

Podczas deserializacji protokół JSON będzie wykonywał konwersje typów. Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana. Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule założono, że czytelnik zna tematy omówione w temacie [wprowadzenie.](xref:tutorials/signalr)

## <a name="what-is-messagepack"></a>Co to jest MessagePack?

[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej. Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze komunikaty w porównaniu z formatem [JSON](https://www.json.org/). Komunikaty binarne nie są odczytywane podczas przeglądania śladów i dzienników sieci, chyba że bajty są przesyłane przez analizator MessagePack. SignalR Program ma wbudowaną obsługę formatu MessagePack oraz udostępnia interfejsy API dla klienta i serwera.

## <a name="configure-messagepack-on-the-server"></a>Konfigurowanie MessagePack na serwerze

Aby włączyć protokół MessagePack Hub na serwerze, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet w aplikacji. W `Startup.ConfigureServices` metodzie Dodaj `AddMessagePackProtocol` do wywołania, `AddSignalR` Aby włączyć obsługę MessagePack na serwerze.

> [!NOTE]
> KOD JSON jest domyślnie włączony. Dodanie MessagePack umożliwia obsługę zarówno dla klientów JSON, jak i MessagePack.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Aby dostosować sposób, w jaki program MessagePack sformatuje dane, `AddMessagePackProtocol` Pobiera delegata na potrzeby konfigurowania opcji. W tym delegatze `FormatterResolvers` Właściwość może służyć do konfigurowania opcji serializacji MessagePack. Aby uzyskać więcej informacji na temat sposobu działania elementów rozpoznawania, odwiedź bibliotekę MessagePack pod adresem [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atrybuty mogą być używane dla obiektów, które mają być serializowane, aby określić sposób ich obsługi.

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
> Zdecydowanie zalecamy zapoznanie się z artykułem [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowaniem zalecanych poprawek. Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData` . Ustawienie `MessagePackSecurity.Active` wymaga ręcznej instalacji [wersji 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalowanie `MessagePack` wersji 1.9. x uaktualnia SignalR użycie. Gdy `MessagePackSecurity.Active` nie jest ustawiona na `MessagePackSecurity.UntrustedData` , złośliwy klient może spowodować odmowę usługi. Ustaw `MessagePackSecurity.Active` w `Program.Main` , jak pokazano w poniższym kodzie:

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurowanie MessagePack na kliencie

> [!NOTE]
> KOD JSON jest domyślnie włączony dla obsługiwanych klientów. Klienci mogą obsługiwać tylko jeden protokół. Dodanie obsługi MessagePack spowoduje zastąpienie wszelkich wcześniej skonfigurowanych protokołów.

### <a name="net-client"></a>Klient .NET

Aby włączyć MessagePack w kliencie .NET, zainstaluj `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` pakiet i Wywołaj `AddMessagePackProtocol` polecenie on `HubConnectionBuilder` .

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` wywołanie przejmuje delegata do konfigurowania opcji, podobnie jak na serwerze.

### <a name="javascript-client"></a>Klient JavaScript

Obsługa MessagePack dla klienta JavaScript jest udostępniana przez [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) pakiet npm. Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Po zainstalowaniu pakietu npm można użyć modułu bezpośrednio za pośrednictwem modułu ładującego języka JavaScript lub zaimportowanego do przeglądarki, odwołując się do następującego pliku:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

W przeglądarce należy `msgpack5` również odwołać się do biblioteki. Użyj `<script>` znacznika, aby utworzyć odwołanie. Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> W przypadku korzystania z `<script>` elementu kolejność jest ważna. Jeśli *signalr-protocol-msgpack.js* jest przywoływana przed *msgpack5.js*, wystąpi błąd podczas próby nawiązania połączenia z MessagePack. *signalr.js* jest również wymagany przed *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do programu `HubConnectionBuilder` spowoduje skonfigurowanie klienta do korzystania z protokołu MessagePack podczas nawiązywania połączenia z serwerem.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> W tej chwili nie ma opcji konfiguracji dla protokołu MessagePack na kliencie JavaScript.

## <a name="messagepack-quirks"></a>MessagePack osobliwości

Należy pamiętać o kilku kwestiach dotyczących korzystania z protokołu centrum MessagePack.

### <a name="messagepack-is-case-sensitive"></a>MessagePack jest rozróżniana wielkość liter

W protokole MessagePack jest rozróżniana wielkość liter. Rozważmy na przykład następujące klasy języka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Podczas wysyłania z klienta JavaScript należy użyć `PascalCased` nazw właściwości, ponieważ wielkość liter musi być zgodna z klasą języka C#. Na przykład:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Używanie `camelCased` nazw nie jest prawidłowo powiązane z klasą języka C#. Można obejść ten element przy użyciu `Key` atrybutu, aby określić inną nazwę właściwości MessagePack. Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Wartość DateTime. Kind nie jest zachowywana podczas serializacji/deserializacji

Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime` . W związku z tym podczas deserializacji daty, protokół MessagePack Hub zakłada, że data przychodząca jest w formacie UTC. Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy przekonwertowanie na czas UTC przed ich wysłaniem. Konwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>Wartość DateTime. MinValue nie jest obsługiwana przez MessagePack w języku JavaScript

Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana przez SignalR klienta JavaScript nie obsługuje `timestamp96` typu w MessagePack. Ten typ jest używany do kodowania bardzo dużych wartości daty (bardzo wczesne w przeszłości lub bardzo daleko w przyszłości). Wartość jest, `DateTime.MinValue` `January 1, 0001` która musi być zakodowana w `timestamp96` wartości. W związku z tym wysyłanie `DateTime.MinValue` do klienta JavaScript nie jest obsługiwane. Gdy `DateTime.MinValue` klient JavaScript otrzymuje komunikat, zostanie zgłoszony następujący błąd:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Zwykle `DateTime.MinValue` jest używany do kodowania "braku" lub `null` wartości. Jeśli musisz zakodować tę wartość w MessagePack, użyj wartości null `DateTime` ( `DateTime?` ) lub Zakoduj oddzielną `bool` wartość wskazującą, czy data jest obecna.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"

Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer platformy .NET używa generowania kodu w celu optymalizacji serializacji. W związku z tym nie jest to obsługiwane domyślnie w środowiskach, w których jest używana Kompilacja "z wyprzedzeniem" (na przykład Xamarin iOS lub Unity). Można używać MessagePack w tych środowiskach przez "wstępne generowanie" kodu serializatora/deserializacji. Aby uzyskać więcej informacji, zobacz [dokumentację MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Po wstępnym wygenerowaniu serializatorów można zarejestrować je za pomocą delegata konfiguracji przekazaną do `AddMessagePackProtocol` :

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Kontrole typu są bardziej rygorystyczne w MessagePack

Podczas deserializacji protokół JSON będzie wykonywał konwersje typów. Na przykład, jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą ( `{ foo: 42 }` ), ale właściwość klasy .NET jest typu `string` , wartość zostanie konwertowana. Jednak MessagePack nie wykonuje tej konwersji i zgłosi wyjątek, który może być widoczny w dziennikach po stronie serwera (i w konsoli programu):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz sekcję problemu w usłudze GitHub [/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie](xref:tutorials/signalr)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScript](xref:signalr/javascript-client)

::: moniker-end
