---
title: Migrowanie usług gRPC z dysku C-Core do ASP.NET Core
author: juntaoluo
description: Dowiedz się, jak przenieść istniejącą aplikację gRPC opartą na procesorze C do uruchamiania na stosie ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/migration
ms.openlocfilehash: 1846195cc43aec703333e69f66380ddcabcf2ad4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768825"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="532a6-103">Migrowanie usług gRPC z dysku C-Core do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="532a6-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="532a6-104">Przez [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="532a6-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="532a6-105">Ze względu na implementację bazowego stosu nie wszystkie funkcje działają w taki sam sposób między aplikacjami [gRPC opartymi na](https://grpc.io/blog/grpc-stacks) procesorze C i aplikacjami opartymi na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="532a6-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="532a6-106">W tym dokumencie przedstawiono najważniejsze różnice dotyczące migracji między dwoma stosami.</span><span class="sxs-lookup"><span data-stu-id="532a6-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="532a6-107">okres istnienia implementacji usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="532a6-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="532a6-108">W stosie ASP.NET Core usługi gRPC są domyślnie tworzone z [okresem istnienia w zakresie](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="532a6-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="532a6-109">Z kolei gRPC C-Core domyślnie wiąże się z usługą z [pojedynczym okresem istnienia](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="532a6-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="532a6-110">Okres istnienia w zakresie pozwala implementacji usługi rozwiązywać inne usługi z okresami istnienia w zakresie.</span><span class="sxs-lookup"><span data-stu-id="532a6-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="532a6-111">Na przykład okres istnienia objęty zakresem można `DbContext` również rozwiązać z kontenera di przez iniekcję konstruktora.</span><span class="sxs-lookup"><span data-stu-id="532a6-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="532a6-112">Korzystanie z okresu istnienia w zakresie:</span><span class="sxs-lookup"><span data-stu-id="532a6-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="532a6-113">Nowe wystąpienie implementacji usługi jest konstruowane dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="532a6-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="532a6-114">Nie jest możliwe udostępnianie stanu między żądaniami za pośrednictwem elementów członkowskich wystąpienia w typie implementacji.</span><span class="sxs-lookup"><span data-stu-id="532a6-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="532a6-115">Oczekuje się, że wszystkie Stany udostępnione są przechowywane w usłudze pojedynczej w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="532a6-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="532a6-116">Przechowywane udostępnione Stany są rozwiązywane w konstruktorze implementacji usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="532a6-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="532a6-117">Aby uzyskać więcej informacji na temat okresów istnienia usługi, zobacz <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="532a6-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="532a6-118">Dodawanie pojedynczej usługi</span><span class="sxs-lookup"><span data-stu-id="532a6-118">Add a singleton service</span></span>

<span data-ttu-id="532a6-119">Aby ułatwić przejście z implementacji gRPC C-Core do ASP.NET Core, można zmienić okres istnienia usługi dla implementacji usługi z zakresu na pojedynczą.</span><span class="sxs-lookup"><span data-stu-id="532a6-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="532a6-120">Obejmuje to dodanie wystąpienia implementacji usługi do kontenera DI:</span><span class="sxs-lookup"><span data-stu-id="532a6-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="532a6-121">Jednak implementacja usługi z pojedynczym okresem istnienia nie jest już w stanie rozpoznać usług objętych zakresem przez iniekcję konstruktora.</span><span class="sxs-lookup"><span data-stu-id="532a6-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="532a6-122">Skonfiguruj opcje usług gRPC Services</span><span class="sxs-lookup"><span data-stu-id="532a6-122">Configure gRPC services options</span></span>

<span data-ttu-id="532a6-123">W przypadku aplikacji opartych na rdzeniu C ustawienia takie `grpc.max_receive_message_length` jak `grpc.max_send_message_length` i są konfigurowane `ChannelOption` przy użyciu programu podczas [konstruowania wystąpienia serwera](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="532a6-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="532a6-124">W ASP.NET Core gRPC zapewnia konfigurację przy użyciu `GrpcServiceOptions` typu.</span><span class="sxs-lookup"><span data-stu-id="532a6-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="532a6-125">Na przykład maksymalny rozmiar komunikatu przychodzącego można skonfigurować przy użyciu `AddGrpc`usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="532a6-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="532a6-126">Poniższy przykład zmienia domyślną wartość `MaxReceiveMessageSize` z 4 MB na 16 MB:</span><span class="sxs-lookup"><span data-stu-id="532a6-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="532a6-127">Aby uzyskać więcej informacji na temat konfiguracji <xref:grpc/configuration>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="532a6-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="532a6-128">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="532a6-128">Logging</span></span>

<span data-ttu-id="532a6-129">Aplikacje oparte na rdzeniu C są zależne `GrpcEnvironment` od programu w celu [skonfigurowania rejestratora](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) do celów debugowania.</span><span class="sxs-lookup"><span data-stu-id="532a6-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="532a6-130">Stos ASP.NET Core udostępnia tę funkcję za pomocą [interfejsu API rejestrowania](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="532a6-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="532a6-131">Na przykład można dodać Rejestrator do usługi gRPC za pośrednictwem iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="532a6-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="532a6-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="532a6-132">HTTPS</span></span>

<span data-ttu-id="532a6-133">Aplikacje oparte na rdzeniu C konfigurują protokół HTTPS za pomocą [właściwości Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="532a6-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="532a6-134">Podobne pojęcie służy do konfigurowania serwerów w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="532a6-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="532a6-135">Na przykład Kestrel używa [konfiguracji punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration) dla tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="532a6-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="532a6-136">Interceptory gRPC i oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="532a6-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="532a6-137">ASP.NET Core [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) oferuje podobne funkcje w porównaniu do przechwyceń w aplikacjach gRPC opartych na rdzeniach języka C.</span><span class="sxs-lookup"><span data-stu-id="532a6-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="532a6-138">ASP.NET Core oprogramowanie pośredniczące i Interceptory są koncepcyjnie podobne.</span><span class="sxs-lookup"><span data-stu-id="532a6-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="532a6-139">Oba:</span><span class="sxs-lookup"><span data-stu-id="532a6-139">Both:</span></span>

* <span data-ttu-id="532a6-140">Służy do konstruowania potoku, który obsługuje żądanie gRPC.</span><span class="sxs-lookup"><span data-stu-id="532a6-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="532a6-141">Zezwalaj na wykonywanie pracy przed lub po następnym składniku w potoku.</span><span class="sxs-lookup"><span data-stu-id="532a6-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="532a6-142">Zapewnianie dostępu `HttpContext`do:</span><span class="sxs-lookup"><span data-stu-id="532a6-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="532a6-143">W oprogramowaniu pośredniczącym `HttpContext` jest parametr.</span><span class="sxs-lookup"><span data-stu-id="532a6-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="532a6-144">W przypadku interceptorów `HttpContext` dostęp do niego można uzyskać `ServerCallContext` za pomocą parametru `ServerCallContext.GetHttpContext` z metodą rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="532a6-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="532a6-145">Należy zauważyć, że ta funkcja jest specyficzna dla przechwyceń działających w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="532a6-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="532a6-146">różnice gRPC wychwytcy z ASP.NET Core oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="532a6-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="532a6-147">Interceptory</span><span class="sxs-lookup"><span data-stu-id="532a6-147">Interceptors:</span></span>
  * <span data-ttu-id="532a6-148">Działa na warstwie gRPC abstrakcji przy użyciu [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="532a6-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="532a6-149">Zapewnianie dostępu do:</span><span class="sxs-lookup"><span data-stu-id="532a6-149">Provide access to:</span></span>
    * <span data-ttu-id="532a6-150">Komunikat z deserializowanym wysłany do wywołania.</span><span class="sxs-lookup"><span data-stu-id="532a6-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="532a6-151">Komunikat zwracany z wywołania, zanim zostanie Zserializowany.</span><span class="sxs-lookup"><span data-stu-id="532a6-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="532a6-152">Może przechwytywać i obsługiwać wyjątki zgłoszone przez usługi gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="532a6-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="532a6-153">Oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="532a6-153">Middleware:</span></span>
  * <span data-ttu-id="532a6-154">Uruchamiany przed interceptorami gRPC.</span><span class="sxs-lookup"><span data-stu-id="532a6-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="532a6-155">Działa na podstawowych komunikatach HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="532a6-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="532a6-156">Można uzyskać dostęp tylko do bajtów z strumienia żądań i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="532a6-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="532a6-157">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="532a6-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
