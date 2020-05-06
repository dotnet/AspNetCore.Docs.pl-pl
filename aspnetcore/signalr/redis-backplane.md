---
title: Redis plan dla ASP.NET Core SignalR skalowanie w poziomie
author: bradygaster
description: Dowiedz się, jak skonfigurować plan Redis, aby włączyć skalowanie w poziomie dla aplikacji ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775352"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>Konfigurowanie planu Redis dla ASP.NET Core SignalR skalowanie w poziomie

Autorzy [Andrew Stanton-pielęgniarki](https://twitter.com/anurse), [Brady Gastera](https://twitter.com/bradygaster)i [Tomasz Dykstra](https://github.com/tdykstra),

W tym artykule SignalRopisano zagadnienia dotyczące konfigurowania serwera [Redis](https://redis.io/) do użycia w celu skalowania aplikacji ASP.NET Core SignalR .

## <a name="set-up-a-redis-backplane"></a>Konfigurowanie planu Redis

* Wdróż serwer Redis.

  > [!IMPORTANT] 
  > W przypadku użycia w środowisku produkcyjnym Redis planuje się tylko wtedy, gdy działa on w tym samym centrum danych SignalR co aplikacja. W przeciwnym razie opóźnienie sieci obniży wydajność. Jeśli SignalR aplikacja jest uruchomiona w chmurze platformy Azure, zalecamy użycie usługi platformy SignalR Azure zamiast planu Redis. Usługi Azure Redis Cache można używać w środowiskach deweloperskich i testowych.

  Więcej informacji zawierają następujące zasoby:

  * <xref:signalr/scale>
  * [Dokumentacja Redis](https://redis.io/)
  * [Dokumentacja Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* W SignalR aplikacji zainstaluj pakiet `Microsoft.AspNetCore.SignalR.Redis` NuGet.
* W `Startup.ConfigureServices` metodzie Wywołaj `AddRedis` po `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Skonfiguruj opcje zgodnie z wymaganiami:
 
  Większość opcji można ustawić w parametrach połączenia lub w obiekcie [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Opcje określone w `ConfigurationOptions` przesłonięciu zestawu w parametrach połączenia.

  Poniższy przykład pokazuje, jak ustawić opcje w `ConfigurationOptions` obiekcie. Ten przykład dodaje prefiks kanału, dzięki czemu wiele aplikacji może współużytkować to samo wystąpienie Redis, co zostało wyjaśnione w poniższym kroku.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  W powyższym kodzie `options.Configuration` , jest inicjowany za pomocą dowolnego elementu, który został określony w parametrach połączenia.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* W SignalR aplikacji zainstaluj jeden z następujących pakietów NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`— Zależy od StackExchange. Redis 2. X.X. Jest to zalecany pakiet dla ASP.NET Core 2,2 i nowszych.
  * `Microsoft.AspNetCore.SignalR.Redis`— Zależy od StackExchange. Redis 1. X.X. Ten pakiet nie jest uwzględniony w ASP.NET Core 3,0 i nowszych.

* W `Startup.ConfigureServices` metodzie Wywołaj <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Podczas używania `Microsoft.AspNetCore.SignalR.Redis`, wywołaj <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

* Skonfiguruj opcje zgodnie z wymaganiami:
 
  Większość opcji można ustawić w parametrach połączenia lub w obiekcie [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Opcje określone w `ConfigurationOptions` przesłonięciu zestawu w parametrach połączenia.

  Poniższy przykład pokazuje, jak ustawić opcje w `ConfigurationOptions` obiekcie. Ten przykład dodaje prefiks kanału, dzięki czemu wiele aplikacji może współużytkować to samo wystąpienie Redis, co zostało wyjaśnione w poniższym kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Podczas używania `Microsoft.AspNetCore.SignalR.Redis`, wywołaj <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

  W powyższym kodzie `options.Configuration` , jest inicjowany za pomocą dowolnego elementu, który został określony w parametrach połączenia.

  Informacje o opcjach Redis można znaleźć w [dokumentacji stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* W SignalR aplikacji zainstaluj następujący pakiet NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* W `Startup.ConfigureServices` metodzie Wywołaj <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Skonfiguruj opcje zgodnie z wymaganiami:
 
  Większość opcji można ustawić w parametrach połączenia lub w obiekcie [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Opcje określone w `ConfigurationOptions` przesłonięciu zestawu w parametrach połączenia.

  Poniższy przykład pokazuje, jak ustawić opcje w `ConfigurationOptions` obiekcie. Ten przykład dodaje prefiks kanału, dzięki czemu wiele aplikacji może współużytkować to samo wystąpienie Redis, co zostało wyjaśnione w poniższym kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  W powyższym kodzie `options.Configuration` , jest inicjowany za pomocą dowolnego elementu, który został określony w parametrach połączenia.

  Informacje o opcjach Redis można znaleźć w [dokumentacji stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Jeśli używasz jednego serwera Redis dla wielu SignalR aplikacji, użyj innego prefiksu kanału dla każdej SignalR aplikacji.

  Ustawienie prefiksu kanału izoluje jedną SignalR aplikację od innych, które używają różnych prefiksów kanałów. Jeśli nie przypiszesz różnych prefiksów, komunikat wysłany z jednej aplikacji do wszystkich klientów będzie przeszedł do wszystkich klientów wszystkich aplikacji korzystających z serwera Redis jako planu wieloplanowego.

* Skonfiguruj oprogramowanie do równoważenia obciążenia farmy serwerów dla sesji programu Sticky Notes. Oto kilka przykładów dokumentacji dotyczącej tego, jak to zrobić:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Błędy serwera Redis

Gdy serwer Redis ulegnie awarii, program SignalR generuje wyjątki wskazujące, że komunikaty nie zostaną dostarczone. Niektóre typowe komunikaty o wyjątkach:

* *Nie można zapisać komunikatu*
* *Nie można wywołać metody centrum "MethodName"*
* *Nie można nawiązać połączenia z Redis*

SignalRnie buforuje komunikatów, aby wysłać je, gdy serwer powróci do tworzenia kopii zapasowej. Wszystkie komunikaty wysyłane w trakcie działania serwera Redis są tracone.

SignalRautomatycznie ponownie nawiązuje połączenie po ponownym udostępnieniu serwera Redis.

### <a name="custom-behavior-for-connection-failures"></a>Niestandardowe zachowanie dla błędów połączeń

Oto przykład, który pokazuje, jak obsługiwać zdarzenia niepowodzeń połączeń Redis.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Redis klastrowanie

[Redis klastrowanie](https://redis.io/topics/cluster-spec) to metoda osiągania wysokiej dostępności przy użyciu wielu serwerów Redis. Klastrowanie nie jest oficjalnie obsługiwane, ale może zadziałało.

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* <xref:signalr/scale>
* [Dokumentacja Redis](https://redis.io/documentation)
* [Dokumentacja StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Dokumentacja Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/)
