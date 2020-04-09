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
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrowanie usług gRPC z C-core do ASP.NET Core

Przez [John Luo](https://github.com/juntaoluo)

Ze względu na implementację stosu bazowego nie wszystkie funkcje działają w ten sam sposób między aplikacjami [GRPC opartymi na rdzeniu C](https://grpc.io/blog/grpc-stacks) i aplikacjami opartymi na rdzeniu ASP.NET. W tym dokumencie przedstawiono kluczowe różnice migracji między dwoma stosami.

## <a name="grpc-service-implementation-lifetime"></a>Okres realizacji usługi gRPC

W stosie ASP.NET Core usługi gRPC są domyślnie tworzone z [okresem istnienia o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes). Natomiast gRPC C-core domyślnie wiąże się z usługą o [okresie istnienia singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Okres istnienia o określonym zakresie umożliwia implementacji usługi, aby rozwiązać inne usługi z okresami istnienia o określonym zakresie. Na przykład okres istnienia o `DbContext` określonym zakresie można również rozwiązać z kontenera DI poprzez iniekcji konstruktora. Przy użyciu okresu istnienia o określonym zakresie:

* Nowe wystąpienie implementacji usługi jest konstruowany dla każdego żądania.
* Nie można udostępniać stanu między żądaniami za pośrednictwem elementów członkowskich wystąpienia na typ implementacji.
* Oczekuje się przechowywania stanów udostępnionych w usłudze singleton w kontenerze DI. Przechowywane stany udostępnione są rozpoznawane w konstruktorze implementacji usługi gRPC.

Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#service-lifetimes>temat okresów istnienia usługi, zobacz .

### <a name="add-a-singleton-service"></a>Dodawanie usługi singleton

Aby ułatwić przejście z implementacji gRPC C-core do ASP.NET Core, można zmienić okres istnienia usługi implementacji usługi z zakresu do singleton. Obejmuje to dodanie wystąpienia implementacji usługi do kontenera DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Jednak implementacja usługi z okresu istnienia singleton nie jest już w stanie rozpoznać zakres usług za pomocą iniekcji konstruktora.

## <a name="configure-grpc-services-options"></a>Konfigurowanie opcji usług gRPC

W aplikacjach opartych na rdzeniu C ustawienia, takie `grpc.max_receive_message_length` jak i `grpc.max_send_message_length` są konfigurowane `ChannelOption` podczas [konstruowania wystąpienia serwera](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

W ASP.NET Core gRPC zapewnia konfigurację `GrpcServiceOptions` za pośrednictwem typu. Na przykład usługa gRPC maksymalny rozmiar wiadomości przychodzącej `AddGrpc`można skonfigurować za pomocą programu . W poniższym przykładzie zmienia się wartość domyślna `MaxReceiveMessageSize` 4 MB na 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Aby uzyskać więcej informacji <xref:grpc/configuration>na temat konfiguracji, zobacz .

## <a name="logging"></a>Rejestrowanie

Aplikacje oparte na rdzeniu `GrpcEnvironment` C opierają się na [skonfigurowaniu rejestratora](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) do celów debugowania. Stos ASP.NET Core zapewnia tę funkcję za pośrednictwem [interfejsu API rejestrowania](xref:fundamentals/logging/index). Na przykład rejestrator można dodać do usługi gRPC za pomocą iniekcji konstruktora:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplikacje oparte na rdzeniu C konfigurować protokół HTTPS za pośrednictwem [server.ports właściwości](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Podobna koncepcja jest używana do konfigurowania serwerów w ASP.NET Core. Na przykład Kestrel używa [konfiguracji punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration) dla tej funkcji.

## <a name="grpc-interceptors-vs-middleware"></a>GRPC Interceptors vs Middleware

ASP.NET [Core oprogramowanie pośredniczące](xref:fundamentals/middleware/index) oferuje podobne funkcje w porównaniu do przechwytujących w aplikacjach GRPC opartych na rdzeniu C. ASP.NET Core oprogramowanie pośredniczące i interceptory są koncepcyjnie podobne. Oba:

* Są używane do konstruowania potoku, który obsługuje żądanie gRPC.
* Zezwalaj na pracę, która ma być wykonywana przed lub po następnym składniku w potoku.
* Zapewnienie dostępu `HttpContext`do:
  * W oprogramowaniu `HttpContext` pośredniczącym jest parametrem.
  * W interceptors `HttpContext` można uzyskać dostęp `ServerCallContext` przy `ServerCallContext.GetHttpContext` użyciu parametru z metody rozszerzenia. Należy zauważyć, że ta funkcja jest specyficzna dla przechwytujących działających w ASP.NET Core.

Różnice gRPC Interceptor od ASP.NET Core Middleware:

* Avicode:
  * Działaj na warstwie abstrakcji gRPC za pomocą [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Zapewnij dostęp do:
    * Wiadomość deserialna wysłana do połączenia.
    * Wiadomość zwracana z wywołania, zanim zostanie serializowana.
  * Można przechwytyć i obsługiwać wyjątki generowane z usług gRPC.
* Middleware:
  * Działa przed przechwytywaczami gRPC.
  * Działa na podstawowych komunikatach HTTP/2.
  * Dostęp tylko bajtów ze strumieni żądania i odpowiedzi.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
