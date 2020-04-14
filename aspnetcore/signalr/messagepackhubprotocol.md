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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>Użyj protokołu MessagePack SignalR Hub w dla ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

W tym artykule założono, że czytelnik jest zaznajomiony z tematami omówionymi w [temacie Wprowadzenie](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co to jest MessagePack?

[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej. Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze wiadomości w porównaniu do [JSON](https://www.json.org/). Wiadomości binarne są nieczytelne, patrząc na ślady sieci i dzienniki, chyba że bajty są przekazywane przez parser MessagePack. SignalRma wbudowaną obsługę formatu MessagePack i zapewnia interfejsy API dla klienta i serwera do użycia.

## <a name="configure-messagepack-on-the-server"></a>Konfigurowanie pakietu MessagePack na serwerze

Aby włączyć messagepack hub protokołu na `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` serwerze, należy zainstalować pakiet w aplikacji. W `Startup.ConfigureServices` metodzie `AddMessagePackProtocol` dodaj `AddSignalR` do wywołania, aby włączyć obsługę MessagePack na serwerze.

> [!NOTE]
> JSON jest domyślnie włączona. Dodawanie MessagePack umożliwia obsługę zarówno JSON i MessagePack klientów.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Aby dostosować sposób messagepack będzie `AddMessagePackProtocol` formatować dane, trwa pełnomocnika do konfigurowania opcji. W tym delegata `SerializerOptions` właściwość może służyć do konfigurowania messagepack opcje serializacji. Aby uzyskać więcej informacji na temat działania programów rozpoznawania nazw, odwiedź bibliotekę MessagePack w [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atrybuty mogą być używane na obiektach, które chcesz serializować, aby zdefiniować sposób ich obsługi.

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
> Zdecydowanie zalecamy zapoznanie się z [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowanie zalecanych plastrów. Na przykład `.WithSecurity(MessagePackSecurity.UntrustedData)` wywołanie `SerializerOptions`podczas zastępowania pliku .

## <a name="configure-messagepack-on-the-client"></a>Konfigurowanie pakietu MessagePack na kliencie

> [!NOTE]
> JSON jest domyślnie włączona dla obsługiwanych klientów. Klienci mogą obsługiwać tylko jeden protokół. Dodanie obsługi messagepack zastąpi wszystkie wcześniej skonfigurowane protokoły.

### <a name="net-client"></a>Klient .NET

Aby włączyć messagepack w kliencie `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`zainstaluj pakiet i zadzwoń .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` wywołanie zajmuje pełnomocnika do konfigurowania opcji, podobnie jak serwer.

### <a name="javascript-client"></a>Klient JavaScript

Obsługa messagepack dla klienta JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) jest dostarczana przez pakiet npm. Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po zainstalowaniu pakietu npm moduł może być używany bezpośrednio za pośrednictwem modułu ładującego JavaScript lub importowany do przeglądarki, odwołując się do następującego pliku:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

W przeglądarce `msgpack5` należy również odwoływać się do biblioteki. Użyj `<script>` znacznika, aby utworzyć odwołanie. Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Podczas korzystania `<script>` z elementu, kolejność jest ważne. Jeśli *signalr-protocol-msgpack.js* odwołuje się przed *msgpack5.js*, błąd występuje podczas próby połączenia z MessagePack. *signalr.js* jest również wymagane przed *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` spowoduje skonfigurowanie klienta do używania protokołu MessagePack podczas łączenia się z serwerem.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> W tej chwili nie ma żadnych opcji konfiguracji protokołu MessagePack na kliencie JavaScript.

## <a name="messagepack-quirks"></a>MessagePack dziwactwa

Istnieje kilka problemów, o których należy pamiętać podczas korzystania z protokołu MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack jest rozróżniana wielkość liter

W protokole MessagePack rozróżniana jest wielkość liter. Rozważmy na przykład następującą klasę języka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Podczas wysyłania z klienta JavaScript, należy użyć `PascalCased` nazwy właściwości, ponieważ wielkość liter musi dokładnie odpowiadać klasy C#. Przykład:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Przy `camelCased` użyciu nazw nie będzie poprawnie powiązać z klasy C#. Można obejść ten problem `Key` za pomocą atrybutu, aby określić inną nazwę messagepack właściwości. Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind nie jest zachowywany podczas serializacji/deserializacji

Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime`. W rezultacie podczas deserializacji daty messagepack hub protokół przekonwertuje `DateTime.Kind` do `DateTimeKind.Local` formatu UTC, jeśli jest inaczej nie dotknie czasu i przekazać go w stanie, w jakim jest. Jeśli pracujesz z `DateTime` wartościami, zalecamy konwersję na UTC przed ich wysłaniem. Przekonwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue nie jest obsługiwany przez messagepack w języku JavaScript

Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana SignalR przez klienta JavaScript `timestamp96` nie obsługuje typu w pakiecie MessagePack. Ten typ jest używany do kodowania bardzo dużych wartości dat (bardzo wcześnie w przeszłości lub bardzo daleko w przyszłości). Wartość `DateTime.MinValue` jest `January 1, 0001`, które muszą być zakodowane w `timestamp96` wartości. Z tego powodu `DateTime.MinValue` wysyłanie do klienta JavaScript nie jest obsługiwane. Po `DateTime.MinValue` odebraniu przez klienta JavaScript zgłaszany jest następujący błąd:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Zazwyczaj `DateTime.MinValue` jest używany do kodowania "brakujące" lub `null` wartość. Jeśli chcesz zakodować tę wartość w messagepack,`DateTime?`należy użyć wartości `bool` nullable `DateTime` ( ) lub zakodować oddzielną wartość wskazującą, czy data jest obecna.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"

Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) używana przez klienta i serwer .NET używa generowania kodu do optymalizacji serializacji. W rezultacie nie jest domyślnie obsługiwana w środowiskach, które używają kompilacji "z wyprzedzeniem" (takich jak Xamarin iOS lub Unity). Jest możliwe użycie MessagePack w tych środowiskach przez "wstępne generowanie" kod serializator/deserializer. Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Po wstępnie wygenerowaniu serializatorów można je zarejestrować za `AddMessagePackProtocol`pomocą delegata konfiguracji przekazanego do:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Sprawdzanie typów jest bardziej rygorystyczne w pakiecie MessagePack

Protokół JSON Hub będzie przeprowadzał konwersje typów podczas deserializacji. Na przykład jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą (`{ foo: 42 }`), `string`ale właściwość w klasie .NET jest typu, wartość zostanie przekonwertowana. Jednak MessagePack nie wykonuje tej konwersji i zda wyjątek, który można zobaczyć w dziennikach po stronie serwera (i w konsoli):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Powiązane zasoby

* [Rozpocząć](xref:tutorials/signalr)
* [Klient platformy .NET](xref:signalr/dotnet-client)
* [Klient środowiska JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

W tym artykule założono, że czytelnik jest zaznajomiony z tematami omówionymi w [temacie Wprowadzenie](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co to jest MessagePack?

[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej. Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze wiadomości w porównaniu do [JSON](https://www.json.org/). Wiadomości binarne są nieczytelne, patrząc na ślady sieci i dzienniki, chyba że bajty są przekazywane przez parser MessagePack. SignalRma wbudowaną obsługę formatu MessagePack i zapewnia interfejsy API dla klienta i serwera do użycia.

## <a name="configure-messagepack-on-the-server"></a>Konfigurowanie pakietu MessagePack na serwerze

Aby włączyć messagepack hub protokołu na `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` serwerze, należy zainstalować pakiet w aplikacji. W `Startup.ConfigureServices` metodzie `AddMessagePackProtocol` dodaj `AddSignalR` do wywołania, aby włączyć obsługę MessagePack na serwerze.

> [!NOTE]
> JSON jest domyślnie włączona. Dodawanie MessagePack umożliwia obsługę zarówno JSON i MessagePack klientów.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Aby dostosować sposób messagepack będzie `AddMessagePackProtocol` formatować dane, trwa pełnomocnika do konfigurowania opcji. W tym delegata `FormatterResolvers` właściwość może służyć do konfigurowania messagepack opcje serializacji. Aby uzyskać więcej informacji na temat działania programów rozpoznawania nazw, odwiedź bibliotekę MessagePack w [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atrybuty mogą być używane na obiektach, które chcesz serializować, aby zdefiniować sposób ich obsługi.

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
> Zdecydowanie zalecamy zapoznanie się z [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowanie zalecanych plastrów. Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData`. Ustawienie `MessagePackSecurity.Active` wymaga ręcznego zainstalowania [wersji 1.9.x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalacja `MessagePack` 1.9.x uaktualnień używanej przez wersję. SignalR Gdy `MessagePackSecurity.Active` nie jest `MessagePackSecurity.UntrustedData`ustawiona na, złośliwy klient może spowodować odmowę usługi. Ustaw `MessagePackSecurity.Active` `Program.Main`w , jak pokazano w poniższym kodzie:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurowanie pakietu MessagePack na kliencie

> [!NOTE]
> JSON jest domyślnie włączona dla obsługiwanych klientów. Klienci mogą obsługiwać tylko jeden protokół. Dodanie obsługi messagepack zastąpi wszystkie wcześniej skonfigurowane protokoły.

### <a name="net-client"></a>Klient .NET

Aby włączyć messagepack w kliencie `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`zainstaluj pakiet i zadzwoń .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` wywołanie zajmuje pełnomocnika do konfigurowania opcji, podobnie jak serwer.

### <a name="javascript-client"></a>Klient JavaScript

Obsługa messagepack dla klienta JavaScript [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) jest dostarczana przez pakiet npm. Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Po zainstalowaniu pakietu npm moduł może być używany bezpośrednio za pośrednictwem modułu ładującego JavaScript lub importowany do przeglądarki, odwołując się do następującego pliku:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

W przeglądarce `msgpack5` należy również odwoływać się do biblioteki. Użyj `<script>` znacznika, aby utworzyć odwołanie. Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Podczas korzystania `<script>` z elementu, kolejność jest ważne. Jeśli *signalr-protocol-msgpack.js* odwołuje się przed *msgpack5.js*, błąd występuje podczas próby połączenia z MessagePack. *signalr.js* jest również wymagane przed *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` spowoduje skonfigurowanie klienta do używania protokołu MessagePack podczas łączenia się z serwerem.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> W tej chwili nie ma żadnych opcji konfiguracji protokołu MessagePack na kliencie JavaScript.

## <a name="messagepack-quirks"></a>MessagePack dziwactwa

Istnieje kilka problemów, o których należy pamiętać podczas korzystania z protokołu MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack jest rozróżniana wielkość liter

W protokole MessagePack rozróżniana jest wielkość liter. Rozważmy na przykład następującą klasę języka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Podczas wysyłania z klienta JavaScript, należy użyć `PascalCased` nazwy właściwości, ponieważ wielkość liter musi dokładnie odpowiadać klasy C#. Przykład:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Przy `camelCased` użyciu nazw nie będzie poprawnie powiązać z klasy C#. Można obejść ten problem `Key` za pomocą atrybutu, aby określić inną nazwę messagepack właściwości. Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind nie jest zachowywany podczas serializacji/deserializacji

Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime`. W rezultacie podczas deserializacji daty protokół MessagePack Hub przyjmuje założenie, że data przychodząca jest w formacie UTC. Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy konwersję na UTC przed wysłaniem ich. Przekonwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue nie jest obsługiwany przez messagepack w języku JavaScript

Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana SignalR przez klienta JavaScript `timestamp96` nie obsługuje typu w pakiecie MessagePack. Ten typ jest używany do kodowania bardzo dużych wartości dat (bardzo wcześnie w przeszłości lub bardzo daleko w przyszłości). Wartość `DateTime.MinValue` jest `January 1, 0001`, które muszą być zakodowane w `timestamp96` wartości. Z tego powodu `DateTime.MinValue` wysyłanie do klienta JavaScript nie jest obsługiwane. Po `DateTime.MinValue` odebraniu przez klienta JavaScript zgłaszany jest następujący błąd:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Zazwyczaj `DateTime.MinValue` jest używany do kodowania "brakujące" lub `null` wartość. Jeśli chcesz zakodować tę wartość w messagepack,`DateTime?`należy użyć wartości `bool` nullable `DateTime` ( ) lub zakodować oddzielną wartość wskazującą, czy data jest obecna.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"

Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer .NET używa generowania kodu do optymalizacji serializacji. W rezultacie nie jest domyślnie obsługiwana w środowiskach, które używają kompilacji "z wyprzedzeniem" (takich jak Xamarin iOS lub Unity). Jest możliwe użycie MessagePack w tych środowiskach przez "wstępne generowanie" kod serializator/deserializer. Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Po wstępnie wygenerowaniu serializatorów można je zarejestrować za `AddMessagePackProtocol`pomocą delegata konfiguracji przekazanego do:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Sprawdzanie typów jest bardziej rygorystyczne w pakiecie MessagePack

Protokół JSON Hub będzie przeprowadzał konwersje typów podczas deserializacji. Na przykład jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą (`{ foo: 42 }`), `string`ale właściwość w klasie .NET jest typu, wartość zostanie przekonwertowana. Jednak MessagePack nie wykonuje tej konwersji i zda wyjątek, który można zobaczyć w dziennikach po stronie serwera (i w konsoli):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Powiązane zasoby

* [Rozpocząć](xref:tutorials/signalr)
* [Klient platformy .NET](xref:signalr/dotnet-client)
* [Klient środowiska JavaScript](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule założono, że czytelnik jest zaznajomiony z tematami omówionymi w [temacie Wprowadzenie](xref:tutorials/signalr).

## <a name="what-is-messagepack"></a>Co to jest MessagePack?

[MessagePack](https://msgpack.org/index.html) to szybki i kompaktowy format serializacji binarnej. Jest to przydatne, gdy wydajność i przepustowość są problemem, ponieważ tworzy mniejsze wiadomości w porównaniu do [JSON](https://www.json.org/). Wiadomości binarne są nieczytelne, patrząc na ślady sieci i dzienniki, chyba że bajty są przekazywane przez parser MessagePack. SignalRma wbudowaną obsługę formatu MessagePack i zapewnia interfejsy API dla klienta i serwera do użycia.

## <a name="configure-messagepack-on-the-server"></a>Konfigurowanie pakietu MessagePack na serwerze

Aby włączyć messagepack hub protokołu na `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` serwerze, należy zainstalować pakiet w aplikacji. W `Startup.ConfigureServices` metodzie `AddMessagePackProtocol` dodaj `AddSignalR` do wywołania, aby włączyć obsługę MessagePack na serwerze.

> [!NOTE]
> JSON jest domyślnie włączona. Dodawanie MessagePack umożliwia obsługę zarówno JSON i MessagePack klientów.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Aby dostosować sposób messagepack będzie `AddMessagePackProtocol` formatować dane, trwa pełnomocnika do konfigurowania opcji. W tym delegata `FormatterResolvers` właściwość może służyć do konfigurowania messagepack opcje serializacji. Aby uzyskać więcej informacji na temat działania programów rozpoznawania nazw, odwiedź bibliotekę MessagePack w [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Atrybuty mogą być używane na obiektach, które chcesz serializować, aby zdefiniować sposób ich obsługi.

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
> Zdecydowanie zalecamy zapoznanie się z [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) i zastosowanie zalecanych plastrów. Na przykład ustawienie `MessagePackSecurity.Active` właściwości statycznej na `MessagePackSecurity.UntrustedData`. Ustawienie `MessagePackSecurity.Active` wymaga ręcznego zainstalowania [wersji 1.9.x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Instalacja `MessagePack` 1.9.x uaktualnień używanej przez wersję. SignalR Gdy `MessagePackSecurity.Active` nie jest `MessagePackSecurity.UntrustedData`ustawiona na, złośliwy klient może spowodować odmowę usługi. Ustaw `MessagePackSecurity.Active` `Program.Main`w , jak pokazano w poniższym kodzie:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurowanie pakietu MessagePack na kliencie

> [!NOTE]
> JSON jest domyślnie włączona dla obsługiwanych klientów. Klienci mogą obsługiwać tylko jeden protokół. Dodanie obsługi messagepack zastąpi wszystkie wcześniej skonfigurowane protokoły.

### <a name="net-client"></a>Klient .NET

Aby włączyć messagepack w kliencie `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` .NET, `AddMessagePackProtocol` `HubConnectionBuilder`zainstaluj pakiet i zadzwoń .

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> To `AddMessagePackProtocol` wywołanie zajmuje pełnomocnika do konfigurowania opcji, podobnie jak serwer.

### <a name="javascript-client"></a>Klient JavaScript

Obsługa messagepack dla klienta JavaScript [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) jest dostarczana przez pakiet npm. Zainstaluj pakiet, wykonując następujące polecenie w powłoce poleceń:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Po zainstalowaniu pakietu npm moduł może być używany bezpośrednio za pośrednictwem modułu ładującego JavaScript lub importowany do przeglądarki, odwołując się do następującego pliku:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

W przeglądarce `msgpack5` należy również odwoływać się do biblioteki. Użyj `<script>` znacznika, aby utworzyć odwołanie. Bibliotekę można znaleźć pod adresem *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Podczas korzystania `<script>` z elementu, kolejność jest ważne. Jeśli *signalr-protocol-msgpack.js* odwołuje się przed *msgpack5.js*, błąd występuje podczas próby połączenia z MessagePack. *signalr.js* jest również wymagane przed *signalr-protocol-msgpack.js*.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Dodanie `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` spowoduje skonfigurowanie klienta do używania protokołu MessagePack podczas łączenia się z serwerem.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> W tej chwili nie ma żadnych opcji konfiguracji protokołu MessagePack na kliencie JavaScript.

## <a name="messagepack-quirks"></a>MessagePack dziwactwa

Istnieje kilka problemów, o których należy pamiętać podczas korzystania z protokołu MessagePack Hub.

### <a name="messagepack-is-case-sensitive"></a>MessagePack jest rozróżniana wielkość liter

W protokole MessagePack rozróżniana jest wielkość liter. Rozważmy na przykład następującą klasę języka C#:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Podczas wysyłania z klienta JavaScript, należy użyć `PascalCased` nazwy właściwości, ponieważ wielkość liter musi dokładnie odpowiadać klasy C#. Przykład:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Przy `camelCased` użyciu nazw nie będzie poprawnie powiązać z klasy C#. Można obejść ten problem `Key` za pomocą atrybutu, aby określić inną nazwę messagepack właściwości. Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind nie jest zachowywany podczas serializacji/deserializacji

Protokół MessagePack nie zapewnia sposobu kodowania `Kind` wartości `DateTime`. W rezultacie podczas deserializacji daty protokół MessagePack Hub przyjmuje założenie, że data przychodząca jest w formacie UTC. Jeśli pracujesz z `DateTime` wartościami w czasie lokalnym, zalecamy konwersję na UTC przed wysłaniem ich. Przekonwertuj je z czasu UTC na czas lokalny po ich otrzymaniu.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue nie jest obsługiwany przez messagepack w języku JavaScript

Biblioteka [msgpack5](https://github.com/mcollina/msgpack5) używana SignalR przez klienta JavaScript `timestamp96` nie obsługuje typu w pakiecie MessagePack. Ten typ jest używany do kodowania bardzo dużych wartości dat (bardzo wcześnie w przeszłości lub bardzo daleko w przyszłości). Wartość `DateTime.MinValue` jest, `January 1, 0001` który musi być zakodowany w `timestamp96` wartości. Z tego powodu `DateTime.MinValue` wysyłanie do klienta JavaScript nie jest obsługiwane. Po `DateTime.MinValue` odebraniu przez klienta JavaScript zgłaszany jest następujący błąd:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Zazwyczaj `DateTime.MinValue` jest używany do kodowania "brakujące" lub `null` wartość. Jeśli chcesz zakodować tę wartość w messagepack,`DateTime?`należy użyć wartości `bool` nullable `DateTime` ( ) lub zakodować oddzielną wartość wskazującą, czy data jest obecna.

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Obsługa MessagePack w środowisku kompilacji "z wyprzedzeniem"

Biblioteka [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) używana przez klienta i serwer .NET używa generowania kodu do optymalizacji serializacji. W rezultacie nie jest domyślnie obsługiwana w środowiskach, które używają kompilacji "z wyprzedzeniem" (takich jak Xamarin iOS lub Unity). Jest możliwe użycie MessagePack w tych środowiskach przez "wstępne generowanie" kod serializator/deserializer. Aby uzyskać więcej informacji, zobacz [messagePack-CSharp dokumentacji](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Po wstępnie wygenerowaniu serializatorów można je zarejestrować za `AddMessagePackProtocol`pomocą delegata konfiguracji przekazanego do:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Sprawdzanie typów jest bardziej rygorystyczne w pakiecie MessagePack

Protokół JSON Hub będzie przeprowadzał konwersje typów podczas deserializacji. Na przykład jeśli obiekt przychodzący ma wartość właściwości, która jest liczbą (`{ foo: 42 }`), `string`ale właściwość w klasie .NET jest typu, wartość zostanie przekonwertowana. Jednak MessagePack nie wykonuje tej konwersji i zda wyjątek, który można zobaczyć w dziennikach po stronie serwera (i w konsoli):

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Aby uzyskać więcej informacji na temat tego ograniczenia, zobacz GitHub problem [aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Powiązane zasoby

* [Rozpocząć](xref:tutorials/signalr)
* [Klient platformy .NET](xref:signalr/dotnet-client)
* [Klient środowiska JavaScript](xref:signalr/javascript-client)

::: moniker-end
