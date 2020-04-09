---
title: Przechowywanie wersji usług gRPC
author: jamesnk
description: Dowiedz się, jak wersję usług gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664116"
---
# <a name="versioning-grpc-services"></a>Przechowywanie wersji usług gRPC

Przez [James Newton-King](https://twitter.com/jamesnk)

Nowe funkcje dodane do aplikacji mogą wymagać, aby usługi gRPC świadczone klientom były zmieniane, czasami w nieoczekiwany i przełomowy sposób. Po zmianie usług gRPC:

* Należy wziąć pod uwagę wpływ zmian na klientów.
* Należy wdrożyć strategię przechowywania wersji do obsługi zmian.

## <a name="backwards-compatibility"></a>Zgodność z przewładem

Protokół gRPC jest przeznaczony do obsługi usług, które zmieniają się w czasie. Ogólnie rzecz biorąc, dodatki do usług i metod gRPC nie są łamane. Nierozerwalne zmiany umożliwiają istniejącym klientom kontynuowanie pracy bez zmian. Zmiana lub usunięcie usług gRPC to przełomowe zmiany. Gdy usługi gRPC mają przełomowe zmiany, klienci korzystający z tej usługi muszą zostać zaktualizowani i ponownie wsiewając.

Wprowadzanie nierozerwowych zmian w usłudze ma wiele zalet:

* Istniejący klienci nadal działają.
* Pozwala uniknąć pracy związanej z powiadamianiem klientów o przełomowych zmianach i aktualizowaniem ich.
* Tylko jedna wersja usługi musi być udokumentowana i utrzymywana.

### <a name="non-breaking-changes"></a>Zmiany nierozłamane

Zmiany te nie są łamanie na poziomie protokołu gRPC i .NET binarny poziom.

* **Dodawanie nowej usługi**
* **Dodawanie nowej metody do usługi**
* **Dodawanie pola do komunikatu żądania** — pola dodane do komunikatu żądania są deserializowane z [wartością domyślną](https://developers.google.com/protocol-buffers/docs/proto3#default) na serwerze, gdy nie są ustawione. Aby usługa była zmianą nierozważną, musi zakończyć się pomyślnie, gdy nowe pole nie jest ustawiane przez starszych klientów.
* **Dodawanie pola do komunikatu odpowiedzi** — pola dodane do wiadomości odpowiedzi są deserializowane do kolekcji [nieznanych pól](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) wiadomości na kliencie.
* **Dodawanie wartości do wyliczenia** — wyliczenia są serializowane jako wartość liczbowa. Nowe wartości wyliczenia są deserializowane na kliencie do wartości wyliczenia bez nazwy wyliczenia. Aby być zmianą nierozłamającą, starsi klienci muszą działać poprawnie podczas odbierania nowej wartości wyliczenia.

### <a name="binary-breaking-changes"></a>Binary przełomowe zmiany

Następujące zmiany nie są przerywane na poziomie protokołu gRPC, ale klient musi zostać zaktualizowany, jeśli uaktualni do najnowszego kontraktu *.proto* lub zestawu .NET klienta. Zgodność binarna jest ważne, jeśli planujesz opublikować bibliotekę gRPC do NuGet.

* **Usuwanie pola** — wartości z usuniętego pola są deserializowane do [nieznanych pól](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)wiadomości . Nie jest to zmiana podziału protokołu gRPC, ale klient musi zostać zaktualizowany, jeśli uaktualni się do najnowszej umowy. Ważne jest, aby usunięty numer pola nie był przypadkowo ponownie odtwarzany w przyszłości. Aby upewnić się, że tak się nie stanie, określ usunięte numery pól i nazwy w wiadomości za pomocą [zastrzeżonego](https://developers.google.com/protocol-buffers/docs/proto3#reserved) słowa kluczowego Protobuf.
* **Zmiana nazwy wiadomości** — nazwy wiadomości nie są zazwyczaj wysyłane w sieci, więc nie jest to zmiana podziału protokołu gRPC. Klient będzie musiał zostać zaktualizowany, jeśli uaktualni do najnowszej umowy. Jedną z sytuacji, w której nazwy wiadomości **są** wysyłane w sieci, są [pola Dowolne,](https://developers.google.com/protocol-buffers/docs/proto3#any) gdy nazwa wiadomości jest używana do identyfikowania typu wiadomości.
* **Zmiana csharp_namespace** — zmiana `csharp_namespace` spowoduje zmianę obszaru nazw wygenerowanych typów .NET. Nie jest to zmiana podziału protokołu gRPC, ale klient musi zostać zaktualizowany, jeśli uaktualni się do najnowszej umowy.

### <a name="protocol-breaking-changes"></a>Zmiany w zerwaniu protokołu

Następujące elementy są protokołem i binarnymi zmianami podziału:

* **Zmiana nazwy pola** — w przypadku zawartości Protobuf nazwy pól są używane tylko w wygenerowanym kodzie. Numer pola służy do identyfikowania pól w sieci. Zmiana nazwy pola nie jest zmianą protokołu dla Protobuf. Jeśli jednak serwer używa zawartości JSON, zmiana nazwy pola jest zmianą podziału.
* **Zmiana typu danych pola** — zmiana typu danych pola na [niezgodny typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) spowoduje błędy podczas deserializacji wiadomości. Nawet jeśli nowy typ danych jest zgodny, jest prawdopodobne, że klient musi zostać zaktualizowany do obsługi nowego typu, jeśli uaktualnia do najnowszego kontraktu.
* **Zmiana numeru pola** — w przypadku ładunków Protobuf numer pola jest używany do identyfikowania pól w sieci.
* **Zmiana nazwy pakietu, usługi lub metody** — gRPC używa nazwy pakietu, nazwy usługi i nazwy metody do tworzenia adresu URL. Klient otrzymuje *status UNIMPLEMENTED* z serwera.
* **Usuwanie usługi lub metody** — klient pobiera *status UNIMPLEMENTED* z serwera podczas wywoływania usuniętej metody.

### <a name="behavior-breaking-changes"></a>Zmiany dotyczące zachowania

Podczas wprowadzania zmian nierozłamających należy również rozważyć, czy starsi klienci mogą kontynuować pracę z nowym zachowaniem usługi. Na przykład dodanie nowego pola do wiadomości żądania:

* Nie jest to zmiana podziału protokołu.
* Zwracanie stanu błędu na serwerze, jeśli nowe pole nie jest ustawione, powoduje, że jest to przełomowa zmiana dla starych klientów.

Zgodność zachowania zależy od kodu specyficznego dla aplikacji.

## <a name="version-number-services"></a>Usługi numerów wersji

Usługi powinny dążyć do zachowania wstecz zgodności ze starymi klientami. Po pewnym czasie zmiany w aplikacji mogą wymagać zmian w przerwaniu pracy. Rozbijanie starych klientów i zmuszanie ich do aktualizacji wraz z usługą nie jest dobrym doświadczeniem użytkownika. Sposobem zachowania zgodności z powrotem podczas wprowadzania zmian podziału jest opublikowanie wielu wersji usługi.

gRPC obsługuje opcjonalny specyfikator [pakietu,](https://developers.google.com/protocol-buffers/docs/proto3#packages) który działa podobnie jak obszar nazw .NET. W rzeczywistości `package` będzie używany jako obszar nazw .NET dla `option csharp_namespace` wygenerowanych typów .NET, jeśli nie jest ustawiony w pliku *.proto.* Pakiet może służyć do określenia numeru wersji usługi i jej komunikatów:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Nazwa pakietu jest łączona z nazwą usługi w celu zidentyfikowania adresu usługi. Adres usługi umożliwia hostowanie wielu wersji usługi obok siebie:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Implementacje usługi są rejestrowane w *Startup.cs:*

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Uwzględnienie numeru wersji w nazwie pakietu daje możliwość opublikowania wersji *2* usługi z przełomowymi zmianami, kontynuując obsługę starszych klientów, którzy wywołują wersję *v1.* Po zaktualizowaniu klientów do korzystania z usługi *w wersji 2* można usunąć starą wersję. Planując opublikowanie wielu wersji usługi:

* Unikaj łamania zmian, jeśli jest to uzasadnione.
* Nie aktualizuj numeru wersji, chyba że wprowadzasz przełomowe zmiany.
* Należy zaktualizować numer wersji po wprowadzeniu zmian w przerwaniu.

Publikowanie wielu wersji usługi powiela ją. Aby ograniczyć powielanie, należy rozważyć przeniesienie logiki biznesowej z implementacji usługi do scentralizowanej lokalizacji, która może być ponownie używana przez stare i nowe implementacje:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Usługi i wiadomości generowane przy różnych nazwach pakietów są **różnymi typami .NET**. Przenoszenie logiki biznesowej do scentralizowanej lokalizacji wymaga mapowania wiadomości do typowych typów.
