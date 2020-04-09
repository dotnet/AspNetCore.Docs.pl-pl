---
title: Zadania w tle z hostowanymi usługami w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zaimplementować zadania w tle za pomocą hostowanych usług w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666202"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Zadania w tle z hostowanymi usługami w ASP.NET Core

Przez [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

W ASP.NET Core zadania w tle mogą być realizowane jako *usługi hostowane.* Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs. W tym temacie przedstawiono trzy przykłady usług hostowanych:

* Zadanie w tle, które działa na czasomierzu.
* Usługa hostowana, która aktywuje [usługę o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes). Usługa o określonym zakresie może używać [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).
* Zadania w tle umieszczone w kolejce, które są uruchamiane sekwencyjnie.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Szablon usługi pracownika

Szablon ASP.NET Core Worker Service stanowi punkt wyjścia do pisania długo działających aplikacji usługi. Aplikacja utworzona na podstawie szablonu Usługi procesu roboczego określa sdk procesu roboczego w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Aby użyć szablonu jako podstawy dla aplikacji usług hostowanych:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Pakiet

Aplikacja oparta na szablonie usługi `Microsoft.NET.Sdk.Worker` procesu roboczego używa zestawu SDK i ma jawne odwołanie do pakietu [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) Na przykład zobacz przykładowy plik projektu aplikacji (*BackgroundTasksSample.csproj*).

W przypadku aplikacji `Microsoft.NET.Sdk.Web` sieci web, które używają zestawu SDK, pakiet [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) odwołuje się niejawnie z udostępnionej struktury. Jawne odwołanie do pakietu w pliku projektu aplikacji nie jest wymagane.

## <a name="ihostedservice-interface"></a>Interfejs IHostedService

Interfejs <xref:Microsoft.Extensions.Hosting.IHostedService> definiuje dwie metody dla obiektów, które są zarządzane przez hosta:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` zawiera logikę uruchamiania zadania w tle. `StartAsync`nazywa się *przed:*

  * Potok przetwarzania żądań aplikacji jest`Startup.Configure`skonfigurowany ( ).
  * Serwer jest uruchamiany i [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) jest wyzwalany.

  Domyślne zachowanie można zmienić tak, aby `StartAsync` usługa hostowana przebiegała po `ApplicationStarted` skonfigurowaniu potoku aplikacji i wywoływaniu. Aby zmienić zachowanie domyślne, dodaj`VideosWatcher` usługę hosta (w poniższym przykładzie) po wywołaniu: `ConfigureWebHostDefaults`

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

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Wyzwalane, gdy host wykonuje pełne wdzięku zamknięcia. `StopAsync`zawiera logikę, aby zakończyć zadanie w tle. Implementowanie <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich zasobów niezarządzanych.

  Token anulowania ma domyślny limit czasu pięciu sekund, aby wskazać, że proces zamykania nie powinien być już pełen wdzięku. Gdy anulowanie jest wymagane na tokenie:

  * Wszystkie pozostałe operacje w tle, które działa aplikacja powinna zostać przerwana.
  * Wszelkie metody `StopAsync` wywoływane w należy zwrócić niezwłocznie.

  Jednak zadania nie są porzucone po&mdash;anulowaniu jest wymagane wywołujący oczekuje na wszystkie zadania do wykonania.

  Jeśli aplikacja zostanie nieoczekiwanie zamknięta (na przykład proces aplikacji `StopAsync` zakończy się niepowodzeniem), może nie zostać wywołana. W związku z tym wszelkie metody `StopAsync` wywoływane lub operacje prowadzone w może nie wystąpić.

  Aby przedłużyć domyślny limit czasu zamknięcia o pięć sekund, ustaw:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>podczas korzystania z hosta ogólnego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Ustawienie konfiguracji hosta limitu czasu zamknięcia podczas korzystania z hosta sieci Web. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostowana usługa jest aktywowana raz przy uruchamianiu aplikacji i bezpiecznie zamykana przy zamykaniu aplikacji. Jeśli podczas wykonywania zadania w `Dispose` tle zostanie wyświetlony `StopAsync` błąd, należy wywołać, nawet jeśli nie jest wywoływany.

## <a name="backgroundservice-base-class"></a>Klasa podstawowa usługi backgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>jest klasą bazową do <xref:Microsoft.Extensions.Hosting.IHostedService>wdrażania długotrwałego .

[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) jest wywoływana do uruchomienia usługi w tle. Implementacja <xref:System.Threading.Tasks.Task> zwraca, który reprezentuje cały okres istnienia usługi w tle. Żadne dalsze usługi nie są uruchamiane, dopóki [ExecuteAsync nie stanie się asynchroniczny,](https://github.com/dotnet/extensions/issues/2149)na przykład przez wywołanie `await`. Unikaj długich czynności, blokując `ExecuteAsync`pracę inicjalizacji w pliku . Bloki hosta w [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) oczekują na `ExecuteAsync` ukończenie.

Token anulowania jest wyzwalany, gdy wywoływana jest [usługa IHostedService.StopAsync.](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) Implementacja `ExecuteAsync` powinna zakończyć się niezwłocznie, gdy token anulowania jest uruchamiany w celu bezpiecznie zamknąć usługę. W przeciwnym razie usługa ungracefully zamyka się na limit czasu zamknięcia. Aby uzyskać więcej informacji, zobacz sekcję [interfejsu IHostedService.](#ihostedservice-interface)

## <a name="timed-background-tasks"></a>Zadania w tle z harmonogramem

Czasowe zadanie w tle korzysta z [Klasy System.Threading.Timer.](xref:System.Threading.Timer) Czasomierz wyzwala `DoWork` metodę zadania. Czasomierz jest `StopAsync` wyłączony i usuwany, gdy pojemnik `Dispose`serwisowy jest usuwany na:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Nie <xref:System.Threading.Timer> czeka na poprzednie wykonanie `DoWork` zakończyć, więc podejście pokazane może nie być odpowiednie dla każdego scenariusza. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) jest używany do zwiększania licznika wykonywania jako operacji niepodzielnej, co `executionCount` zapewnia, że wiele wątków nie aktualizuje jednocześnie.

Usługa jest zarejestrowana w `IHostBuilder.ConfigureServices` (*Program.cs* `AddHostedService` ) z metodą rozszerzenia:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Korzystanie z usługi o określonym zakresie w zadaniu w tle

Aby użyć [usług o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w [backgroundservice](#backgroundservice-base-class), należy utworzyć zakres. Domyślnie nie jest tworzony żaden zakres dla usługi hosta.

Usługa zadań w tle o określonym zakresie zawiera logikę zadania w tle. W poniższym przykładzie:

* Usługa jest asynchroniza. Metoda `DoWork` zwraca `Task`. W celach demonstracyjnych w metodzie `DoWork` oczekuje się opóźnienia o dziesięć sekund.
* An <xref:Microsoft.Extensions.Logging.ILogger> jest wstrzykiwany do serwisu.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Usługa hostowana tworzy zakres, aby rozwiązać zakres usługi `DoWork` zadań w tle, aby wywołać jego metodę. `DoWork`zwraca `Task`, który jest `ExecuteAsync`oczekiwany w :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Usługi są zarejestrowane w `IHostBuilder.ConfigureServices` (*Program.cs*). Usługa hostowana jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Zadania w tle umieszczone w kolejce

Kolejka zadań w tle jest oparta <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> na .NET 4.x[(wstępnie zaplanowane do wbudowania dla ASP.NET Core):](https://github.com/aspnet/Hosting/issues/1280)

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

W poniższym `QueueHostedService` przykładzie:

* Metoda `BackgroundProcessing` zwraca `Task`, który jest `ExecuteAsync`oczekiwany w .
* Zadania w tle w kolejce są `BackgroundProcessing`usuwane z kolejki i wykonywane w pliku .
* Elementy robocze są oczekiwane `StopAsync`przed zatrzymaniem usługi w .

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Usługa `MonitorLoop` obsługuje zadania wyjęcie w kolejce `w` dla usługi hosta, gdy klucz jest wybrany na urządzeniu wejściowym:

* Jest `IBackgroundTaskQueue` wstrzykiwany do `MonitorLoop` serwisu.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`jest wywoływana w celu wyłowienia w kolejce elementu roboczego.
* Element roboczy symuluje długotrwałe zadanie w tle:
  * Wykonywane są trzy 5-sekundowe opóźnienia (`Task.Delay`).
  * Pułapki `try-catch` <xref:System.OperationCanceledException> instrukcji, jeśli zadanie zostało anulowane.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Usługi są zarejestrowane w `IHostBuilder.ConfigureServices` (*Program.cs*). Usługa hostowana jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop`rozpoczyna się `Program.Main`w:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W ASP.NET Core zadania w tle mogą być realizowane jako *usługi hostowane.* Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs. W tym temacie przedstawiono trzy przykłady usług hostowanych:

* Zadanie w tle, które działa na czasomierzu.
* Usługa hostowana, która aktywuje [usługę o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes). Usługa o określonym zakresie może używać [iniekcji zależności (DI)](xref:fundamentals/dependency-injection)
* Zadania w tle umieszczone w kolejce, które są uruchamiane sekwencyjnie.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pakiet

Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)

## <a name="ihostedservice-interface"></a>Interfejs IHostedService

Usługi hostowane <xref:Microsoft.Extensions.Hosting.IHostedService> implementują interfejs. Interfejs definiuje dwie metody dla obiektów, które są zarządzane przez hosta:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` zawiera logikę uruchamiania zadania w tle. Podczas korzystania z `StartAsync` [hosta sieci Web](xref:fundamentals/host/web-host), jest wywoływana po uruchomieniu serwera i [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) jest wyzwalany. Podczas korzystania z `StartAsync` [hosta ogólnego](xref:fundamentals/host/generic-host), jest wywoływana przed `ApplicationStarted` jest wyzwalany.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Wyzwalane, gdy host wykonuje pełne wdzięku zamknięcia. `StopAsync`zawiera logikę, aby zakończyć zadanie w tle. Implementowanie <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich zasobów niezarządzanych.

  Token anulowania ma domyślny limit czasu pięciu sekund, aby wskazać, że proces zamykania nie powinien być już pełen wdzięku. Gdy anulowanie jest wymagane na tokenie:

  * Wszystkie pozostałe operacje w tle, które działa aplikacja powinna zostać przerwana.
  * Wszelkie metody `StopAsync` wywoływane w należy zwrócić niezwłocznie.

  Jednak zadania nie są porzucone po&mdash;anulowaniu jest wymagane wywołujący oczekuje na wszystkie zadania do wykonania.

  Jeśli aplikacja zostanie nieoczekiwanie zamknięta (na przykład proces aplikacji `StopAsync` zakończy się niepowodzeniem), może nie zostać wywołana. W związku z tym wszelkie metody `StopAsync` wywoływane lub operacje prowadzone w może nie wystąpić.

  Aby przedłużyć domyślny limit czasu zamknięcia o pięć sekund, ustaw:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>podczas korzystania z hosta ogólnego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Ustawienie konfiguracji hosta limitu czasu zamknięcia podczas korzystania z hosta sieci Web. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostowana usługa jest aktywowana raz przy uruchamianiu aplikacji i bezpiecznie zamykana przy zamykaniu aplikacji. Jeśli podczas wykonywania zadania w `Dispose` tle zostanie wyświetlony `StopAsync` błąd, należy wywołać, nawet jeśli nie jest wywoływany.

## <a name="timed-background-tasks"></a>Zadania w tle z harmonogramem

Czasowe zadanie w tle korzysta z [Klasy System.Threading.Timer.](xref:System.Threading.Timer) Czasomierz wyzwala `DoWork` metodę zadania. Czasomierz jest `StopAsync` wyłączony i usuwany, gdy pojemnik `Dispose`serwisowy jest usuwany na:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Nie <xref:System.Threading.Timer> czeka na poprzednie wykonanie `DoWork` zakończyć, więc podejście pokazane może nie być odpowiednie dla każdego scenariusza.

Usługa jest zarejestrowana `Startup.ConfigureServices` `AddHostedService` przy metodzie rozszerzenia:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Korzystanie z usługi o określonym zakresie w zadaniu w tle

Aby użyć usług o `IHostedService`określonym [zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w ramach programu , utwórz zakres. Domyślnie nie jest tworzony żaden zakres dla usługi hosta.

Usługa zadań w tle o określonym zakresie zawiera logikę zadania w tle. W poniższym przykładzie jest <xref:Microsoft.Extensions.Logging.ILogger> wstrzykiwany do usługi:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Usługa hostowana tworzy zakres, aby rozwiązać zakres usługi `DoWork` zadań w tle, aby wywołać jego metodę:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Usługi są zarejestrowane w `Startup.ConfigureServices`. Implementacja `IHostedService` jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Zadania w tle umieszczone w kolejce

Kolejka zadań w tle jest oparta na <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> programie .NET Framework 4.x[(wstępnie zaplanowano, że zostanie wbudowana dla ASP.NET Core):](https://github.com/aspnet/Hosting/issues/1280)

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

W `QueueHostedService`, zadania w tle w kolejce są usuwane z kolejki i wykonywane jako `IHostedService` [BackgroundService](#backgroundservice-base-class), który jest klasą podstawową do implementowania długo działa:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Usługi są zarejestrowane w `Startup.ConfigureServices`. Implementacja `IHostedService` jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

W klasie modelu strony Indeks:

* Jest `IBackgroundTaskQueue` wstrzykiwany do konstruktora `Queue`i przypisany do .
* An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> jest wstrzykiwany i `_serviceScopeFactory`przypisany do . Fabryka służy do tworzenia <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>wystąpień , który jest używany do tworzenia usług w zakresie. Zakres jest tworzony w celu użycia `AppDbContext` aplikacji [(usługa o określonym zakresie)](xref:fundamentals/dependency-injection#service-lifetimes) `IBackgroundTaskQueue` do zapisu rekordów bazy danych w (usługa singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Po wybraniu przycisku **Dodaj zadanie** na `OnPostAddTask` stronie Indeks, metoda jest wykonywana. `QueueBackgroundWorkItem`jest wywoływana w celu wyłowienia elementu roboczego w kolejce:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Implementowanie zadań w tle w mikrousługach za pomocą usługi IHostedService i klasy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Uruchamianie zadań w tle za pomocą aplikacji WebJobs w usłudze Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
