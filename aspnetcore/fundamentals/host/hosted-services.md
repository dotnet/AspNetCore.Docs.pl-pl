---
title: Zadania w tle z usługami hostowanymi w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zaimplementować zadania w tle za pomocą usług hostowanych w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/hosted-services
ms.openlocfilehash: ebc39e7a6869911d464a340caea8eadc93ea72e0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407204"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Zadania w tle z usługami hostowanymi w ASP.NET Core

Według [Jeow li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*. Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs. Ten temat zawiera trzy przykłady usługi hostowanej:

* Zadanie w tle, które jest uruchamiane na czasomierzu.
* Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie. Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection).
* Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Szablon usługi procesu roboczego

Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi. Aplikacja utworzona na podstawie szablonu usługi procesu roboczego określa zestaw SDK procesu roboczego w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Aby użyć szablonu jako podstawy dla aplikacji usług hostowanych:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Pakiet

Aplikacja oparta na szablonie usługi procesu roboczego używa `Microsoft.NET.Sdk.Worker` zestawu SDK i ma jawne odwołanie do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) . Na przykład zapoznaj się z plikiem projektu przykładowej aplikacji (*BackgroundTasksSample. csproj*).

W przypadku aplikacji sieci Web, które używają `Microsoft.NET.Sdk.Web` zestawu SDK, pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) jest przywoływany niejawnie z udostępnionej struktury. Jawne odwołanie do pakietu w pliku projektu aplikacji nie jest wymagane.

## <a name="ihostedservice-interface"></a>IHostedService, interfejs

<xref:Microsoft.Extensions.Hosting.IHostedService>Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle. `StartAsync`jest wywoływana *przed*:

  * Skonfigurowano potok przetwarzania żądania aplikacji ( `Startup.Configure` ).
  * Serwer został uruchomiony i zostanie wyzwolony [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .

  Zachowanie domyślne można zmienić tak, aby usługa hostowana `StartAsync` działała po skonfigurowaniu potoku aplikacji i `ApplicationStarted` jest wywoływana. Aby zmienić zachowanie domyślne, należy dodać usługę hostowaną ( `VideosWatcher` w poniższym przykładzie) po wywołaniu `ConfigureWebHostDefaults` :

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie. `StopAsync`zawiera logikę końcową zadania w tle. Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.

  Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny. Gdy żądanie anulowania jest wymagane na tokenie:

  * Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.
  * Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.

  Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.

  Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany. W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.

  Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>w przypadku korzystania z hosta ogólnego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.

Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji. Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.

## <a name="backgroundservice-base-class"></a>Klasa bazowa BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>jest klasą bazową do implementowania długotrwałego działania <xref:Microsoft.Extensions.Hosting.IHostedService> .

[Wywoływanie ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) jest wywoływana w celu uruchomienia usługi w tle. Implementacja zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą cały okres istnienia usługi w tle. Żadne dalsze usługi nie są uruchamiane do momentu, gdy [wywoływanie ExecuteAsync staną się asynchroniczne](https://github.com/dotnet/extensions/issues/2149), na przykład przez wywołanie `await` . Unikaj długotrwałych operacji inicjalizacji `ExecuteAsync` . Bloki hosta w [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) oczekują na `ExecuteAsync` zakończenie.

Token anulowania jest wyzwalany, gdy wywoływana jest [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) . Implementacja programu `ExecuteAsync` powinna zakończyć się natychmiast po uruchomieniu tokenu anulowania w celu łagodnego zamknięcia usługi. W przeciwnym razie usługa niebezpiecznie zamyka się po upływie limitu czasu zamykania. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [interfejsu IHostedService](#ihostedservice-interface) .

## <a name="timed-background-tasks"></a>Zadania w tle czasu

Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) . Czasomierz wyzwala `DoWork` metodę zadania. Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza. Z [blokadą. przyrost](xref:System.Threading.Interlocked.Increment*) służy do zwiększania licznika wykonywania jako operacja niepodzielna, która zapewnia, że wiele wątków nie zostanie jednocześnie zaktualizowanych `executionCount` .

Usługa jest zarejestrowana w `IHostBuilder.ConfigureServices` (*program.cs*) z `AddHostedService` metodą rozszerzenia:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Zużywanie usługi w zakresie w zadaniu w tle

Aby użyć [usług z zakresem](xref:fundamentals/dependency-injection#service-lifetimes) w ramach [BackgroundService](#backgroundservice-base-class), Utwórz zakres. Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.

Usługa zadań w tle w zakresie zawiera logikę zadania w tle. W poniższym przykładzie:

* Usługa jest asynchroniczna. `DoWork`Metoda zwraca `Task` . W celach demonstracyjnych oczekuje się, że w metodzie występuje opóźnienie o dziesięć sekund `DoWork` .
* <xref:Microsoft.Extensions.Logging.ILogger>Jest wstrzykiwana do usługi.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie, aby wywołać `DoWork` metodę. `DoWork`Zwraca element `Task` , który jest oczekiwany w `ExecuteAsync` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*). Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Zadania w kolejce w dół

Kolejka zadań w tle jest oparta na platformie .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

W poniższym `QueueHostedService` przykładzie:

* `BackgroundProcessing`Metoda zwraca obiekt `Task` , który jest oczekiwany w `ExecuteAsync` .
* Zadania w tle w kolejce są dekolejkowane i wykonywane w `BackgroundProcessing` .
* Elementy robocze oczekują na zatrzymanie usługi w usłudze `StopAsync` .

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

`MonitorLoop`Usługa obsługuje zadania umieszczenie dla usługi hostowanej za każdym razem, gdy `w` klucz zostanie wybrany na urządzeniu wejściowym:

* `IBackgroundTaskQueue`Jest wstrzykiwana do `MonitorLoop` usługi.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`jest wywoływana w celu dodawania do kolejki elementu pracy.
* Element roboczy symuluje długotrwałe zadanie w tle:
  * Trzy 5-sekundowe opóźnienia są wykonywane ( `Task.Delay` ).
  * `try-catch`Pułapka instrukcji <xref:System.OperationCanceledException> , jeśli zadanie zostało anulowane.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*). Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MonitorLoop`jest uruchamiany w `Program.Main` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*. Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs. Ten temat zawiera trzy przykłady usługi hostowanej:

* Zadanie w tle, które jest uruchamiane na czasomierzu.
* Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie. Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection)
* Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pakiet

Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .

## <a name="ihostedservice-interface"></a>IHostedService, interfejs

Usługi hostowane implementują <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs. Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle. W przypadku korzystania z [hosta sieci Web](xref:fundamentals/host/web-host) `StartAsync` jest wywoływana po uruchomieniu serwera i wyzwoleniu [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) . W przypadku korzystania z [hosta generycznego](xref:fundamentals/host/generic-host) `StartAsync` jest wywoływana przed wywołaniem `ApplicationStarted` .

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie. `StopAsync`zawiera logikę końcową zadania w tle. Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.

  Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny. Gdy żądanie anulowania jest wymagane na tokenie:

  * Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.
  * Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.

  Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.

  Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany. W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.

  Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>w przypadku korzystania z hosta ogólnego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.

Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji. Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.

## <a name="timed-background-tasks"></a>Zadania w tle czasu

Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) . Czasomierz wyzwala `DoWork` metodę zadania. Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.

