---
title: GRPC usługi i klienci z programem .NET w kodzie
author: jamesnk
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi pisania kodu-First gRPC przy użyciu platformy .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880636"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="660aa-103">GRPC usługi i klienci z programem .NET w kodzie</span><span class="sxs-lookup"><span data-stu-id="660aa-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="660aa-104">Przez [Kuba Kowalski-król](https://twitter.com/jamesnk) i [Marcy Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="660aa-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="660aa-105">GRPC do kodu używa typów .NET do definiowania kontraktów usług i komunikatów.</span><span class="sxs-lookup"><span data-stu-id="660aa-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="660aa-106">Kod — najpierw jest dobrym wyborem, gdy cały system używa platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="660aa-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="660aa-107">Typy kontraktów usług i danych platformy .NET mogą być współużytkowane przez serwer i klientów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="660aa-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="660aa-108">Pozwala uniknąć konieczności definiowania kontraktów w `.proto` plikach i generacjach kodu.</span><span class="sxs-lookup"><span data-stu-id="660aa-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="660aa-109">Kod — pierwsze nie jest zalecane w systemach Polyglot z wieloma językami.</span><span class="sxs-lookup"><span data-stu-id="660aa-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="660aa-110">Typów kontraktów usług i danych platformy .NET nie można używać z platformami non-.NET.</span><span class="sxs-lookup"><span data-stu-id="660aa-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="660aa-111">Aby wywołać usługę gRPC zapisaną przy użyciu kodu — po pierwsze, inne platformy muszą utworzyć `.proto` kontrakt zgodny z usługą.</span><span class="sxs-lookup"><span data-stu-id="660aa-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="660aa-112">protobuf-net. Grpc</span><span class="sxs-lookup"><span data-stu-id="660aa-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="660aa-113">Aby uzyskać pomoc dotyczącą usługi protobuf-net. GRPC, odwiedź stronę [protobuf-net. GRPC witrynę sieci Web](https://protobuf-net.github.io/protobuf-net.Grpc/) lub Utwórz problem w usłudze [protobuf-net. GRPC repozytorium GitHub](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="660aa-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="660aa-114">[protobuf-net. GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/) to projekt społecznościowy i nie jest obsługiwany przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="660aa-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="660aa-115">Dodaje obsługę kodu w pierwszej kolejności do `Grpc.AspNetCore` i `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="660aa-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="660aa-116">Używa typów platformy .NET z adnotacjami z atrybutami do definiowania usług gRPC i komunikatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="660aa-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="660aa-117">Pierwszym krokiem tworzenia usługi gRPC w kodzie jest Definiowanie kontraktu kodu:</span><span class="sxs-lookup"><span data-stu-id="660aa-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="660aa-118">Utwórz nowy projekt, który będzie współużytkowany przez serwer i klienta.</span><span class="sxs-lookup"><span data-stu-id="660aa-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="660aa-119">Dodaj [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Odwołanie do pakietu GRPC.</span><span class="sxs-lookup"><span data-stu-id="660aa-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="660aa-120">Tworzenie typów kontraktu usług i danych.</span><span class="sxs-lookup"><span data-stu-id="660aa-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="660aa-121">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="660aa-121">The preceding code:</span></span>

* <span data-ttu-id="660aa-122">Definiuje `HelloRequest` i `HelloReply` komunikaty.</span><span class="sxs-lookup"><span data-stu-id="660aa-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="660aa-123">Definiuje `IGreeterService` interfejs kontraktu z jednoargumentową `SayHelloAsync` metodą gRPC.</span><span class="sxs-lookup"><span data-stu-id="660aa-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="660aa-124">Kontrakt usługi jest zaimplementowany na serwerze i wywoływany z klienta programu.</span><span class="sxs-lookup"><span data-stu-id="660aa-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="660aa-125">Metody zdefiniowane w interfejsach usług muszą być zgodne z określonymi sygnaturami w zależności od tego, czy są one jednoargumentowe, przesyłane strumieniowo serwera, przesyłania strumieniowego klienta lub dwukierunkowego przesyłania</span><span class="sxs-lookup"><span data-stu-id="660aa-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="660aa-126">Aby uzyskać więcej informacji na temat definiowania kontraktów usług, zobacz [protobuf-net. Dokumentacja wprowadzająca GRPC](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="660aa-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="660aa-127">Tworzenie usługi gRPC w pierwszej kolejności</span><span class="sxs-lookup"><span data-stu-id="660aa-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="660aa-128">Aby dodać usługę gRPC (kod pierwszej) do aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="660aa-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="660aa-129">Dodaj [protobuf-net. Odwołanie do pakietu GRPC. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="660aa-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="660aa-130">Dodaj odwołanie do projektu współużytkowanego kodu-kontraktu.</span><span class="sxs-lookup"><span data-stu-id="660aa-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="660aa-131">Utwórz nowy `GreeterService.cs` plik i zaimplementuj `IGreeterService` Interfejs usługi:</span><span class="sxs-lookup"><span data-stu-id="660aa-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="660aa-132">Aktualizowanie `Startup.cs` pliku:</span><span class="sxs-lookup"><span data-stu-id="660aa-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="660aa-133">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="660aa-133">In the preceding code:</span></span>

* <span data-ttu-id="660aa-134">`AddCodeFirstGrpc` rejestruje usługi, które włączają kod w pierwszej kolejności.</span><span class="sxs-lookup"><span data-stu-id="660aa-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="660aa-135">`MapGrpcService<GreeterService>` dodaje punkt końcowy usługi Code First.</span><span class="sxs-lookup"><span data-stu-id="660aa-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="660aa-136">usługi gRPC zaimplementowane przy użyciu kodu i `.proto` plików mogą współistnieć w tej samej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="660aa-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="660aa-137">Wszystkie usługi gRPC korzystają z [konfiguracji usługi gRPC](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="660aa-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="660aa-138">Tworzenie klienta gRPC z kodem pierwszym</span><span class="sxs-lookup"><span data-stu-id="660aa-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="660aa-139">Klient programu gRPC pierwszy kod używa kontraktu usługi do wywoływania usług gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="660aa-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="660aa-140">Aby wywołać usługę gRPC przy użyciu klienta kodu-pierwszego:</span><span class="sxs-lookup"><span data-stu-id="660aa-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="660aa-141">Dodaj [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Odwołanie do pakietu GRPC.</span><span class="sxs-lookup"><span data-stu-id="660aa-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="660aa-142">Dodaj odwołanie do projektu współużytkowanego kodu-kontraktu.</span><span class="sxs-lookup"><span data-stu-id="660aa-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="660aa-143">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="660aa-143">The preceding code:</span></span>

* <span data-ttu-id="660aa-144">Tworzy kanał gRPC.</span><span class="sxs-lookup"><span data-stu-id="660aa-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="660aa-145">Tworzy klient z kodem po raz pierwszy z kanału z `CreateGrpcService<IGreeterService>` metodą rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="660aa-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="660aa-146">Wywołuje usługę gRPC z `SayHelloAsync` .</span><span class="sxs-lookup"><span data-stu-id="660aa-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="660aa-147">Klient gRPC pierwszy kod jest tworzony na podstawie kanału.</span><span class="sxs-lookup"><span data-stu-id="660aa-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="660aa-148">Podobnie jak w przypadku zwykłego klienta, klient pierwszy kod używa [konfiguracji kanału](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="660aa-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="660aa-149">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="660aa-149">Additional resources</span></span>

* [<span data-ttu-id="660aa-150">protobuf-net. Witryna sieci Web GRPC</span><span class="sxs-lookup"><span data-stu-id="660aa-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="660aa-151">protobuf-net. Repozytorium GitHub GRPC</span><span class="sxs-lookup"><span data-stu-id="660aa-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
