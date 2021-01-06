---
title: GRPC usługi i klienci z programem .NET w kodzie
author: jamesnk
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi pisania kodu-First gRPC przy użyciu platformy .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880636"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>GRPC usługi i klienci z programem .NET w kodzie

Przez [Kuba Kowalski-król](https://twitter.com/jamesnk) i [Marcy Gravell](https://twitter.com/marcgravell)

GRPC do kodu używa typów .NET do definiowania kontraktów usług i komunikatów.

Kod — najpierw jest dobrym wyborem, gdy cały system używa platformy .NET:

* Typy kontraktów usług i danych platformy .NET mogą być współużytkowane przez serwer i klientów platformy .NET.
* Pozwala uniknąć konieczności definiowania kontraktów w `.proto` plikach i generacjach kodu.

Kod — pierwsze nie jest zalecane w systemach Polyglot z wieloma językami. Typów kontraktów usług i danych platformy .NET nie można używać z platformami non-.NET. Aby wywołać usługę gRPC zapisaną przy użyciu kodu — po pierwsze, inne platformy muszą utworzyć `.proto` kontrakt zgodny z usługą.

## <a name="protobuf-netgrpc"></a>protobuf-net. Grpc

> [!IMPORTANT]
> Aby uzyskać pomoc dotyczącą usługi protobuf-net. GRPC, odwiedź stronę [protobuf-net. GRPC witrynę sieci Web](https://protobuf-net.github.io/protobuf-net.Grpc/) lub Utwórz problem w usłudze [protobuf-net. GRPC repozytorium GitHub](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net. GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/) to projekt społecznościowy i nie jest obsługiwany przez firmę Microsoft. Dodaje obsługę kodu w pierwszej kolejności do `Grpc.AspNetCore` i `Grpc.Net.Client` . Używa typów platformy .NET z adnotacjami z atrybutami do definiowania usług gRPC i komunikatów aplikacji.

Pierwszym krokiem tworzenia usługi gRPC w kodzie jest Definiowanie kontraktu kodu:

* Utwórz nowy projekt, który będzie współużytkowany przez serwer i klienta.
* Dodaj [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Odwołanie do pakietu GRPC.
* Tworzenie typów kontraktu usług i danych.

[!code-csharp[](code-first/Contracts.cs)]

Powyższy kod ma następujące działanie:

* Definiuje `HelloRequest` i `HelloReply` komunikaty.
* Definiuje `IGreeterService` interfejs kontraktu z jednoargumentową `SayHelloAsync` metodą gRPC.

Kontrakt usługi jest zaimplementowany na serwerze i wywoływany z klienta programu. Metody zdefiniowane w interfejsach usług muszą być zgodne z określonymi sygnaturami w zależności od tego, czy są one jednoargumentowe, przesyłane strumieniowo serwera, przesyłania strumieniowego klienta lub dwukierunkowego przesyłania

Aby uzyskać więcej informacji na temat definiowania kontraktów usług, zobacz [protobuf-net. Dokumentacja wprowadzająca GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Tworzenie usługi gRPC w pierwszej kolejności

Aby dodać usługę gRPC (kod pierwszej) do aplikacji ASP.NET Core:

* Dodaj [protobuf-net. Odwołanie do pakietu GRPC. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .
* Dodaj odwołanie do projektu współużytkowanego kodu-kontraktu.

Utwórz nowy `GreeterService.cs` plik i zaimplementuj `IGreeterService` Interfejs usługi:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Aktualizowanie `Startup.cs` pliku:

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

Powyższy kod ma następujące działanie:

* `AddCodeFirstGrpc` rejestruje usługi, które włączają kod w pierwszej kolejności.
* `MapGrpcService<GreeterService>` dodaje punkt końcowy usługi Code First.

usługi gRPC zaimplementowane przy użyciu kodu i `.proto` plików mogą współistnieć w tej samej aplikacji. Wszystkie usługi gRPC korzystają z [konfiguracji usługi gRPC](xref:grpc/configuration#configure-services-options).

## <a name="create-a-code-first-grpc-client"></a>Tworzenie klienta gRPC z kodem pierwszym

Klient programu gRPC pierwszy kod używa kontraktu usługi do wywoływania usług gRPC Services. Aby wywołać usługę gRPC przy użyciu klienta kodu-pierwszego:

* Dodaj [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Odwołanie do pakietu GRPC.
* Dodaj odwołanie do projektu współużytkowanego kodu-kontraktu.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

Powyższy kod ma następujące działanie:

* Tworzy kanał gRPC.
* Tworzy klient z kodem po raz pierwszy z kanału z `CreateGrpcService<IGreeterService>` metodą rozszerzenia.
* Wywołuje usługę gRPC z `SayHelloAsync` .

Klient gRPC pierwszy kod jest tworzony na podstawie kanału. Podobnie jak w przypadku zwykłego klienta, klient pierwszy kod używa [konfiguracji kanału](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [protobuf-net. Witryna sieci Web GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net. Repozytorium GitHub GRPC](https://github.com/protobuf-net/protobuf-net.Grpc)
