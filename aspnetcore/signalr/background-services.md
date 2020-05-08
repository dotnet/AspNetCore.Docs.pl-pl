---
title: ASP.NET Core SignalR hosta w usługach w tle
author: bradygaster
description: Dowiedz się, jak wysyłać SignalR komunikaty do klientów z klas BackgroundService platformy .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777296"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR hosta w usługach w tle

Autor [Brady gastera](https://twitter.com/bradygaster)

Ten artykuł zawiera wskazówki dotyczące:

* Hosting SignalR centrów przy użyciu procesu roboczego w tle hostowanego z ASP.NET Core.
* Wysyłanie komunikatów do podłączonych klientów z poziomu platformy .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Włącz SignalR przy uruchamianiu

::: moniker range=">= aspnetcore-3.0"

Hostowanie SignalR ASP.NET Core Hub w kontekście procesu roboczego w tle jest takie samo, jak hostowanie centrum w aplikacji sieci Web ASP.NET Core. W `Startup.ConfigureServices` metodzie wywoływanie `services.AddSignalR` powoduje dodanie wymaganych usług do warstwy iniekcja (ASP.NET Core zależność) do obsługi SignalR. W `Startup.Configure`programie `MapHub` Metoda jest wywoływana w `UseEndpoints` wywołaniu zwrotnym, aby połączyć punkty końcowe centrum w potoku żądania ASP.NET Core.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Hostowanie SignalR ASP.NET Core Hub w kontekście procesu roboczego w tle jest takie samo, jak hostowanie centrum w aplikacji sieci Web ASP.NET Core. W `Startup.ConfigureServices` metodzie wywoływanie `services.AddSignalR` powoduje dodanie wymaganych usług do warstwy iniekcja (ASP.NET Core zależność) do obsługi SignalR. W `Startup.Configure`programie `UseSignalR` Metoda jest wywoływana, aby połączyć punkty końcowe centrum w potoku żądania ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

W poprzednim przykładzie `ClockHub` Klasa implementuje `Hub<T>` klasę, aby utworzyć koncentrator o jednoznacznie określonym typie. Program `ClockHub` został skonfigurowany w `Startup` klasie w celu reagowania na żądania w punkcie końcowym `/hubs/clock`.

Aby uzyskać więcej informacji na temat silnych typach centrów, zobacz [Używanie centrów w SignalR dla ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Ta funkcja nie jest ograniczona do [klasy\<Hub t>](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Każda klasa, która dziedziczy z [koncentratora](xref:Microsoft.AspNetCore.SignalR.Hub), na przykład [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), również będzie działała.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Interfejs używany przez silnie typ `ClockHub` jest `IClock` interfejsem.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Wywoływanie SignalR centrum z poziomu usługi w tle

Podczas uruchamiania, `Worker` Klasa, a `BackgroundService`, jest włączona przy użyciu `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Ponieważ SignalR jest również włączona w `Startup` fazie, w którym każde centrum jest dołączone do pojedynczego punktu końcowego w potoku żądania HTTP ASP.NET Core, każde centrum jest reprezentowane przez `IHubContext<T>` serwer. Przy użyciu ASP.NET Core DI Features, inne klasy tworzone przez warstwę hostingu, takie jak `BackgroundService` klasy, klasy kontrolerów MVC lub Razor modele stron, mogą uzyskać odwołania do centrów po stronie serwera, akceptując wystąpienia `IHubContext<ClockHub, IClock>` w trakcie konstruowania.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Ponieważ `ExecuteAsync` Metoda jest wywoływana iteracyjnie w usłudze w tle, bieżąca data i godzina serwera są wysyłane do podłączonych klientów przy użyciu `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Reagowanie SignalR na zdarzenia za pomocą usług w tle

Podobnie jak w przypadku aplikacji jednostronicowej korzystającej SignalR z klienta języka JavaScript dla programu lub aplikacji klasycznej platformy <xref:signalr/dotnet-client>.NET, `BackgroundService` można `IHostedService` również użyć funkcji, a lub do łączenia SignalR się z centrami i odpowiadać na zdarzenia.

`ClockHubClient` Klasa implementuje `IClock` interfejs i `IHostedService` interfejs. W `Startup` ten sposób można ją włączyć w sposób ciągły i reagować na zdarzenia centrów z serwera.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

`ClockHubClient` Podczas inicjowania program tworzy `HubConnection` wystąpienie a i włącza `IClock.ShowTime` metodę jako procedurę obsługi dla `ShowTime` zdarzenia centrum.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

W `IHostedService.StartAsync` implementacji `HubConnection` jest uruchamiany asynchronicznie.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

W trakcie `IHostedService.StopAsync` metody `HubConnection` jest usuwany asynchronicznie.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Rozpoczęcie pracy](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
* [Centra o jednoznacznie określonym typie](xref:signalr/hubs#strongly-typed-hubs)
