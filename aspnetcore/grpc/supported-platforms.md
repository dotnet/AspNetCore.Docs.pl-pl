---
title: gRPC na platformach obsługiwanych przez platformę .NET
author: jamesnk
description: Dowiedz się więcej na temat obsługiwanych platform for gRPC na platformie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
ms.openlocfilehash: c2bd808d16f11077e39aada829d79e8aedf2755b
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413421"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="a405b-103">gRPC na platformach obsługiwanych przez platformę .NET</span><span class="sxs-lookup"><span data-stu-id="a405b-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="a405b-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a405b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a405b-105">W tym artykule omówiono wymagania i obsługiwane platformy dotyczące używania gRPC z platformą .NET.</span><span class="sxs-lookup"><span data-stu-id="a405b-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span> <span data-ttu-id="a405b-106">Istnieją różne wymagania dotyczące dwóch głównych obciążeń gRPC:</span><span class="sxs-lookup"><span data-stu-id="a405b-106">There are different requirements for the two major gRPC workloads:</span></span>

* [<span data-ttu-id="a405b-107">Hosting usług gRPC Services w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a405b-107">Hosting gRPC services in ASP.NET Core</span></span>](#aspnet-core-grpc-server-requirements)
* [<span data-ttu-id="a405b-108">Wywoływanie gRPC z aplikacji klienckich platformy .NET</span><span class="sxs-lookup"><span data-stu-id="a405b-108">Calling gRPC from .NET client apps</span></span>](#net-grpc-client-requirements)

## <a name="wire-formats"></a><span data-ttu-id="a405b-109">Formaty druciane</span><span class="sxs-lookup"><span data-stu-id="a405b-109">Wire-formats</span></span>

<span data-ttu-id="a405b-110">gRPC wykorzystuje zaawansowane funkcje dostępne w protokole HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="a405b-110">gRPC takes advantage of advanced features available in HTTP/2.</span></span> <span data-ttu-id="a405b-111">Protokół HTTP/2 nie jest obsługiwany wszędzie, ale dla gRPC jest dostępny drugi format sieci z protokołem HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="a405b-111">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="a405b-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC za pośrednictwem protokołu HTTP/2 to sposób, w jaki gRPC jest zazwyczaj używany.</span><span class="sxs-lookup"><span data-stu-id="a405b-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="a405b-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modyfikuje protokół gRPC, aby był zgodny z protokołem HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="a405b-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="a405b-114">gRPC — sieć Web może być używana w większej liczbie miejsc.</span><span class="sxs-lookup"><span data-stu-id="a405b-114">gRPC-Web can be used in more places.</span></span> <span data-ttu-id="a405b-115">gRPC — sieć Web może być używana przez aplikacje przeglądarki i w sieciach bez pełnej obsługi protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="a405b-115">gRPC-Web can be used by browser apps and in networks without complete support for HTTP/2.</span></span> <span data-ttu-id="a405b-116">Dwie zaawansowane funkcje gRPC nie są już obsługiwane: przesyłanie strumieniowe klientów i dwukierunkowe przesyłanie strumieniowe.</span><span class="sxs-lookup"><span data-stu-id="a405b-116">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="a405b-117">gRPC na platformie .NET obsługuje obydwa formaty.</span><span class="sxs-lookup"><span data-stu-id="a405b-117">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="a405b-118">`application/grpc` jest używana domyślnie.</span><span class="sxs-lookup"><span data-stu-id="a405b-118">`application/grpc` is used by default.</span></span> <span data-ttu-id="a405b-119">gRPC — sieć Web musi być skonfigurowana na kliencie i na serwerze w celu pomyślnego wywołania gRPC-sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a405b-119">gRPC-Web must be configured on the client and the server for successful gRPC-Web calls.</span></span> <span data-ttu-id="a405b-120">Aby uzyskać informacje na temat konfigurowania usługi gRPC-Web, zobacz <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="a405b-120">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="aspnet-core-grpc-server-requirements"></a><span data-ttu-id="a405b-121">ASP.NET Core wymagania dotyczące serwera gRPC</span><span class="sxs-lookup"><span data-stu-id="a405b-121">ASP.NET Core gRPC server requirements</span></span>

<span data-ttu-id="a405b-122">Hosting usług gRPC Services przy użyciu ASP.NET Core wymaga platformy .NET Core 3. x lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="a405b-122">Hosting gRPC services with ASP.NET Core requires .NET Core 3.x or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="a405b-123">.NET 5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="a405b-123">.NET 5 or later</span></span>
> * <span data-ttu-id="a405b-124">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="a405b-124">.NET Core 3</span></span>

<span data-ttu-id="a405b-125">Usługi ASP.NET Core gRPC Services mogą być hostowane we wszystkich systemach operacyjnych obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a405b-125">ASP.NET Core gRPC services can be hosted on all operating system that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="a405b-126">Windows</span><span class="sxs-lookup"><span data-stu-id="a405b-126">Windows</span></span>
> * <span data-ttu-id="a405b-127">Linux</span><span class="sxs-lookup"><span data-stu-id="a405b-127">Linux</span></span>
> * <span data-ttu-id="a405b-128">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="a405b-128">macOS&dagger;</span></span>

<span data-ttu-id="a405b-129">&dagger;[macOS nie obsługuje hostingu aplikacji ASP.NET Core przy użyciu protokołu HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="a405b-129">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="supported-aspnet-core-servers"></a><span data-ttu-id="a405b-130">Obsługiwane serwery ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a405b-130">Supported ASP.NET Core servers</span></span>

<span data-ttu-id="a405b-131">Obsługiwane są wszystkie wbudowane serwery ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a405b-131">All built-in ASP.NET Core servers are supported.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="a405b-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="a405b-132">Kestrel</span></span>
> * <span data-ttu-id="a405b-133">TestServer</span><span class="sxs-lookup"><span data-stu-id="a405b-133">TestServer</span></span>
> * <span data-ttu-id="a405b-134">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="a405b-134">IIS&dagger;</span></span>
> * <span data-ttu-id="a405b-135">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="a405b-135">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="a405b-136">&dagger;Usługi IIS wymagają programu .NET 5 i systemu Windows 10 Build 20241 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="a405b-136">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="a405b-137">&Dagger;HTTP.sys wymaga programu .NET 5 i systemu Windows 10 Build 19529 lub nowszego.</span><span class="sxs-lookup"><span data-stu-id="a405b-137">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="a405b-138">Aby uzyskać informacje dotyczące konfigurowania serwerów ASP.NET Core do uruchamiania gRPC, zobacz <xref:grpc/aspnetcore#server-options> .</span><span class="sxs-lookup"><span data-stu-id="a405b-138">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

### <a name="azure-services"></a><span data-ttu-id="a405b-139">Usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="a405b-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="a405b-140">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="a405b-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="a405b-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="a405b-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="a405b-142">&dagger;Azure App Service nie obsługuje hostingu gRPC za pośrednictwem protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="a405b-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="a405b-143">gRPC-Web jest zgodną alternatywą.</span><span class="sxs-lookup"><span data-stu-id="a405b-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="a405b-144">Działa w toku, aby ulepszyć obsługę gRPC przy użyciu protokołu HTTP/2 w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a405b-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="a405b-145">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/9020)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="a405b-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="net-grpc-client-requirements"></a><span data-ttu-id="a405b-146">Wymagania dotyczące klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="a405b-146">.NET gRPC client requirements</span></span>

<span data-ttu-id="a405b-147">Pakiet [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client/) obsługuje wywołania GRPC za pośrednictwem protokołu HTTP/2 w przypadku programów .NET Core 3 i .NET 5 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="a405b-147">The [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) package supports gRPC calls over HTTP/2 on .NET Core 3 and .NET 5 or later.</span></span>

<span data-ttu-id="a405b-148">Ograniczona pomoc techniczna jest dostępna dla gRPC za pośrednictwem protokołu HTTP/2 na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a405b-148">Limited support is available for gRPC over HTTP/2 on .NET Framework.</span></span> <span data-ttu-id="a405b-149">Inne wersje platformy .NET, takie jak platformy UWP, Xamarin i Unity, nie mają wymaganej obsługi protokołu HTTP/2 i w zamian muszą używać gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a405b-149">Other .NET versions such as UWP, Xamarin and Unity don't have required HTTP/2 support, and must use gRPC-Web instead.</span></span>

<span data-ttu-id="a405b-150">W poniższej tabeli przedstawiono implementacje platformy .NET i ich obsługę klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="a405b-150">The following table lists .NET implementations and their gRPC client support:</span></span>

| <span data-ttu-id="a405b-151">Implementacja platformy .NET</span><span class="sxs-lookup"><span data-stu-id="a405b-151">.NET implementation</span></span>                          | <span data-ttu-id="a405b-152">gRPC za pośrednictwem protokołu HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a405b-152">gRPC over HTTP/2</span></span>   | <span data-ttu-id="a405b-153">gRPC — sieć Web</span><span class="sxs-lookup"><span data-stu-id="a405b-153">gRPC-Web</span></span>   |
|----------------------------------------------|--------------------|------------|
| <span data-ttu-id="a405b-154">.NET 5 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="a405b-154">.NET 5 or later</span></span>                              | <span data-ttu-id="a405b-155">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-155">✔️</span></span>                | <span data-ttu-id="a405b-156">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-156">✔️</span></span>         |
| <span data-ttu-id="a405b-157">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="a405b-157">.NET Core 3</span></span>                                  | <span data-ttu-id="a405b-158">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-158">✔️</span></span>                | <span data-ttu-id="a405b-159">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-159">✔️</span></span>         |
| <span data-ttu-id="a405b-160">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="a405b-160">.NET Core 2.1</span></span>                                | ❌                | <span data-ttu-id="a405b-161">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-161">✔️</span></span>         |
| <span data-ttu-id="a405b-162">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="a405b-162">.NET Framework 4.6.1</span></span>                         | ⚠️&dagger;        | <span data-ttu-id="a405b-163">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-163">✔️</span></span>         |
| Blazor WebAssembly                           | ❌                | <span data-ttu-id="a405b-164">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-164">✔️</span></span>         |
| <span data-ttu-id="a405b-165">Mono 5,4</span><span class="sxs-lookup"><span data-stu-id="a405b-165">Mono 5.4</span></span>                                     | ❌                | <span data-ttu-id="a405b-166">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-166">✔️</span></span>         |
| <span data-ttu-id="a405b-167">Platforma Xamarin. iOS 10,14</span><span class="sxs-lookup"><span data-stu-id="a405b-167">Xamarin.iOS 10.14</span></span>                            | ❌                | <span data-ttu-id="a405b-168">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-168">✔️</span></span>         |
| <span data-ttu-id="a405b-169">Xamarin. Android 8,0</span><span class="sxs-lookup"><span data-stu-id="a405b-169">Xamarin.Android 8.0</span></span>                          | ❌                | <span data-ttu-id="a405b-170">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-170">✔️</span></span>         |
| <span data-ttu-id="a405b-171">Platforma uniwersalna systemu Windows 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="a405b-171">Universal Windows Platform 10.0.16299</span></span>        | ❌                | <span data-ttu-id="a405b-172">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-172">✔️</span></span>         |
| <span data-ttu-id="a405b-173">Unity 2018,1</span><span class="sxs-lookup"><span data-stu-id="a405b-173">Unity 2018.1</span></span>                                 | ❌                | <span data-ttu-id="a405b-174">✔️</span><span class="sxs-lookup"><span data-stu-id="a405b-174">✔️</span></span>         |

<span data-ttu-id="a405b-175">&dagger;<xref:System.Net.Http.WinHttpHandler>Należy skonfigurować .NET Framework i system Windows 10 Build 19622 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="a405b-175">&dagger;.NET Framework requires <xref:System.Net.Http.WinHttpHandler> to be configured and Windows 10 Build 19622 or later.</span></span>

<span data-ttu-id="a405b-176">Używanie `Grpc.Net.Client` na .NET Framework lub z gRPC-Web wymaga dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a405b-176">Using `Grpc.Net.Client` on .NET Framework or with gRPC-Web requires additional configuration.</span></span> <span data-ttu-id="a405b-177">Aby uzyskać więcej informacji, zobacz <xref:grpc/netstandard>.</span><span class="sxs-lookup"><span data-stu-id="a405b-177">For more information, see <xref:grpc/netstandard>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a405b-178">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a405b-178">Additional resources</span></span>

* <xref:grpc/netstandard>
* [<span data-ttu-id="a405b-179">gRPC C# — Biblioteka</span><span class="sxs-lookup"><span data-stu-id="a405b-179">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
