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
# <a name="use-grpc-client-with-net-standard-20"></a><span data-ttu-id="16855-103">Używanie klienta gRPC z .NET Standard 2,0</span><span class="sxs-lookup"><span data-stu-id="16855-103">Use gRPC client with .NET Standard 2.0</span></span>

<span data-ttu-id="16855-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="16855-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="16855-105">W tym artykule omówiono sposób używania klienta .NET gRPC z implementacjami platformy .NET, które obsługują [.NET Standard 2,0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="16855-105">This article discusses how to use the .NET gRPC client with .NET implementations that support [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span>

## <a name="net-implementations"></a><span data-ttu-id="16855-106">Implementacje platformy .NET</span><span class="sxs-lookup"><span data-stu-id="16855-106">.NET implementations</span></span>

<span data-ttu-id="16855-107">Następujące implementacje platformy .NET (lub nowsze) obsługują [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client/) , ale nie mają pełnej obsługi protokołu HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="16855-107">The following .NET implementations (or later) support [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) but don't have full support for HTTP/2:</span></span>

* <span data-ttu-id="16855-108">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="16855-108">.NET Core 2.1</span></span>
* <span data-ttu-id="16855-109">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="16855-109">.NET Framework 4.6.1</span></span>
* <span data-ttu-id="16855-110">Mono 5,4</span><span class="sxs-lookup"><span data-stu-id="16855-110">Mono 5.4</span></span>
* <span data-ttu-id="16855-111">Platforma Xamarin. iOS 10,14</span><span class="sxs-lookup"><span data-stu-id="16855-111">Xamarin.iOS 10.14</span></span>
* <span data-ttu-id="16855-112">Xamarin. Android 8,0</span><span class="sxs-lookup"><span data-stu-id="16855-112">Xamarin.Android 8.0</span></span>
* <span data-ttu-id="16855-113">Platforma uniwersalna systemu Windows 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="16855-113">Universal Windows Platform 10.0.16299</span></span>
* <span data-ttu-id="16855-114">Unity 2018,1</span><span class="sxs-lookup"><span data-stu-id="16855-114">Unity 2018.1</span></span>

<span data-ttu-id="16855-115">Klient .NET gRPC może wywoływać usługi z tych implementacji platformy .NET z dodatkową konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="16855-115">The .NET gRPC client can call services from these .NET implementations with some additional configuration.</span></span>

## <a name="httphandler-configuration"></a><span data-ttu-id="16855-116">Konfiguracja HttpHandler</span><span class="sxs-lookup"><span data-stu-id="16855-116">HttpHandler configuration</span></span>

<span data-ttu-id="16855-117">Dostawca protokołu HTTP musi być skonfigurowany przy użyciu programu `GrpcChannelOptions.HttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="16855-117">An HTTP provider must be configured using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="16855-118">Jeśli program obsługi nie jest skonfigurowany, zostanie zgłoszony błąd:</span><span class="sxs-lookup"><span data-stu-id="16855-118">If a handler isn't configured, an error is thrown:</span></span>

> <span data-ttu-id="16855-119">`System.PlatformNotSupportedException`: gRPC wymaga dodatkowej konfiguracji, aby pomyślnie wykonywać wywołania RPC w implementacjach platformy .NET, które nie obsługują gRPC za pośrednictwem protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="16855-119">`System.PlatformNotSupportedException`: gRPC requires extra configuration to successfully make RPC calls on .NET implementations that don't have support for gRPC over HTTP/2.</span></span> <span data-ttu-id="16855-120">Dostawca HTTP musi być określony przy użyciu `GrpcChannelOptions.HttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="16855-120">An HTTP provider must be specified using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="16855-121">Skonfigurowany dostawca HTTP musi obsługiwać protokół HTTP/2 lub być skonfigurowany do korzystania z programu gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="16855-121">The configured HTTP provider must either support HTTP/2 or be configured to use gRPC-Web.</span></span>

<span data-ttu-id="16855-122">Implementacje platformy .NET, które nie obsługują protokołu HTTP/2, takie jak platformy UWP, Xamarin i Unity, mogą używać funkcji gRPC-Web jako alternatywy.</span><span class="sxs-lookup"><span data-stu-id="16855-122">.NET implementations that don't support HTTP/2, such as UWP, Xamarin, and Unity, can use gRPC-Web as an alternative.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

<span data-ttu-id="16855-123">Aby uzyskać więcej informacji, zobacz [Configure gRPC-Web with the .NET gRPC Client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span><span class="sxs-lookup"><span data-stu-id="16855-123">For more information, see [Configure gRPC-Web with the .NET gRPC client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span></span>

## <a name="net-framework"></a><span data-ttu-id="16855-124">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="16855-124">.NET Framework</span></span>

<span data-ttu-id="16855-125">.NET Framework ma ograniczoną obsługę gRPC za pośrednictwem protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="16855-125">.NET Framework has limited support for gRPC over HTTP/2.</span></span> <span data-ttu-id="16855-126">Aby włączyć gRPC za pośrednictwem protokołu HTTP/2 na .NET Framework, skonfiguruj kanał do użycia <xref:System.Net.Http.WinHttpHandler> .</span><span class="sxs-lookup"><span data-stu-id="16855-126">To enable gRPC over HTTP/2 on .NET Framework, configure the channel to use <xref:System.Net.Http.WinHttpHandler>.</span></span>

<span data-ttu-id="16855-127">Wymagania i ograniczenia dotyczące korzystania z programu `WinHttpHandler` :</span><span class="sxs-lookup"><span data-stu-id="16855-127">Requirements and restrictions to using `WinHttpHandler`:</span></span>

* <span data-ttu-id="16855-128">Windows 10 kompilacja 19622 lub nowsza.</span><span class="sxs-lookup"><span data-stu-id="16855-128">Windows 10 Build 19622 or later.</span></span>
* <span data-ttu-id="16855-129">Odwołanie do pakietu NuGet [systemu .NET. http. WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) .</span><span class="sxs-lookup"><span data-stu-id="16855-129">A reference to the [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet package.</span></span>
* <span data-ttu-id="16855-130">Obsługiwane są tylko wywołania wieloargumentowe i gRPC przesyłania strumieniowego serwera.</span><span class="sxs-lookup"><span data-stu-id="16855-130">Only unary and server streaming gRPC calls are supported.</span></span>
* <span data-ttu-id="16855-131">Obsługiwane są tylko wywołania gRPC przez protokół TLS.</span><span class="sxs-lookup"><span data-stu-id="16855-131">Only gRPC calls over TLS are supported.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> <span data-ttu-id="16855-132">Obsługa .NET Framework jest na wczesnych etapach i wymaga użycia oprogramowania w wersji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="16855-132">.NET Framework support is in its early stages and requires using pre-release software.</span></span>
> * <span data-ttu-id="16855-133">System Windows 10 Build 19622 lub nowszy jest dostępny jako kompilacja [niejawnych testerów systemu Windows](https://insider.windows.com/) .</span><span class="sxs-lookup"><span data-stu-id="16855-133">Windows 10 Build 19622 or later is available as a [Windows Insiders](https://insider.windows.com/) build.</span></span>
> * <span data-ttu-id="16855-134">Wymagana wersja programu `System.Net.Http.WinHttpHandler` nie jest obecnie dostępna w NuGet.org. Najnowsza wersja wstępna jest dostępna w [tym źródle danych NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) .</span><span class="sxs-lookup"><span data-stu-id="16855-134">The required version of `System.Net.Http.WinHttpHandler` is not currently available on NuGet.org. The latest pre-release version is available on [this NuGet feed](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) should be used.</span></span>

## <a name="grpc-c-core-library"></a><span data-ttu-id="16855-135">gRPC C# — Biblioteka</span><span class="sxs-lookup"><span data-stu-id="16855-135">gRPC C# core-library</span></span>

<span data-ttu-id="16855-136">Alternatywna opcja dla .NET Framework i Xamarin polega na użyciu [biblioteki GRPC C# Core](https://grpc.io/docs/languages/csharp/quickstart/) do wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="16855-136">An alternative option for .NET Framework and Xamarin is to use [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) to make gRPC calls.</span></span> <span data-ttu-id="16855-137">Jest to biblioteka innej firmy, która obsługuje wykonywanie wywołań gRPC za pośrednictwem protokołu HTTP/2 w .NET Framework i Xamarin.</span><span class="sxs-lookup"><span data-stu-id="16855-137">It's a third party library that supports making gRPC calls over HTTP/2 on .NET Framework and Xamarin.</span></span> <span data-ttu-id="16855-138">gRPC C-Core nie jest obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="16855-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16855-139">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="16855-139">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/browser>
* [<span data-ttu-id="16855-140">gRPC C# — Biblioteka</span><span class="sxs-lookup"><span data-stu-id="16855-140">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
