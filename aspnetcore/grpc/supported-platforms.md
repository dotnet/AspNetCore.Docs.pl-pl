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
# <a name="grpc-on-net-supported-platforms"></a>gRPC na platformach obsługiwanych przez platformę .NET

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

W tym artykule omówiono wymagania i obsługiwane platformy dotyczące używania gRPC z platformą .NET. Istnieją różne wymagania dotyczące dwóch głównych obciążeń gRPC:

* [Hosting usług gRPC Services w ASP.NET Core](#aspnet-core-grpc-server-requirements)
* [Wywoływanie gRPC z aplikacji klienckich platformy .NET](#net-grpc-client-requirements)

## <a name="wire-formats"></a>Formaty druciane

gRPC wykorzystuje zaawansowane funkcje dostępne w protokole HTTP/2. Protokół HTTP/2 nie jest obsługiwany wszędzie, ale dla gRPC jest dostępny drugi format sieci z protokołem HTTP/1.1:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC za pośrednictwem protokołu HTTP/2 to sposób, w jaki gRPC jest zazwyczaj używany.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web modyfikuje protokół gRPC, aby był zgodny z protokołem HTTP/1.1. gRPC — sieć Web może być używana w większej liczbie miejsc. gRPC — sieć Web może być używana przez aplikacje przeglądarki i w sieciach bez pełnej obsługi protokołu HTTP/2. Dwie zaawansowane funkcje gRPC nie są już obsługiwane: przesyłanie strumieniowe klientów i dwukierunkowe przesyłanie strumieniowe.

gRPC na platformie .NET obsługuje obydwa formaty. `application/grpc` jest używana domyślnie. gRPC — sieć Web musi być skonfigurowana na kliencie i na serwerze w celu pomyślnego wywołania gRPC-sieci Web. Aby uzyskać informacje na temat konfigurowania usługi gRPC-Web, zobacz <xref:grpc/browser> .

## <a name="aspnet-core-grpc-server-requirements"></a>ASP.NET Core wymagania dotyczące serwera gRPC

Hosting usług gRPC Services przy użyciu ASP.NET Core wymaga platformy .NET Core 3. x lub nowszej.

> [!div class="checklist"]
>
> * .NET 5 lub nowszy
> * .NET Core 3

Usługi ASP.NET Core gRPC Services mogą być hostowane we wszystkich systemach operacyjnych obsługiwanych przez platformę .NET Core.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;

&dagger;[macOS nie obsługuje hostingu aplikacji ASP.NET Core przy użyciu protokołu HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="supported-aspnet-core-servers"></a>Obsługiwane serwery ASP.NET Core

Obsługiwane są wszystkie wbudowane serwery ASP.NET Core.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;Usługi IIS wymagają programu .NET 5 i systemu Windows 10 Build 20241 lub nowszego.

&Dagger;HTTP.sys wymaga programu .NET 5 i systemu Windows 10 Build 19529 lub nowszego.

Aby uzyskać informacje dotyczące konfigurowania serwerów ASP.NET Core do uruchamiania gRPC, zobacz <xref:grpc/aspnetcore#server-options> .

### <a name="azure-services"></a>Usługi platformy Azure

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service nie obsługuje hostingu gRPC za pośrednictwem protokołu HTTP/2. gRPC-Web jest zgodną alternatywą.

Działa w toku, aby ulepszyć obsługę gRPC przy użyciu protokołu HTTP/2 w Azure App Service. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/9020)w serwisie GitHub.

## <a name="net-grpc-client-requirements"></a>Wymagania dotyczące klienta .NET gRPC

Pakiet [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client/) obsługuje wywołania GRPC za pośrednictwem protokołu HTTP/2 w przypadku programów .NET Core 3 i .NET 5 lub nowszych.

Ograniczona pomoc techniczna jest dostępna dla gRPC za pośrednictwem protokołu HTTP/2 na .NET Framework. Inne wersje platformy .NET, takie jak platformy UWP, Xamarin i Unity, nie mają wymaganej obsługi protokołu HTTP/2 i w zamian muszą używać gRPC-Web.

W poniższej tabeli przedstawiono implementacje platformy .NET i ich obsługę klienta gRPC:

| Implementacja platformy .NET                          | gRPC za pośrednictwem protokołu HTTP/2   | gRPC — sieć Web   |
|----------------------------------------------|--------------------|------------|
| .NET 5 lub nowszy                              | ✔️                | ✔️         |
| .NET Core 3                                  | ✔️                | ✔️         |
| .NET Core 2.1                                | ❌                | ✔️         |
| .NET Framework 4.6.1                         | ⚠️&dagger;        | ✔️         |
| Blazor WebAssembly                           | ❌                | ✔️         |
| Mono 5,4                                     | ❌                | ✔️         |
| Platforma Xamarin. iOS 10,14                            | ❌                | ✔️         |
| Xamarin. Android 8,0                          | ❌                | ✔️         |
| Platforma uniwersalna systemu Windows 10.0.16299        | ❌                | ✔️         |
| Unity 2018,1                                 | ❌                | ✔️         |

&dagger;<xref:System.Net.Http.WinHttpHandler>Należy skonfigurować .NET Framework i system Windows 10 Build 19622 lub nowszy.

Używanie `Grpc.Net.Client` na .NET Framework lub z gRPC-Web wymaga dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:grpc/netstandard>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/netstandard>
* [gRPC C# — Biblioteka](https://grpc.io/docs/languages/csharp/quickstart/)
