---
title: gRPC dla konfiguracji .NET
author: jamesnk
description: Dowiedz się, jak skonfigurować gRPC dla aplikacji .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: cabe2d86f535bf3063dd7ede9e8a3bc5de70e244
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666902"
---
# <a name="grpc-for-net-configuration"></a>gRPC dla konfiguracji .NET

## <a name="configure-services-options"></a>Konfigurowanie opcji usług

Usługi gRPC są `AddGrpc` konfigurowane w *Startup.cs*. W poniższej tabeli opisano opcje konfigurowania usług gRPC:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| Rozmiar MaxSendMessageSize | `null` | Maksymalny rozmiar wiadomości w bajtach, które mogą być wysyłane z serwera. Próba wysłania wiadomości, która przekracza skonfigurowany maksymalny rozmiar wiadomości powoduje wyjątek. |
| Maksymalna Liczba | 4 MB | Maksymalny rozmiar wiadomości w bajtach, który może być odbierany przez serwer. Jeśli serwer otrzyma komunikat, który przekracza ten limit, zgłasza wyjątek. Zwiększenie tej wartości umożliwia serwerowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci. |
| Włącz szczegółoweerrory | `false` | Jeśli `true`, szczegółowe komunikaty wyjątku są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie usługi. Wartość domyślna to `false`. Ustawienie `EnableDetailedErrors` `true` może wyciekać poufne informacje. |
| Funkcje kompresji | Gzip | Kolekcja dostawców kompresji używane do kompresji i dekompresji wiadomości. Niestandardowe dostawców kompresji można tworzyć i dodawać do kolekcji. Domyślni skonfigurowani dostawcy obsługują **kompresję gzip.** |
| <span style="word-break:normal;word-wrap:normal">OdpowiedźKompresjaAlgorytm</span> | `null` | Algorytm kompresji używany do kompresji wiadomości wysyłanych z serwera. Algorytm musi być zgodny `CompressionProviders`z dostawcą kompresji w pliku . Aby algorytm skompresować odpowiedź, klient musi wskazać, że obsługuje algorytm, wysyłając go w nagłówku **grpc-accept-encoding.** |
| OdpowiedźKompresjaPoziom | `null` | Poziom kompresji używany do kompresji wiadomości wysyłanych z serwera. |
| Interceptory | Brak | Kolekcja interceptors, które są uruchamiane przy każdym wywołaniu gRPC. Interceptory są uruchamiane w kolejności, w jakiej są zarejestrowane. Globalnie skonfigurowane interceptory są uruchamiane przed interceptory skonfigurowane dla jednej usługi. Aby uzyskać więcej informacji na temat przechwytujących gRPC, zobacz [gRPC Interceptors vs. Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Opcje można skonfigurować dla wszystkich usług, zapewniając `AddGrpc` opcje `Startup.ConfigureServices`delegowania do połączenia w:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Opcje dla pojedynczej usługi zastępują opcje `AddGrpc` globalne podane w `AddServiceOptions<TService>`i mogą być konfigurowane za pomocą:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Konfiguracja klienta gRPC `GrpcChannelOptions`jest ustawiona na . W poniższej tabeli opisano opcje konfigurowania kanałów gRPC:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| Funkcja HttpClient | Nowe wystąpienie | Używane `HttpClient` do wykonywania połączeń gRPC. Klienta można ustawić, aby skonfigurować niestandardowe `HttpClientHandler`lub dodać dodatkowe programy obsługi do potoku HTTP dla wywołań gRPC. Jeśli `HttpClient` nie jest określony, `HttpClient` a następnie nowe wystąpienie jest tworzony dla kanału. Zostanie on automatycznie usunięty. |
| Unieszkodliwijhttpclient | `false` | Jeśli `true`, `HttpClient` a jest określony, a następnie `HttpClient` wystąpienie zostanie usunięte, gdy `GrpcChannel` jest usuwany. |
| LoggerFactory (Dziennik) | `null` | Używane `LoggerFactory` przez klienta do rejestrowania informacji o wywołaniach gRPC. Wystąpienie `LoggerFactory` można rozpoznać na podstawie iniekcji zależności lub utworzyć przy użyciu programu `LoggerFactory.Create`. Przykłady konfigurowania rejestrowania można <xref:grpc/diagnostics#grpc-client-logging>znaleźć w temacie . |
| Rozmiar MaxSendMessageSize | `null` | Maksymalny rozmiar wiadomości w bajtach, które mogą być wysyłane z klienta. Próba wysłania wiadomości, która przekracza skonfigurowany maksymalny rozmiar wiadomości powoduje wyjątek. |
| <span style="word-break:normal;word-wrap:normal">Maksymalna Liczba</span> | 4 MB | Maksymalny rozmiar wiadomości w bajtach, które mogą być odbierane przez klienta. Jeśli klient otrzyma komunikat, który przekracza ten limit, zgłasza wyjątek. Zwiększenie tej wartości umożliwia klientowi odbieranie większych komunikatów, ale może negatywnie wpłynąć na zużycie pamięci. |
| Poświadczenia | `null` | Wystąpienie. `ChannelCredentials` Poświadczenia są używane do dodawania metadanych uwierzytelniania do wywołań gRPC. |
| Funkcje kompresji | Gzip | Kolekcja dostawców kompresji używane do kompresji i dekompresji wiadomości. Niestandardowe dostawców kompresji można tworzyć i dodawać do kolekcji. Domyślni skonfigurowani dostawcy obsługują **kompresję gzip.** |

Następujący kod:

* Ustawia maksymalny rozmiar wiadomości wysyłania i odbierania na kanale.
* Tworzy klienta.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