Usługa jest zarejestrowana w ramach `Startup.ConfigureServices` `AddHostedService` metody rozszerzającej:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Zużywanie usługi w zakresie w zadaniu w tle

Aby korzystać z usług o określonym [zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w ramach programu `IHostedService` , należy utworzyć zakres. Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.

Usługa zadań w tle w zakresie zawiera logikę zadania w tle. W poniższym przykładzie <xref:Microsoft.Extensions.Logging.ILogger> jest wstrzykiwana do usługi:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie w celu wywołania jej `DoWork` metody:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` . `IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Zadania w kolejce w dół

Kolejka zadań w tle jest oparta na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([wstępnie zaplanowano wbudowaną ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

W programie `QueueHostedService` zadania w tle w kolejce są odrzucane i wykonywane jako [BackgroundService](#backgroundservice-base-class), która jest klasą bazową do implementowania długotrwałego działania `IHostedService` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` . `IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

W klasie modelu strony indeksu:

* `IBackgroundTaskQueue`Zostaje wstrzyknięty do konstruktora i przypisany do `Queue` .
* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Jest wstrzykiwany i przypisany do `_serviceScopeFactory` . Fabryka służy do tworzenia wystąpień <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> , które są używane do tworzenia usług w zakresie. Zakres jest tworzony w celu używania aplikacji `AppDbContext` ( [usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes)) do zapisywania rekordów bazy danych w `IBackgroundTaskQueue` (pojedynczej usłudze).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Po wybraniu przycisku **Dodaj zadanie** na stronie indeks `OnPostAddTask` jest wykonywana metoda. `QueueBackgroundWorkItem`jest wywoływana w celu dodawania do kolejki elementu pracy:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Implementowanie zadań w tle w mikrousługach za pomocą IHostedService i klasy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
