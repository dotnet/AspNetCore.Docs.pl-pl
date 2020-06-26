---
title: Migrowanie usług gRPC z dysku C-Core do ASP.NET Core
author: juntaoluo
description: Dowiedz się, jak przenieść istniejącą aplikację gRPC opartą na procesorze C do uruchamiania na stosie ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/migration
ms.openlocfilehash: 2f0cd5f224453ee7be16f8a1d10e383de2a0d426
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407255"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrowanie usług gRPC z dysku C-Core do ASP.NET Core

Przez [Jan Luo](https://github.com/juntaoluo)

Ze względu na implementację bazowego stosu nie wszystkie funkcje działają w taki sam sposób między aplikacjami [gRPC opartymi na](https://grpc.io/blog/grpc-stacks) procesorze C i aplikacjami opartymi na ASP.NET Core. W tym dokumencie przedstawiono najważniejsze różnice dotyczące migracji między dwoma stosami.

## <a name="grpc-service-implementation-lifetime"></a>okres istnienia implementacji usługi gRPC

W stosie ASP.NET Core usługi gRPC są domyślnie tworzone z [okresem istnienia w zakresie](xref:fundamentals/dependency-injection#service-lifetimes). Z kolei gRPC C-Core domyślnie wiąże się z usługą z [pojedynczym okresem istnienia](xref:fundamentals/dependency-injection#service-lifetimes).

Okres istnienia w zakresie pozwala implementacji usługi rozwiązywać inne usługi z okresami istnienia w zakresie. Na przykład okres istnienia objęty zakresem można również rozwiązać `DbContext` z kontenera di przez iniekcję konstruktora. Korzystanie z okresu istnienia w zakresie:

* Nowe wystąpienie implementacji usługi jest konstruowane dla każdego żądania.
* Nie jest możliwe udostępnianie stanu między żądaniami za pośrednictwem elementów członkowskich wystąpienia w typie implementacji.
* Oczekuje się, że wszystkie Stany udostępnione są przechowywane w usłudze pojedynczej w kontenerze DI. Przechowywane udostępnione Stany są rozwiązywane w konstruktorze implementacji usługi gRPC.

Aby uzyskać więcej informacji na temat okresów istnienia usługi, zobacz <xref:fundamentals/dependency-injection#service-lifetimes> .

### <a name="add-a-singleton-service"></a>Dodawanie pojedynczej usługi

Aby ułatwić przejście z implementacji gRPC C-Core do ASP.NET Core, można zmienić okres istnienia usługi dla implementacji usługi z zakresu na pojedynczą. Obejmuje to dodanie wystąpienia implementacji usługi do kontenera DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Jednak implementacja usługi z pojedynczym okresem istnienia nie jest już w stanie rozpoznać usług objętych zakresem przez iniekcję konstruktora.

## <a name="configure-grpc-services-options"></a>Skonfiguruj opcje usług gRPC Services

W przypadku aplikacji opartych na rdzeniu C ustawienia takie jak `grpc.max_receive_message_length` i `grpc.max_send_message_length` są konfigurowane przy użyciu programu `ChannelOption` podczas [konstruowania wystąpienia serwera](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

W ASP.NET Core gRPC zapewnia konfigurację przy użyciu `GrpcServiceOptions` typu. Na przykład maksymalny rozmiar komunikatu przychodzącego można skonfigurować przy użyciu usługi gRPC `AddGrpc` . Poniższy przykład zmienia domyślną wartość `MaxReceiveMessageSize` z 4 MB na 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Aby uzyskać więcej informacji na temat konfiguracji, zobacz <xref:grpc/configuration> .

## <a name="logging"></a>Rejestrowanie

Aplikacje oparte na rdzeniu C są zależne od programu w `GrpcEnvironment` celu [skonfigurowania rejestratora](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) do celów debugowania. Stos ASP.NET Core udostępnia tę funkcję za pomocą [interfejsu API rejestrowania](xref:fundamentals/logging/index). Na przykład można dodać Rejestrator do usługi gRPC za pośrednictwem iniekcji konstruktora:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplikacje oparte na rdzeniu C konfigurują protokół HTTPS za pomocą [właściwości Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Podobne pojęcie służy do konfigurowania serwerów w ASP.NET Core. Na przykład Kestrel używa [konfiguracji punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration) dla tej funkcji.

## <a name="grpc-interceptors-vs-middleware"></a>Interceptory gRPC i oprogramowanie pośredniczące

ASP.NET Core [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) oferuje podobne funkcje w porównaniu do przechwyceń w aplikacjach gRPC opartych na rdzeniach języka C. ASP.NET Core oprogramowanie pośredniczące i Interceptory są koncepcyjnie podobne. Oba:

* Służy do konstruowania potoku, który obsługuje żądanie gRPC.
* Zezwalaj na wykonywanie pracy przed lub po następnym składniku w potoku.
* Zapewnianie dostępu do `HttpContext` :
  * W oprogramowaniu pośredniczącym `HttpContext` jest parametr.
  * W przypadku interceptorów `HttpContext` dostęp do niego można uzyskać za pomocą `ServerCallContext` parametru z `ServerCallContext.GetHttpContext` metodą rozszerzenia. Należy zauważyć, że ta funkcja jest specyficzna dla przechwyceń działających w ASP.NET Core.

różnice gRPC wychwytcy z ASP.NET Core oprogramowania pośredniczącego:

* Interceptory
  * Działa na warstwie gRPC abstrakcji przy użyciu [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Zapewnianie dostępu do:
    * Komunikat z deserializowanym wysłany do wywołania.
    * Komunikat zwracany z wywołania, zanim zostanie Zserializowany.
  * Może przechwytywać i obsługiwać wyjątki zgłoszone przez usługi gRPC Services.
* Oprogramowania pośredniczącego
  * Uruchamiany przed interceptorami gRPC.
  * Działa na podstawowych komunikatach HTTP/2.
  * Można uzyskać dostęp tylko do bajtów z strumienia żądań i odpowiedzi.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
