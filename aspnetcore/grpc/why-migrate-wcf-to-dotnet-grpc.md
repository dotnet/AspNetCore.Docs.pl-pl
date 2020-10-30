---
title: Dlaczego warto przeprowadzić migrację aplikacji WCF do usługi gRPC platformy ASP.NET Core
author: markrendle
description: W tym artykule opisano, dlaczego ASP.NET Core gRPC jest dobrą zaletą dla deweloperów Windows Communication Foundation (WCF), którzy chcą migrować do nowoczesnych architektur i platform.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058691"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC for Windows Communication Foundation (WCF) — deweloperzy

W tym artykule opisano, dlaczego ASP.NET Core gRPC jest dobrą zaletą dla deweloperów Windows Communication Foundation (WCF), którzy chcą migrować do nowoczesnych architektur i platform.

## <a name="comparison-to-wcf"></a>Porównanie z WCF

Chociaż implementacja i podejście różnią się w zależności od gRPC, doświadczenia związane z tworzeniem i zużywaniem usług z gRPC powinny być intuicyjne dla deweloperów WCF. WCF i gRPC to platformy RPC (zdalne wywołanie procedury) z tymi samymi celami:

* Umożliwia to kod, tak jakby klient i serwer znajdują się na tej samej platformie.
* Zapewnianie uproszczonego interfejsu API sieci przenośnej.

Obie platformy korzystają z wymogu deklarowania i implementacji interfejsu, chociaż proces deklarowania interfejsu jest inny. Wiele typów wywołań RPC, które gRPC obsługuje mapowanie do powiązań dostępnych dla usług WCF. Aby uzyskać więcej informacji i przykładów, zobacz [Migrowanie rozwiązania WCF do gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Zalety gRPC

gRPC zapewnia lepszą strukturę niż inne podejścia z następujących powodów.

### <a name="performance"></a>Wydajność

gRPC używa protokołu HTTP/2. W przeciwieństwie do protokołu HTTP/1.1, HTTP/2:

* Jest mniejszym, szybszym protokołem binarnym.
* Jest wydajniejszy w przypadku komputerów do analizy.
* Obsługuje multipleksowanie żądań za pośrednictwem jednego połączenia. Multipleksowanie umożliwia wysyłanie wielu żądań w ramach jednego połączenia bez konieczności blokowania siebie. W przypadku protokołu HTTP/1.1 Blokowanie jest znane jako blokowanie "linia główna (HOL)".

gRPC używa protobuf, wydajnego formatu binarnego, do serializacji komunikatów. Komunikaty protobuf:
* Szybka do serializacji i deserializacji.
* Używaj mniejszej przepustowości niż formaty tekstowe. 

gRPC to dobre rozwiązanie dla urządzeń przenośnych i sieci z ograniczeniami przepustowości.

### <a name="interoperability"></a>Współdziałanie

Istnieją narzędzia i biblioteki gRPC dla wszystkich głównych języków programowania i platform, w tym .NET, Java, Python, go, C++, Node.js, Swift, dart, Ruby i PHP. Dzięki protobuf binarnej sieci szkieletowej i wydajnej generacji kodu dla każdej platformy deweloperzy mogą tworzyć aplikacje na różnych platformach.

### <a name="usability-and-productivity"></a>Użyteczność i produktywność

gRPC to kompleksowe rozwiązanie RPC. Działa spójnie w wielu językach i na różnych platformach. Zapewnia również doskonałe narzędzia, z dużą ilością kodu standardowego generowanego automatycznie. Podobnie jak w przypadku WCF, gRPC automatycznie generuje komunikaty i klient o jednoznacznie określonym typie. Czas dewelopera jest bezpłatny do skoncentrowania się na logice biznesowej.

### <a name="streaming"></a>Przesyłanie strumieniowe

gRPC ma pełne dwukierunkowe przesyłanie strumieniowe, które zapewnia podobną funkcjonalność usług WCF w pełni dupleks. gRPC Streaming może działać za pośrednictwem zwykłych połączeń internetowych, modułów równoważenia obciążenia i sieci usług.

### <a name="deadlines-timeouts-and-cancellation"></a>Terminy, limity czasu i anulowanie

gRPC umożliwia klientom określenie maksymalnego czasu na zakończenie zdalnego wywoływania procedur. W przypadku przekroczenia określonego terminu ostatecznego serwer może anulować operację niezależnie od klienta. Terminy i anulowania można rozpropagować za pomocą kolejnych wywołań gRPC, aby ułatwić wymuszanie limitów użycia zasobów. Klienci mogą zatrzymać operacje po przekroczeniu terminu ostatecznego lub wcześniej w razie potrzeby. Na przykład klienci mogą zatrzymać operacje ze względu na interakcję z użytkownikiem.

### <a name="security"></a>Zabezpieczenia

gRPC może używać protokołów TLS i HTTP/2, aby zapewnić kompleksowe połączenie szyfrowane między klientem a serwerem. Obsługa uwierzytelniania certyfikatu klienta dalsze zwiększa bezpieczeństwo i zaufanie między klientem a serwerem.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC jako ścieżkę migracji dla usług WCF do .NET Core i .NET 5

Programy .NET Core i .NET 5 oznaczają zmianę w sposobie, w jaki firma Microsoft dostarcza rozwiązania komunikacji zdalnej dla deweloperów, którzy chcą dostarczać usługi na różne platformy. Oprogramowanie .NET Core i .NET 5 obsługują [wywoływanie usług WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), ale nie oferuje obsługi klienta po stronie serwera do hostowania WCF.

Istnieją dwie zalecane ścieżki do modernizacji aplikacji WCF:

* gRPC jest oparta na nowoczesnych technologiach i okazała się najbardziej popularnym wyborem w całej społeczności deweloperów dla aplikacji RPC. Począwszy od platformy .NET Core 3,0, nowoczesne platformy .NET mają doskonałą obsługę gRPC. Migrowanie usług WCF do korzystania z programu gRPC pomaga zapewnić funkcje usługi RPC, wydajność i współdziałanie w nowoczesnych aplikacjach.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) to wysiłki społecznościowe umożliwiające obsługę hostingu usług WCF w oprogramowaniu .NET Core i .NET 5. Dostępna jest wersja zapoznawcza, a projekt jest gotowy do produkcji. CoreWCF obsługuje tylko podzbiór funkcji WCF, a .NET Framework aplikacje, które migruje do korzystania z niej, będą wymagały zmiany kodu i testy zostały pomyślnie wykonane. CoreWCF jest dobrym rozwiązaniem, jeśli aplikacja musi zachować zgodność z istniejącymi klientami, którzy wywołują usługi WCF.

## <a name="get-started"></a>Rozpoczęcie pracy

Aby uzyskać szczegółowe wskazówki dotyczące tworzenia usług gRPC Services w ASP.NET Core dla deweloperów WCF, zobacz [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers) .
