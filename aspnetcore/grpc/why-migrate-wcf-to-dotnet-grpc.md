---
title: Dlaczego warto przeprowadzić migrację aplikacji WCF do usługi gRPC platformy ASP.NET Core
author: markrendle
description: W tym artykule opisano, dlaczego ASP.NET Core gRPC jest dobrą zaletą dla deweloperów Windows Communication Foundation (WCF), którzy chcą migrować do nowoczesnych architektur i platform.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058691"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="79cc8-103">gRPC for Windows Communication Foundation (WCF) — deweloperzy</span><span class="sxs-lookup"><span data-stu-id="79cc8-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="79cc8-104">W tym artykule opisano, dlaczego ASP.NET Core gRPC jest dobrą zaletą dla deweloperów Windows Communication Foundation (WCF), którzy chcą migrować do nowoczesnych architektur i platform.</span><span class="sxs-lookup"><span data-stu-id="79cc8-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="79cc8-105">Porównanie z WCF</span><span class="sxs-lookup"><span data-stu-id="79cc8-105">Comparison to WCF</span></span>

<span data-ttu-id="79cc8-106">Chociaż implementacja i podejście różnią się w zależności od gRPC, doświadczenia związane z tworzeniem i zużywaniem usług z gRPC powinny być intuicyjne dla deweloperów WCF.</span><span class="sxs-lookup"><span data-stu-id="79cc8-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="79cc8-107">WCF i gRPC to platformy RPC (zdalne wywołanie procedury) z tymi samymi celami:</span><span class="sxs-lookup"><span data-stu-id="79cc8-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="79cc8-108">Umożliwia to kod, tak jakby klient i serwer znajdują się na tej samej platformie.</span><span class="sxs-lookup"><span data-stu-id="79cc8-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="79cc8-109">Zapewnianie uproszczonego interfejsu API sieci przenośnej.</span><span class="sxs-lookup"><span data-stu-id="79cc8-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="79cc8-110">Obie platformy korzystają z wymogu deklarowania i implementacji interfejsu, chociaż proces deklarowania interfejsu jest inny.</span><span class="sxs-lookup"><span data-stu-id="79cc8-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="79cc8-111">Wiele typów wywołań RPC, które gRPC obsługuje mapowanie do powiązań dostępnych dla usług WCF.</span><span class="sxs-lookup"><span data-stu-id="79cc8-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="79cc8-112">Aby uzyskać więcej informacji i przykładów, zobacz [Migrowanie rozwiązania WCF do gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="79cc8-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="79cc8-113">Zalety gRPC</span><span class="sxs-lookup"><span data-stu-id="79cc8-113">Benefits of gRPC</span></span>

<span data-ttu-id="79cc8-114">gRPC zapewnia lepszą strukturę niż inne podejścia z następujących powodów.</span><span class="sxs-lookup"><span data-stu-id="79cc8-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="79cc8-115">Wydajność</span><span class="sxs-lookup"><span data-stu-id="79cc8-115">Performance</span></span>

<span data-ttu-id="79cc8-116">gRPC używa protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="79cc8-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="79cc8-117">W przeciwieństwie do protokołu HTTP/1.1, HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="79cc8-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="79cc8-118">Jest mniejszym, szybszym protokołem binarnym.</span><span class="sxs-lookup"><span data-stu-id="79cc8-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="79cc8-119">Jest wydajniejszy w przypadku komputerów do analizy.</span><span class="sxs-lookup"><span data-stu-id="79cc8-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="79cc8-120">Obsługuje multipleksowanie żądań za pośrednictwem jednego połączenia.</span><span class="sxs-lookup"><span data-stu-id="79cc8-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="79cc8-121">Multipleksowanie umożliwia wysyłanie wielu żądań w ramach jednego połączenia bez konieczności blokowania siebie.</span><span class="sxs-lookup"><span data-stu-id="79cc8-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="79cc8-122">W przypadku protokołu HTTP/1.1 Blokowanie jest znane jako blokowanie "linia główna (HOL)".</span><span class="sxs-lookup"><span data-stu-id="79cc8-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="79cc8-123">gRPC używa protobuf, wydajnego formatu binarnego, do serializacji komunikatów.</span><span class="sxs-lookup"><span data-stu-id="79cc8-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="79cc8-124">Komunikaty protobuf:</span><span class="sxs-lookup"><span data-stu-id="79cc8-124">Protobuf messages are:</span></span>
* <span data-ttu-id="79cc8-125">Szybka do serializacji i deserializacji.</span><span class="sxs-lookup"><span data-stu-id="79cc8-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="79cc8-126">Używaj mniejszej przepustowości niż formaty tekstowe.</span><span class="sxs-lookup"><span data-stu-id="79cc8-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="79cc8-127">gRPC to dobre rozwiązanie dla urządzeń przenośnych i sieci z ograniczeniami przepustowości.</span><span class="sxs-lookup"><span data-stu-id="79cc8-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="79cc8-128">Współdziałanie</span><span class="sxs-lookup"><span data-stu-id="79cc8-128">Interoperability</span></span>

<span data-ttu-id="79cc8-129">Istnieją narzędzia i biblioteki gRPC dla wszystkich głównych języków programowania i platform, w tym .NET, Java, Python, go, C++, Node.js, Swift, dart, Ruby i PHP.</span><span class="sxs-lookup"><span data-stu-id="79cc8-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="79cc8-130">Dzięki protobuf binarnej sieci szkieletowej i wydajnej generacji kodu dla każdej platformy deweloperzy mogą tworzyć aplikacje na różnych platformach.</span><span class="sxs-lookup"><span data-stu-id="79cc8-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="79cc8-131">Użyteczność i produktywność</span><span class="sxs-lookup"><span data-stu-id="79cc8-131">Usability and productivity</span></span>

<span data-ttu-id="79cc8-132">gRPC to kompleksowe rozwiązanie RPC.</span><span class="sxs-lookup"><span data-stu-id="79cc8-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="79cc8-133">Działa spójnie w wielu językach i na różnych platformach.</span><span class="sxs-lookup"><span data-stu-id="79cc8-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="79cc8-134">Zapewnia również doskonałe narzędzia, z dużą ilością kodu standardowego generowanego automatycznie.</span><span class="sxs-lookup"><span data-stu-id="79cc8-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="79cc8-135">Podobnie jak w przypadku WCF, gRPC automatycznie generuje komunikaty i klient o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="79cc8-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="79cc8-136">Czas dewelopera jest bezpłatny do skoncentrowania się na logice biznesowej.</span><span class="sxs-lookup"><span data-stu-id="79cc8-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="79cc8-137">Przesyłanie strumieniowe</span><span class="sxs-lookup"><span data-stu-id="79cc8-137">Streaming</span></span>

<span data-ttu-id="79cc8-138">gRPC ma pełne dwukierunkowe przesyłanie strumieniowe, które zapewnia podobną funkcjonalność usług WCF w pełni dupleks.</span><span class="sxs-lookup"><span data-stu-id="79cc8-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="79cc8-139">gRPC Streaming może działać za pośrednictwem zwykłych połączeń internetowych, modułów równoważenia obciążenia i sieci usług.</span><span class="sxs-lookup"><span data-stu-id="79cc8-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="79cc8-140">Terminy, limity czasu i anulowanie</span><span class="sxs-lookup"><span data-stu-id="79cc8-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="79cc8-141">gRPC umożliwia klientom określenie maksymalnego czasu na zakończenie zdalnego wywoływania procedur.</span><span class="sxs-lookup"><span data-stu-id="79cc8-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="79cc8-142">W przypadku przekroczenia określonego terminu ostatecznego serwer może anulować operację niezależnie od klienta.</span><span class="sxs-lookup"><span data-stu-id="79cc8-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="79cc8-143">Terminy i anulowania można rozpropagować za pomocą kolejnych wywołań gRPC, aby ułatwić wymuszanie limitów użycia zasobów.</span><span class="sxs-lookup"><span data-stu-id="79cc8-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="79cc8-144">Klienci mogą zatrzymać operacje po przekroczeniu terminu ostatecznego lub wcześniej w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="79cc8-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="79cc8-145">Na przykład klienci mogą zatrzymać operacje ze względu na interakcję z użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="79cc8-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="79cc8-146">Zabezpieczenia</span><span class="sxs-lookup"><span data-stu-id="79cc8-146">Security</span></span>

<span data-ttu-id="79cc8-147">gRPC może używać protokołów TLS i HTTP/2, aby zapewnić kompleksowe połączenie szyfrowane między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="79cc8-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="79cc8-148">Obsługa uwierzytelniania certyfikatu klienta dalsze zwiększa bezpieczeństwo i zaufanie między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="79cc8-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="79cc8-149">gRPC jako ścieżkę migracji dla usług WCF do .NET Core i .NET 5</span><span class="sxs-lookup"><span data-stu-id="79cc8-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="79cc8-150">Programy .NET Core i .NET 5 oznaczają zmianę w sposobie, w jaki firma Microsoft dostarcza rozwiązania komunikacji zdalnej dla deweloperów, którzy chcą dostarczać usługi na różne platformy.</span><span class="sxs-lookup"><span data-stu-id="79cc8-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="79cc8-151">Oprogramowanie .NET Core i .NET 5 obsługują [wywoływanie usług WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), ale nie oferuje obsługi klienta po stronie serwera do hostowania WCF.</span><span class="sxs-lookup"><span data-stu-id="79cc8-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="79cc8-152">Istnieją dwie zalecane ścieżki do modernizacji aplikacji WCF:</span><span class="sxs-lookup"><span data-stu-id="79cc8-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="79cc8-153">gRPC jest oparta na nowoczesnych technologiach i okazała się najbardziej popularnym wyborem w całej społeczności deweloperów dla aplikacji RPC.</span><span class="sxs-lookup"><span data-stu-id="79cc8-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="79cc8-154">Począwszy od platformy .NET Core 3,0, nowoczesne platformy .NET mają doskonałą obsługę gRPC.</span><span class="sxs-lookup"><span data-stu-id="79cc8-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="79cc8-155">Migrowanie usług WCF do korzystania z programu gRPC pomaga zapewnić funkcje usługi RPC, wydajność i współdziałanie w nowoczesnych aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="79cc8-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="79cc8-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) to wysiłki społecznościowe umożliwiające obsługę hostingu usług WCF w oprogramowaniu .NET Core i .NET 5.</span><span class="sxs-lookup"><span data-stu-id="79cc8-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="79cc8-157">Dostępna jest wersja zapoznawcza, a projekt jest gotowy do produkcji.</span><span class="sxs-lookup"><span data-stu-id="79cc8-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="79cc8-158">CoreWCF obsługuje tylko podzbiór funkcji WCF, a .NET Framework aplikacje, które migruje do korzystania z niej, będą wymagały zmiany kodu i testy zostały pomyślnie wykonane.</span><span class="sxs-lookup"><span data-stu-id="79cc8-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="79cc8-159">CoreWCF jest dobrym rozwiązaniem, jeśli aplikacja musi zachować zgodność z istniejącymi klientami, którzy wywołują usługi WCF.</span><span class="sxs-lookup"><span data-stu-id="79cc8-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="79cc8-160">Rozpoczęcie pracy</span><span class="sxs-lookup"><span data-stu-id="79cc8-160">Get started</span></span>

<span data-ttu-id="79cc8-161">Aby uzyskać szczegółowe wskazówki dotyczące tworzenia usług gRPC Services w ASP.NET Core dla deweloperów WCF, zobacz [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers) .</span><span class="sxs-lookup"><span data-stu-id="79cc8-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
