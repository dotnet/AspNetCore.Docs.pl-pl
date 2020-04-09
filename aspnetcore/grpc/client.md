---
title: Dzwoń z usług gRPC za pomocą klienta .NET
author: jamesnk
description: Dowiedz się, jak dzwonić do usług gRPC z klientem gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667175"
---
# <a name="call-grpc-services-with-the-net-client"></a>Dzwoń z usług gRPC za pomocą klienta .NET

Biblioteka klienta gRPC .NET jest dostępna w pakiecie [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet. W tym dokumencie wyjaśniono, jak:

* Skonfiguruj klienta gRPC, aby dzwonił do usług gRPC.
* Wykonuj wywołania gRPC do arysowych, strumieniowych serwerów, przesyłania strumieniowego klienta i dwukierunkowych metod przesyłania strumieniowego.

## <a name="configure-grpc-client"></a>Konfigurowanie klienta gRPC

Klienci gRPC to konkretne typy klientów [generowane z * \*plików .proto.* ](xref:grpc/basics#generated-c-assets) Konkretny klient gRPC ma metody, które przekładają się na usługę gRPC w pliku * \*.proto.*

Klient gRPC jest tworzony z kanału. Zacznij od `GrpcChannel.ForAddress` utworzenia kanału, a następnie użyj kanału, aby utworzyć klienta gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanał reprezentuje długotrwałe połączenie z usługą gRPC. Po utworzeniu kanału jest on konfigurowany z opcjami związanymi z wywoływaniem usługi. Na przykład `HttpClient` używany do nawiązywania połączeń, maksymalny rozmiar wiadomości wysyłania i odbierania oraz rejestrowanie można określić `GrpcChannelOptions` i używać z programem `GrpcChannel.ForAddress`. Aby uzyskać pełną listę opcji, zobacz [opcje konfiguracji klienta](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Wydajność i wykorzystanie kanałów i klientów:

* Tworzenie kanału może być kosztowną operacją. Ponowne korzystać z kanału dla połączeń gRPC zapewnia korzyści wydajności.
* Klienci gRPC są tworzone za pomocą kanałów. Klienci gRPC są lekkimi obiektami i nie muszą być buforowane ani ponownie zażywane.
* Wielu klientów gRPC można utworzyć z kanału, w tym różnych typów klientów.
* Kanał i klientów utworzonych z kanału mogą być bezpiecznie używane przez wiele wątków.
* Klienci utworzone z kanału można wykonywać wiele jednoczesnych połączeń.

`GrpcChannel.ForAddress`nie jest jedyną opcją tworzenia klienta gRPC. Jeśli dzwonisz do usług gRPC z aplikacji ASP.NET Core, rozważ [integrację fabryki klienta gRPC.](xref:grpc/clientfactory) Integracja gRPC z `HttpClientFactory` oferuje scentralizowaną alternatywę dla tworzenia klientów gRPC.

> [!NOTE]
> Dodatkowa konfiguracja jest wymagana do [wywoływania niezabezpieczonych usług gRPC z klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

> [!NOTE]
> Wywołanie gRPC przez HTTP/2 z `Grpc.Net.Client` obecnie nie jest obsługiwane na xamarin. Pracujemy nad ulepszeniem obsługi protokołu HTTP/2 w przyszłej wersji platformy Xamarin. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) i [gRPC-Web](xref:grpc/browser) są realnymi alternatywami, które działają dzisiaj.

## <a name="make-grpc-calls"></a>Wykonywanie połączeń gRPC

Wywołanie gRPC jest inicjowane przez wywołanie metody na kliencie. Klient gRPC będzie obsługiwać serializacji wiadomości i adresowania wywołania gRPC do poprawnej usługi.

gRPC ma różne rodzaje metod. Sposób korzystania z klienta do wywołania gRPC zależy od typu metody, którą wywołujesz. Typy metod gRPC to:

* Jednoargumentowy
* Przesyłanie strumieniowe serwera
* Przesyłanie strumieniowe klienta
* Dwukierunkowe przesyłanie strumieniowe

### <a name="unary-call"></a>Połączenie bezemisowe

Połączenie bezemisowe rozpoczyna się od wysłania przez klienta wiadomości żądania. Komunikat odpowiedzi jest zwracany po zakończeniu usługi.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Każda metoda usługi dwuarchifowej w pliku * \*.proto* spowoduje dwie metody .NET na typie klienta gRPC dla wywoływania metody: metoda asynchroniza i metoda blokowania. Na przykład, `GreeterClient` na istnieją dwa `SayHello`sposoby wywoływania:

* `GreeterClient.SayHelloAsync`- `Greeter.SayHello` wywołuje usługę asynchronicznie. Można oczekiwać.
* `GreeterClient.SayHello`- `Greeter.SayHello` dzwoni serwis i blokuje się do czasu zakończenia. Nie używaj w kodzie asynchroniza.

### <a name="server-streaming-call"></a>Wywołanie przesyłania strumieniowego serwera

Wywołanie przesyłania strumieniowego serwera rozpoczyna się od wysłania przez klienta komunikatu żądania. `ResponseStream.MoveNext()`odczytuje wiadomości przesyłane strumieniowo z usługi. Połączenie przesyłania strumieniowego `ResponseStream.MoveNext()` serwera `false`zostanie zakończone po zwróceniu .

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

Jeśli używasz języka C# 8 `await foreach` lub nowszego, składni może służyć do odczytywania wiadomości. Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozszerzenia odczytuje wszystkie komunikaty ze strumienia odpowiedzi:

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a>Połączenie strumieniowe klienta

Połączenie przesyłania strumieniowego klienta rozpoczyna się *bez* wysyłania wiadomości przez klienta. Klient może wybrać opcję `RequestStream.WriteAsync`wysyłania wiadomości za pomocą pliku . Po zakończeniu wysyłania `RequestStream.CompleteAsync` wiadomości przez klienta należy wywołać, aby powiadomić usługę. Wywołanie jest zakończone, gdy usługa zwraca komunikat odpowiedzi.

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a>Dwukierunkowe połączenie strumieniowe

Dwukierunkowe połączenie przesyłania strumieniowego rozpoczyna się *bez* wysyłania wiadomości przez klienta. Klient może wybrać opcję `RequestStream.WriteAsync`wysyłania wiadomości za pomocą pliku . Wiadomości przesyłane strumieniowo z `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()`usługi są dostępne za pomocą pliku . Dwukierunkowe wywołanie przesyłania strumieniowego `ResponseStream` jest zakończone, gdy nie ma więcej wiadomości.

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

Podczas dwukierunkowego połączenia przesyłania strumieniowego klient i usługa mogą wysyłać do siebie wiadomości w dowolnym momencie. Najlepsza logika klienta do interakcji z wywołaniem dwukierunkowym różni się w zależności od logiki usługi.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
