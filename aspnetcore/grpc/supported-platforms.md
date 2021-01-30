---
title: gRPC for .NET — obsługiwane platformy
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
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057716"
---
# <a name="grpc-for-net-supported-platforms"></a><span data-ttu-id="6e468-103">gRPC for .NET — obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="6e468-103">gRPC for .NET supported platforms</span></span>

<span data-ttu-id="6e468-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6e468-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6e468-105">W tym artykule omówiono wymagania i obsługiwane platformy dotyczące używania gRPC z platformą .NET.</span><span class="sxs-lookup"><span data-stu-id="6e468-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="6e468-106">gRPC jest przeznaczony do używania protokołu HTTP/2 w przypadku niektórych bardziej zaawansowanych funkcji.</span><span class="sxs-lookup"><span data-stu-id="6e468-106">gRPC is designed to use HTTP/2 for some of its more advanced features.</span></span> <span data-ttu-id="6e468-107">Protokół HTTP/2 nie jest obsługiwany wszędzie, co może uniemożliwić korzystanie z gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e468-107">HTTP/2 isn't supported everywhere which can prevent using gRPC.</span></span> <span data-ttu-id="6e468-108">Ze względu na to, że istnieje inny format okablowania zgodny z protokołem HTTP/1.1 do wysyłania wywołań gRPC między klientami a serwerami:</span><span class="sxs-lookup"><span data-stu-id="6e468-108">Because of this there is second wire-format that is compatible with HTTP/1.1 for sending gRPC calls between clients and servers:</span></span>

* <span data-ttu-id="6e468-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC za pośrednictwem protokołu HTTP/2 to sposób, w jaki gRPC jest zazwyczaj używany.</span><span class="sxs-lookup"><span data-stu-id="6e468-109">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="6e468-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modyfikuje protokół gRPC, aby był zgodny z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6e468-110">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="6e468-111">gRPC — sieć Web może być używana w większej liczbie miejsc, w szczególności jest wywoływana przez aplikacje przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="6e468-111">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="6e468-112">Dwie zaawansowane funkcje gRPC nie są już obsługiwane: przesyłanie strumieniowe klientów i dwukierunkowe przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="6e468-112">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="6e468-113">Program gRPC for .NET obsługuje zarówno formaty przewodowe.</span><span class="sxs-lookup"><span data-stu-id="6e468-113">gRPC for .NET supports both wire-formats.</span></span> <span data-ttu-id="6e468-114">Aby uzyskać informacje na temat konfigurowania usługi gRPC-Web, zobacz <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="6e468-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="6e468-115">Wymagania dotyczące urządzeń</span><span class="sxs-lookup"><span data-stu-id="6e468-115">Device requirements</span></span>

<span data-ttu-id="6e468-116">Program gRPC for .NET obsługuje dowolne urządzenie obsługiwane przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6e468-116">gRPC for .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="6e468-117">Windows</span><span class="sxs-lookup"><span data-stu-id="6e468-117">Windows</span></span>
> * <span data-ttu-id="6e468-118">Linux</span><span class="sxs-lookup"><span data-stu-id="6e468-118">Linux</span></span>
> * <span data-ttu-id="6e468-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e468-119">macOS&dagger;</span></span>
> * <span data-ttu-id="6e468-120">Przeglądarki&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6e468-120">Browsers&Dagger;</span></span>

<span data-ttu-id="6e468-121">&dagger;Aplikacje ASP.NET Core hostowane w usłudze macOS nie obsługują protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6e468-121">&dagger;ASP.NET Core apps hosted on macOS don't support HTTPS.</span></span> <span data-ttu-id="6e468-122">Klienci gRPC na macOS mogą nadal korzystać z protokołu HTTPS podczas wywoływania usług zdalnych.</span><span class="sxs-lookup"><span data-stu-id="6e468-122">gRPC clients on macOS can still use HTTPS when calling remote services.</span></span>

<span data-ttu-id="6e468-123">&Dagger;Blazor WebAssembly aplikacje mogą wywoływać usługi gRPC Services za pomocą gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6e468-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="6e468-124">Wymagania dotyczące serwera ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e468-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="6e468-125">usługi gRPC Services mogą być hostowane na wszystkich wbudowanych serwerach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6e468-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="6e468-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="6e468-126">Kestrel</span></span>
> * <span data-ttu-id="6e468-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="6e468-127">TestServer</span></span>
> * <span data-ttu-id="6e468-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e468-128">IIS&dagger;</span></span>
> * <span data-ttu-id="6e468-129">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e468-129">HTTP.sys&dagger;</span></span>

<span data-ttu-id="6e468-130">&dagger;Usługi IIS i HTTP.sys wymagają programu .NET 5 i systemu Windows 10 Build 20241 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="6e468-130">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="6e468-131">Aby uzyskać więcej informacji, zobacz <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="6e468-131">For more information, see <xref:grpc/aspnetcore>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="6e468-132">Wymagania dotyczące wersji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="6e468-132">.NET version requirements</span></span>

<span data-ttu-id="6e468-133">Program gRPC for .NET obsługuje programy .NET Core 3 i .NET 5 lub nowsze.</span><span class="sxs-lookup"><span data-stu-id="6e468-133">gRPC for .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="6e468-134">.NET 5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="6e468-134">.NET 5 or later</span></span>
> * <span data-ttu-id="6e468-135">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="6e468-135">.NET Core 3</span></span>

<span data-ttu-id="6e468-136">Program gRPC for .NET nie obsługuje uruchamiania w systemach .NET Framework i Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6e468-136">gRPC for .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="6e468-137">[GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) to biblioteka innej firmy, która obsługuje .NET Framework i Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6e468-137">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="6e468-138">gRPC C-Core nie jest obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6e468-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="6e468-139">Usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="6e468-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="6e468-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="6e468-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="6e468-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="6e468-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="6e468-142">&dagger;Azure App Service nie obsługuje hostingu gRPC za pośrednictwem protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6e468-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="6e468-143">gRPC-Web jest zgodną alternatywą.</span><span class="sxs-lookup"><span data-stu-id="6e468-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="6e468-144">Działa w toku, aby ulepszyć obsługę gRPC przy użyciu protokołu HTTP/2 w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="6e468-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="6e468-145">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/9020)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="6e468-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e468-146">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="6e468-146">Additional resources</span></span>

* [<span data-ttu-id="6e468-147">gRPC C# — Biblioteka</span><span class="sxs-lookup"><span data-stu-id="6e468-147">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
