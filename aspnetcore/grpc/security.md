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
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="9d5c8-103">Względy bezpieczeństwa w gRPC dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d5c8-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="9d5c8-104">Przez [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9d5c8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9d5c8-105">Ten artykuł zawiera informacje na temat zabezpieczania gRPC za pomocą platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="9d5c8-106">Bezpieczeństwo transportu</span><span class="sxs-lookup"><span data-stu-id="9d5c8-106">Transport security</span></span>

<span data-ttu-id="9d5c8-107">Wiadomości gRPC są wysyłane i odbierane za pomocą protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="9d5c8-108">Zalecamy:</span><span class="sxs-lookup"><span data-stu-id="9d5c8-108">We recommend:</span></span>

* <span data-ttu-id="9d5c8-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) służy do zabezpieczania wiadomości w aplikacjach gRPC produkcji.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="9d5c8-110">Usługi gRPC powinny nasłuchiwają i reagować tylko za dostęp do zabezpieczonych portów.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="9d5c8-111">TLS jest skonfigurowany w Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="9d5c8-112">Aby uzyskać więcej informacji na temat konfigurowania punktów końcowych Kestrel, zobacz [Konfiguracja punktu końcowego Pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="9d5c8-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="9d5c8-113">Wyjątki</span><span class="sxs-lookup"><span data-stu-id="9d5c8-113">Exceptions</span></span>

<span data-ttu-id="9d5c8-114">Komunikaty o wyjątkach są zazwyczaj uważane za poufne dane, które nie powinny być ujawniane klientowi.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="9d5c8-115">Domyślnie gRPC nie wysyła do klienta szczegółów wyjątku zgłaszanego przez usługę gRPC.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="9d5c8-116">Zamiast tego klient odbiera ogólny komunikat wskazujący wystąpił błąd.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="9d5c8-117">Dostarczanie komunikatów o wyjątku do klienta można zastąpić (na przykład w rozwoju lub test) za pomocą [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="9d5c8-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="9d5c8-118">Komunikaty o wyjątkach nie powinny być udostępniane klientowi w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="9d5c8-119">Limity rozmiaru wiadomości</span><span class="sxs-lookup"><span data-stu-id="9d5c8-119">Message size limits</span></span>

<span data-ttu-id="9d5c8-120">Przychodzące wiadomości do klientów i usług gRPC są ładowane do pamięci.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="9d5c8-121">Limity rozmiaru komunikatów są mechanizmem, który pomaga zapobiegać zużywaniu nadmiernych zasobów przez gRPC.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="9d5c8-122">GRPC używa limitów rozmiaru wiadomości do zarządzania wiadomościami przychodzącymi i wychodzącymi.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="9d5c8-123">Domyślnie gRPC ogranicza przychodzące wiadomości do 4 MB.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="9d5c8-124">Nie ma limitu na wiadomości wychodzące.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="9d5c8-125">Na serwerze limity komunikatów gRPC można skonfigurować `AddGrpc`dla wszystkich usług w aplikacji z :</span><span class="sxs-lookup"><span data-stu-id="9d5c8-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

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

<span data-ttu-id="9d5c8-126">Limity można również skonfigurować dla `AddServiceOptions<TService>`pojedynczej usługi za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="9d5c8-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="9d5c8-127">Aby uzyskać więcej informacji na temat konfigurowania limitów rozmiaru wiadomości, zobacz [konfiguracja gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="9d5c8-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="9d5c8-128">Sprawdzanie poprawności certyfikatu klienta</span><span class="sxs-lookup"><span data-stu-id="9d5c8-128">Client certificate validation</span></span>

<span data-ttu-id="9d5c8-129">[Certyfikaty klienta](https://tools.ietf.org/html/rfc5246#section-7.4.4) są początkowo sprawdzane podczas ustanawiania połączenia.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="9d5c8-130">Domyślnie Kestrel nie wykonuje dodatkowej weryfikacji certyfikatu klienta połączenia.</span><span class="sxs-lookup"><span data-stu-id="9d5c8-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="9d5c8-131">Zaleca się, aby usługi gRPC zabezpieczone certyfikatami klienta używały pakietu [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth)</span><span class="sxs-lookup"><span data-stu-id="9d5c8-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="9d5c8-132">ASP.NET Uwierzytelnianie certyfikacji Core przeprowadzi dodatkową weryfikację certyfikatu klienta, w tym:</span><span class="sxs-lookup"><span data-stu-id="9d5c8-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="9d5c8-133">Certyfikat ma prawidłowe użycie klucza rozszerzonego (EKU)</span><span class="sxs-lookup"><span data-stu-id="9d5c8-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="9d5c8-134">Jest w okresie ważności</span><span class="sxs-lookup"><span data-stu-id="9d5c8-134">Is within its validity period</span></span>
* <span data-ttu-id="9d5c8-135">Sprawdzanie odwołania certyfikatu</span><span class="sxs-lookup"><span data-stu-id="9d5c8-135">Check certificate revocation</span></span>
