---
title: Zadania w tle z usługami hostowanymi w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zaimplementować zadania w tle za pomocą usług hostowanych w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c0492c0c5b660e1387b0d0a4f6be405ded49ee92
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587479"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="534b4-103">Zadania w tle z usługami hostowanymi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="534b4-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="534b4-104">Według [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="534b4-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="534b4-105">W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*.</span><span class="sxs-lookup"><span data-stu-id="534b4-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="534b4-106">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="534b4-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="534b4-107">Ten temat zawiera trzy przykłady usługi hostowanej:</span><span class="sxs-lookup"><span data-stu-id="534b4-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="534b4-108">Zadanie w tle, które jest uruchamiane na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="534b4-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="534b4-109">Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="534b4-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="534b4-110">Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="534b4-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="534b4-111">Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="534b4-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="534b4-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="534b4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="534b4-113">Szablon usługi procesu roboczego</span><span class="sxs-lookup"><span data-stu-id="534b4-113">Worker Service template</span></span>

<span data-ttu-id="534b4-114">Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="534b4-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="534b4-115">Aplikacja utworzona na podstawie szablonu usługi procesu roboczego określa zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="534b4-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="534b4-116">Aby użyć szablonu jako podstawy dla aplikacji usług hostowanych:</span><span class="sxs-lookup"><span data-stu-id="534b4-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="534b4-117">Pakiet</span><span class="sxs-lookup"><span data-stu-id="534b4-117">Package</span></span>

<span data-ttu-id="534b4-118">Aplikacja oparta na szablonie usługi procesu roboczego używa `Microsoft.NET.Sdk.Worker` zestawu SDK i ma jawne odwołanie do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="534b4-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="534b4-119">Na przykład zapoznaj się z plikiem projektu przykładowej aplikacji (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="534b4-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="534b4-120">W przypadku aplikacji sieci Web, które używają `Microsoft.NET.Sdk.Web` zestawu SDK, pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) jest przywoływany niejawnie z udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="534b4-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="534b4-121">Jawne odwołanie do pakietu w pliku projektu aplikacji nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="534b4-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="534b4-122">IHostedService, interfejs</span><span class="sxs-lookup"><span data-stu-id="534b4-122">IHostedService interface</span></span>

<span data-ttu-id="534b4-123"><xref:Microsoft.Extensions.Hosting.IHostedService>Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:</span><span class="sxs-lookup"><span data-stu-id="534b4-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="534b4-124">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="534b4-125">`StartAsync` jest wywoływana *przed*:</span><span class="sxs-lookup"><span data-stu-id="534b4-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="534b4-126">Skonfigurowano potok przetwarzania żądania aplikacji ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="534b4-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="534b4-127">Serwer został uruchomiony i zostanie wyzwolony [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="534b4-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="534b4-128">Zachowanie domyślne można zmienić tak, aby usługa hostowana `StartAsync` działała po skonfigurowaniu potoku aplikacji i `ApplicationStarted` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="534b4-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="534b4-129">Aby zmienić zachowanie domyślne, należy dodać usługę hostowaną ( `VideosWatcher` w poniższym przykładzie) po wywołaniu `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="534b4-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="534b4-130">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="534b4-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="534b4-131">`StopAsync` zawiera logikę końcową zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="534b4-132">Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="534b4-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="534b4-133">Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny.</span><span class="sxs-lookup"><span data-stu-id="534b4-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="534b4-134">Gdy żądanie anulowania jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="534b4-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="534b4-135">Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.</span><span class="sxs-lookup"><span data-stu-id="534b4-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="534b4-136">Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.</span><span class="sxs-lookup"><span data-stu-id="534b4-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="534b4-137">Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.</span><span class="sxs-lookup"><span data-stu-id="534b4-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="534b4-138">Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany.</span><span class="sxs-lookup"><span data-stu-id="534b4-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="534b4-139">W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="534b4-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="534b4-140">Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:</span><span class="sxs-lookup"><span data-stu-id="534b4-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="534b4-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> w przypadku korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="534b4-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="534b4-142">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="534b4-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="534b4-143">Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="534b4-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="534b4-144">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="534b4-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="534b4-145">Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="534b4-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="534b4-146">Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="534b4-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="534b4-147">Klasa bazowa BackgroundService</span><span class="sxs-lookup"><span data-stu-id="534b4-147">BackgroundService base class</span></span>

