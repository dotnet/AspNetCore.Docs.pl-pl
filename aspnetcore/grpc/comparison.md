---
title: Porównanie usług gRPC za pomocą interfejsów API protokołu HTTP
author: jamesnk
description: Dowiedz się, jak gRPC porównuje z interfejsami API protokołu HTTP i jakie są zalecane scenariusze.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
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
uid: grpc/comparison
ms.openlocfilehash: 1ec553d54a9cad170cb322bc186bb67ac8bbded4
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658732"
---
# <a name="compare-grpc-services-with-http-apis"></a>Porównanie usług gRPC za pomocą interfejsów API protokołu HTTP

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

W tym artykule wyjaśniono, w jaki sposób [usługi gRPC Services](https://grpc.io/docs/guides/) porównują interfejsy API protokołu HTTP z JSON (w tym ASP.NET Core [interfejsy API sieci Web](xref:web-api/index)) Technologia używana do udostępniania interfejsu API dla aplikacji jest ważnym wyborem, a gRPC oferuje unikatowe korzyści w porównaniu do interfejsów API protokołu HTTP. W tym artykule omówiono mocne i słabe zalety gRPC oraz zalecane scenariusze dotyczące korzystania z gRPC przez inne technologie.

## <a name="high-level-comparison"></a>Porównanie wysokiego poziomu

Poniższa tabela zawiera porównanie funkcji między gRPC i interfejsami API protokołu HTTP z kodem JSON.

| Cechy          | gRPC                                               | Interfejsy API protokołu HTTP z JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Kontrakt         | Wymagane (*. proto*)                                | Opcjonalnie (OpenAPI)            |
| Protokół         | HTTP/2                                             | HTTP                          |
| Ładunku          | [Protobuf (mały, binarny)](#performance)           | JSON (duże, czytelne dla ludzi)  |
| Prescriptiveness | [Specyfikacja Strict](#strict-specification)      | Sypki. Wszystkie protokoły HTTP są prawidłowe.     |
| Przesyłanie strumieniowe        | [Klient, serwer, dwukierunkowa](#streaming)       | Klient, serwer                |
| Obsługa przeglądarki  | [Nie (wymaga GRPC-Web)](#limited-browser-support) | Tak                           |
| Zabezpieczenia         | Transport (TLS)                                    | Transport (TLS)               |
| Generowanie kodu klienta | [Tak](#code-generation)                      | Narzędzia OpenAPI + inne firmy |

## <a name="grpc-strengths"></a>mocne gRPC

### <a name="performance"></a>Wydajność

komunikaty gRPC są serializowane przy użyciu [protobuf](https://developers.google.com/protocol-buffers/docs/overview), wydajnego formatu komunikatów binarnych. Protobuf się bardzo szybko na serwerze i kliencie. Serializacja protobuf skutkuje niewielkimi ładunekmi komunikatów, co jest ważne w ograniczonych scenariuszach dotyczących przepustowości, takich jak aplikacje mobilne.

gRPC została zaprojektowana dla protokołu HTTP/2, która stanowi znaczną wersję protokołu HTTP, która zapewnia znaczący wpływ na wydajność w porównaniu z protokołem HTTP 1. x:

* Binarne ramki i kompresja. Protokół HTTP/2 jest kompaktowy i wydajny zarówno podczas wysyłania, jak i otrzymywania.
* Multipleksowanie wielu wywołań HTTP/2 za pośrednictwem jednego połączenia TCP. Multipleksowanie eliminuje [blokowanie głowy](https://en.wikipedia.org/wiki/Head-of-line_blocking).

Protokół HTTP/2 nie jest wyłączny do gRPC. Wiele typów żądań, w tym interfejsy API protokołu HTTP z notacją JSON, może korzystać z protokołu HTTP/2 i korzystać z jego ulepszeń wydajności.

### <a name="code-generation"></a>Generowanie kodu

Wszystkie struktury gRPC zapewniają obsługę pierwszej klasy w celu generowania kodu. Podstawowy plik do gRPC Development to [ `.proto` plik](https://developers.google.com/protocol-buffers/docs/proto3), który definiuje kontrakt usług gRPC Services i komunikatów. Z tego pliku gRPC struktury generują klasę bazową usługi, komunikaty i kompletny klient.

Udostępniając plik *. proto* między serwerem a klientem, można wygenerować komunikaty i kod klienta na końcu. Generowanie kodu klienta eliminuje duplikowanie komunikatów na kliencie i serwerze, a następnie tworzy klienta o jednoznacznie określonym typie. Nie trzeba pisać klienta powoduje oszczędność czasu projektowania w aplikacjach z wieloma usługami.

### <a name="strict-specification"></a>Specyfikacja Strict

Specyfikacja formalna dla interfejsu API protokołu HTTP with JSON nie istnieje. Deweloperzy zanotują najlepszy format adresów URL, czasowników HTTP i kodów odpowiedzi.

[Specyfikacja gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) jest przedskryptowa o formacie, którego musi przestrzegać usługa gRPC. gRPC eliminuje debatę i zapisuje czas dewelopera, ponieważ gRPC jest spójny dla wielu platform i implementacji.

### <a name="streaming"></a>Przesyłanie strumieniowe

Protokół HTTP/2 stanowi podstawę do długotrwałych strumieni komunikacji w czasie rzeczywistym. gRPC zapewnia wsparcie pierwszej klasy do przesyłania strumieniowego za pośrednictwem protokołu HTTP/2.

Usługa gRPC obsługuje wszystkie kombinacje przesyłania strumieniowego:

* Jednoargumentowy (bez przesyłania strumieniowego)
* Przesyłanie strumieniowe z serwera do klienta
* Przesyłanie strumieniowe klienta do serwera
* Dwukierunkowe przesyłanie strumieniowe

### <a name="deadlinetimeouts-and-cancellation"></a>Termin/limity czasu i anulowanie

gRPC umożliwia klientom określenie, jak długo czekają na ukończenie zdalnego wywoływania procedur. [Ostateczny termin](https://grpc.io/blog/deadlines) jest wysyłany do serwera, a serwer może zdecydować, jakie działania należy podjąć, jeśli przekracza termin ostateczny. Na przykład serwer może anulować żądania gRPC/HTTP/Database w trakcie limitu czasu.

Propagowanie terminu i anulowania za pomocą podrzędnych wywołań gRPC ułatwia wymuszenie limitów użycia zasobów.

## <a name="grpc-recommended-scenarios"></a>gRPC zalecane scenariusze

gRPC doskonale nadaje się do następujących scenariuszy:

* **Mikrousługi**: gRPC został zaprojektowany z myślą o małym opóźnieniu i komunikacji o dużej przepływności. gRPC doskonale nadaje się do lekkich mikrousług, w których wydajność jest krytyczna.
* **Komunikacja punkt-punkt w czasie rzeczywistym**: gRPC ma doskonałą obsługę przesyłania strumieniowego dwukierunkowego. usługi gRPC umożliwiają wypychanie komunikatów w czasie rzeczywistym bez sondowania.
* **Środowiska Polyglot**: narzędzia gRPC obsługują wszystkie popularne języki deweloperskie, co sprawia, że gRPC to dobry wybór w środowiskach wielojęzycznych.
* **Środowiska ograniczone sieci**: komunikaty gRPC są serializowane z protobuf, formatem uproszczonego komunikatu. Komunikat gRPC jest zawsze krótszy niż odpowiedni komunikat JSON.
* **Komunikacja między procesami (IPC)**: transport IPC, taki jak gniazda domen systemu UNIX i nazwane potoki, może być używany z gRPC do komunikacji między aplikacjami na tym samym komputerze. Aby uzyskać więcej informacji, zobacz <xref:grpc/interprocess>.

## <a name="grpc-weaknesses"></a>słabe gRPC

### <a name="limited-browser-support"></a>Ograniczona obsługa przeglądarki

Obecnie nie można bezpośrednio wywołać usługi gRPC z przeglądarki. gRPC intensywnie używa funkcji protokołu HTTP/2 i żadna przeglądarka nie zapewnia poziomu kontroli wymaganego w przypadku żądań sieci Web do obsługi klienta gRPC. Na przykład przeglądarki nie zezwalają obiektowi wywołującemu na użycie protokołu HTTP/2 lub zapewniają dostęp do bazowych ramek HTTP/2.

Istnieją dwa typowe podejścia do gRPC do aplikacji przeglądarki:

* [gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) to dodatkowa technologia od zespołu gRPC, która zapewnia pomoc techniczną gRPC w przeglądarce. gRPC — sieć Web umożliwia aplikacjom przeglądarki korzystanie z wysokiej wydajności i niskiego użycia sieci w gRPC. Nie wszystkie funkcje gRPC są obsługiwane przez gRPC-Web. Przesyłanie strumieniowe klienta i dwukierunkowego nie jest obsługiwane i ma ograniczoną obsługę przesyłania strumieniowego serwera.

  Platforma .NET Core obsługuje program gRPC-Web. Aby uzyskać więcej informacji, zobacz <xref:grpc/browser>.

* Interfejsy API sieci Web RESTful JSON można utworzyć automatycznie z poziomu usług gRPC, dodając adnotację do pliku *. proto* z [metadanymi http](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule). Dzięki temu aplikacja może obsługiwać zarówno gRPC, jak i interfejsy API sieci Web w formacie JSON, bez duplikowania nakładów pracy związanych z tworzeniem oddzielnych usług.

  Platforma .NET Core ma eksperymentalną obsługę tworzenia interfejsów API sieci Web JSON z usług gRPC Services. Aby uzyskać więcej informacji, zobacz <xref:grpc/httpapi>.

### <a name="not-human-readable"></a>Nie można odczytać ludzi

Żądania interfejsu API protokołu HTTP są wysyłane jako tekst i mogą być odczytywane i tworzone przez człowieka.

wiadomości gRPC są domyślnie kodowane przy użyciu protobuf. Gdy protobuf jest wydajny do wysyłania i odbierania, jego format binarny nie jest czytelny. Protobuf wymaga, aby opis interfejsu komunikatu określony w pliku *. proto* w celu poprawnego deserializacji. Dodatkowe narzędzia są wymagane do analizowania ładunków protobuf w sieci oraz do ręcznego tworzenia żądań.

Funkcje takie jak [odbicie serwera](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) i [Narzędzie wiersza polecenia gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) istnieją, aby ułatwić wykonywanie binarnych komunikatów protobuf. Ponadto komunikaty protobuf obsługują [konwersję do i z formatu JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). Wbudowana konwersja JSON zapewnia wydajny sposób konwersji komunikatów protobuf na i z formularza, który można odczytać przez człowieka podczas debugowania.

## <a name="alternative-framework-scenarios"></a>Scenariusze dotyczące platformy alternatywnej

Inne struktury są zalecane w porównaniu z gRPC w następujących scenariuszach:

* **Interfejsy API dostępne dla przeglądarki**: gRPC nie jest w pełni obsługiwane w przeglądarce. gRPC — sieć Web może oferować pomoc techniczną przeglądarki, ale ma ograniczenia i wprowadza serwer proxy serwera.
* **Rozgłaszanie komunikacji** w czasie rzeczywistym: gRPC obsługuje komunikację w czasie rzeczywistym za pośrednictwem przesyłania strumieniowego, ale pojęcie rozgłaszania komunikatów do zarejestrowanych połączeń nie istnieje. Na przykład w scenariuszu pokoju rozmów, w którym nowe wiadomości czatu powinny być wysyłane do wszystkich klientów w pokoju rozmowy, każde wywołanie gRPC jest wymagane do narzucania strumieniowego przesyłania nowych komunikatów rozmowy do klienta. [SignalR](xref:signalr/introduction) jest przydatną strukturą dla tego scenariusza. SignalR ma koncepcję trwałych połączeń i wbudowaną obsługę rozgłaszania komunikatów.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
