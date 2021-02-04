---
title: gRPC na platformach obsługiwanych przez platformę .NET
author: jamesnk
description: Dowiedz się więcej na temat obsługiwanych platform for gRPC na platformie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530167"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="436df-103">gRPC na platformach obsługiwanych przez platformę .NET</span><span class="sxs-lookup"><span data-stu-id="436df-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="436df-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="436df-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="436df-105">W tym artykule omówiono wymagania i obsługiwane platformy dotyczące używania gRPC z platformą .NET.</span><span class="sxs-lookup"><span data-stu-id="436df-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="436df-106">gRPC wykorzystuje zaawansowane funkcje dostępne w protokole HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="436df-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="436df-107">Protokół HTTP/2 nie jest obsługiwany wszędzie, ale dla gRPC jest dostępny drugi format sieci z protokołem HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="436df-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="436df-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC za pośrednictwem protokołu HTTP/2 to sposób, w jaki gRPC jest zazwyczaj używany.</span><span class="sxs-lookup"><span data-stu-id="436df-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="436df-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modyfikuje protokół gRPC, aby był zgodny z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="436df-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="436df-110">gRPC — sieć Web może być używana w większej liczbie miejsc, w szczególności jest wywoływana przez aplikacje przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="436df-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="436df-111">Dwie zaawansowane funkcje gRPC nie są już obsługiwane: przesyłanie strumieniowe klientów i dwukierunkowe przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="436df-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="436df-112">gRPC na platformie .NET obsługuje obydwa formaty.</span><span class="sxs-lookup"><span data-stu-id="436df-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="436df-113">gRPC za pośrednictwem protokołu HTTP/2 jest używany domyślnie.</span><span class="sxs-lookup"><span data-stu-id="436df-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="436df-114">Aby uzyskać informacje na temat konfigurowania usługi gRPC-Web, zobacz <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="436df-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="436df-115">Wymagania dotyczące urządzeń</span><span class="sxs-lookup"><span data-stu-id="436df-115">Device requirements</span></span>

<span data-ttu-id="436df-116">gRPC na platformie .NET obsługuje dowolne urządzenie obsługiwane przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="436df-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="436df-117">Windows</span><span class="sxs-lookup"><span data-stu-id="436df-117">Windows</span></span>
> * <span data-ttu-id="436df-118">Linux</span><span class="sxs-lookup"><span data-stu-id="436df-118">Linux</span></span>
> * <span data-ttu-id="436df-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="436df-119">macOS&dagger;</span></span>
> * <span data-ttu-id="436df-120">Przeglądarki&Dagger;</span><span class="sxs-lookup"><span data-stu-id="436df-120">Browsers&Dagger;</span></span>

<span data-ttu-id="436df-121">&dagger;[macOS nie obsługuje hostingu aplikacji ASP.NET Core przy użyciu protokołu HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="436df-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="436df-122">Klienci gRPC na macOS mogą wywoływać usługi zdalne korzystające z protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="436df-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="436df-123">&Dagger;Blazor WebAssembly aplikacje mogą wywoływać usługi gRPC Services za pomocą gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="436df-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="436df-124">Wymagania dotyczące serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="436df-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="436df-125">usługi gRPC Services mogą być hostowane na wszystkich wbudowanych serwerach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="436df-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="436df-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="436df-126">Kestrel</span></span>
> * <span data-ttu-id="436df-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="436df-127">TestServer</span></span>
> * <span data-ttu-id="436df-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="436df-128">IIS&dagger;</span></span>
> * <span data-ttu-id="436df-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="436df-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="436df-130">&dagger;Usługi IIS wymagają programu .NET 5 i systemu Windows 10 Build 20241 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="436df-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="436df-131">&Dagger;HTTP.sys wymaga programu .NET 5 i systemu Windows 10 Build 19529 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="436df-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="436df-132">Aby uzyskać informacje dotyczące konfigurowania serwerów ASP.NET Core do uruchamiania gRPC, zobacz <xref:grpc/aspnetcore#server-options> .</span><span class="sxs-lookup"><span data-stu-id="436df-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="436df-133">Wymagania dotyczące wersji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="436df-133">.NET version requirements</span></span>

<span data-ttu-id="436df-134">gRPC na platformie .NET obsługuje programy .NET Core 3 i .NET 5 lub nowsze.</span><span class="sxs-lookup"><span data-stu-id="436df-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="436df-135">.NET 5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="436df-135">.NET 5 or later</span></span>
> * <span data-ttu-id="436df-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="436df-136">.NET Core 3</span></span>

<span data-ttu-id="436df-137">Program gRPC na platformie .NET nie obsługuje uruchamiania w systemach .NET Framework i Xamarin.</span><span class="sxs-lookup"><span data-stu-id="436df-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="436df-138">[GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) to biblioteka innej firmy, która obsługuje .NET Framework i Xamarin.</span><span class="sxs-lookup"><span data-stu-id="436df-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="436df-139">gRPC C-Core nie jest obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="436df-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="436df-140">Usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="436df-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="436df-141">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="436df-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="436df-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="436df-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="436df-143">&dagger;Azure App Service nie obsługuje hostingu gRPC za pośrednictwem protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="436df-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="436df-144">gRPC-Web jest zgodną alternatywą.</span><span class="sxs-lookup"><span data-stu-id="436df-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="436df-145">Działa w toku, aby ulepszyć obsługę gRPC przy użyciu protokołu HTTP/2 w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="436df-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="436df-146">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/9020)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="436df-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="436df-147">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="436df-147">Additional resources</span></span>

* [<span data-ttu-id="436df-148">gRPC C# — Biblioteka</span><span class="sxs-lookup"><span data-stu-id="436df-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
