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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 58c1ff2c9334e75535f6e5f0f418976176822724
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408477"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="be78c-103">Konfigurowanie planu Redis dla ASP.NET Core SignalR skalowanie w poziomie</span><span class="sxs-lookup"><span data-stu-id="be78c-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="be78c-104">Autorzy [Andrew Stanton-pielęgniarki](https://twitter.com/anurse), [Brady Gastera](https://twitter.com/bradygaster)i [Tomasz Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="be78c-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="be78c-105">W tym artykule opisano SignalR zagadnienia dotyczące konfigurowania serwera [Redis](https://redis.io/) do użycia w celu skalowania SignalR aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be78c-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="be78c-106">Konfigurowanie planu Redis</span><span class="sxs-lookup"><span data-stu-id="be78c-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="be78c-107">Wdróż serwer Redis.</span><span class="sxs-lookup"><span data-stu-id="be78c-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="be78c-108">W przypadku użycia w środowisku produkcyjnym Redis planuje się tylko wtedy, gdy działa on w tym samym centrum danych co SignalR aplikacja.</span><span class="sxs-lookup"><span data-stu-id="be78c-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="be78c-109">W przeciwnym razie opóźnienie sieci obniży wydajność.</span><span class="sxs-lookup"><span data-stu-id="be78c-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="be78c-110">Jeśli SignalR aplikacja jest uruchomiona w chmurze platformy Azure, zalecamy użycie usługi platformy Azure SignalR zamiast planu Redis.</span><span class="sxs-lookup"><span data-stu-id="be78c-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="be78c-111">Usługi Azure Redis Cache można używać w środowiskach deweloperskich i testowych.</span><span class="sxs-lookup"><span data-stu-id="be78c-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="be78c-112">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="be78c-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="be78c-113">Dokumentacja Redis</span><span class="sxs-lookup"><span data-stu-id="be78c-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="be78c-114">Dokumentacja Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="be78c-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="be78c-115">W SignalR aplikacji zainstaluj `Microsoft.AspNetCore.SignalR.Redis` pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="be78c-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="be78c-116">W `Startup.ConfigureServices` metodzie Wywołaj `AddRedis` po `AddSignalR` :</span><span class="sxs-lookup"><span data-stu-id="be78c-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="be78c-117">Skonfiguruj opcje zgodnie z wymaganiami:</span><span class="sxs-lookup"><span data-stu-id="be78c-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="be78c-118">Większość opcji można ustawić w parametrach połączenia lub w obiekcie [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="be78c-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="be78c-119">Opcje określone w `ConfigurationOptions` przesłonięciu zestawu w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="be78c-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="be78c-120">Poniższy przykład pokazuje, jak ustawić opcje w `ConfigurationOptions` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="be78c-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="be78c-121">Ten przykład dodaje prefiks kanału, dzięki czemu wiele aplikacji może współużytkować to samo wystąpienie Redis, co zostało wyjaśnione w poniższym kroku.</span><span class="sxs-lookup"><span data-stu-id="be78c-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="be78c-122">W powyższym kodzie, `options.Configuration` jest inicjowany za pomocą dowolnego elementu, który został określony w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="be78c-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="be78c-123">W SignalR aplikacji zainstaluj jeden z następujących pakietów NuGet:</span><span class="sxs-lookup"><span data-stu-id="be78c-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="be78c-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`— Zależy od StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="be78c-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="be78c-125">Jest to zalecany pakiet dla ASP.NET Core 2,2 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="be78c-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="be78c-126">`Microsoft.AspNetCore.SignalR.Redis`— Zależy od StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="be78c-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="be78c-127">Ten pakiet nie jest uwzględniony w ASP.NET Core 3,0 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="be78c-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="be78c-128">W `Startup.ConfigureServices` metodzie Wywołaj <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="be78c-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="be78c-129">Podczas używania `Microsoft.AspNetCore.SignalR.Redis` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="be78c-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="be78c-130">Skonfiguruj opcje zgodnie z wymaganiami:</span><span class="sxs-lookup"><span data-stu-id="be78c-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="be78c-131">Większość opcji można ustawić w parametrach połączenia lub w obiekcie [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="be78c-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="be78c-132">Opcje określone w `ConfigurationOptions` przesłonięciu zestawu w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="be78c-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="be78c-133">Poniższy przykład pokazuje, jak ustawić opcje w `ConfigurationOptions` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="be78c-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="be78c-134">Ten przykład dodaje prefiks kanału, dzięki czemu wiele aplikacji może współużytkować to samo wystąpienie Redis, co zostało wyjaśnione w poniższym kroku.</span><span class="sxs-lookup"><span data-stu-id="be78c-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="be78c-135">Podczas używania `Microsoft.AspNetCore.SignalR.Redis` , wywołaj <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="be78c-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="be78c-136">W powyższym kodzie, `options.Configuration` jest inicjowany za pomocą dowolnego elementu, który został określony w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="be78c-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="be78c-137">Informacje o opcjach Redis można znaleźć w [dokumentacji stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="be78c-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="be78c-138">W SignalR aplikacji zainstaluj następujący pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="be78c-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="be78c-139">W `Startup.ConfigureServices` metodzie Wywołaj <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="be78c-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="be78c-140">Skonfiguruj opcje zgodnie z wymaganiami:</span><span class="sxs-lookup"><span data-stu-id="be78c-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="be78c-141">Większość opcji można ustawić w parametrach połączenia lub w obiekcie [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="be78c-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="be78c-142">Opcje określone w `ConfigurationOptions` przesłonięciu zestawu w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="be78c-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="be78c-143">Poniższy przykład pokazuje, jak ustawić opcje w `ConfigurationOptions` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="be78c-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="be78c-144">Ten przykład dodaje prefiks kanału, dzięki czemu wiele aplikacji może współużytkować to samo wystąpienie Redis, co zostało wyjaśnione w poniższym kroku.</span><span class="sxs-lookup"><span data-stu-id="be78c-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="be78c-145">W powyższym kodzie, `options.Configuration` jest inicjowany za pomocą dowolnego elementu, który został określony w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="be78c-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="be78c-146">Informacje o opcjach Redis można znaleźć w [dokumentacji stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="be78c-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="be78c-147">Jeśli używasz jednego serwera Redis dla wielu SignalR aplikacji, użyj innego prefiksu kanału dla każdej SignalR aplikacji.</span><span class="sxs-lookup"><span data-stu-id="be78c-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="be78c-148">Ustawienie prefiksu kanału izoluje jedną SignalR aplikację od innych, które używają różnych prefiksów kanałów.</span><span class="sxs-lookup"><span data-stu-id="be78c-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="be78c-149">Jeśli nie przypiszesz różnych prefiksów, komunikat wysłany z jednej aplikacji do wszystkich klientów będzie przeszedł do wszystkich klientów wszystkich aplikacji korzystających z serwera Redis jako planu wieloplanowego.</span><span class="sxs-lookup"><span data-stu-id="be78c-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="be78c-150">Skonfiguruj oprogramowanie do równoważenia obciążenia farmy serwerów dla sesji programu Sticky Notes.</span><span class="sxs-lookup"><span data-stu-id="be78c-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="be78c-151">Oto kilka przykładów dokumentacji dotyczącej tego, jak to zrobić:</span><span class="sxs-lookup"><span data-stu-id="be78c-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="be78c-152">IIS</span><span class="sxs-lookup"><span data-stu-id="be78c-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="be78c-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="be78c-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="be78c-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="be78c-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="be78c-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="be78c-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="be78c-156">Błędy serwera Redis</span><span class="sxs-lookup"><span data-stu-id="be78c-156">Redis server errors</span></span>

<span data-ttu-id="be78c-157">Gdy serwer Redis ulegnie awarii, program SignalR generuje wyjątki wskazujące, że komunikaty nie zostaną dostarczone.</span><span class="sxs-lookup"><span data-stu-id="be78c-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="be78c-158">Niektóre typowe komunikaty o wyjątkach:</span><span class="sxs-lookup"><span data-stu-id="be78c-158">Some typical exception messages:</span></span>

* <span data-ttu-id="be78c-159">*Nie można zapisać komunikatu*</span><span class="sxs-lookup"><span data-stu-id="be78c-159">*Failed writing message*</span></span>
* <span data-ttu-id="be78c-160">*Nie można wywołać metody centrum "MethodName"*</span><span class="sxs-lookup"><span data-stu-id="be78c-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="be78c-161">*Nie można nawiązać połączenia z Redis*</span><span class="sxs-lookup"><span data-stu-id="be78c-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="be78c-162">nie buforuje komunikatów, aby wysłać je, gdy serwer powróci do tworzenia kopii zapasowej.</span><span class="sxs-lookup"><span data-stu-id="be78c-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="be78c-163">Wszystkie komunikaty wysyłane w trakcie działania serwera Redis są tracone.</span><span class="sxs-lookup"><span data-stu-id="be78c-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="be78c-164">automatycznie ponownie nawiązuje połączenie po ponownym udostępnieniu serwera Redis.</span><span class="sxs-lookup"><span data-stu-id="be78c-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="be78c-165">Niestandardowe zachowanie dla błędów połączeń</span><span class="sxs-lookup"><span data-stu-id="be78c-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="be78c-166">Oto przykład, który pokazuje, jak obsługiwać zdarzenia niepowodzeń połączeń Redis.</span><span class="sxs-lookup"><span data-stu-id="be78c-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="be78c-167">Redis klastrowanie</span><span class="sxs-lookup"><span data-stu-id="be78c-167">Redis Clustering</span></span>

<span data-ttu-id="be78c-168">[Redis klastrowanie](https://redis.io/topics/cluster-spec) to metoda osiągania wysokiej dostępności przy użyciu wielu serwerów Redis.</span><span class="sxs-lookup"><span data-stu-id="be78c-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="be78c-169">Klastrowanie nie jest oficjalnie obsługiwane, ale może zadziałało.</span><span class="sxs-lookup"><span data-stu-id="be78c-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="be78c-170">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="be78c-170">Next steps</span></span>

<span data-ttu-id="be78c-171">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="be78c-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="be78c-172">Dokumentacja Redis</span><span class="sxs-lookup"><span data-stu-id="be78c-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="be78c-173">Dokumentacja StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="be78c-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="be78c-174">Dokumentacja Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="be78c-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
