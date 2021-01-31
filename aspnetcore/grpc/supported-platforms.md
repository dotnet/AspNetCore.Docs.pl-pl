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
ms.openlocfilehash: 88d371f460839261b618a32564a723c257b0b119
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217495"
---
# <a name="grpc-on-net-supported-platforms"></a>gRPC na platformach obsługiwanych przez platformę .NET

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

W tym artykule omówiono wymagania i obsługiwane platformy dotyczące używania gRPC z platformą .NET.

gRPC wykorzystuje zaawansowane funkcje dostępne w protokole HTTP/2. Protokół HTTP/2 nie jest obsługiwany wszędzie, ale dla gRPC jest dostępny drugi format sieci z protokołem HTTP/1.1:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC za pośrednictwem protokołu HTTP/2 to sposób, w jaki gRPC jest zazwyczaj używany.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modyfikuje protokół gRPC, aby był zgodny z protokołem HTTP/1.1. gRPC — sieć Web może być używana w większej liczbie miejsc, w szczególności jest wywoływana przez aplikacje przeglądarki. Dwie zaawansowane funkcje gRPC nie są już obsługiwane: przesyłanie strumieniowe klientów i dwukierunkowe przesyłanie strumieniowe.

gRPC na platformie .NET obsługuje obydwa formaty. gRPC za pośrednictwem protokołu HTTP/2 jest używany domyślnie. Aby uzyskać informacje na temat konfigurowania usługi gRPC-Web, zobacz <xref:grpc/browser> .

## <a name="device-requirements"></a>Wymagania dotyczące urządzeń

gRPC na platformie .NET obsługuje dowolne urządzenie obsługiwane przez platformę .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Przeglądarki&Dagger;

&dagger;[macOS nie obsługuje hostingu aplikacji ASP.NET Core przy użyciu protokołu HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos). Klienci gRPC na macOS mogą wywoływać usługi zdalne korzystające z protokołu HTTPS.

&Dagger;Blazor WebAssembly aplikacje mogą wywoływać usługi gRPC Services za pomocą gRPC-Web.

## <a name="aspnet-core-server-requirements"></a>Wymagania dotyczące serwera ASP.NET Core

usługi gRPC Services mogą być hostowane na wszystkich wbudowanych serwerach ASP.NET Core.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&dagger;

&dagger;Usługi IIS i HTTP.sys wymagają programu .NET 5 i systemu Windows 10 Build 20241 lub nowszego.

Aby uzyskać więcej informacji, zobacz <xref:grpc/aspnetcore>.

## <a name="net-version-requirements"></a>Wymagania dotyczące wersji platformy .NET

gRPC na platformie .NET obsługuje programy .NET Core 3 i .NET 5 lub nowsze.

> [!div class="checklist"]
>
> * .NET 5 lub nowszy
> * .NET Core 3

Program gRPC na platformie .NET nie obsługuje uruchamiania w systemach .NET Framework i Xamarin. [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) to biblioteka innej firmy, która obsługuje .NET Framework i Xamarin. gRPC C-Core nie jest obsługiwane przez firmę Microsoft.

## <a name="azure-services"></a>Usługi platformy Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service nie obsługuje hostingu gRPC za pośrednictwem protokołu HTTP/2. gRPC-Web jest zgodną alternatywą.

Działa w toku, aby ulepszyć obsługę gRPC przy użyciu protokołu HTTP/2 w Azure App Service. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/9020)w serwisie GitHub.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [gRPC C# — Biblioteka](https://grpc.io/docs/languages/csharp/quickstart/)
