---
title: Porównanie usług gRPC za pomocą interfejsów API protokołu HTTP
author: jamesnk
description: Dowiedz się, jak gRPC porównuje się z interfejsami API HTTP i jakie są zalecane scenariusze.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405870"
---
# <a name="compare-grpc-services-with-http-apis"></a>Porównanie usług gRPC za pomocą interfejsów API protokołu HTTP

Przez [James Newton-King](https://twitter.com/jamesnk)

W tym artykule wyjaśniono, jak [usługi GRPC](https://grpc.io/docs/guides/) porównują się do interfejsów API HTTP (w tym ASP.NET core [internetowych interfejsów API](xref:web-api/index)). Technologia używana do dostarczania interfejsu API dla aplikacji jest ważnym wyborem, a gRPC oferuje unikalne korzyści w porównaniu do interfejsów API HTTP. W tym artykule omówiono mocne i słabe strony gRPC i zaleca się scenariusze wykorzystania gRPC w stosunku do innych technologii.

## <a name="high-level-comparison"></a>Porównanie wysokiego poziomu

W poniższej tabeli przedstawiono porównanie funkcji wysokiego poziomu między interfejsami API gRPC i HTTP z usługą JSON.

| Funkcja          | gRPC                                               | Interfejsy API HTTP z json           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Kontrakt         | Wymagane (*.proto*)                                | Opcjonalnie (OpenAPI)            |
| Protocol (Protokół)         | HTTP/2                                             | HTTP                          |
| Ładunku          | [Protobuf (mały, binarny)](#performance)           | JSON (duży, czytelny dla człowieka)  |
| Nakazowa | [Ścisła specyfikacja](#strict-specification)      | Luźne. Każdy protokół HTTP jest prawidłowy.     |
| Przesyłanie strumieniowe        | [Klient, serwer, dwukierunkowy](#streaming)       | Klient, serwer                |
| Obsługa przeglądarki  | [Nie (wymaga grpc-web)](#limited-browser-support) | Tak                           |
| Zabezpieczenia         | Transport (TLS)                                    | Transport (TLS)               |
| Generowanie kodu klienta | [Tak](#code-generation)                      | OpenAPI + narzędzia innych firm |

## <a name="grpc-strengths"></a>Mocne strony gRPC

### <a name="performance"></a>Wydajność

Komunikaty gRPC są serializowane za pomocą [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), wydajnego formatu wiadomości binarnych. Protobuf serializuje bardzo szybko na serwerze i kliencie. Protobuf serializacji wyniki w małych ładunków wiadomości, ważne w scenariuszach o ograniczonej przepustowości, takich jak aplikacje mobilne.

GRPC jest przeznaczony dla HTTP/2, głównej wersji PROTOKOŁU HTTP, która zapewnia znaczące korzyści z wydajności w stosunku do protokołu HTTP 1.x:

* Kadrowanie binarne i kompresja. Protokół HTTP/2 jest kompaktowy i wydajny zarówno w wysyłaniu, jak i odbieraniu.
* Multipleksowanie wielu wywołań HTTP/2 za pośrednictwem jednego połączenia TCP. Multipleksowanie eliminuje [blokowanie head-of-line](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Generowanie kodu

Wszystkie struktury gRPC zapewniają najwyższej klasy obsługę generowania kodu. Podstawowym plikiem do rozwoju gRPC jest [plik .proto](https://developers.google.com/protocol-buffers/docs/proto3), który definiuje umowę usług i komunikatów gRPC. Z tego pliku gRPC struktury będzie kod generowania klasy podstawowej usługi, wiadomości i kompletnego klienta.

Udostępniając plik *.proto* między serwerem a klientem, wiadomości i kod klienta mogą być generowane od końca do końca. Generowanie kodu klienta eliminuje powielanie wiadomości na kliencie i serwerze i tworzy silnie typizowanego klienta dla Ciebie. Nie konieczności pisania klienta oszczędza znaczny czas rozwoju w aplikacjach z wielu usług.

### <a name="strict-specification"></a>Ścisła specyfikacja

Formalna specyfikacja interfejsu API HTTP z JSON nie istnieje. Deweloperzy dyskutują o najlepszym formacie adresów URL, zleceń HTTP i kodów odpowiedzi.

[Specyfikacja gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) jest nakazowa dotycząca formatu, który musi przestrzegać usługa gRPC. gRPC eliminuje debatę i oszczędza czas programistów, ponieważ gRPC jest spójny na różnych platformach i wdrożeniach.

### <a name="streaming"></a>Przesyłanie strumieniowe

Http/2 stanowi podstawę dla długowiecznych strumieni komunikacji w czasie rzeczywistym. gRPC zapewnia pierwszorzędną obsługę przesyłania strumieniowego za pośrednictwem protokołu HTTP/2.

Usługa gRPC obsługuje wszystkie kombinacje przesyłania strumieniowego:

* Unary (bez przesyłania strumieniowego)
* Przesyłanie strumieniowe serwera do klienta
* Przesyłanie strumieniowe między klientami
* Dwukierunkowe przesyłanie strumieniowe

### <a name="deadlinetimeouts-and-cancellation"></a>Termin/limit czasu i anulowanie

gRPC pozwala klientom określić, jak długo są gotowi czekać na zakończenie RPC. [Termin jest](https://grpc.io/blog/deadlines) wysyłany do serwera, a serwer może zdecydować, jakie działania należy podjąć, jeśli przekroczy ono ostateczny termin. Na przykład serwer może anulować w toku gRPC/HTTP/database żądań na limit czasu.

Propagowanie terminu i anulowania za pośrednictwem podrzędnych wywołań gRPC pomaga wymusić limity użycia zasobów.

## <a name="grpc-recommended-scenarios"></a>gRPC zalecane scenariusze

gRPC jest dobrze dopasowany do następujących scenariuszy:

* **Mikrousług gRPC** &ndash; został zaprojektowany do komunikacji o niskim opóźnieniu i wysokiej przepływności. GRPC doskonale nadaje się do lekkich mikrousług, w których wydajność ma kluczowe znaczenie.
* **GRPC komunikacji** &ndash; w czasie rzeczywistym w czasie rzeczywistym ma doskonałe wsparcie dla dwukierunkowego przesyłania strumieniowego. Usługi gRPC mogą wypychać wiadomości w czasie rzeczywistym bez sondowania.
* **Oprzyrządowanie gRPC w środowiskach** &ndash; poliglota obsługuje wszystkie popularne języki programowania, dzięki czemu gRPC jest dobrym wyborem dla środowisk wielojęzycznych.
* Komunikaty gRPC **o ograniczonych środowiskach sieciowych** &ndash; są serializowane za pomocą Protobuf, lekkiego formatu wiadomości. Komunikat gRPC jest zawsze mniejszy niż równoważny komunikat JSON.

## <a name="grpc-weaknesses"></a>gRPC słabości

### <a name="limited-browser-support"></a>Ograniczona obsługa przeglądarki

Dziś nie można bezpośrednio wywołać usługi gRPC z przeglądarki. GRPC intensywnie korzysta z funkcji HTTP/2 i żadna przeglądarka nie zapewnia poziomu kontroli wymaganej nad żądaniami internetowymi do obsługi klienta gRPC. Na przykład przeglądarki nie zezwalają wywołującemu na wymaganie użycia protokołu HTTP/2 ani na zapewnianie dostępu do podstawowych ramek HTTP/2.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) to dodatkowa technologia zespołu gRPC, która zapewnia ograniczoną obsługę gRPC w przeglądarce. gRPC-Web składa się z dwóch części: klienta JavaScript, który obsługuje wszystkie nowoczesne przeglądarki, i serwera proxy gRPC-Web na serwerze. Klient gRPC-Web wywołuje serwer proxy, a serwer proxy przekaże żądania gRPC do serwera gRPC.

Nie wszystkie funkcje gRPC są obsługiwane przez gRPC-Web. Przesyłanie strumieniowe klienta i dwukierunkowe nie jest obsługiwane i istnieje ograniczona obsługa przesyłania strumieniowego serwera.

> [!TIP]
> .NET Core ma eksperymentalną obsługę gRPC-Web. Odwiedź, <xref:grpc/browser> aby uzyskać więcej informacji.

### <a name="not-human-readable"></a>Nie czytelny dla człowieka

Żądania interfejsu API HTTP są wysyłane jako tekst i mogą być odczytywane i tworzone przez ludzi.

Komunikaty gRPC są domyślnie kodowane z Protobuf. Podczas gdy Protobuf jest skuteczny w wysyłaniu i odbieranie, jego format binarny nie jest czytelny dla człowieka. Protobuf wymaga, aby opis interfejsu wiadomości został określony w pliku *.proto,* aby prawidłowo deserialize. Dodatkowe oprzyrządowanie jest wymagane do analizy ładunków Protobuf na przewodzie i do komponowania żądań ręcznie.

Funkcje, takie jak [odbicie serwera](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) i [narzędzie wiersza polecenia gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) istnieją, aby pomóc w binarnych komunikatach Protobuf. Ponadto komunikaty Protobuf obsługują [konwersję do i z JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). Wbudowana konwersja JSON zapewnia skuteczny sposób konwertowania wiadomości Protobuf do i z postaci czytelnej dla człowieka podczas debugowania.

## <a name="alternative-framework-scenarios"></a>Alternatywne scenariusze ramowe

Inne struktury są zalecane w gRPC w następujących scenariuszach:

* **Interfejsy** &ndash; API dostępne dla przeglądarki gRPC nie są w pełni obsługiwane w przeglądarce. gRPC-Web może oferować obsługę przeglądarki, ale ma ograniczenia i wprowadza serwer proxy.
* **Transmisja komunikacji w** &ndash; czasie rzeczywistym gRPC obsługuje komunikację w czasie rzeczywistym za pośrednictwem przesyłania strumieniowego, ale koncepcja emitowania wiadomości do zarejestrowanych połączeń nie istnieje. Na przykład w scenariuszu pokoju rozmów, w którym nowe wiadomości czatu powinny być wysyłane do wszystkich klientów w pokoju rozmów, każde połączenie gRPC jest wymagane do indywidualnego przesyłania strumieniowego nowych wiadomości czatu do klienta. [SignalR](xref:signalr/introduction)jest użyteczną ramą dla tego scenariusza. SignalRma koncepcję trwałych połączeń i wbudowaną obsługę wiadomości nadawczych.
* **Komunikacja** &ndash; między procesami Proces musi obsługiwać serwer HTTP/2, aby akceptować przychodzące wywołania gRPC. W systemie Windows [między procesami komunikacyjnymi rury komunikacyjne](/dotnet/standard/io/pipe-operations) są szybką i lekką metodą komunikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
