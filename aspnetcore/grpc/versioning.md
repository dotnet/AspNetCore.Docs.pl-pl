---
title: Przechowywanie wersji usług gRPC
author: jamesnk
description: Dowiedz się, jak w wersji gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058704"
---
# <a name="versioning-grpc-services"></a>Przechowywanie wersji usług gRPC

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Nowe funkcje dodane do aplikacji mogą wymagać zmiany usług gRPC Services na potrzeby klientów, czasami w nieoczekiwany sposób. Gdy usługi gRPC Services zmienią się:

* Należy zwrócić uwagę na to, jak zmiany wpływają na klientów.
* Należy zaimplementować strategię obsługi wersji, aby obsługiwać zmiany.

## <a name="backwards-compatibility"></a>Zgodność z poprzednimi wersjami

Protokół gRPC jest przeznaczony do obsługi usług, które zmieniają się w miarę upływu czasu. Ogólnie rzecz biorąc, dodatki do usług gRPC i metod są nieistotne. Niekrytyczne zmiany umożliwiają istniejącym klientom kontynuowanie pracy bez zmian. Zmiana lub usunięcie usług gRPC powoduje przerwanie zmian. Gdy usługi gRPC mają istotne zmiany, klienci korzystający z tej usługi muszą zostać zaktualizowani i wdrożoni ponownie.

Wprowadzanie nieistotnych zmian do usługi ma wiele korzyści:

* Istniejący klienci będą nadal działać.
* Zapobiega pracy związanym z powiadamianiem klientów o istotnych zmianach i aktualizowanie ich.
* Tylko jedna wersja usługi musi być udokumentowana i utrzymywana.

### <a name="non-breaking-changes"></a>Niekrytyczne zmiany

Te zmiany nie są przerywane na poziomie protokołu gRPC i pliku binarnego platformy .NET.

