---
title: Konfiguracja programu gRPC for .NET
author: jamesnk
description: Dowiedz się, jak skonfigurować gRPC dla aplikacji .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: 6e4259b538d32490d5281f189a04ab5a04dbcce5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774694"
---
# <a name="grpc-for-net-configuration"></a>Konfiguracja programu gRPC for .NET

## <a name="configure-services-options"></a>Konfigurowanie opcji usług

usługi gRPC Services są skonfigurowane `AddGrpc` w *Startup.cs*. W poniższej tabeli opisano opcje konfigurowania usług gRPC:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Maksymalny rozmiar wiadomości w bajtach, które mogą być wysyłane z serwera. Próba wysłania komunikatu, który przekracza skonfigurowany maksymalny rozmiar komunikatu, spowoduje wyjątek. Po ustawieniu na `null`wartość rozmiar komunikatu jest nieograniczony. |
| MaxReceiveMessageSize | 4 MB | Maksymalny rozmiar komunikatu w bajtach, który może zostać odebrany przez serwer. Jeśli serwer odbiera komunikat, który przekracza ten limit, zgłasza wyjątek. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. Po ustawieniu na `null`wartość rozmiar komunikatu jest nieograniczony. |
| EnableDetailedErrors | `false` | Jeśli `true`szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie usługi. Wartość domyślna to `false`. Ustawienie `EnableDetailedErrors` , `true` aby można było wyciekować poufne informacje. |
| CompressionProviders | gzip | Kolekcja dostawców kompresji służąca do kompresowania i dekompresowania komunikatów. Niestandardowych dostawców kompresji można utworzyć i dodać do kolekcji. Domyślnie skonfigurowane dostawcy obsługują kompresję w formacie **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algorytm kompresji używany do kompresowania komunikatów wysyłanych z serwera. Algorytm musi być zgodny z dostawcą kompresji w `CompressionProviders`. Aby algorytm był kompresowany odpowiedzi, klient musi wskazać, że obsługuje algorytm, wysyłając go w nagłówku **GRPC-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Poziom kompresji używany do kompresowania komunikatów wysyłanych z serwera. |
| Interceptory | Brak | Kolekcja przechwyceń, które są uruchamiane z każdym wywołaniem gRPC. Interceptory są uruchamiane w kolejności, w jakiej zostały zarejestrowane. Skonfigurowane globalnie Interceptory są uruchamiane przed przechwyceniami skonfigurowanymi dla jednej usługi. Aby uzyskać więcej informacji na temat interceptorów gRPC, zobacz [GRPC Interceptory i oprogramowanie pośredniczące](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Opcje można skonfigurować dla wszystkich usług, dostarczając opcje delegata `AddGrpc` wywołania w: `Startup.ConfigureServices`

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Opcje pojedynczej usługi przesłaniają opcje globalne podane w `AddGrpc` i można je skonfigurować przy użyciu `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Konfiguracja klienta gRPC jest ustawiona na `GrpcChannelOptions`. W poniższej tabeli opisano opcje konfigurowania kanałów gRPC:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| HttpClient | Nowe wystąpienie | `HttpClient` Używane do wykonywania wywołań gRPC. Można ustawić klienta, aby skonfigurować niestandardowe `HttpClientHandler`lub dodać dodatkowe programy obsługi do potoku HTTP dla wywołań gRPC. Jeśli nie `HttpClient` zostanie określona, nowe `HttpClient` wystąpienie dla tego kanału zostanie utworzone. Zostanie on automatycznie usunięty. |
| DisposeHttpClient | `false` | Jeśli `true`jest określony, `HttpClient` `HttpClient` wystąpienie zostanie usunięte po `GrpcChannel` usunięciu elementu. |
| LoggerFactory | `null` | `LoggerFactory` Używany przez klienta do rejestrowania informacji o wywołaniach gRPC. `LoggerFactory` Wystąpienie może zostać rozpoznane z iniekcji zależności lub utworzone za `LoggerFactory.Create`pomocą. Przykłady konfigurowania rejestrowania znajdują się w <xref:grpc/diagnostics#grpc-client-logging>temacie. |
| MaxSendMessageSize | `null` | Maksymalny rozmiar wiadomości w bajtach, które mogą być wysyłane z klienta. Próba wysłania komunikatu, który przekracza skonfigurowany maksymalny rozmiar komunikatu, spowoduje wyjątek. Po ustawieniu na `null`wartość rozmiar komunikatu jest nieograniczony. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Maksymalny rozmiar komunikatu w bajtach, który może zostać odebrany przez klienta. Jeśli klient odbiera komunikat, który przekracza ten limit, zgłasza wyjątek. Zwiększenie tej wartości umożliwia klientowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. Po ustawieniu na `null`wartość rozmiar komunikatu jest nieograniczony. |
| Poświadczenia | `null` | `ChannelCredentials` Wystąpienie. Poświadczenia służą do dodawania metadanych uwierzytelniania do wywołań gRPC. |
| CompressionProviders | gzip | Kolekcja dostawców kompresji służąca do kompresowania i dekompresowania komunikatów. Niestandardowych dostawców kompresji można utworzyć i dodać do kolekcji. Domyślnie skonfigurowane dostawcy obsługują kompresję w formacie **gzip** . |

Następujący kod:

* Ustawia maksymalny rozmiar wiadomości wysyłania i odbierania w kanale.
* Tworzy klienta.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
