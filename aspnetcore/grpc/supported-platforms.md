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
# <a name="grpc-for-net-supported-platforms"></a>gRPC for .NET — obsługiwane platformy

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

W tym artykule omówiono wymagania i obsługiwane platformy dotyczące używania gRPC z platformą .NET.

gRPC jest przeznaczony do używania protokołu HTTP/2 w przypadku niektórych bardziej zaawansowanych funkcji. Protokół HTTP/2 nie jest obsługiwany wszędzie, co może uniemożliwić korzystanie z gRPC. Ze względu na to, że istnieje inny format okablowania zgodny z protokołem HTTP/1.1 do wysyłania wywołań gRPC między klientami a serwerami:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC za pośrednictwem protokołu HTTP/2 to sposób, w jaki gRPC jest zazwyczaj używany.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modyfikuje protokół gRPC, aby był zgodny z protokołem HTTP/1.1. gRPC — sieć Web może być używana w większej liczbie miejsc, w szczególności jest wywoływana przez aplikacje przeglądarki. Dwie zaawansowane funkcje gRPC nie są już obsługiwane: przesyłanie strumieniowe klientów i dwukierunkowe przesyłanie strumieniowe.

Program gRPC for .NET obsługuje zarówno formaty przewodowe. Aby uzyskać informacje na temat konfigurowania usługi gRPC-Web, zobacz <xref:grpc/browser> .

## <a name="device-requirements"></a>Wymagania dotyczące urządzeń

Program gRPC for .NET obsługuje dowolne urządzenie obsługiwane przez platformę .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Przeglądarki&Dagger;

&dagger;Aplikacje ASP.NET Core hostowane w usłudze macOS nie obsługują protokołu HTTPS. Klienci gRPC na macOS mogą nadal korzystać z protokołu HTTPS podczas wywoływania usług zdalnych.

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

Program gRPC for .NET obsługuje programy .NET Core 3 i .NET 5 lub nowsze.

> [!div class="checklist"]
>
> * .NET 5 lub nowszy
> * .NET Core 3

Program gRPC for .NET nie obsługuje uruchamiania w systemach .NET Framework i Xamarin. [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) to biblioteka innej firmy, która obsługuje .NET Framework i Xamarin. gRPC C-Core nie jest obsługiwane przez firmę Microsoft.

## <a name="azure-services"></a>Usługi platformy Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service nie obsługuje hostingu gRPC za pośrednictwem protokołu HTTP/2. gRPC-Web jest zgodną alternatywą.

Działa w toku, aby ulepszyć obsługę gRPC przy użyciu protokołu HTTP/2 w Azure App Service. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/9020)w serwisie GitHub.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [gRPC C# — Biblioteka](https://grpc.io/docs/languages/csharp/quickstart/)
