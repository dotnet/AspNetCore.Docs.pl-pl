---
title: Zagadnienia dotyczące zabezpieczeń w programie gRPC ASP.NET Core
author: jamesnk
description: Dowiedz się więcej na temat zagadnień związanych z zabezpieczeniami dla programu gRPC ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/security
ms.openlocfilehash: f06e239054b1c4edf126d1cf974dff1ca36ee56a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400131"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="d091a-103">Zagadnienia dotyczące zabezpieczeń w programie gRPC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d091a-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="d091a-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d091a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d091a-105">Ten artykuł zawiera informacje dotyczące zabezpieczania gRPC za pomocą platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d091a-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="d091a-106">Zabezpieczenia transportu</span><span class="sxs-lookup"><span data-stu-id="d091a-106">Transport security</span></span>

<span data-ttu-id="d091a-107">komunikaty gRPC są wysyłane i odbierane przy użyciu protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d091a-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="d091a-108">Zalecamy:</span><span class="sxs-lookup"><span data-stu-id="d091a-108">We recommend:</span></span>

* <span data-ttu-id="d091a-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) służy do zabezpieczania komunikatów w środowisku produkcyjnym aplikacje gRPC.</span><span class="sxs-lookup"><span data-stu-id="d091a-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="d091a-110">usługi gRPC powinny nasłuchiwać i reagować tylko za pośrednictwem zabezpieczonych portów.</span><span class="sxs-lookup"><span data-stu-id="d091a-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="d091a-111">Protokół TLS jest skonfigurowany w Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d091a-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="d091a-112">Aby uzyskać więcej informacji na temat konfigurowania punktów końcowych Kestrel, zobacz [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="d091a-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="d091a-113">Wyjątki</span><span class="sxs-lookup"><span data-stu-id="d091a-113">Exceptions</span></span>

<span data-ttu-id="d091a-114">Komunikaty o wyjątkach są zwykle uznawane za dane poufne, które nie powinny być ujawnione dla klienta.</span><span class="sxs-lookup"><span data-stu-id="d091a-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="d091a-115">Domyślnie gRPC nie wysyła szczegółów wyjątku zgłoszonego przez usługę gRPC do klienta.</span><span class="sxs-lookup"><span data-stu-id="d091a-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="d091a-116">Zamiast tego klient otrzymuje komunikat ogólny informujący o wystąpieniu błędu.</span><span class="sxs-lookup"><span data-stu-id="d091a-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="d091a-117">Dostarczenie komunikatu o wyjątku do klienta może zostać zastąpione (na przykład w trakcie programowania lub testowania) za pomocą [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="d091a-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="d091a-118">Komunikaty o wyjątkach nie powinny być udostępniane klientowi w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="d091a-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="d091a-119">Limity rozmiaru komunikatów</span><span class="sxs-lookup"><span data-stu-id="d091a-119">Message size limits</span></span>

<span data-ttu-id="d091a-120">Komunikaty przychodzące do gRPC klientów i usług są ładowane do pamięci.</span><span class="sxs-lookup"><span data-stu-id="d091a-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="d091a-121">Limity rozmiaru komunikatów to mechanizm, który zapobiega gRPC nadmiernej ilości zasobów.</span><span class="sxs-lookup"><span data-stu-id="d091a-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="d091a-122">gRPC używa limitów rozmiaru poszczególnych komunikatów do zarządzania wiadomościami przychodzącymi i wychodzącymi.</span><span class="sxs-lookup"><span data-stu-id="d091a-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="d091a-123">Domyślnie gRPC ogranicza komunikaty przychodzące do 4 MB.</span><span class="sxs-lookup"><span data-stu-id="d091a-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="d091a-124">Nie ma limitu dla komunikatów wychodzących.</span><span class="sxs-lookup"><span data-stu-id="d091a-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="d091a-125">Na serwerze limity komunikatów gRPC można skonfigurować dla wszystkich usług w aplikacji z `AddGrpc` :</span><span class="sxs-lookup"><span data-stu-id="d091a-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

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

<span data-ttu-id="d091a-126">Limity można również skonfigurować dla pojedynczej usługi przy użyciu programu `AddServiceOptions<TService>` .</span><span class="sxs-lookup"><span data-stu-id="d091a-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="d091a-127">Aby uzyskać więcej informacji na temat konfigurowania limitów rozmiaru komunikatów, zobacz [Konfiguracja gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="d091a-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="d091a-128">Sprawdzanie poprawności certyfikatu klienta</span><span class="sxs-lookup"><span data-stu-id="d091a-128">Client certificate validation</span></span>

<span data-ttu-id="d091a-129">[Certyfikaty klienta](https://tools.ietf.org/html/rfc5246#section-7.4.4) są początkowo weryfikowane po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="d091a-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="d091a-130">Domyślnie Kestrel nie wykonuje dodatkowej weryfikacji certyfikatu klienta połączenia.</span><span class="sxs-lookup"><span data-stu-id="d091a-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="d091a-131">Zalecamy, aby usługi gRPC zabezpieczone przez certyfikaty klienta korzystały z pakietu [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) .</span><span class="sxs-lookup"><span data-stu-id="d091a-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="d091a-132">ASP.NET Core uwierzytelnianie certyfikacji będzie przeprowadzać dodatkowe sprawdzanie poprawności certyfikatu klienta, w tym:</span><span class="sxs-lookup"><span data-stu-id="d091a-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="d091a-133">Certyfikat ma prawidłowe rozszerzone użycie klucza (EKU)</span><span class="sxs-lookup"><span data-stu-id="d091a-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="d091a-134">Jest w jego okresie ważności</span><span class="sxs-lookup"><span data-stu-id="d091a-134">Is within its validity period</span></span>
* <span data-ttu-id="d091a-135">Sprawdź odwołanie certyfikatu</span><span class="sxs-lookup"><span data-stu-id="d091a-135">Check certificate revocation</span></span>
