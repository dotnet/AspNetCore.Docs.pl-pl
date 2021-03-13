---
title: Używanie klienta gRPC z .NET Standard 2,0
author: jamesnk
description: Dowiedz się, jak używać klienta .NET gRPC w aplikacjach i bibliotekach, które obsługują .NET Standard 2,0.
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
uid: grpc/netstandard
ms.openlocfilehash: a6b066979dcdcdf648b8b0326bef47fe0e466266
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103422502"
---
# <a name="use-grpc-client-with-net-standard-20"></a>Używanie klienta gRPC z .NET Standard 2,0

Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)

W tym artykule omówiono sposób używania klienta .NET gRPC z implementacjami platformy .NET, które obsługują [.NET Standard 2,0](/dotnet/standard/net-standard).

## <a name="net-implementations"></a>Implementacje platformy .NET

Następujące implementacje platformy .NET (lub nowsze) obsługują [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client/) , ale nie mają pełnej obsługi protokołu HTTP/2:

* .NET Core 2.1
* .NET Framework 4.6.1
* Mono 5,4
* Platforma Xamarin. iOS 10,14
* Xamarin. Android 8,0
* Platforma uniwersalna systemu Windows 10.0.16299
* Unity 2018,1

Klient .NET gRPC może wywoływać usługi z tych implementacji platformy .NET z dodatkową konfiguracją.

## <a name="httphandler-configuration"></a>Konfiguracja HttpHandler

Dostawca protokołu HTTP musi być skonfigurowany przy użyciu programu `GrpcChannelOptions.HttpHandler` . Jeśli program obsługi nie jest skonfigurowany, zostanie zgłoszony błąd:

> `System.PlatformNotSupportedException`: gRPC wymaga dodatkowej konfiguracji, aby pomyślnie wykonywać wywołania RPC w implementacjach platformy .NET, które nie obsługują gRPC za pośrednictwem protokołu HTTP/2. Dostawca HTTP musi być określony przy użyciu `GrpcChannelOptions.HttpHandler` . Skonfigurowany dostawca HTTP musi obsługiwać protokół HTTP/2 lub być skonfigurowany do korzystania z programu gRPC-Web.

Implementacje platformy .NET, które nie obsługują protokołu HTTP/2, takie jak platformy UWP, Xamarin i Unity, mogą używać funkcji gRPC-Web jako alternatywy.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

Aby uzyskać więcej informacji, zobacz [Configure gRPC-Web with the .NET gRPC Client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).

## <a name="net-framework"></a>.NET Framework

.NET Framework ma ograniczoną obsługę gRPC za pośrednictwem protokołu HTTP/2. Aby włączyć gRPC za pośrednictwem protokołu HTTP/2 na .NET Framework, skonfiguruj kanał do użycia <xref:System.Net.Http.WinHttpHandler> .

Wymagania i ograniczenia dotyczące korzystania z programu `WinHttpHandler` :

* Windows 10 kompilacja 19622 lub nowsza.
* Odwołanie do pakietu NuGet [systemu .NET. http. WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) .
* Obsługiwane są tylko wywołania wieloargumentowe i gRPC przesyłania strumieniowego serwera.
* Obsługiwane są tylko wywołania gRPC przez protokół TLS.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> Obsługa .NET Framework jest na wczesnych etapach i wymaga użycia oprogramowania w wersji wstępnej.
> * System Windows 10 Build 19622 lub nowszy jest dostępny jako kompilacja [niejawnych testerów systemu Windows](https://insider.windows.com/) .
> * Wymagana wersja programu `System.Net.Http.WinHttpHandler` nie jest obecnie dostępna w NuGet.org. Najnowsza wersja wstępna jest dostępna w [tym źródle danych NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) .

## <a name="grpc-c-core-library"></a>gRPC C# — Biblioteka

Alternatywna opcja dla .NET Framework i Xamarin polega na użyciu [biblioteki GRPC C# Core](https://grpc.io/docs/languages/csharp/quickstart/) do wykonywania wywołań gRPC. Jest to biblioteka innej firmy, która obsługuje wykonywanie wywołań gRPC za pośrednictwem protokołu HTTP/2 w .NET Framework i Xamarin. gRPC C-Core nie jest obsługiwane przez firmę Microsoft.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:grpc/client>
* <xref:grpc/browser>
* [gRPC C# — Biblioteka](https://grpc.io/docs/languages/csharp/quickstart/)
