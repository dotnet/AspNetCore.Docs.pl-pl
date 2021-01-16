---
title: Zagadnienia dotyczące zabezpieczeń w programie gRPC ASP.NET Core
author: jamesnk
description: Dowiedz się więcej na temat zagadnień związanych z zabezpieczeniami dla programu gRPC ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252815"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Zagadnienia dotyczące zabezpieczeń w programie gRPC ASP.NET Core

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

Ten artykuł zawiera informacje dotyczące zabezpieczania gRPC za pomocą platformy .NET Core.

## <a name="transport-security"></a>Zabezpieczenia transportu

komunikaty gRPC są wysyłane i odbierane przy użyciu protokołu HTTP/2. Zalecamy:

* [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) służy do zabezpieczania komunikatów w środowisku produkcyjnym aplikacje gRPC.
* usługi gRPC powinny nasłuchiwać i reagować tylko za pośrednictwem zabezpieczonych portów.

::: moniker range=">= aspnetcore-5.0"
Protokół TLS jest skonfigurowany w Kestrel. Aby uzyskać więcej informacji na temat konfigurowania punktów końcowych Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel/endpoints).
::: moniker-end

::: moniker range="< aspnetcore-5.0"
Protokół TLS jest skonfigurowany w Kestrel. Aby uzyskać więcej informacji na temat konfigurowania punktów końcowych Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).
::: moniker-end

## <a name="exceptions"></a>Wyjątki

Komunikaty o wyjątkach są zwykle uznawane za dane poufne, które nie powinny być ujawnione dla klienta. Domyślnie gRPC nie wysyła szczegółów wyjątku zgłoszonego przez usługę gRPC do klienta. Zamiast tego klient otrzymuje komunikat ogólny informujący o wystąpieniu błędu. Dostarczenie komunikatu o wyjątku do klienta może zostać zastąpione (na przykład w trakcie programowania lub testowania) za pomocą [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). Komunikaty o wyjątkach nie powinny być udostępniane klientowi w aplikacjach produkcyjnych.

## <a name="message-size-limits"></a>Limity rozmiaru komunikatów

Komunikaty przychodzące do gRPC klientów i usług są ładowane do pamięci. Limity rozmiaru komunikatów to mechanizm, który zapobiega gRPC nadmiernej ilości zasobów.

gRPC używa limitów rozmiaru poszczególnych komunikatów do zarządzania wiadomościami przychodzącymi i wychodzącymi. Domyślnie gRPC ogranicza komunikaty przychodzące do 4 MB. Nie ma limitu dla komunikatów wychodzących.

Na serwerze limity komunikatów gRPC można skonfigurować dla wszystkich usług w aplikacji z `AddGrpc` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Limity można również skonfigurować dla pojedynczej usługi przy użyciu programu `AddServiceOptions<TService>` . Aby uzyskać więcej informacji na temat konfigurowania limitów rozmiaru komunikatów, zobacz [Konfiguracja gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Sprawdzanie poprawności certyfikatu klienta

[Certyfikaty klienta](https://tools.ietf.org/html/rfc5246#section-7.4.4) są początkowo weryfikowane po nawiązaniu połączenia. Domyślnie Kestrel nie wykonuje dodatkowej weryfikacji certyfikatu klienta połączenia.

Zalecamy, aby usługi gRPC zabezpieczone przez certyfikaty klienta korzystały z pakietu [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) . ASP.NET Core uwierzytelnianie certyfikacji będzie przeprowadzać dodatkowe sprawdzanie poprawności certyfikatu klienta, w tym:

* Certyfikat ma prawidłowe rozszerzone użycie klucza (EKU)
* Jest w jego okresie ważności
* Sprawdź odwołanie certyfikatu