<span data-ttu-id="534b4-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> jest klasą bazową do implementowania długotrwałego działania <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="534b4-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="534b4-149">[Wywoływanie ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) jest wywoływana w celu uruchomienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="534b4-150">Implementacja zwraca wartość <xref:System.Threading.Tasks.Task> reprezentującą cały okres istnienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="534b4-151">Żadne dalsze usługi nie są uruchamiane do momentu, gdy [wywoływanie ExecuteAsync staną się asynchroniczne](https://github.com/dotnet/extensions/issues/2149), na przykład przez wywołanie `await` .</span><span class="sxs-lookup"><span data-stu-id="534b4-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="534b4-152">Unikaj długotrwałych operacji inicjalizacji `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="534b4-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="534b4-153">Bloki hosta w [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) oczekują na `ExecuteAsync` zakończenie.</span><span class="sxs-lookup"><span data-stu-id="534b4-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="534b4-154">Token anulowania jest wyzwalany, gdy wywoływana jest [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="534b4-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="534b4-155">Implementacja programu `ExecuteAsync` powinna zakończyć się natychmiast po uruchomieniu tokenu anulowania w celu łagodnego zamknięcia usługi.</span><span class="sxs-lookup"><span data-stu-id="534b4-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="534b4-156">W przeciwnym razie usługa niebezpiecznie zamyka się po upływie limitu czasu zamykania.</span><span class="sxs-lookup"><span data-stu-id="534b4-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="534b4-157">Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [interfejsu IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="534b4-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="534b4-158">Zadania w tle czasu</span><span class="sxs-lookup"><span data-stu-id="534b4-158">Timed background tasks</span></span>

<span data-ttu-id="534b4-159">Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="534b4-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="534b4-160">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="534b4-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="534b4-161">Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="534b4-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="534b4-162"><xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="534b4-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="534b4-163">Z [blokadą. przyrost](xref:System.Threading.Interlocked.Increment*) służy do zwiększania licznika wykonywania jako operacja niepodzielna, która zapewnia, że wiele wątków nie zostanie jednocześnie zaktualizowanych `executionCount` .</span><span class="sxs-lookup"><span data-stu-id="534b4-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="534b4-164">Usługa jest zarejestrowana w `IHostBuilder.ConfigureServices` (*program.cs*) z `AddHostedService` metodą rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="534b4-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="534b4-165">Zużywanie usługi w zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="534b4-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="534b4-166">Aby użyć [usług z zakresem](xref:fundamentals/dependency-injection#service-lifetimes) w ramach [BackgroundService](#backgroundservice-base-class), Utwórz zakres.</span><span class="sxs-lookup"><span data-stu-id="534b4-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="534b4-167">Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.</span><span class="sxs-lookup"><span data-stu-id="534b4-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="534b4-168">Usługa zadań w tle w zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="534b4-169">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="534b4-169">In the following example:</span></span>

* <span data-ttu-id="534b4-170">Usługa jest asynchroniczna.</span><span class="sxs-lookup"><span data-stu-id="534b4-170">The service is asynchronous.</span></span> <span data-ttu-id="534b4-171">`DoWork`Metoda zwraca `Task` .</span><span class="sxs-lookup"><span data-stu-id="534b4-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="534b4-172">W celach demonstracyjnych oczekuje się, że w metodzie występuje opóźnienie o dziesięć sekund `DoWork` .</span><span class="sxs-lookup"><span data-stu-id="534b4-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="534b4-173"><xref:Microsoft.Extensions.Logging.ILogger>Jest wstrzykiwana do usługi.</span><span class="sxs-lookup"><span data-stu-id="534b4-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="534b4-174">Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie, aby wywołać `DoWork` metodę.</span><span class="sxs-lookup"><span data-stu-id="534b4-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="534b4-175">`DoWork` Zwraca element `Task` , który jest oczekiwany w `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="534b4-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="534b4-176">Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="534b4-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="534b4-177">Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="534b4-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="534b4-178">Zadania w kolejce w dół</span><span class="sxs-lookup"><span data-stu-id="534b4-178">Queued background tasks</span></span>

<span data-ttu-id="534b4-179">Kolejka zadań w tle jest oparta na platformie .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :</span><span class="sxs-lookup"><span data-stu-id="534b4-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="534b4-180">W poniższym `QueueHostedService` przykładzie:</span><span class="sxs-lookup"><span data-stu-id="534b4-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="534b4-181">`BackgroundProcessing`Metoda zwraca obiekt `Task` , który jest oczekiwany w `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="534b4-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="534b4-182">Zadania w tle w kolejce są dekolejkowane i wykonywane w `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="534b4-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="534b4-183">Elementy robocze oczekują na zatrzymanie usługi w usłudze `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="534b4-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="534b4-184">`MonitorLoop`Usługa obsługuje zadania umieszczenie dla usługi hostowanej za każdym razem, gdy `w` klucz zostanie wybrany na urządzeniu wejściowym:</span><span class="sxs-lookup"><span data-stu-id="534b4-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="534b4-185">`IBackgroundTaskQueue`Jest wstrzykiwana do `MonitorLoop` usługi.</span><span class="sxs-lookup"><span data-stu-id="534b4-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="534b4-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` jest wywoływana w celu dodawania do kolejki elementu pracy.</span><span class="sxs-lookup"><span data-stu-id="534b4-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="534b4-187">Element roboczy symuluje długotrwałe zadanie w tle:</span><span class="sxs-lookup"><span data-stu-id="534b4-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="534b4-188">3 5 — dwa opóźnienia są wykonywane ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="534b4-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="534b4-189">`try-catch`Pułapka instrukcji <xref:System.OperationCanceledException> , jeśli zadanie zostało anulowane.</span><span class="sxs-lookup"><span data-stu-id="534b4-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="534b4-190">Usługi są zarejestrowane w usłudze `IHostBuilder.ConfigureServices` (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="534b4-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="534b4-191">Usługa hostowana jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="534b4-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="534b4-192">`MonitorLoop` jest uruchamiany w `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="534b4-192">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="534b4-193">W ASP.NET Core zadania w tle można zaimplementować jako *usługi hostowane*.</span><span class="sxs-lookup"><span data-stu-id="534b4-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="534b4-194">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="534b4-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="534b4-195">Ten temat zawiera trzy przykłady usługi hostowanej:</span><span class="sxs-lookup"><span data-stu-id="534b4-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="534b4-196">Zadanie w tle, które jest uruchamiane na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="534b4-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="534b4-197">Usługa hostowana, która aktywuje [usługę](xref:fundamentals/dependency-injection#service-lifetimes)o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="534b4-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="534b4-198">Usługa objęta zakresem może używać [iniekcji zależności (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="534b4-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="534b4-199">Umieszczone w kolejce zadania w tle, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="534b4-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="534b4-200">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="534b4-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="534b4-201">Pakiet</span><span class="sxs-lookup"><span data-stu-id="534b4-201">Package</span></span>

<span data-ttu-id="534b4-202">Odwołuje się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="534b4-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="534b4-203">IHostedService, interfejs</span><span class="sxs-lookup"><span data-stu-id="534b4-203">IHostedService interface</span></span>

<span data-ttu-id="534b4-204">Usługi hostowane implementują <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="534b4-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="534b4-205">Interfejs definiuje dwie metody dla obiektów zarządzanych przez hosta:</span><span class="sxs-lookup"><span data-stu-id="534b4-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="534b4-206">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` zawiera logikę do uruchomienia zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="534b4-207">W przypadku korzystania z [hosta sieci Web](xref:fundamentals/host/web-host) `StartAsync` jest wywoływana po uruchomieniu serwera i wyzwoleniu [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .</span><span class="sxs-lookup"><span data-stu-id="534b4-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="534b4-208">W przypadku korzystania z [hosta generycznego](xref:fundamentals/host/generic-host) `StartAsync` jest wywoływana przed wywołaniem `ApplicationStarted` .</span><span class="sxs-lookup"><span data-stu-id="534b4-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="534b4-209">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): wyzwalane, gdy host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="534b4-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="534b4-210">`StopAsync` zawiera logikę końcową zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="534b4-211">Implementacja <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich niezarządzanych zasobów.</span><span class="sxs-lookup"><span data-stu-id="534b4-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="534b4-212">Token anulowania ma domyślnie pięciu sekund, aby wskazać, że proces zamykania nie powinien już być łagodny.</span><span class="sxs-lookup"><span data-stu-id="534b4-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="534b4-213">Gdy żądanie anulowania jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="534b4-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="534b4-214">Wszystkie pozostałe operacje w tle, które wykonuje aplikacja, powinny zostać przerwane.</span><span class="sxs-lookup"><span data-stu-id="534b4-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="534b4-215">Wszelkie metody wywoływane w programie `StopAsync` powinny zwracać monity.</span><span class="sxs-lookup"><span data-stu-id="534b4-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="534b4-216">Jednak zadania nie są porzucane po zażądaniu anulowania &mdash; obiekt wywołujący czeka na ukończenie wszystkich zadań.</span><span class="sxs-lookup"><span data-stu-id="534b4-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="534b4-217">Jeśli aplikacja nieoczekiwanie się zamknie (na przykład proces aplikacji kończy się niepowodzeniem), `StopAsync` może nie zostać wywołany.</span><span class="sxs-lookup"><span data-stu-id="534b4-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="534b4-218">W związku z tym wszystkie metody wywoływane lub operacje wykonywane w programie `StopAsync` mogą nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="534b4-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="534b4-219">Aby zwiększyć domyślne pięć sekund limitu czasu zamykania, ustaw:</span><span class="sxs-lookup"><span data-stu-id="534b4-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="534b4-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> w przypadku korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="534b4-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="534b4-221">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="534b4-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="534b4-222">Ustawienie konfiguracji hosta limitu czasu zamykania w przypadku korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="534b4-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="534b4-223">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="534b4-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="534b4-224">Usługa hostowana jest uaktywniana raz podczas uruchamiania aplikacji i bezpiecznie zamykana podczas zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="534b4-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="534b4-225">Jeśli wystąpi błąd podczas wykonywania zadania w tle, `Dispose` powinien zostać wywołany, nawet jeśli `StopAsync` nie jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="534b4-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="534b4-226">Zadania w tle czasu</span><span class="sxs-lookup"><span data-stu-id="534b4-226">Timed background tasks</span></span>

<span data-ttu-id="534b4-227">Zadanie w tle czasu używa klasy [System. Threading. Timer](xref:System.Threading.Timer) .</span><span class="sxs-lookup"><span data-stu-id="534b4-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="534b4-228">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="534b4-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="534b4-229">Czasomierz jest wyłączony `StopAsync` i zlikwidowany po usunięciu kontenera usługi `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="534b4-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="534b4-230"><xref:System.Threading.Timer>Nie czeka na zakończenie poprzednich wykonań `DoWork` , więc wskazane podejście może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="534b4-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="534b4-231">Usługa jest zarejestrowana w ramach `Startup.ConfigureServices` `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="534b4-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="534b4-232">Zużywanie usługi w zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="534b4-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="534b4-233">Aby korzystać z usług o określonym [zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w ramach programu `IHostedService` , należy utworzyć zakres.</span><span class="sxs-lookup"><span data-stu-id="534b4-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="534b4-234">Żaden zakres nie jest tworzony domyślnie dla usługi hostowanej.</span><span class="sxs-lookup"><span data-stu-id="534b4-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="534b4-235">Usługa zadań w tle w zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="534b4-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="534b4-236">W poniższym przykładzie <xref:Microsoft.Extensions.Logging.ILogger> jest wstrzykiwana do usługi:</span><span class="sxs-lookup"><span data-stu-id="534b4-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="534b4-237">Usługa hostowana tworzy zakres, aby rozwiązać usługę zadań w tle w zakresie w celu wywołania jej `DoWork` metody:</span><span class="sxs-lookup"><span data-stu-id="534b4-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="534b4-238">Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="534b4-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="534b4-239">`IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="534b4-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="534b4-240">Zadania w kolejce w dół</span><span class="sxs-lookup"><span data-stu-id="534b4-240">Queued background tasks</span></span>

<span data-ttu-id="534b4-241">Kolejka zadań w tle jest oparta na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([wstępnie zaplanowano wbudowaną ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="534b4-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="534b4-242">W programie `QueueHostedService` zadania w tle w kolejce są odrzucane i wykonywane jako [BackgroundService](#backgroundservice-base-class), która jest klasą bazową do implementowania długotrwałego działania `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="534b4-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="534b4-243">Usługi są zarejestrowane w usłudze `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="534b4-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="534b4-244">`IHostedService`Implementacja jest zarejestrowana przy użyciu `AddHostedService` metody rozszerzającej:</span><span class="sxs-lookup"><span data-stu-id="534b4-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="534b4-245">W klasie modelu strony indeksu:</span><span class="sxs-lookup"><span data-stu-id="534b4-245">In the Index page model class:</span></span>

* <span data-ttu-id="534b4-246">`IBackgroundTaskQueue`Zostaje wstrzyknięty do konstruktora i przypisany do `Queue` .</span><span class="sxs-lookup"><span data-stu-id="534b4-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="534b4-247"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Jest wstrzykiwany i przypisany do `_serviceScopeFactory` .</span><span class="sxs-lookup"><span data-stu-id="534b4-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="534b4-248">Fabryka służy do tworzenia wystąpień <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> , które są używane do tworzenia usług w zakresie.</span><span class="sxs-lookup"><span data-stu-id="534b4-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="534b4-249">Zakres jest tworzony w celu używania aplikacji `AppDbContext` ( [usługi w zakresie](xref:fundamentals/dependency-injection#service-lifetimes)) do zapisywania rekordów bazy danych w `IBackgroundTaskQueue` (pojedynczej usłudze).</span><span class="sxs-lookup"><span data-stu-id="534b4-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="534b4-250">Po wybraniu przycisku **Dodaj zadanie** na stronie indeks `OnPostAddTask` jest wykonywana metoda.</span><span class="sxs-lookup"><span data-stu-id="534b4-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="534b4-251">`QueueBackgroundWorkItem` jest wywoływana w celu dodawania do kolejki elementu pracy:</span><span class="sxs-lookup"><span data-stu-id="534b4-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="534b4-252">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="534b4-252">Additional resources</span></span>

* [<span data-ttu-id="534b4-253">Implementowanie zadań w tle w mikrousługach za pomocą IHostedService i klasy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="534b4-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="534b4-254">Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="534b4-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
