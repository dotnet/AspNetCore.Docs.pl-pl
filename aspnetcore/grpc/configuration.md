---
title: Konfiguracja programu gRPC for .NET
author: jamesnk
description: Dowiedz się, jak skonfigurować gRPC dla aplikacji .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: e6767aedcf9c6dbcbd7eec97e1c7a5b6161992aa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016196"
---
# <a name="grpc-for-net-configuration"></a>Konfiguracja programu gRPC for .NET

## <a name="configure-services-options"></a>Konfigurowanie opcji usług

usługi gRPC Services są skonfigurowane `AddGrpc` w *Startup.cs*. W poniższej tabeli opisano opcje konfigurowania usług gRPC:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Maksymalny rozmiar wiadomości w bajtach, które mogą być wysyłane z serwera. Próba wysłania komunikatu, który przekracza skonfigurowany maksymalny rozmiar komunikatu, spowoduje wyjątek. Po ustawieniu na wartość `null` rozmiar komunikatu jest nieograniczony. |
| MaxReceiveMessageSize | 4 MB | Maksymalny rozmiar komunikatu w bajtach, który może zostać odebrany przez serwer. Jeśli serwer odbiera komunikat, który przekracza ten limit, zgłasza wyjątek. Zwiększenie tej wartości umożliwia serwerowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. Po ustawieniu na wartość `null` rozmiar komunikatu jest nieograniczony. |
| EnableDetailedErrors | `false` | Jeśli `true` szczegółowe komunikaty o wyjątkach są zwracane do klientów, gdy wyjątek jest zgłaszany w metodzie usługi. Wartość domyślna to `false`. Ustawienie `EnableDetailedErrors` , aby `true` można było wyciekować poufne informacje. |
| CompressionProviders | gzip | Kolekcja dostawców kompresji służąca do kompresowania i dekompresowania komunikatów. Niestandardowych dostawców kompresji można utworzyć i dodać do kolekcji. Domyślnie skonfigurowane dostawcy obsługują kompresję w formacie **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algorytm kompresji używany do kompresowania komunikatów wysyłanych z serwera. Algorytm musi być zgodny z dostawcą kompresji w `CompressionProviders` . Aby algorytm był kompresowany odpowiedzi, klient musi wskazać, że obsługuje algorytm, wysyłając go w nagłówku **GRPC-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Poziom kompresji używany do kompresowania komunikatów wysyłanych z serwera. |
| Interceptory | Brak | Kolekcja przechwyceń, które są uruchamiane z każdym wywołaniem gRPC. Interceptory są uruchamiane w kolejności, w jakiej zostały zarejestrowane. Skonfigurowane globalnie Interceptory są uruchamiane przed przechwyceniami skonfigurowanymi dla jednej usługi. Aby uzyskać więcej informacji na temat interceptorów gRPC, zobacz [GRPC Interceptory i oprogramowanie pośredniczące](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| IgnoreUnknownServices | `false` | Jeśli `true` program wywołuje nieznane usługi i metody nie zwróci stanu **niezaimplementowanego** , a żądanie przejdzie do następnego zarejestrowanego oprogramowania pośredniczącego w ASP.NET Core. |

Opcje można skonfigurować dla wszystkich usług, dostarczając opcje delegata `AddGrpc` wywołania w `Startup.ConfigureServices` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Opcje pojedynczej usługi przesłaniają opcje globalne podane w `AddGrpc` i można je skonfigurować przy użyciu `AddServiceOptions<TService>` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurowanie opcji klienta

Konfiguracja klienta gRPC jest ustawiona na `GrpcChannelOptions` . W poniższej tabeli opisano opcje konfigurowania kanałów gRPC:

| Opcja | Wartość domyślna | Opis |
| ------ | ------------- | ----------- |
| HttpHandler | Nowe wystąpienie | `HttpMessageHandler`Używane do wykonywania wywołań gRPC. Klienta można ustawić, aby skonfigurować niestandardowe `HttpClientHandler` lub dodać dodatkowe programy obsługi do potoku HTTP dla wywołań gRPC. Jeśli nie `HttpMessageHandler` zostanie określona, `HttpClientHandler` zostanie utworzone nowe wystąpienie dla kanału z automatycznym usuwaniem. |
| HttpClient | `null` | `HttpClient`Używane do wykonywania wywołań gRPC. To ustawienie jest alternatywą dla `HttpHandler` . |
| DisposeHttpClient | `false` | Jeśli jest ustawiona na, `true` a `HttpMessageHandler` lub `HttpClient` jest określona, lub `HttpHandler` `HttpClient` odpowiednio, jest usuwana, gdy `GrpcChannel` zostanie usunięty. |
| LoggerFactory | `null` | `LoggerFactory`Używany przez klienta do rejestrowania informacji o wywołaniach gRPC. `LoggerFactory`Wystąpienie może zostać rozpoznane z iniekcji zależności lub utworzone za pomocą `LoggerFactory.Create` . Przykłady konfigurowania rejestrowania znajdują się w temacie <xref:grpc/diagnostics#grpc-client-logging> . |
| MaxSendMessageSize | `null` | Maksymalny rozmiar wiadomości w bajtach, które mogą być wysyłane z klienta. Próba wysłania komunikatu, który przekracza skonfigurowany maksymalny rozmiar komunikatu, spowoduje wyjątek. Po ustawieniu na wartość `null` rozmiar komunikatu jest nieograniczony. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Maksymalny rozmiar komunikatu w bajtach, który może zostać odebrany przez klienta. Jeśli klient odbiera komunikat, który przekracza ten limit, zgłasza wyjątek. Zwiększenie tej wartości umożliwia klientowi otrzymywanie większych komunikatów, ale może mieć negatywny wpływ na użycie pamięci. Po ustawieniu na wartość `null` rozmiar komunikatu jest nieograniczony. |
| Poświadczenia | `null` | `ChannelCredentials`Wystąpienie. Poświadczenia służą do dodawania metadanych uwierzytelniania do wywołań gRPC. |
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