* **Dodawanie nowej usługi**
* **Dodawanie nowej metody do usługi**
* **Dodawanie pola do komunikatu żądania** — pola dodawane do komunikatu żądania są deserializowane przy użyciu [wartości domyślnej](https://developers.google.com/protocol-buffers/docs/proto3#default) na serwerze, gdy nie jest ustawiony. Aby nastąpić nieprzerwaną zmianę, usługa musi się powieść, jeśli nowe pole nie zostanie ustawione przez starszych klientów.
* **Dodawanie pola do komunikatu odpowiedzi** — pola dodane do komunikatu odpowiedzi są deserializowane do kolekcji [nieznanych pól](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) na komputerze klienckim.
* **Dodawanie wartości do** wyliczenia-wyliczenia jest serializowane jako wartość liczbowa. Nowe wartości wyliczeniowe są deserializowane na kliencie do wartości wyliczenia bez nazwy wyliczenia. Aby mieć nieistotną zmianę, starsze komputery klienckie muszą działać poprawnie, gdy otrzymuje nową wartość enum.

### <a name="binary-breaking-changes"></a>Zmiany kodu binarnego

Następujące zmiany nie są rozrywane na poziomie protokołu gRPC, ale klient należy zaktualizować w przypadku uaktualnienia do najnowszej wersji kontraktu *. proto* lub zestawu .NET klienta. Zgodność binarna jest ważna, jeśli planujesz opublikowanie biblioteki gRPC w programie NuGet.

* **Usuwanie wartości pól** z usuniętego pola jest deserializowane do [nieznanych pól](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)komunikatu. Nie jest to gRPCa zmiana protokołu, ale klient należy zaktualizować w przypadku uaktualnienia do najnowszego kontraktu. Należy pamiętać, że usunięty numer pola nie jest przypadkowo ponownie używany w przyszłości. Aby się upewnić, że to się nie dzieje, określ usunięte numery pól i nazwy w komunikacie przy użyciu [zastrzeżonego](https://developers.google.com/protocol-buffers/docs/proto3#reserved) słowa kluczowego protobuf.
* Zmiana **nazwy** komunikatów nie jest zazwyczaj wysyłana w sieci, więc nie jest to gRPCa. Po uaktualnieniu do najnowszej kontraktu klient musi zostać zaktualizowany. Jedną z sytuacji, w której nazwy komunikatów **są** wysyłane w sieci, są z [dowolnymi](https://developers.google.com/protocol-buffers/docs/proto3#any) polami, gdy nazwa komunikatu jest używana do identyfikowania typu wiadomości.
* **Zmiana csharp_namespace** -zmiana zmieni `csharp_namespace` przestrzeń nazw wygenerowanych typów .NET. Nie jest to gRPCa zmiana protokołu, ale klient należy zaktualizować w przypadku uaktualnienia do najnowszego kontraktu.

### <a name="protocol-breaking-changes"></a>Zmiany podczas łamania protokołu

Poniżej przedstawiono następujące elementy:

* **Zmiana nazwy pola** — z zawartością protobuf, nazwy pól są używane tylko w wygenerowanym kodzie. Numer pola służy do identyfikowania pól w sieci. Zmiana nazwy pola nie jest zmianą protokołu dla protobuf. Jeśli jednak serwer używa zawartości JSON, zmiana nazwy pola jest istotną zmianą.
* **Zmiana typu danych pola** — zmiana typu danych pola na [niezgodny typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) spowoduje błędy podczas deserializacji komunikatu. Nawet jeśli nowy typ danych jest zgodny, prawdopodobnie klient musi zostać zaktualizowany do obsługi nowego typu w przypadku uaktualnienia do najnowszego kontraktu.
* **Zmiana numeru pola** — przy użyciu ładunków protobuf numer pola służy do identyfikowania pól w sieci.
* **Zmiana nazwy pakietu, usługi lub metody** — gRPC używa nazwy pakietu, nazwy usługi i nazwy metody do KOMPILOWANIA adresu URL. Klient Pobiera stan *NIEZAimplementowany* z serwera.
* **Usuwanie usługi lub metody** — klient Pobiera stan *niezaimplementowany* z serwera podczas wywoływania usuniętej metody.

### <a name="behavior-breaking-changes"></a>Zmiany powodujące przerwanie działania

Podczas wprowadzania nieistotnych zmian należy również rozważyć, czy starsze komputery klienckie mogą kontynuować pracę z nowym zachowaniem usługi. Na przykład Dodaj nowe pole do komunikatu żądania:

* Nie jest to zmiana podziału protokołu.
* Zwrócenie stanu błędu na serwerze, jeśli nowe pole nie zostało ustawione sprawia, że jest to istotna zmiana dla starych klientów.

Zgodność z zachowaniem jest określana na podstawie kodu specyficznego dla aplikacji.

## <a name="version-number-services"></a>Usługi numeru wersji

Usługi powinny dążyć do zachowania zgodności z poprzednimi klientami. Ostatecznie zmiany w aplikacji mogą wymagać przerwania zmian. Przerywanie starych klientów i wymuszanie ich aktualizacji wraz z usługą nie jest dobrym doświadczeniem użytkownika. Sposób zapewnienia zgodności z poprzednimi wersjami podczas wprowadzania istotnych zmian polega na opublikowaniu wielu wersji usługi.

gRPC obsługuje opcjonalny specyfikator [pakietu](https://developers.google.com/protocol-buffers/docs/proto3#packages) , który działa podobnie jak przestrzeń nazw platformy .NET. W rzeczywistości `package` zostanie użyta jako przestrzeń nazw .NET dla wygenerowanych typów .NET, jeśli `option csharp_namespace` nie jest ustawiona w pliku *. proto* . Pakiet może służyć do określania numeru wersji usługi i jej komunikatów:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Nazwa pakietu jest połączona z nazwą usługi, aby zidentyfikować adres usługi. Adres usługi umożliwia obsługiwanie wielu wersji usługi po stronie:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Implementacje usługi z wersjami są zarejestrowane w *Startup.cs* :

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Dołączenie numeru wersji w nazwie pakietu daje możliwość opublikowania w wersji *2* usługi z uwzględnieniem istotnych zmian, przy jednoczesnym dalszym obsłudze starszych klientów, którzy wywołują wersję *V1* . Gdy klienci zostali zaktualizowani do korzystania z usługi w *wersji 2* , możesz wybrać opcję usunięcia starej wersji. Planując Publikowanie wielu wersji usługi:

* Unikaj znaczących zmian, jeśli jest to uzasadnione.
* Nie Aktualizuj numeru wersji, chyba że wprowadzasz istotne zmiany.
* Należy zaktualizować numer wersji po wprowadzeniu zmian.

Publikowanie wielu wersji usługi duplikuje ją. Aby zmniejszyć duplikowanie, rozważ przeniesienie logiki biznesowej z implementacji usługi do scentralizowanej lokalizacji, która może być ponownie używana przez stare i nowe implementacje:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Usługi i komunikaty generowane z różnymi nazwami pakietów są **różnymi typami .NET** . Przeniesienie logiki biznesowej do scentralizowanej lokalizacji wymaga mapowania komunikatów do typów wspólnych.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/protobuf>
