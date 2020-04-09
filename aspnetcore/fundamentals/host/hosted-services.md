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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="a4e97-103">Zadania w tle z hostowanymi usługami w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4e97-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="a4e97-104">Przez [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="a4e97-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4e97-105">W ASP.NET Core zadania w tle mogą być realizowane jako *usługi hostowane.*</span><span class="sxs-lookup"><span data-stu-id="a4e97-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a4e97-106">Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs.</span><span class="sxs-lookup"><span data-stu-id="a4e97-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a4e97-107">W tym temacie przedstawiono trzy przykłady usług hostowanych:</span><span class="sxs-lookup"><span data-stu-id="a4e97-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a4e97-108">Zadanie w tle, które działa na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="a4e97-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a4e97-109">Usługa hostowana, która aktywuje [usługę o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="a4e97-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a4e97-110">Usługa o określonym zakresie może używać [iniekcji zależności (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a4e97-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="a4e97-111">Zadania w tle umieszczone w kolejce, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a4e97-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a4e97-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="a4e97-113">Szablon usługi pracownika</span><span class="sxs-lookup"><span data-stu-id="a4e97-113">Worker Service template</span></span>

<span data-ttu-id="a4e97-114">Szablon ASP.NET Core Worker Service stanowi punkt wyjścia do pisania długo działających aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="a4e97-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="a4e97-115">Aplikacja utworzona na podstawie szablonu Usługi procesu roboczego określa sdk procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="a4e97-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="a4e97-116">Aby użyć szablonu jako podstawy dla aplikacji usług hostowanych:</span><span class="sxs-lookup"><span data-stu-id="a4e97-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="a4e97-117">Pakiet</span><span class="sxs-lookup"><span data-stu-id="a4e97-117">Package</span></span>

<span data-ttu-id="a4e97-118">Aplikacja oparta na szablonie usługi `Microsoft.NET.Sdk.Worker` procesu roboczego używa zestawu SDK i ma jawne odwołanie do pakietu [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)</span><span class="sxs-lookup"><span data-stu-id="a4e97-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="a4e97-119">Na przykład zobacz przykładowy plik projektu aplikacji (*BackgroundTasksSample.csproj*).</span><span class="sxs-lookup"><span data-stu-id="a4e97-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="a4e97-120">W przypadku aplikacji `Microsoft.NET.Sdk.Web` sieci web, które używają zestawu SDK, pakiet [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) odwołuje się niejawnie z udostępnionej struktury.</span><span class="sxs-lookup"><span data-stu-id="a4e97-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="a4e97-121">Jawne odwołanie do pakietu w pliku projektu aplikacji nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="a4e97-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a4e97-122">Interfejs IHostedService</span><span class="sxs-lookup"><span data-stu-id="a4e97-122">IHostedService interface</span></span>

<span data-ttu-id="a4e97-123">Interfejs <xref:Microsoft.Extensions.Hosting.IHostedService> definiuje dwie metody dla obiektów, które są zarządzane przez hosta:</span><span class="sxs-lookup"><span data-stu-id="a4e97-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a4e97-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` zawiera logikę uruchamiania zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a4e97-125">`StartAsync`nazywa się *przed:*</span><span class="sxs-lookup"><span data-stu-id="a4e97-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="a4e97-126">Potok przetwarzania żądań aplikacji jest`Startup.Configure`skonfigurowany ( ).</span><span class="sxs-lookup"><span data-stu-id="a4e97-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="a4e97-127">Serwer jest uruchamiany i [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) jest wyzwalany.</span><span class="sxs-lookup"><span data-stu-id="a4e97-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="a4e97-128">Domyślne zachowanie można zmienić tak, aby `StartAsync` usługa hostowana przebiegała po `ApplicationStarted` skonfigurowaniu potoku aplikacji i wywoływaniu.</span><span class="sxs-lookup"><span data-stu-id="a4e97-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="a4e97-129">Aby zmienić zachowanie domyślne, dodaj`VideosWatcher` usługę hosta (w poniższym przykładzie) po wywołaniu: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="a4e97-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="a4e97-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Wyzwalane, gdy host wykonuje pełne wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="a4e97-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a4e97-131">`StopAsync`zawiera logikę, aby zakończyć zadanie w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a4e97-132">Implementowanie <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich zasobów niezarządzanych.</span><span class="sxs-lookup"><span data-stu-id="a4e97-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a4e97-133">Token anulowania ma domyślny limit czasu pięciu sekund, aby wskazać, że proces zamykania nie powinien być już pełen wdzięku.</span><span class="sxs-lookup"><span data-stu-id="a4e97-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a4e97-134">Gdy anulowanie jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="a4e97-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a4e97-135">Wszystkie pozostałe operacje w tle, które działa aplikacja powinna zostać przerwana.</span><span class="sxs-lookup"><span data-stu-id="a4e97-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a4e97-136">Wszelkie metody `StopAsync` wywoływane w należy zwrócić niezwłocznie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a4e97-137">Jednak zadania nie są porzucone po&mdash;anulowaniu jest wymagane wywołujący oczekuje na wszystkie zadania do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a4e97-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a4e97-138">Jeśli aplikacja zostanie nieoczekiwanie zamknięta (na przykład proces aplikacji `StopAsync` zakończy się niepowodzeniem), może nie zostać wywołana.</span><span class="sxs-lookup"><span data-stu-id="a4e97-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a4e97-139">W związku z tym wszelkie metody `StopAsync` wywoływane lub operacje prowadzone w może nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="a4e97-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a4e97-140">Aby przedłużyć domyślny limit czasu zamknięcia o pięć sekund, ustaw:</span><span class="sxs-lookup"><span data-stu-id="a4e97-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a4e97-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>podczas korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="a4e97-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a4e97-142">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a4e97-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a4e97-143">Ustawienie konfiguracji hosta limitu czasu zamknięcia podczas korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a4e97-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a4e97-144">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a4e97-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a4e97-145">Hostowana usługa jest aktywowana raz przy uruchamianiu aplikacji i bezpiecznie zamykana przy zamykaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e97-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a4e97-146">Jeśli podczas wykonywania zadania w `Dispose` tle zostanie wyświetlony `StopAsync` błąd, należy wywołać, nawet jeśli nie jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="a4e97-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="a4e97-147">Klasa podstawowa usługi backgroundService</span><span class="sxs-lookup"><span data-stu-id="a4e97-147">BackgroundService base class</span></span>

<span data-ttu-id="a4e97-148"><xref:Microsoft.Extensions.Hosting.BackgroundService>jest klasą bazową do <xref:Microsoft.Extensions.Hosting.IHostedService>wdrażania długotrwałego .</span><span class="sxs-lookup"><span data-stu-id="a4e97-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="a4e97-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) jest wywoływana do uruchomienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="a4e97-150">Implementacja <xref:System.Threading.Tasks.Task> zwraca, który reprezentuje cały okres istnienia usługi w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="a4e97-151">Żadne dalsze usługi nie są uruchamiane, dopóki [ExecuteAsync nie stanie się asynchroniczny,](https://github.com/dotnet/extensions/issues/2149)na przykład przez wywołanie `await`.</span><span class="sxs-lookup"><span data-stu-id="a4e97-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="a4e97-152">Unikaj długich czynności, blokując `ExecuteAsync`pracę inicjalizacji w pliku .</span><span class="sxs-lookup"><span data-stu-id="a4e97-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="a4e97-153">Bloki hosta w [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) oczekują na `ExecuteAsync` ukończenie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="a4e97-154">Token anulowania jest wyzwalany, gdy wywoływana jest [usługa IHostedService.StopAsync.](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)</span><span class="sxs-lookup"><span data-stu-id="a4e97-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="a4e97-155">Implementacja `ExecuteAsync` powinna zakończyć się niezwłocznie, gdy token anulowania jest uruchamiany w celu bezpiecznie zamknąć usługę.</span><span class="sxs-lookup"><span data-stu-id="a4e97-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="a4e97-156">W przeciwnym razie usługa ungracefully zamyka się na limit czasu zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="a4e97-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="a4e97-157">Aby uzyskać więcej informacji, zobacz sekcję [interfejsu IHostedService.](#ihostedservice-interface)</span><span class="sxs-lookup"><span data-stu-id="a4e97-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a4e97-158">Zadania w tle z harmonogramem</span><span class="sxs-lookup"><span data-stu-id="a4e97-158">Timed background tasks</span></span>

<span data-ttu-id="a4e97-159">Czasowe zadanie w tle korzysta z [Klasy System.Threading.Timer.](xref:System.Threading.Timer)</span><span class="sxs-lookup"><span data-stu-id="a4e97-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a4e97-160">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="a4e97-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a4e97-161">Czasomierz jest `StopAsync` wyłączony i usuwany, gdy pojemnik `Dispose`serwisowy jest usuwany na:</span><span class="sxs-lookup"><span data-stu-id="a4e97-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="a4e97-162">Nie <xref:System.Threading.Timer> czeka na poprzednie wykonanie `DoWork` zakończyć, więc podejście pokazane może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="a4e97-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="a4e97-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) jest używany do zwiększania licznika wykonywania jako operacji niepodzielnej, co `executionCount` zapewnia, że wiele wątków nie aktualizuje jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="a4e97-164">Usługa jest zarejestrowana w `IHostBuilder.ConfigureServices` (*Program.cs* `AddHostedService` ) z metodą rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a4e97-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a4e97-165">Korzystanie z usługi o określonym zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="a4e97-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a4e97-166">Aby użyć [usług o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w [backgroundservice](#backgroundservice-base-class), należy utworzyć zakres.</span><span class="sxs-lookup"><span data-stu-id="a4e97-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="a4e97-167">Domyślnie nie jest tworzony żaden zakres dla usługi hosta.</span><span class="sxs-lookup"><span data-stu-id="a4e97-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a4e97-168">Usługa zadań w tle o określonym zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a4e97-169">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a4e97-169">In the following example:</span></span>

* <span data-ttu-id="a4e97-170">Usługa jest asynchroniza.</span><span class="sxs-lookup"><span data-stu-id="a4e97-170">The service is asynchronous.</span></span> <span data-ttu-id="a4e97-171">Metoda `DoWork` zwraca `Task`.</span><span class="sxs-lookup"><span data-stu-id="a4e97-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="a4e97-172">W celach demonstracyjnych w metodzie `DoWork` oczekuje się opóźnienia o dziesięć sekund.</span><span class="sxs-lookup"><span data-stu-id="a4e97-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="a4e97-173">An <xref:Microsoft.Extensions.Logging.ILogger> jest wstrzykiwany do serwisu.</span><span class="sxs-lookup"><span data-stu-id="a4e97-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a4e97-174">Usługa hostowana tworzy zakres, aby rozwiązać zakres usługi `DoWork` zadań w tle, aby wywołać jego metodę.</span><span class="sxs-lookup"><span data-stu-id="a4e97-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="a4e97-175">`DoWork`zwraca `Task`, który jest `ExecuteAsync`oczekiwany w :</span><span class="sxs-lookup"><span data-stu-id="a4e97-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="a4e97-176">Usługi są zarejestrowane w `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a4e97-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a4e97-177">Usługa hostowana jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a4e97-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a4e97-178">Zadania w tle umieszczone w kolejce</span><span class="sxs-lookup"><span data-stu-id="a4e97-178">Queued background tasks</span></span>

<span data-ttu-id="a4e97-179">Kolejka zadań w tle jest oparta <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> na .NET 4.x[(wstępnie zaplanowane do wbudowania dla ASP.NET Core):](https://github.com/aspnet/Hosting/issues/1280)</span><span class="sxs-lookup"><span data-stu-id="a4e97-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a4e97-180">W poniższym `QueueHostedService` przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a4e97-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="a4e97-181">Metoda `BackgroundProcessing` zwraca `Task`, który jest `ExecuteAsync`oczekiwany w .</span><span class="sxs-lookup"><span data-stu-id="a4e97-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="a4e97-182">Zadania w tle w kolejce są `BackgroundProcessing`usuwane z kolejki i wykonywane w pliku .</span><span class="sxs-lookup"><span data-stu-id="a4e97-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="a4e97-183">Elementy robocze są oczekiwane `StopAsync`przed zatrzymaniem usługi w .</span><span class="sxs-lookup"><span data-stu-id="a4e97-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="a4e97-184">Usługa `MonitorLoop` obsługuje zadania wyjęcie w kolejce `w` dla usługi hosta, gdy klucz jest wybrany na urządzeniu wejściowym:</span><span class="sxs-lookup"><span data-stu-id="a4e97-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="a4e97-185">Jest `IBackgroundTaskQueue` wstrzykiwany do `MonitorLoop` serwisu.</span><span class="sxs-lookup"><span data-stu-id="a4e97-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="a4e97-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem`jest wywoływana w celu wyłowienia w kolejce elementu roboczego.</span><span class="sxs-lookup"><span data-stu-id="a4e97-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="a4e97-187">Element roboczy symuluje długotrwałe zadanie w tle:</span><span class="sxs-lookup"><span data-stu-id="a4e97-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="a4e97-188">Wykonywane są trzy 5-sekundowe opóźnienia (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="a4e97-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="a4e97-189">Pułapki `try-catch` <xref:System.OperationCanceledException> instrukcji, jeśli zadanie zostało anulowane.</span><span class="sxs-lookup"><span data-stu-id="a4e97-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="a4e97-190">Usługi są zarejestrowane w `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="a4e97-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="a4e97-191">Usługa hostowana jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a4e97-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="a4e97-192">`MontiorLoop`rozpoczyna się `Program.Main`w:</span><span class="sxs-lookup"><span data-stu-id="a4e97-192">`MontiorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4e97-193">W ASP.NET Core zadania w tle mogą być realizowane jako *usługi hostowane.*</span><span class="sxs-lookup"><span data-stu-id="a4e97-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="a4e97-194">Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs.</span><span class="sxs-lookup"><span data-stu-id="a4e97-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a4e97-195">W tym temacie przedstawiono trzy przykłady usług hostowanych:</span><span class="sxs-lookup"><span data-stu-id="a4e97-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="a4e97-196">Zadanie w tle, które działa na czasomierzu.</span><span class="sxs-lookup"><span data-stu-id="a4e97-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="a4e97-197">Usługa hostowana, która aktywuje [usługę o określonym zakresie](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="a4e97-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a4e97-198">Usługa o określonym zakresie może używać [iniekcji zależności (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a4e97-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="a4e97-199">Zadania w tle umieszczone w kolejce, które są uruchamiane sekwencyjnie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="a4e97-200">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a4e97-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="a4e97-201">Pakiet</span><span class="sxs-lookup"><span data-stu-id="a4e97-201">Package</span></span>

<span data-ttu-id="a4e97-202">Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)</span><span class="sxs-lookup"><span data-stu-id="a4e97-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a4e97-203">Interfejs IHostedService</span><span class="sxs-lookup"><span data-stu-id="a4e97-203">IHostedService interface</span></span>

<span data-ttu-id="a4e97-204">Usługi hostowane <xref:Microsoft.Extensions.Hosting.IHostedService> implementują interfejs.</span><span class="sxs-lookup"><span data-stu-id="a4e97-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a4e97-205">Interfejs definiuje dwie metody dla obiektów, które są zarządzane przez hosta:</span><span class="sxs-lookup"><span data-stu-id="a4e97-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="a4e97-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` zawiera logikę uruchamiania zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="a4e97-207">Podczas korzystania z `StartAsync` [hosta sieci Web](xref:fundamentals/host/web-host), jest wywoływana po uruchomieniu serwera i [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) jest wyzwalany.</span><span class="sxs-lookup"><span data-stu-id="a4e97-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="a4e97-208">Podczas korzystania z `StartAsync` [hosta ogólnego](xref:fundamentals/host/generic-host), jest wywoływana przed `ApplicationStarted` jest wyzwalany.</span><span class="sxs-lookup"><span data-stu-id="a4e97-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="a4e97-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Wyzwalane, gdy host wykonuje pełne wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="a4e97-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="a4e97-210">`StopAsync`zawiera logikę, aby zakończyć zadanie w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="a4e97-211">Implementowanie <xref:System.IDisposable> i [finalizatory (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) do usuwania wszelkich zasobów niezarządzanych.</span><span class="sxs-lookup"><span data-stu-id="a4e97-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a4e97-212">Token anulowania ma domyślny limit czasu pięciu sekund, aby wskazać, że proces zamykania nie powinien być już pełen wdzięku.</span><span class="sxs-lookup"><span data-stu-id="a4e97-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a4e97-213">Gdy anulowanie jest wymagane na tokenie:</span><span class="sxs-lookup"><span data-stu-id="a4e97-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="a4e97-214">Wszystkie pozostałe operacje w tle, które działa aplikacja powinna zostać przerwana.</span><span class="sxs-lookup"><span data-stu-id="a4e97-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="a4e97-215">Wszelkie metody `StopAsync` wywoływane w należy zwrócić niezwłocznie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="a4e97-216">Jednak zadania nie są porzucone po&mdash;anulowaniu jest wymagane wywołujący oczekuje na wszystkie zadania do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a4e97-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="a4e97-217">Jeśli aplikacja zostanie nieoczekiwanie zamknięta (na przykład proces aplikacji `StopAsync` zakończy się niepowodzeniem), może nie zostać wywołana.</span><span class="sxs-lookup"><span data-stu-id="a4e97-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a4e97-218">W związku z tym wszelkie metody `StopAsync` wywoływane lub operacje prowadzone w może nie wystąpić.</span><span class="sxs-lookup"><span data-stu-id="a4e97-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="a4e97-219">Aby przedłużyć domyślny limit czasu zamknięcia o pięć sekund, ustaw:</span><span class="sxs-lookup"><span data-stu-id="a4e97-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="a4e97-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>podczas korzystania z hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="a4e97-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="a4e97-221">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a4e97-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="a4e97-222">Ustawienie konfiguracji hosta limitu czasu zamknięcia podczas korzystania z hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a4e97-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a4e97-223">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a4e97-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a4e97-224">Hostowana usługa jest aktywowana raz przy uruchamianiu aplikacji i bezpiecznie zamykana przy zamykaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4e97-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a4e97-225">Jeśli podczas wykonywania zadania w `Dispose` tle zostanie wyświetlony `StopAsync` błąd, należy wywołać, nawet jeśli nie jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="a4e97-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a4e97-226">Zadania w tle z harmonogramem</span><span class="sxs-lookup"><span data-stu-id="a4e97-226">Timed background tasks</span></span>

<span data-ttu-id="a4e97-227">Czasowe zadanie w tle korzysta z [Klasy System.Threading.Timer.](xref:System.Threading.Timer)</span><span class="sxs-lookup"><span data-stu-id="a4e97-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="a4e97-228">Czasomierz wyzwala `DoWork` metodę zadania.</span><span class="sxs-lookup"><span data-stu-id="a4e97-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a4e97-229">Czasomierz jest `StopAsync` wyłączony i usuwany, gdy pojemnik `Dispose`serwisowy jest usuwany na:</span><span class="sxs-lookup"><span data-stu-id="a4e97-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="a4e97-230">Nie <xref:System.Threading.Timer> czeka na poprzednie wykonanie `DoWork` zakończyć, więc podejście pokazane może nie być odpowiednie dla każdego scenariusza.</span><span class="sxs-lookup"><span data-stu-id="a4e97-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="a4e97-231">Usługa jest zarejestrowana `Startup.ConfigureServices` `AddHostedService` przy metodzie rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a4e97-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a4e97-232">Korzystanie z usługi o określonym zakresie w zadaniu w tle</span><span class="sxs-lookup"><span data-stu-id="a4e97-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="a4e97-233">Aby użyć usług o `IHostedService`określonym [zakresie](xref:fundamentals/dependency-injection#service-lifetimes) w ramach programu , utwórz zakres.</span><span class="sxs-lookup"><span data-stu-id="a4e97-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="a4e97-234">Domyślnie nie jest tworzony żaden zakres dla usługi hosta.</span><span class="sxs-lookup"><span data-stu-id="a4e97-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="a4e97-235">Usługa zadań w tle o określonym zakresie zawiera logikę zadania w tle.</span><span class="sxs-lookup"><span data-stu-id="a4e97-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="a4e97-236">W poniższym przykładzie jest <xref:Microsoft.Extensions.Logging.ILogger> wstrzykiwany do usługi:</span><span class="sxs-lookup"><span data-stu-id="a4e97-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a4e97-237">Usługa hostowana tworzy zakres, aby rozwiązać zakres usługi `DoWork` zadań w tle, aby wywołać jego metodę:</span><span class="sxs-lookup"><span data-stu-id="a4e97-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="a4e97-238">Usługi są zarejestrowane w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4e97-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4e97-239">Implementacja `IHostedService` jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a4e97-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a4e97-240">Zadania w tle umieszczone w kolejce</span><span class="sxs-lookup"><span data-stu-id="a4e97-240">Queued background tasks</span></span>

<span data-ttu-id="a4e97-241">Kolejka zadań w tle jest oparta na <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> programie .NET Framework 4.x[(wstępnie zaplanowano, że zostanie wbudowana dla ASP.NET Core):](https://github.com/aspnet/Hosting/issues/1280)</span><span class="sxs-lookup"><span data-stu-id="a4e97-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a4e97-242">W `QueueHostedService`, zadania w tle w kolejce są usuwane z kolejki i wykonywane jako `IHostedService` [BackgroundService](#backgroundservice-base-class), który jest klasą podstawową do implementowania długo działa:</span><span class="sxs-lookup"><span data-stu-id="a4e97-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="a4e97-243">Usługi są zarejestrowane w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4e97-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4e97-244">Implementacja `IHostedService` jest zarejestrowana przy metodzie `AddHostedService` rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="a4e97-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="a4e97-245">W klasie modelu strony Indeks:</span><span class="sxs-lookup"><span data-stu-id="a4e97-245">In the Index page model class:</span></span>

* <span data-ttu-id="a4e97-246">Jest `IBackgroundTaskQueue` wstrzykiwany do konstruktora `Queue`i przypisany do .</span><span class="sxs-lookup"><span data-stu-id="a4e97-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="a4e97-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> jest wstrzykiwany i `_serviceScopeFactory`przypisany do .</span><span class="sxs-lookup"><span data-stu-id="a4e97-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="a4e97-248">Fabryka służy do tworzenia <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>wystąpień , który jest używany do tworzenia usług w zakresie.</span><span class="sxs-lookup"><span data-stu-id="a4e97-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="a4e97-249">Zakres jest tworzony w celu użycia `AppDbContext` aplikacji [(usługa o określonym zakresie)](xref:fundamentals/dependency-injection#service-lifetimes) `IBackgroundTaskQueue` do zapisu rekordów bazy danych w (usługa singleton).</span><span class="sxs-lookup"><span data-stu-id="a4e97-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="a4e97-250">Po wybraniu przycisku **Dodaj zadanie** na `OnPostAddTask` stronie Indeks, metoda jest wykonywana.</span><span class="sxs-lookup"><span data-stu-id="a4e97-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="a4e97-251">`QueueBackgroundWorkItem`jest wywoływana w celu wyłowienia elementu roboczego w kolejce:</span><span class="sxs-lookup"><span data-stu-id="a4e97-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a4e97-252">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a4e97-252">Additional resources</span></span>

* [<span data-ttu-id="a4e97-253">Implementowanie zadań w tle w mikrousługach za pomocą usługi IHostedService i klasy BackgroundService</span><span class="sxs-lookup"><span data-stu-id="a4e97-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="a4e97-254">Uruchamianie zadań w tle za pomocą aplikacji WebJobs w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a4e97-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
