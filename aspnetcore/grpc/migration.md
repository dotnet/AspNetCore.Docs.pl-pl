---
title: Migrowanie usług gRPC z C-core do ASP.NET Core
author: juntaoluo
description: Dowiedz się, jak przenieść istniejącą aplikację gRPC opartą na rdzeniu C, aby działała na stosie ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664137"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="bce66-103">Migrowanie usług gRPC z C-core do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bce66-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="bce66-104">Przez [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="bce66-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="bce66-105">Ze względu na implementację stosu bazowego nie wszystkie funkcje działają w ten sam sposób między aplikacjami [GRPC opartymi na rdzeniu C](https://grpc.io/blog/grpc-stacks) i aplikacjami opartymi na rdzeniu ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bce66-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="bce66-106">W tym dokumencie przedstawiono kluczowe różnice migracji między dwoma stosami.</span><span class="sxs-lookup"><span data-stu-id="bce66-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="bce66-107">Okres realizacji usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="bce66-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="bce66-108">W stosie ASP.NET Core usługi gRPC są domyślnie tworzone z [okresem istnienia o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="bce66-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="bce66-109">Natomiast gRPC C-core domyślnie wiąże się z usługą o [okresie istnienia singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="bce66-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="bce66-110">Okres istnienia o określonym zakresie umożliwia implementacji usługi, aby rozwiązać inne usługi z okresami istnienia o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="bce66-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="bce66-111">Na przykład okres istnienia o `DbContext` określonym zakresie można również rozwiązać z kontenera DI poprzez iniekcji konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bce66-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="bce66-112">Przy użyciu okresu istnienia o określonym zakresie:</span><span class="sxs-lookup"><span data-stu-id="bce66-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="bce66-113">Nowe wystąpienie implementacji usługi jest konstruowany dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="bce66-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="bce66-114">Nie można udostępniać stanu między żądaniami za pośrednictwem elementów członkowskich wystąpienia na typ implementacji.</span><span class="sxs-lookup"><span data-stu-id="bce66-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="bce66-115">Oczekuje się przechowywania stanów udostępnionych w usłudze singleton w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="bce66-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="bce66-116">Przechowywane stany udostępnione są rozpoznawane w konstruktorze implementacji usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="bce66-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="bce66-117">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#service-lifetimes>temat okresów istnienia usługi, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bce66-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="bce66-118">Dodawanie usługi singleton</span><span class="sxs-lookup"><span data-stu-id="bce66-118">Add a singleton service</span></span>

<span data-ttu-id="bce66-119">Aby ułatwić przejście z implementacji gRPC C-core do ASP.NET Core, można zmienić okres istnienia usługi implementacji usługi z zakresu do singleton.</span><span class="sxs-lookup"><span data-stu-id="bce66-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="bce66-120">Obejmuje to dodanie wystąpienia implementacji usługi do kontenera DI:</span><span class="sxs-lookup"><span data-stu-id="bce66-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="bce66-121">Jednak implementacja usługi z okresu istnienia singleton nie jest już w stanie rozpoznać zakres usług za pomocą iniekcji konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bce66-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="bce66-122">Konfigurowanie opcji usług gRPC</span><span class="sxs-lookup"><span data-stu-id="bce66-122">Configure gRPC services options</span></span>

<span data-ttu-id="bce66-123">W aplikacjach opartych na rdzeniu C ustawienia, takie `grpc.max_receive_message_length` jak i `grpc.max_send_message_length` są konfigurowane `ChannelOption` podczas [konstruowania wystąpienia serwera](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="bce66-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="bce66-124">W ASP.NET Core gRPC zapewnia konfigurację `GrpcServiceOptions` za pośrednictwem typu.</span><span class="sxs-lookup"><span data-stu-id="bce66-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="bce66-125">Na przykład usługa gRPC maksymalny rozmiar wiadomości przychodzącej `AddGrpc`można skonfigurować za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="bce66-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="bce66-126">W poniższym przykładzie zmienia się wartość domyślna `MaxReceiveMessageSize` 4 MB na 16 MB:</span><span class="sxs-lookup"><span data-stu-id="bce66-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="bce66-127">Aby uzyskać więcej informacji <xref:grpc/configuration>na temat konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bce66-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="bce66-128">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bce66-128">Logging</span></span>

<span data-ttu-id="bce66-129">Aplikacje oparte na rdzeniu `GrpcEnvironment` C opierają się na [skonfigurowaniu rejestratora](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) do celów debugowania.</span><span class="sxs-lookup"><span data-stu-id="bce66-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="bce66-130">Stos ASP.NET Core zapewnia tę funkcję za pośrednictwem [interfejsu API rejestrowania](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="bce66-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="bce66-131">Na przykład rejestrator można dodać do usługi gRPC za pomocą iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="bce66-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="bce66-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="bce66-132">HTTPS</span></span>

<span data-ttu-id="bce66-133">Aplikacje oparte na rdzeniu C konfigurować protokół HTTPS za pośrednictwem [server.ports właściwości](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="bce66-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="bce66-134">Podobna koncepcja jest używana do konfigurowania serwerów w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bce66-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="bce66-135">Na przykład Kestrel używa [konfiguracji punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration) dla tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="bce66-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="bce66-136">GRPC Interceptors vs Middleware</span><span class="sxs-lookup"><span data-stu-id="bce66-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="bce66-137">ASP.NET [Core oprogramowanie pośredniczące](xref:fundamentals/middleware/index) oferuje podobne funkcje w porównaniu do przechwytujących w aplikacjach GRPC opartych na rdzeniu C.</span><span class="sxs-lookup"><span data-stu-id="bce66-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="bce66-138">ASP.NET Core oprogramowanie pośredniczące i interceptory są koncepcyjnie podobne.</span><span class="sxs-lookup"><span data-stu-id="bce66-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="bce66-139">Oba:</span><span class="sxs-lookup"><span data-stu-id="bce66-139">Both:</span></span>

* <span data-ttu-id="bce66-140">Są używane do konstruowania potoku, który obsługuje żądanie gRPC.</span><span class="sxs-lookup"><span data-stu-id="bce66-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="bce66-141">Zezwalaj na pracę, która ma być wykonywana przed lub po następnym składniku w potoku.</span><span class="sxs-lookup"><span data-stu-id="bce66-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="bce66-142">Zapewnienie dostępu `HttpContext`do:</span><span class="sxs-lookup"><span data-stu-id="bce66-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="bce66-143">W oprogramowaniu `HttpContext` pośredniczącym jest parametrem.</span><span class="sxs-lookup"><span data-stu-id="bce66-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="bce66-144">W interceptors `HttpContext` można uzyskać dostęp `ServerCallContext` przy `ServerCallContext.GetHttpContext` użyciu parametru z metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="bce66-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="bce66-145">Należy zauważyć, że ta funkcja jest specyficzna dla przechwytujących działających w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bce66-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="bce66-146">Różnice gRPC Interceptor od ASP.NET Core Middleware:</span><span class="sxs-lookup"><span data-stu-id="bce66-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="bce66-147">Avicode:</span><span class="sxs-lookup"><span data-stu-id="bce66-147">Interceptors:</span></span>
  * <span data-ttu-id="bce66-148">Działaj na warstwie abstrakcji gRPC za pomocą [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="bce66-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="bce66-149">Zapewnij dostęp do:</span><span class="sxs-lookup"><span data-stu-id="bce66-149">Provide access to:</span></span>
    * <span data-ttu-id="bce66-150">Wiadomość deserialna wysłana do połączenia.</span><span class="sxs-lookup"><span data-stu-id="bce66-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="bce66-151">Wiadomość zwracana z wywołania, zanim zostanie serializowana.</span><span class="sxs-lookup"><span data-stu-id="bce66-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="bce66-152">Można przechwytyć i obsługiwać wyjątki generowane z usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="bce66-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="bce66-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="bce66-153">Middleware:</span></span>
  * <span data-ttu-id="bce66-154">Działa przed przechwytywaczami gRPC.</span><span class="sxs-lookup"><span data-stu-id="bce66-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="bce66-155">Działa na podstawowych komunikatach HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bce66-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="bce66-156">Dostęp tylko bajtów ze strumieni żądania i odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="bce66-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bce66-157">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bce66-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
