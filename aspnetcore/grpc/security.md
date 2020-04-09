---
title: Względy bezpieczeństwa w gRPC dla ASP.NET Core
author: jamesnk
description: Dowiedz się więcej o zagadnieniach dotyczących zabezpieczeń dla gRPC dla ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667322"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Względy bezpieczeństwa w gRPC dla ASP.NET Core

Przez [James Newton-King](https://twitter.com/jamesnk)

Ten artykuł zawiera informacje na temat zabezpieczania gRPC za pomocą platformy .NET Core.

## <a name="transport-security"></a>Bezpieczeństwo transportu

Wiadomości gRPC są wysyłane i odbierane za pomocą protokołu HTTP/2. Zalecamy:

* [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) służy do zabezpieczania wiadomości w aplikacjach gRPC produkcji.
* Usługi gRPC powinny nasłuchiwają i reagować tylko za dostęp do zabezpieczonych portów.

TLS jest skonfigurowany w Kestrel. Aby uzyskać więcej informacji na temat konfigurowania punktów końcowych Kestrel, zobacz [Konfiguracja punktu końcowego Pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Wyjątki

Komunikaty o wyjątkach są zazwyczaj uważane za poufne dane, które nie powinny być ujawniane klientowi. Domyślnie gRPC nie wysyła do klienta szczegółów wyjątku zgłaszanego przez usługę gRPC. Zamiast tego klient odbiera ogólny komunikat wskazujący wystąpił błąd. Dostarczanie komunikatów o wyjątku do klienta można zastąpić (na przykład w rozwoju lub test) za pomocą [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). Komunikaty o wyjątkach nie powinny być udostępniane klientowi w aplikacjach produkcyjnych.

## <a name="message-size-limits"></a>Limity rozmiaru wiadomości

Przychodzące wiadomości do klientów i usług gRPC są ładowane do pamięci. Limity rozmiaru komunikatów są mechanizmem, który pomaga zapobiegać zużywaniu nadmiernych zasobów przez gRPC.

GRPC używa limitów rozmiaru wiadomości do zarządzania wiadomościami przychodzącymi i wychodzącymi. Domyślnie gRPC ogranicza przychodzące wiadomości do 4 MB. Nie ma limitu na wiadomości wychodzące.

Na serwerze limity komunikatów gRPC można skonfigurować `AddGrpc`dla wszystkich usług w aplikacji z :

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

Limity można również skonfigurować dla `AddServiceOptions<TService>`pojedynczej usługi za pomocą programu . Aby uzyskać więcej informacji na temat konfigurowania limitów rozmiaru wiadomości, zobacz [konfiguracja gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Sprawdzanie poprawności certyfikatu klienta

[Certyfikaty klienta](https://tools.ietf.org/html/rfc5246#section-7.4.4) są początkowo sprawdzane podczas ustanawiania połączenia. Domyślnie Kestrel nie wykonuje dodatkowej weryfikacji certyfikatu klienta połączenia.

Zaleca się, aby usługi gRPC zabezpieczone certyfikatami klienta używały pakietu [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth) ASP.NET Uwierzytelnianie certyfikacji Core przeprowadzi dodatkową weryfikację certyfikatu klienta, w tym:

* Certyfikat ma prawidłowe użycie klucza rozszerzonego (EKU)
* Jest w okresie ważności
* Sprawdzanie odwołania certyfikatu
